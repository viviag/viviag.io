---
layout: post
title: "Release: preprint on persistent homological Quillen-McCord theorem"
tags: [math-at]
---

[Here it is](https://arxiv.org/abs/2307.00177). Below is the story and the context of this work.

### Intro

In 2019, [faculty of computer science at HSE](https://cs.hse.ru/en/) and personally [Anton Ayzenberg](https://www.ayzenberg.xyz/) and [Vsevolod Chernyshev](https://www.hse.ru/en/org/persons/35919212) organized the laboratory for applied geometry and topology. The main project of the laboratory was inspired by [paper](https://pubmed.ncbi.nlm.nih.gov/18974826/) by Curto and Itskov and aimed to reconstruct topology of a room by neuronal activity of an animal.

This task is indeed complex and is strong enough for the dedicated laboratory. The laboratory in initial form was started as educational, hence one of the first action taken by organizers was to open a recruitment of research interns. I was among the first interns selected. The laboratory also collaborated with faculty of MSU, who were responsible for experimental data.

### Main problem

Several words must be written about the major task.

Assume there is a genetically modified mouse (rats were not available) walking over the room with several obstacles. During her walks, spikes of place cells of her brain happen in certain regions of the room. We can detect these spikes and, theoretically, map them to coordinates of the mouse.

Assume that regions of coverage of single neurons are good enough to be thought of as of open disks of a fixed radius. Then as mouse moves and eventually covers all the room, these regions also cover all the room. Coverage by open disks is good in sense that all n-wise intersections of covering sets are contractible. And the room is modeled by a closed bounded set on a plane, hence is compact. Hence the conditions of the Čech theorem are satisfied. The covering is known by experimental data, hence the Čech complex is also (theoretically) known. And this is the way to the declared aim.

There are two ways in which persistence come into play:

1. Spikes are not a discrete thing. Choosing different activation threshold, one can (in the model, again) vary radius of disks in the covering. This variation gives a filtration on Čech complex.
2. Mouse learns the room better with each step. Hence the whole map of the room in the brain changes over time.

If one wants to obtain the best reliable data about the room, she should focus on both dimensions. The second is way harder to measure, so the lab focused on the first.

I'm not sure if there are some reliable results out of this work. I know about preprint, published by [Konstantin Sorokin](https://www.hse.ru/en/org/persons/305123525), but I didn't read it. But anyways, there were obstacles on the way, in particular, computational complexity of persistent homology calculation. 

### About this specific release

As a pure math student, I was mostly out of the work with data, or at least I didn't think about it much. But I was assigned a specific problem: explore ways to reduce dimensions of Čech complexes we work with. I was also given a hint to look at the Quillen-McCord theorem (or Quillen fiber lemma, or Quillen theorem A for posets, I'm never sure which name is best known).

On a second year of studies, I was not skilled enough technically for the task. By the end (in May '20) I thought I made an advancement, i.e. a persistent version of the theorem. But at the same time I became a father and started to create my department in MCCME from scratch. I had to quit the lab, I was mentally unable to continue with the theorem, and I finally stopped to think about it.

Two and a half years later I decided I'm ready to finish this job. Basic examination of the text convinced me the strategy was correct, but the proof was actually missing. I told Anton that there are enough changes to be made to make it a diploma work, and started to rewrite it. This preprint is an important milestone, is substantially the same as my graduation thesis, and, to my current understanding, contains the theorem with the correct proof.

The process of the work [is reflected](https://github.com/viviag/Approx_Quillen_McCord) of Github. I find such development of everything a very good practice.

_I plan to present it as a poster on [YTM 2023](https://ytm2023.epfl.ch/). I'm glad that I came with this release before the event -- now I have a text for reference in some usual place._