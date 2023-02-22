---
layout: post
title:  "Case study: asynchronous database calls"
tags: [production]
usemathjax: true
---

This is a brief description of a problem setting and a solution adopted
for checking answers given by students to [Hypermath](http://7.math.ru/)
system. It does not pretend to be a best practice, it's just a working
solution I came up with. It is not necessary to mention Hypermath
through the text, but problem needs an illustrative example.

#### Actors

Hypermath is a client-server application receiving requests via HTTP. It
is connected with remote relational database and with remote in-memory
storage mostly used as a database cache. Application is solidly
orchestrated (thanks to
[Egor](https://www.linkedin.com/in/egor-kuzmichev/)) and supports
rolling releases.

System is dedicated to serve as a teaching assistant for school teachers
in mathematics. Its target audience is quite big hence system must work
with high throughput. Specifics of the system yield an obvious critical
point: testing of given answers.

#### Problem statement

When student press button "Check" the following actions must happen:

1.  Result of the check should be computed.
2.  Result should be communicated to teachers of the student via
    websocket in order to maintain realtime statistics of their classes.
    During this step we ask database about list of teachers to receive
    the message. It seems not convenient to store mapping
    student-teacher in a cache.
3.  Data describing the check request should be collected to database to
    be accessible in student's statistics.

At a first glance there is no problem with consecutive execution of
these steps in a handler thread (server uses
[warp](https://hackage.haskell.org/package/warp-3.3.23)). But if we
perform a load testing we immediately become quite disappointed by the
result.

The hardest computation is performed during answer test itself. This
step is absolutely unavoidable since student needs to see result
immediately. If there was no solution to simplify this step post would
be over.

But answer determines the check result. Hence check results can be
cached. Every math problem is tested by people before publication to the
system hence in practice all frequent answers are known to system prior
to publication. Hence step 1 is actually very fast in overwhelming
majority of cases.

This solution unlocks the problem I want to talk about. Given this
sequence of required operations performed by a handler we have to
redesign it and reduce latency of a request at an acceptable cost of
delays in statistics updates. Importantly, we must guarantee data safety
in case of emergency or, more frequently, new releases.

#### Adopted solution

Header of the post suggests that some actions can be performed
asynchronously.

In particular, both operations 2 and 3 can be performed asynchronously.

Let's talk about 3 first. Since statistics is stored in a table with
several indices write operations are better to be performed not very
frequently. So we store data to cache at the same action as 2 and then a
separate job periodically flushes it to database.

Naive approach to 2 is to simply (modulo explicit passing of computation
context or accurate usage of `MonadBaseControl IO` or similar technique)
put `forkIO` before action.

But wait. Consider the basic version of the handler and its behavior
under load.

Assume we give load of 1000rps with throughput of a handler 100rps. Then
we first see normal dynamics with 100rps and when near the response
timeout a massive and exponentially increasing list of 504s. At some
point application freezes. It is a freeze of a web-server running the
app which is at capacity with waiting requests. Good model of server
crash under load can be found
[here](https://www.linuxjournal.com/article/4878).

Substantial part of the server latency comes from communication with
database. Regarding PostgreSQL, there is a setting `max_connections`
which limits number of simultaneously active sessions. It is recommended
to keep it small because each connection is maintained by separate
system process on a database host.

So assume we run computation which connects to database asynchronously
and we give app a constant load of 1000rps. Indeed we see desired
1000rps for some time in a testing protocol. But in a background we have
quickly reached `max_connections` . Hence all other handlers requesting
database are put under stress and slowed down --- we have allowed
actually more attempts to connect than in consecutive implementations
since web-server lets more requests to reach application in a moment.
Moreover we have moved exponential growth in number of requests to
application side. Hence we have created a little fork-bomb which
eventually explodes and causes application to freeze in a process of
context-switching. Green threads are lightweight but there are a lot of
them.

This is the natural setting to introduce threadpools. However this
approach is not widely used in Haskell, new threads can be created with
almost no overhead. Solution we adopted is to limit number of
simultaneously run limited computations using `QSem`.

Explicitly it is written as follows:

``` haskell
sem <- getThreadPool
liftBaseWith (\runInBase -> forkIO
                          . bracket_ (waitQSem sem) (signalQSem sem)
                          $ runInBase action
             )
```

where `sem` is a semaphore stored in global application context. We
actually allow creation of new threads and only freeze before running
heavy actions. But this is enough to avoid excess context-switching,
most of the threads are locked. And indeed we limit burden on a
database.

From this point handler works as expected. Cost applied --- delays in
receiving students' statistics may become significant.

But what's with data safety? Assume release comes during operation under
stress. Orchestration system stops replaced service only when all
requests to it are answered but it ignores silent computations. We get a
risk of statistical data loss before write to cache, which is increased
by increased delays.

To deal with it we had to implement graceful termination. It est we
track number of active explicitly forked threads and upon receiving
interceptible termination signal wait for them all to finish. Regarding
handler threads we rely on warp's graceful termination.

Example code of how threads can be counted is
[here](https://gist.github.com/viviag/107782396a0809acbac09123a0c4155c).
Note that semaphores also count threads but we need some concurrency
primitive anyway to lock signal handler.

So here it is: caching, periodic flushes of cached data to database,
asynchronous actions with limit on number of simultaneously run ones,
and graceful termination primitive.
