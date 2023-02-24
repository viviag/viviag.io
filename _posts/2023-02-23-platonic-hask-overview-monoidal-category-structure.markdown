---
layout: post
title:  "Platonic Hask overview: representable functors and monoidal category structures"
tags: [hask]
usemathjax: true
---

_[To index of the series](https://viviag.io/tagged/hask/)_

In previous post I promised to justify change of signature of `fmap` from `(a -> b) -> f a -> f b` from `Prelude` to `(a -> b) -> (f a -> f b)`. And occasionally explain the former notation. There is a categorical construction to help, it is called Cartesian closedness.

Here we leave indices of $$\operatorname{Hom}$$-sets to context, same for definitions of objects --- each subsection operates fixed category.

#### Cartesian closedness

##### Definitions

Category $$\mathrm{C}$$ is called **cartesian closed** if it has terminal object, for all $$A,B \in \operatorname{Ob}(\mathrm{C})$$ there exists a product $$A \times B$$ and exponential object $$B^A$$.

To define all relevant notions it is convenient (following [lectures in Russian by A. Gorodentsev](http://gorod.bogomolov-lab.ru/ps/stud/homalg/1920/list.html)) to define **representable functor**.

Functor $$F :: \mathrm{C}^{op} \to Set$$ is said to be **representable** if is it naturally isomorphic to $$\operatorname{Hom}(\_,A)$$ for some $$A$$. $$A$$ is called **representing object** of functor $$F$$. Representing object is unique up to canonical isomorphism.

Any representing object has associated **universal property**. Let's see it by example.

Consider objects $$A,B \in \mathrm{C}$$. If functor $$\operatorname{Hom}(\_,A) \times \operatorname{Hom}(\_,B)$$ is representable, its representing object $$A \times B$$ is called the **product** of $$A$$ and $$B$$.

Write this definition: $$\forall Y\; Hom(Y, A \times B) \cong \operatorname{Hom}(Y,A) \times \operatorname{Hom}(Y,B)$$.

By setting $$Y = A \times B$$ we obtain via this isomorphism pair of maps $$\pi_A :: A \times B \to A$$ and $$\pi_B :: A \times B \to B$$ which are image of $$Id_{A \times B}$$ under isomorphism. They are called **canonical projections**. For arbitrary $$Y$$ this isomorphism guarantees existence and uniqueness of map $$\phi :: Y \to A \times B$$ for any pair of maps $$\psi_A :: Y \to A$$ and $$\psi_B :: Y \to B$$. There are maps $$\pi_A \circ \phi$$ and $$\pi_B \circ \phi$$, since isomorphism is natural, $$\pi_A \circ \phi = \psi_A$$, $$\pi_B \circ \phi = \psi_B$$.

Thus we obtain the universal property of a product:

For any $$Y$$, $$f :: Y \to A$$ and $$g :: Y \to B$$ there exists map $$\phi :: Y \to A \times B$$ such that the following diagram commutes:

![productprop](/assets/productprop.svg)

Analogously we call representing object of a functor $$\operatorname{Hom}(\_, \emptyset)$$ **terminal object** of category $$\mathrm{C}$$. By evaluating its universal property we have that it is the object $$T$$ with exactly one morphism $$Y \to T$$ for any $$Y$$.

Assume $$\mathrm{C}$$ has all binary products. Consider functor $$\operatorname{Hom}(\_ \times A, B)$$. Its representing object $$B^A$$ is called **exponential object**. Actually we defined it via isomorphism $$\operatorname{Hom}(Y, B^A) \cong \operatorname{Hom}(Y \times A, B)$$ which may look familiar.

By taking $$Y = B^A$$ we obtain map $$eval :: B^A \times A \to B$$. We can draw the universal property:

![expprop](/assets/expprop.svg)

Usually terminal objects and products are defined in terms of limits. But limits are examples of representing object and are not really important here. We will turn back to them later.

##### Hask is cartesian closed

To prove the statement in the header we have to demonstrate terminal object, exponentials and products.

Since morphisms in $$Hask$$ do not have to preserve any structure the only candidates for terminal object are types with single term. These are types isomorphic to `()`. They are all isomorphic (admit invertible bijections between each other), hence there is no need to check universality.

Evaluation map of exponential object suggests object satisfying universal property for types `a, b` --- it is `a -> b`. Products are given by type with two natural projections --- `(a,b)`. Since universal objects are defined up to natural isomorphisms, product types `data Cons = Cons B C` are also products, same for newtypes over arrow types etc.

Definition of exponential object gives us a natural isomorphism `(a,b) -> c` $$\cong$$ `a -> (b -> c)` for any types `a, b, c`. This isomorphism is called currying. In Haskell it is common and is reflected in both implementation and notation --- type `a -> b -> c` is literally equivalent to `a -> (b -> c)` (which is known as partial evaluation) and is isomorphic to `(a,b) -> c` via inverse functions `curry` and `uncurry`.

There is an important proposition that any cartesian closed category is closed symmetric monoidal category with tensor product given by product in sense of given universal property. Closedness is encapsulated into existence of all exponentials. So let's proceed to definition of symmetric monoidal category.

#### Monoidal category structure

##### Definitions

Category $$\mathrm{C}$$ is called **monoidal** if
- It is equipped with **tensor product** bifunctor $$\otimes :: \mathrm{C} \times \mathrm{C} \to \mathrm{C}$$ and associator functor $$\alpha :: \mathrm{C} \to \mathrm{C}$$ satisfying associative law.
- There exists unit object $$I$$ and unitor functors $$\lambda$$, $$\rho$$ satisfying identity law.
- Both associator and unitor maps are natural isomorphisms where defined.

Associativity law is the commutativity of the following diagram:

![monass](/assets/monass.svg)

Identity law is the commutativity of the following diagram:

![monid](/assets/monid.svg)

Monoidal category is called **symmetric** if $$\forall A,B$$ there exists isomorphism $$s_{AB} :: A \otimes B \to B \otimes A$$ such that the following diagram commutes:

![monsym](/assets/monsym.svg)

##### Hask

From cartesian closedness there automatically follows that $$Hask$$ is symmetric monoidal with $$\otimes = $$`(,)` and unit --- `()`. Associator and unitor functors are obvious. Denote this structure as $$(Hask, (,), ())$$.

Consider $$End(Hask)$$. It is a monoidal category with tensor product given by composition, unit --- by `Identity` functor, and with identical unitors and associator. Denote this structure as $$(End(Hask),\circ,Id)$$.

These two statements will be central in next two posts.

#### Dual counterparts

Universal properties allows us to construct dual objects by formally reversing all arrows.

While doing it with already defined representing objects we obtain the following definitions:

- Initial object is defined as an object $$I$$ such that it has unique morphism to any other object
- Coproduct is defined by the following diagram:

![coprod](/assets/coprod.svg)

- Coexponential object is defined by the following diagram:

![coexp](/assets/coexp.svg)

Category with initial object, all coexponentials and all binary coproducts is called **cocartesian coclosed**.

$$Hask$$ does not have all coexponentials and is not cocartesian coclosed.

But it has all coproducts given by `Either a b` and it has initial object `Void`.
This data is enough to equip $$Hask$$ with another structure of monoidal category. Tensor product is given by coproduct and unit is given by initial object. Denote this structure as $$(Hask, Either, Void)$$.

#### Note on corepresentable functors

These universal objects with reversed arrows can be defined via duals to representable functors.

Functor $$F :: \mathrm{C} \to Set$$ is said to be **corepresentable** if is it naturally isomorphic to $$\operatorname{Hom}(A,\_)$$ for some $$A$$. $$A$$ is called **corepresenting object** of functor $$F$$. Representing object is unique up to canonical isomorphism.

Initial object corepresents $$\operatorname{Hom}(\emptyset, \_)$$, $$A \coprod B$$ corepresents $$\operatorname{Hom}(A,\_) \otimes \operatorname{Hom}(B,\_)$$, coexponential corepresents $$\operatorname{Hom}(B, \_ \coprod A)$$.

In $$Hask$$ $$\operatorname{Hom}(A,\_)$$ is written as `(->) A`. We can restrict functors to instances of `Functor` type class and obtain the following definition of representable functor: functor `F` is said to be representable if there exists natural isomorphism between `F a` and `(->) A a` for every type `a`. `A` is corepresenting object of `F`. But `F` must be considered as a functor to Set with values --- sets of terms of resulting types of `Functor` we started with instead of types.

This definition is captured in package [representable-functors](https://hackage.haskell.org/package/representable-functors-3.2.0.2/docs/Data-Functor-Representable.html). Isomorphism `Cons a ~ A -> a` in terms of this library is written as `f a ~ Key a -> a`, to the left it is given by `tabulate`, to the right by `index`.
