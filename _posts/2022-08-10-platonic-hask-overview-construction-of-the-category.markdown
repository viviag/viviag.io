---
layout: post
title:  "Platonic Hask overview: construction of the category"
tags: [hask]
date:  2022-08-10 20:00 MSK
usemathjax: true
---

_[To index of the series](https://viviag.io/tagged/hask/)_

Let's start with a basic definition:

##### Definition

An entity $$\mathrm{C}$$ is a category if all of the following holds:

1.  There exists class $$\operatorname{Ob}(\mathrm{C})$$ of objects of $$\mathrm{C}$$.
2.  $$\forall A, B \in \operatorname{Ob}(\mathrm{C})$$ there exists class $$\operatorname{Hom}_{\mathrm{C}}(A,B)$$ of morphisms between $$A$$ and $$B$$ in $$\mathrm{C}$$.
3.  There exists a binary operation $$\circ$$ called composition such that:
    - $$\forall A, B, C \in \operatorname{Ob}(\mathrm{C}),\; f \in \operatorname{Hom}_{\mathrm{C}}(A,B),\; g \in \operatorname{Hom}_{\mathrm{C}}(B,C)$$ there exists $$g \circ f \in \operatorname{Hom}_{\mathrm{C}}(A,C)$$.
    - $$\forall A \in \operatorname{Ob}(\mathrm{C})$$ there exists morphism $$Id_A \in \operatorname{Hom}_{\mathrm{C}}(A,A)$$ such that $$\forall f \in \operatorname{Hom}(A,B)$$ holds $$f = f \circ Id_A = Id_A \circ f$$.
    - $$\circ$$ is associative.

Category with class $$\operatorname{Ob}$$ and all $$\operatorname{Hom}$$-classes being sets is called small.

##### Examples

A basic example of a category is $$Set$$ --- category with objects --- sets and morphisms --- functions between sets.

We want to construct a reasonable category out of Haskell types. We might want to construct a reasonable category out of abstract types. For this purpose Haskell is a good yardstick and support --- it was designed with respect to category theory and we can go far by judging the existence of some constructions in our category by the existence of their GHC implementations.

**First attempt**

Let's consider $$Hask'$$ with $$\operatorname{Ob}(Hask')$$ --- types of Haskell and $$\operatorname{Hom}_{Hask'}(A,B)$$ --- all functions (closed expressions) of type $A \to B$.

It is not a category --- `seq undefined () = _|_` and
`seq (undefined . id) () = ()`, hence `undefined` $$\neq$$ `undefined . id`. See
[post by Andrej Bauer and discussion](http://math.andrej.com/2016/08/06/hask-is-not-a-category/).
[Haskell wiki](https://wiki.haskell.org/Hask) knows several more examples where the bottom breaks abstractions.

Failure of this attempt makes whole categorical reasoning about Haskell limited but still useful. Unfortunately, the real world is contradictory.

**Second attempt**

Consider $$\operatorname{Ob}(Hask)$$ --- Haskell types without $$\bot$$ with natural $$\operatorname{Hom}$$-sets --- all functions between these platonic types excluding partial and nonterminating functions. To shorten notation here we overload terms of Haskell Wiki. `Wiki.Hask = Hask'; Wiki.Platonic Hask = Hask`.

Note: I'm not using the term "Maximal total subset of Haskell" in the sense of
[Wikipedia article](https://en.wikipedia.org/wiki/Total_functional_programming)
since I'm not talking about provability here. The set of functions we take seems not to have a constructive definition and is broader.

##### Claim

$$Hask$$ is a category.

##### Proof

With the assumption of totality and termination of all functions equational
reasoning is legal (Church-Rosser property holds --- it was the exact property broken by $$\bot$$).

We only have to check the properties of the composition:

-   Composition [exists](https://hackage.haskell.org/package/base-4.17.0.0/docs/src/GHC.Base.html#.).
-   Composition is associative:
    -   `f . (g . h) =(1) \y -> f (\x -> g (h x) $ y) =(2) \y -> f (g (h y))`
    -   `(f . g) . h =(1) \y -> (\x -> f (g x) $ h y) =(2) \y -> f (g (h y))`
-   Identity [exists](https://hackage.haskell.org/package/base-4.17.0.0/docs/src/GHC.Base.html#id).
-   `seq` in a previous attempt violated identity law. Proof of the identity law by equational reasoning is trivial.

(1) in equations represents taking definition, (2) refers to $$\beta$$-reduction, in both cases, we follow the applicative order of evaluation. Equality is the $$\alpha$$-congruence relation.

Following posts will operate $$Hask$$.

The category $$Hask$$ is not equivalent to $$Set$$: consider the type `data Foo = Foo (Foo -> Bool)`. The map `Foo :: (Foo -> Bool) -> Foo` is an injective map from $$2^{Foo}$$ to $$Foo$$. This situation is impossible in $$Set$$. Here is an older [Reference](https://www.reddit.com/r/haskell/comments/sz4ghr/comment/hy3916c/?utm_source=share&utm_medium=web2x&context=3).

##### Remarks

1. It is also common to denote $$Hom_{\mathrm{C}}(A,A)$$ as $$End_{\mathrm{C}}(A)$$ --- set of endomorphisms of $$A$$.

2. I will frequently use the following notation:
- `f : A` for element $$f$$ of $$A \in \operatorname{Ob}(Hask)$$
- `f : A -> B` for $$f \in \operatorname{Hom}_{Hask}(A,B)$$
