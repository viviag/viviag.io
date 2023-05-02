---
layout: post
title:  "Run to classifying spaces"
tags: [math.AT]
usemathjax: true
---

I recently went out for a run with a friend. It quickly turned out I was not in a condition to run that run. So instead we started a walk with a conversation which eventually touched a notion of "a classifying space of a category".

My friend has mathematical education and knows linear algebra, discrete optimization and real analysis, but he had never had any course in topology and does not work as a mathematician. I tried to sketch a path to the subject. At the end we agreed that an exercise sheet would support it a lot.

So here is this sketch of a path towards classifying spaces, extended and supplied with exercises. Let's run.

## Simplicial complexes

Let's consider a finite set $$X$$ of points in the Euclidean space $$\mathbb{R}^n$$. We can consider multiple closed shapes in the space such that $$X$$ lies on a boundary of the shape. We want to pick one standard shape.

What could we want from such a shape? At first, we want it to be constructible directly from $$X$$. At second, we want to have one-to-one correspondence between shapes and sets. Hence points of $$X$$ must have some special geometric property which other points on a shape does not have.

Natural choices are balls and polytopes. Balls pass the first demand, but fail the second. Polytopes pass both since they have a naturally distinguished set of points --- its vertices. So let's choose a polytope.

Polytope can be informally defined as a shape with flat sides. It means that each face of a polytope contains all geodesics between pairs of points of its boundary. In Euclidean case, a face is a _convex hull_ of its vertices. Id est for a set $$X = \{x_i\}$$ its convex hull $$Conv(X) = \{\sum_i a_ix_i|\; \sum_i a_i = 1,\; a_i \geq 0\}$$.<br>
__Exercise__: play with conditions of the definition and check what it matches proposed informal definition with geodesics and all conditions match with intuition of a face.

Convex hull of $$k+1$$ ($$k \leq n$$) vertices is called a _geometric simplex_. It is a polytope. If its set of vertices does not coincide with initial set of vertices, a simplex is called degenerate, otherwise we say that a simplex has dimension $$k$$.

Each polytope can be decomposed into union of convex polytopes --- convex hulls of some vertices. We could admit it as a definition, but anyways where is something to think about. Each point in a convex hull of set $$V \subset \mathbb{R}^n$$ lies in a convex hull of some subset $$W \subset V$$ of cardinality at most $$n+1$$, i.e. in a simplex. This statement is called the Carathéodory's theorem and it gives us a decomposition of a polytope into a union of simplices of dimension at most $$n$$. This decomposition (simplicial decomposition/triangulation) has two good properties: all faces of simplices lie in a polytope and simplices intersect at faces (__Exercise__: prove it).

Hence by triangulation of a polytope we obtain a first example of a _geometric simplicial complex_ --- union of simplices satisfying two conditions at the end of the last paragraph.  

We can observe that our geometric simplicial complex is only defined by vertices of its faces. Hence we can abstract from geometry here and define an abstract _simplicial complex_. Let $$X$$ be a finite set and $$\mathcal{P}(X)$$ be its powerset. Then $$S \subset \mathcal{P}(X)$$ is called an abstract simplicial complex if for any $$V \in S$$ and $$W \subset V$$ $$W \in S$$. Elements of $$S$$ are called simplices, number of elements in a simplex minus one is called its dimension.

We can also define a _geometric realization_ which maps points of $$X$$ to points in Euclidean space of appropriate dimension in such a way that each simplex maps into geometric simplex of the same dimension. There can be plenty of geometric realizations, the most commonly referred to is a standard geometric realization, which maps simplices to regular polytopes.

Finally, let $$(P,\leq)$$ be a finite partially ordered set (poset). We can construct an abstract simplicial complex by taking chains of length $$n+1$$ to be $$n$$-simplices. Note that equalities are excluded from this construction.<br>
__Exercise__: check that it is a simplicial complex.<br>
This complex is called an order complex of a poset. We denote its geometric realization as $$BP$$.

## Nerves of categories

Any order relation satisfies two properties: $$a \leq b;\; b \leq c$$ imply $$a \leq c$$ and $$a \leq a$$ for all elements in a poset.

Let's introduce a definition: a (small) category is a set called a set of objects with sets $$\operatorname{Hom}(A,B)$$ (homsets) of arrows between objects $$A$$ and $$B$$ for each pair $$(A,B)$$. The following two properties must be satisfied: for each pair of arrows $$f : A \to B$$ and $$B \to C$$ there exists an arrow $$g \circ f : A \to C$$ and for each object $$A$$ there exists an arrow $$Id_A : A \to A$$.

Each poset forms a category with $$P$$ being a set of objects and arrows between objects $$a$$ and $$b$$ present if and only if $$a \leq b$$. This category has a property that each homset has at most one element. Order complex construction remains valid on this category simply by change of terms --- we replace chains of length $$n+1$$ with sequences of morphisms of length $$n+1$$, excluding identities. A result of this general construction is called a nerve of a category.

Categories with finite sets of objects and finite sets of morphisms can be displayed as diagrams. Consider some examples (compositions and identity arrows are omitted):<br>
1. ![twocycle](/assets/twocycle.svg)
2. ![onecycle](/assets/onecycle.svg)
3. ![multipath](/assets/multipath.svg)

All of them have more than one sequence on the same set of objects. Hence nerves of these categories cannot be represented as simplicial complexes. But they have geometric realizations defined by similar construction and comprised of geometric simplices and their continuous deformations. Objects like these are called simplicial sets. Let's think of them exactly as of nerves of categories and refer an [expository text](https://arxiv.org/pdf/0809.4221.pdf) by Greg Friedman for the formal definition and an overview.

Let's define geometric realization inductively by procedure of attaching $$n$$-simplices to simplices of smaller dimensions. Vertices define set of geometric vertices. Assume we have a geometric realization of subset of simplices of dimension not above $$n$$. When we can attach $$n+1$$-simplices by deforming and gluing them to their boundaries, which are already in a structure.

We can examine it by the third example drawn: we have two vertices and three segments of dimension one glued to them. Actually as the diagram shows. This drawing can be continuously deformed to a wegde of two circles by contracting a segment in the middle.

Note that in general this realization is not a structure in some Euclidean space.

Denote a category by $$\mathcal{C}$$. Geometric realization of its nerve $$B\mathcal{C}$$ is called a classifying space of a category.

__Exercise__: Classifying space of the second category is a simplical decomposition of infinite-dimensional real projective space (space of lines intersecting the origin in $$\mathbb{R}^n$$).

## Topology section

In a previous section, we noted without [explanation](https://epub.ub.uni-muenchen.de/4524/1/4524.pdf) that geometric realization of a simplicial set is not necessarily a subset of an Euclidean space. When what is it? Also we used words "continuously deformed" and "contracting" without proper definitions. Here we have to introduce topology.

## Classifying spaces of groups

## What do the classifying spaces classify?
