---
layout: post
title:  "Run to classifying spaces"
tags: [math.AT]
usemathjax: true
---

I recently went out for a run with a friend. It quickly turned out I was not in a condition to run that run. So instead we started a walk with a conversation which eventually touched a notion of "a classifying space of a category".

My friend has mathematical education and is familiar with algebra, discrete optimization and real analysis, but he had never had any course in topology and does not work as a mathematician. I tried to sketch a path to the subject. At the end we agreed that an exercise sheet would support it a lot.

So here is this sketch of a path towards classifying spaces, extended and supplied with exercises. Let's run.

## Simplicial complexes

Let's consider a finite set $$X$$ of points in the Euclidean space $$\mathbb{R}^n$$. We can consider multiple closed shapes in the space such that $$X$$ lies on a boundary of the shape. We want to pick one standard shape.

What could we want from such a shape? At first, we want it to be constructible directly from $$X$$. At second, we want to have one-to-one correspondence between shapes and sets. Hence points of $$X$$ must have some special geometric property which other points on a shape does not have.

Natural choices are balls and polytopes. Balls pass the first demand, but fail the second. Polytopes pass both since they have a naturally distinguished set of points --- its vertices. So let's choose a polytope.

Polytope can be informally defined as a shape with flat sides. It means that each face of a polytope contains all geodesics between pairs of points of its boundary. In Euclidean case, a face is a _convex hull_ of its vertices. Id est for a set $$X = \{x_i\}$$ its convex hull $$Conv(X) = \{\sum_i a_ix_i|\; \sum_i a_i = 1,\; a_i \geq 0\}$$.<br>
__Exercise__: play with conditions of the definition and check what it matches proposed informal definition with geodesics and all conditions match with intuition of a face.

Convex hull of $$k+1$$ ($$k \leq n$$) vertices is called a _geometric simplex_. It is a polytope. If its set of vertices does not coincide with initial set of vertices, a simplex is called degenerate, otherwise we say that a simplex has dimension $$k$$.

Each polytope can be decomposed into union of convex polytopes intersecting at faces. We could admit it as a definition, but anyways where is something to think about. Each convex polytope is a convex hull of its vertices $$V$$. Each point in a convex hull of set $$V \subset \mathbb{R}^n$$ lies in a convex hull of some subset $$W \subset V$$ of cardinality at most $$n+1$$, i.e. in a simplex. This statement is called the Carathéodory's theorem. It gives us a decomposition of a polytope into a union of simplices of dimension at most $$n$$. This decomposition (simplicial decomposition/triangulation) has two good properties: all faces of simplices lie in a polytope and simplices intersect at faces.

Hence by triangulation of a polytope we obtain a first example of a _geometric simplicial complex_ --- union of simplices satisfying two conditions at the end of the last paragraph.  

__Exercise:__ Details of a decomposition using Caratheodory's theorem:
1. Let $$V$$ be a set and $$W \subset V$$. Then $$Conv(W) \subset Conv(V)$$. Hence union of simplices arising from Caratheodory's theorem is equal to $$Conv(V)$$.
2. Assume simplices $$A$$ and $$B$$ intersect. Then either their intersection is a common face of them or at least one of the vertices of $$A$$ and $$B$$ is internal to $$A \cup B$$.
3. See that the latter case contradicts our choice of set $$V$$.

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

We can examine it by the third example drawn: we have two vertices and three segments of dimension one glued to them. Actually as the diagram shows. This drawing can be deformed to a wegde of two circles by contracting a segment in the middle.

Note that in general this realization is not a structure in some Euclidean space.

Denote a category by $$\mathcal{C}$$. Geometric realization of its nerve $$B\mathcal{C}$$ is called a classifying space of a category.

__Exercise__: Construct a simplicial decomposition of a circle such that is has two simplices of each dimensions.<br>
__Exercise__: Construct a simplicial decomposition of a sphere such that is has two simplices of each dimensions.<br>
__Exercise__: Classifying space of the second category is a simplical decomposition of infinite-dimensional real projective space (space of lines intersecting the origin in $$\mathbb{R}^n$$).

## Topology: homeomorphisms and cellular complexes

