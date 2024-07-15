---
layout: post
title:  "Case study: transitive closure computation in the wild"
date:   2024-07-15 17:58 CET
tags: [algorithms]
---

In programmers' education, at least in Russia, there is a strong competitive tradition. Students compete in olympiads and contests, these contests mainly consist of algorithmic problems. Especially authors of them like graph algorithms. Many people invest a lot into these skills and excel in solving algorithmic problems. Some of them become university professors and teach algorithms to a broader audience.

Some of them and whose they taught will use these skills outside of the tournament or the classroom once in next several years on an algorithmic section of their successful interview to Google. Algorithms from courses in discrete mathematics are not a part of daily work of many programmers. For some, they are. For me, they are not, but scope of issues reaching me in hope of being closed is broad enough to regularly involve implementation of some algorithm. Some of these issues can be easily disclosed for the public. Some of them contain encounters of algorithms, that may seem surprising at a first glance and leave a feeling as if you see a rare animal from your window. "Hey, look at it!". This post is short and is likely of no interest for whose who see graph algorithms everyday.

#### The setting

Assume you work on a big project. "Big" here means that it has several local packages with dependencies between them in a single repository. You have a build tool which can build the given set of packages or the whole project. In our case, the tool is [Stack](https://docs.haskellstack.org/en/stable/) and the packages are a set of packages grouped under the same global configuration file (`stack.yaml`).

You also have a continuous integration set up. So you commit, system builds the code, deploy new executables to registry in a convenient form and then some tool look the registry updates up and deploy them to the applications cluster.

Packages are very different. Some of them are libraries. Some of them are stateful services. For these either some persistence should be designed or we have to accept that each restart leads to loss of the state. Some of them are high-throughput services. Restart of them under load may cause different types of troubles --- sudden bottleneck while scaling replicas `n \to 1 \to n`, episodes of inconsistent behavior under some circumstances etc.

So it's important not to restart application if it was not updated. The `stack install` command without arguments copies all the executables to folder with binaries regardless if they were updated or not. So we have to avoid it.

One possible solution is to parse the output of something like `git diff --stat` to get the list of changed packages and feed it to `stack install <args>`.

#### The problem

The project has internal dependency relations. Assume we only change a library which is used in some executable. The solution proposed will lead to rebuild of the library, but no executable will be updated. Effectively the release will not happen.

This problem is easy to formalize. Packages with dependencies between them form an oriented graph. Let's orient edges as `dependency -> dependent`. The set of packages directly changed by update is a set of vertices. We need to find all vertices reachable from the set by dependency arrows, i.e. to find a transivite closure of a subgraph.

#### The solution

The problem is very famous. Even [wikipedia](https://en.wikipedia.org/wiki/Transitive_closure#Algorithms) lists a set of solutions. But we have this problem in the wild, hence it has an additional context.

In this case, the context is given by the input format. Stack is a smart tool which allows to print the local dependency graph by a `stack dot` command. It prints the [.dot](https://en.wikipedia.org/wiki/DOT_(graph_description_language)) file and allows to skip writing a program to construct the graph from package-level configuration files.

The dot file it prints is organized as follows:
```dot
v1 -> v2
v1 -> v4
v2 -> v3
v2 -> v4
v3 -> v4
```

Id est it represents the graph as a list of edges grouped by the initial vertex. This is a ready to use representation to run a search like [breadth-first search](https://en.wikipedia.org/wiki/Breadth-first_search). On this size of a graph, we could exclude time complexity from consideration at all.

The breadth-first search differs from depth-first search and all other searches in the traversal order. In my input file I have the graph already traversed. As it is small by the nature of the setting, I can just run through this list of edges.
How to do it --- start with the initial list `l` of vertices and on each egde add the end vertex to `l` if the start is in `l`. Let's refer to the positive case of this conditional by "use vertex". 

Single run will not solve the problem. Consider the example
```dot
a -> c
b -> d
c -> b
```

On a single run starting from the single vertex `a`, the algorithm will find `c` and `b`, but skip `d`. So we traverse edges from unused vertices again until we fail to find anything new.

That's it. For a long explanation based on algorithmic training there are plenty of recourses like [this](https://www.geeksforgeeks.org/transitive-closure-of-a-graph-using-dfs/).

#### P.S.

The solution was designed under impression that Stack always rebuilds all the executables if `stack build` is called without specific targets. As [Mike Pilgrem](https://github.com/mpilgrem) [explains](https://github.com/commercialhaskell/stack/issues/6622#issuecomment-2227093821) to me, it is not true, so the problem is almost solvable by tracking the `last_modified` property of binaries coming from the build and from the build cache. His solution applied to my setting has the only one failure point --- if the build cache on CI is unavailable, the procedure will redeploy everything. In-house implementation of the transitive closure algorithm does not depend on specific build properties. I guess we will deploy this solution.

#### P.P.S.

From this point it's quite easy to fall to mathematical discussion. The transitive closure problem can be solved using the Floyd-Warshall algorithm. As [Alexander Tiskin](https://math-cs.spbu.ru/en/people/alexander-v-tiskin/) told me in January, it is essentially the Gauss algorithm in the tropical semiring. I hope to write some post on the algebraic path problem soon. 