---
layout: post
title:  "Platonic Hask overview: subcategories, functors and natural transformations"
tags: [hask]
usemathjax: true
---

_[To index of the series](https://viviag.io/tagged/hask/)_

We have constructed the category of restricted Haskell types. It gave us a coherent notion of a composition. But it's not enough. The major strength of Haskell is its separation of computations of different nature. So we need to be able at least to cluster types into objects with common property. Let's develop machinery to deal with it.

Consider categories $$\mathrm{C}$$ and $$\mathrm{D}$$ with pair of mappings $$F_{\operatorname{Ob}} : \operatorname{Ob}(\mathrm{C}) \to \operatorname{Ob}(\mathrm{D})$$ and $$F_{\operatorname{Hom}}$$ with one of the
following signatures: $$\operatorname{Hom}_{\mathrm{C}}(A,B) \to \operatorname{Hom}_{\mathrm{D}}(F_{\operatorname{Ob}}(A),F_{\operatorname{Ob}}(B))$$ or $$\operatorname{Hom}_{\mathrm{C}}(A,B) \to \operatorname{Hom}_{\mathrm{D}}(F_{\operatorname{Ob}}(B),F_{\operatorname{Ob}}(A))$$ --- mapping of all morphisms of a category, defined on each Hom-set.

We can construct a pair $$F = (F_{\operatorname{Ob}}, F_{\operatorname{Hom}})$$. Its definition contains all data necessary to define map between categories. But composition of such maps is not well-defined (check it). It can be fixed by the following definitions:

##### Definitions

$$F$$ is called a **covariant functor** or **functor** if the following
diagram commutes:

![functor](/assets/functor.svg)

is a called a **contravariant functor** if the following diagram
commutes:

![functor](/assets/cofunctor.svg)

These two definitions are related to notion of **dual category** ---
category $$\mathrm{C}^{op}$$ is constructed from $$\mathrm{C}$$ by formally reverting all arrows in a
diagram of $$\mathrm{C}$$. This notion allows us to identify contravariant functor $$F : \mathrm{C} \to \mathrm{D}$$ with covariant $$F^{op} : \mathrm{C} \to \mathrm{D}^{op}$$.

Here are several useful definitions:

Let $$F : \mathrm{C} \to \mathrm{D}$$ be a functor (covariant, contravariant definitions are similar). Consider $$F_{\operatorname{Hom}}$$.

If $$\forall A, B \in \mathrm{C}\; F_{\operatorname{Hom}} : \operatorname{Hom}_{\mathrm{C}}(A,B) \to \operatorname{Hom}_{\mathrm{D}}(F_{\operatorname{Ob}}(A),F_{\operatorname{Ob}}(B))$$ is injective, $$F$$ is called **faithful**. If surjective --- **full**. If
bijective --- **fully faithful**.

Categories $$\mathrm{C}$$ and $$\mathrm{D}$$ are said to be equivalent if there exists fully faithful functor $$F : \mathrm{C} \to \mathrm{D}$$ such that every object of $$\mathrm{D}$$ is isomorphic to $$F(A)$$ for some $$A \in \operatorname{C}$$.

We have constructed the category and consider its internal
structure. Functors from category to itself are called **endofunctors**.
However, it may be convenient to talk about subcategories in $$Hask$$ and about
functors to them.

##### Definition

Category $$\mathrm{D}$$ is a subcategory of $$\mathrm{C}$$ if $$\operatorname{Ob}(\mathrm{D}) \subseteq \operatorname{Ob}(\mathrm{C})$$ and $$\forall A,B \in \operatorname{Ob}(\mathrm{D})\; \operatorname{Hom}_{\mathrm{D}}(A,B) \subseteq \operatorname{Hom}_{\mathrm{C}}(A,B)$$.

If $$\forall A,B \in \operatorname{Ob}(\mathrm{D})\; \operatorname{Hom}_{\mathrm{D}}(A,B) = \operatorname{Hom}_{\mathrm{C}}(A,B)$$ $$\mathrm{D}$$ is called **full subcategory** of $$\mathrm{C}$$.

Every subcategory gives rise to faithful embedding functor $$Emb : \mathrm{D} \to \mathrm{C}$$ with identical actions both on objects and morphisms. If $$\mathrm{D}$$ is a full subcategory $$Emb$$ is fully faithful.

Now let's take a look at functors in Hask.

##### Example: Functor typeclass and parametric types

Consider declaration of new data type like
`data Either a b = Left a | Right b`.

There are several possible constructions of $$Hask$$-endofunctor arising from this definition. Two most natural are defined here:

1. $$Left_{\operatorname{Ob}}(a : a)$$ = `Left a`; $$Left_{\operatorname{Hom}}(f : a \to c)$$ = `(\Left a -> Left (f a))`.
2. $$Right_{\operatorname{Ob}}(a : b)$$ = `Right a`; $$Right_{\operatorname{Hom}}(f : b \to c)$$ = `(\Right a -> Right (f a))`.

Both of them are well-defined covariant faithful functors in $$Hask$$.

Moreover, each parametric type form a full subcategory in $$Hask$$, hence these
functors can be considered as $$Hask \to Either$$. Or 2. can be considered as functor $$Hask \to Either\;a$$. And there are corresponding embedding functors to other direction.

However, only $$Right$$ is supported by valid `Functor` instance in Haskell.
Instance for `Either`:

``` haskell
instance Functor (Either c) where
  fmap :: (a -> b) -> (f a ->  f b)
  fmap _ (Left x) = Left x
  fmap f (Right y) = Right (f y)
```

Note: `fmap` defines action of functor on morphisms. We change `Prelude` definition for now --- it is valid and will be justified in the next post.

Laws of `Functor` typeclass represent usual definition of functor via following diagram:
![haskfunctor](/assets/hasfunctor.svg)

Haskell does not allow to define `Functor`s over non-terminal parameters in its own terms.

Note that uniqueness and derivability of `Functor` is not an elementary
question. Since it's not a question of category theory, let me refer to
[SO](https://stackoverflow.com/questions/19774904/are-functor-instances-unique).

Yet another restriction on Haskell `Functor` typeclass is that it does
not allow functors between nontrivial subcategories of $$Hask$$.

Example of such a functor: $$LM : [] \to Maybe$$;

$$LM_{\operatorname{Ob}}$$ = [listToMaybe](https://hackage.haskell.org/package/base-4.17.0.0/docs/Data-Maybe.html#v:listToMaybe);

$$LM_{\operatorname{Hom}}$$ = `\f -> listToMaybe . f . maybeToList`.

This functor is fully faithful. It admits faithful functor to the other
side:

$$ML_{\operatorname{Ob}}$$ = `maybeToList`;

$$ML_{\operatorname{Hom}}$$ = `\f -> ``maybeToList`` . f . listToMaybe`.

It's easy to check that functor $$toList : Vector \to []$$ with similar definition makes subcategories of vectors and of lists equivalent.

All three these functors are not endofunctors in $$Hask$$ since they are not everywhere defined.

##### Example: Hom-functors

For any category $$\mathrm{C}$$ and object $$A$$ there exist two functors.

First --- $$\operatorname{Hom}(A,\_) : \mathrm{C} \to Set$$ is a covariant functor, moving $$X$$ to $$\operatorname{Hom}(A,X)$$. Second --- contravariant $$\operatorname{Hom}(\_,A)$$ with same signature, moving $$X$$ to $$\operatorname{Hom}(X,A)$$.

Both functors matter a lot for future constructions and obviously exist in $$Hask$$.

Morphisms --- functions between types, hence arrows `a -> b`. They are
ordinary types, hence their prefix form is `(->) a b` and there can
only exist `Functor` instance for covariant $$\operatorname{Hom}$$.

Here it is (note that fully applied `(->)` is a function):

``` haskell
instance Functor ((->) a) where
  fmap f g = f . g
```

Let's define alternating type `<-` isomorphic to `(->) b a`.

For this type we can define instance of
[Contravariant](https://hackage.haskell.org/package/contravariant-1.4/docs/Data-Functor-Contravariant.html),
which represent contravariant functors.

``` haskell
instance Contravariant ((<-) b) where
  contramap :: (a -> c) -> f c -> f a
  contramap f g = g . f
```

Laws of `Contravariant` form the following familiar diagram:

![haskcofunctor](/assets/hascofunctor.svg)

##### Other useful examples of functors

-   Forgetful functors from category to $$Set$$ --- ones forgetting all the
    structure imposed on objects and morphisms. For example, forgetful
    functor from category of groups $$Grp$$ moves group to set of its elements
    and homomorphism to itself as a function between sets.
-   Free functors from $$Set$$ to some category --- ones which allow to impose
    defining relations of a category on a set and thus define free
    object in a category. For example, free functor from $$Set$$ to $$Grp$$ yields the
    group called free with presentation $$set \mapsto \{set\;|\;\emptyset\}$$.
-   Tensor product with fixed object (left or right).
-   To say it out --- basic example is identity functor.

All these functors are relevant to $$Hask$$.



Let's take a look at introduced structures. At the level of types we have types and morphisms between them. Morphisms can be surjective, injective or bijective, in last case they are isomorphisms. Now we turn to level of subcategories of $$Hask$$ and we have functors with their own properties. These properties are finer than propeties of Set-level morphisms but they are similar in spirit.

At the moment we can take one of two steps:
1. Broad: try to introduce some category of subcategories of $$Hask$$ and explore it. Probably more than one if we want to take care of contravariant functors.
2. Deep: try to stack the next layer and define morphisms between functors.

For now we follow the second path.

##### Definition

Consider $$F,G$$, --- covariant functors from $$\mathrm{C}$$ to $$\mathrm{D}$$. We will call family $$\eta$$ of
morphisms in $$\mathrm{D}$$ **natural transformation** from $$F$$ to $$G$$ if for any object $$X$$ in $$\operatorname{Ob}(C)$$ and morphism $$f : X \to Y$$ there exist morphisms $$\eta_X : F(X) \to G(X)$$ and $$\eta_Y : F(Y) \to G(Y)$$ in $$\mathrm{D}$$ such that $$\forall f \in \operatorname{Hom}_{\mathrm{C}}(X,Y)$$ the following diagram commutes:

![natural](/assets/natural.svg)

If both functors are contravariant, vertical arrows are reversed.

This definition lets us to see $$LM$$-functor from the other side --- as
natural transformation between endofunctors $$[]$$ and $$Maybe$$. Naturality is checked
by the same reasoning as being a functor.

Haskell ecosystem contains several packages trying to express natural
transformation. For example, [natural-transformation](https://hackage.haskell.org/package/natural-transformation)
package.

It's worth noting that popular [servant (0.10, link to enter
function)](https://hackage.haskell.org/package/servant-server-0.10/docs/Servant-Server.html#v:enter)
web-framework used to use explicitly typed natural transformations very
close to its user interface for a long time. Here is how it was used:
[v0.10 tutorial](https://docs.servant.dev/en/v0.10/tutorial/Server.html)

##### Statement

Functors (covariant without loss of generality) between tho categories $$\mathrm{C}$$ and $$\mathrm{D}$$ with objects --- functors and morphisms --- natural transformations form a category.

This is well-known statement not about $$Hask$$ with obvious proof by construction, so it will not be given.

Note that in example above $$LM \circ ML = Id_{Maybe}$$.

This category of functors is denoted as $$\operatorname{Fun}(\mathrm{C},\mathrm{D})$$. $$\operatorname{Fun}(\mathrm{C},\mathrm{C})$$ has a more convenient synonym --- $$\operatorname{End}(\mathrm{C})$$ and is called category of endofunctors of $$\mathrm{C}$$.

We come up with another notable object we will use in the future --- category $$\operatorname{End}(Hask)$$ of endofunctors of $$Hask$$.