In a previous section, we noted without [explanation](https://epub.ub.uni-muenchen.de/4524/1/4524.pdf) that geometric realization of a simplicial set is not necessarily a subset of an Euclidean space. Then what is it? Here we have to introduce topology.

Let $$X$$ be a set and $$\mathcal{P}(X)$$ be its powerset. We say that $$\tau \subset \mathcal{P}(X)$$ is a topology if it satisfies the following properties: $$X \in \tau$$; $$\emptyset \in \tau$$; union of any collections of sets of $$\tau$$ lies in $$\tau$$ and intersection of any finite ([why?](https://math.stackexchange.com/a/4116)) collection of sets of $$\tau$$ lies in $$\tau$$. A set $$X$$ with a topology is called a _topological space_.

Elements of $$\tau$$ are called _open sets_. Family $$B$$ of open sets such that open set can be represented as union of sets of $$B$$ is called a base of the topology.<br>
__Exercise__: Check that open balls in a metric space are a _base_ of a topology.

Analogously one can define a _subbase_ --- collection of open sets such that they _with their finite intersection_ form a base of a topological space.

We can study functions on topological spaces. We define a function to be _continuous_ if a preimage of any open set is an open set.<br>
__Exercise__: Check that for metric spaces this definition is equivalent to an $$\varepsilon-\delta$$-definition.

Two topological spaces $$X$$ and $$Y$$ are said to be homeomorphic if there exist functions $$f : X \to Y$$ and $$f : Y \to X$$ such that $$f \circ g = Id_Y$$ and $$g \circ f = Id_X$$.<br>
__Exercise__: Write a homeomorphism between a unit segment and a line.<br>
__Exercise__: Draw a homeomorphism between a circle and a square.

We have defined geometric simplices as subsets of $$R_n$$ with boundary. Each of them can be turned into a topological space by induced topology: we say that a set in a subspace is open if it is open in a greater space or is an intersection of the subspace with an open set.

We can now formalize notion of "continuous deformation of a simplex" --- it is a space homeomorphic to given simplex. For example, a closed ball of corresponding dimension fits.<br>
__Exercise__: draw such a homeomorphism for planar case.

Let's now play a constructor. We have variety of simplices of different dimensions; we can deform them to homeomorphic ones; and we can attach any simplex to simplices of smaller dimension by continuous maps defined everywhere on a boundary by continuous attaching maps. Union of all these simplices (cells) glued by attached maps is called a cellular complex. By construction, we see that a classifying space of a category is a cellular complex.

To make a statement that a classifying space of a category is a topological space we have to introduce topology on cellular complexes: its subset $$U$$ is open if and only if pairwise intersections of $$U$$ with all cells are open.

Note that cellular complex up to homeomorphisms is defined by purely combinatorial data. This data is encoded in a notion of a simplicial set similarly to how geometric simplicial complex is encoded in its abstract counterpart.

__Exercise__: Provide examples of two abstract simplicial complexes with homeomorhic geometric realizations. _Hint:_ barycentric subdivision.

## Topology: homotopy equivalences

We also used the word "contracting" without a proper definition. Indeed, it deserves it.

At first, we have to define a product of topological spaces. As a set, it is a Cartesian product. We have to define topology. It is natural to require projections onto components to be continuous and usual choice is to select preimages of all open sets under these projections to be a subbase of a topology. Specific topology is not important for a talk, but we want to be able to speak about continuous functions from $$X \times [0,1]$$.

Now assume we have two continuous functions $$f,g : X \to Y$$. They are said to be _homotopic_ (denoted by $$f \simeq g$$) if there exists a continuous function $$F : X \times [0,1] \to Y$$ such that $$F(\_,0) = f$$ and $$F(\_,1) = g$$.<br>
__Exercise__: Give a formula for homotopy between $$Id : [0,1] \to [0,1]$$ and a constant map $$pt_0 : [0,1] \to [0,1]$$.

Two spaces $$X, Y$$ are said to be homotopy-equivalent if there exist functions $$f : X \to Y$$ and $$f : Y \to X$$ such that $$f \circ g \simeq Id_Y$$ and $$g \circ f \simeq Id_X$$.<br>
__Exercise__: Prove that a real line is homotopy-equivalent to a point.
__Exercise__: Prove that infinite-dimensional sphere is homotopy-equivalent to a point. Instead of its cellular decomposition, it's better to use explicit definition: $$S^{\infty} = \{(x_0,\ldots,x_i,\ldots) \in \mathbb{R}^{\infty}|\; \sum_i x_i^2 = 1\}$$.

A topological space is said to be contractible if it is homotopy-equivalent to a point.
Thus we have a formal meaning of an operation we conducted on our example 3. We replaced a contractible cell by a point with corresponding update of attaching maps.

For a motivation we can formulate a first theorem which essentially uses classifying spaces.

At first define an _open covering_: family $$\mathcal{U}$$ of open sets in $$X$$ is called an open covering if $$\bigcup \mathcal{U} = X$$. We can associate a poset $$P(\mathcal{U})$$ with a covering by considering all possible nonempty intersections of sets of $$\mathcal{U}$$ with inclusions order relation. This poset represents a category, this category has a classifying space.

We say that the space is compact if every open cover has a finite subcover.<br>
__Exercise__ _(long)_: prove criterion of compactness for Euclidean spaces: subset of an Euclidean space is compact if and only if it is closed and bounded.

__Theorem (Borsuk 1948, referred to as Čech theorem, Alexandrov-Čech theorem or nerve theorem)__:<br>
Let $$X$$ be a compact topological space with cover $$U$$ such that all nonempty intersections of sets of $$U$$ are contractible. Then $$BP(\mathcal{U}) \simeq X$$.

__Exercise:__ If you know some other formulation of the theorem, prove its equivalence with given (with compactness requirement on both sides).

Now we know something about structure of classifying spaces and what they are. We know some statement which essentially uses this notion (or equally powerful one). But we barely can compute them once we have loops, infinite-dimensional projective space is our simplest example.

We need some other technique to see some classifying spaces at hand. Classifying spaces of categories are a generalization of a more established notion with the same name. Let's meet with them also and hope it helps.

## Classifying spaces of groups

Every topological space $$X$$ has its group of transformations $$\operatorname{End}(X)$$. Let $$\rho : G \to \operatorname{End}(X)$$ be a homomorphism. It is called a group action.

Let's endow $$G$$ with a topology. For example, every group can be supplied with discrete topology --- we declare each subset of $$G$$ to be open. Then we can fix an arbitrary point $$x \in X$$ and consider its orbit under continuous (as a map $$G \times X \to X$$) action of $$G$$. This orbit is embedded into $$X$$ via continuous map.<br>
__Exercise__: If two orbits of an action have a common point, they coincide.

By exercise, we see that being in a same orbit is an equivalence relation. Hence we can take quotient of $$X$$ by it.
We did not discuss a quotient topology yet: sets that have an open preimage under quotient map are declared to be open.

If $$X=EG$$ is a contractible space with an action of $$G$$, this quotient $$BG$$ is called a classifying space of a group $$G$$.

__Exercise__: Let $$G = \mathbb{Z}_2$$ and $$EG = S^{\infty}$$. Describe action $$G \hookrightarrow \operatorname{End}{EG}$$ and its quotient space.<br>
__Exercise__: What is the classifying space of $$\mathbb{Z}$$.

By looking at this action on a contractible space, it is easy to catch how group can be represented as a category --- we have a single homotopically trivial object with different endomorphisms indexed by elements of a group. By drawing it we come up with a category with one object and morphisms indexed by elements of a group.

For instance, $$\mathbb{Z}_2$$is represented by diagram of example 1,
denote it by $$\mathcal{D}_{\mathcal{Z}_2}$$.
We see (given the last exercise) that $$B\mathbb{Z}_2 = B\mathcal{D}_{\mathcal{Z}_2}$$.

There is a general fact that $$BG \simeq B\mathcal{D}_G$$. It is approachable within the technique given in this text. Almost. Up to the fact that classifying space is unique up to (weak) homotopy equivalence.

__Last exercise:__ Prove independently from this fact that $$B\mathcal{D}_{\mathcal{Z}}$$ is homotopy-equivalent to a circle.<br>
This proof may also require some methods which were not mentioned in the text.

#### Questions

I leave several important questions not covered for now:
1. Proof of the proposition $$BG \simeq B\mathcal{D}_G$$.
2. Are there standard constructions of classifying spaces of groups? Of categories?
3. What do classifying spaces of groups classify? Of categories?<br>
...

The intention of the text was to run to classifying spaces and here we are, surrounded by questions.
