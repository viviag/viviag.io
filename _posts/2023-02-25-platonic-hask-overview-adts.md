---
layout: post
title:  "Platonic Hask overview: algebraic data types as corollary of monoidal structure"
tags: [hask]
usemathjax: true
---

#### Algebraic data types

We have only considered binary products and coproducts. We can consider family of products of arbitrary arity defining them as representing objects of $$\Pi_{i \in I}\operatorname{Hom}(\_,A_i)$$ for finite ordinals $$I$$. This definition has a problem with $$I = 0$$, we treat this case specially and define empty product as the terminal object. Universal properties of these objects can be drawn easily from the binary case.

$$Hask$$ has all the finite products, for instance, product `(a,(b,c))` satisfy the universal property. This is an instance of general statement that a category with binary products and terminal objects has all finite products.

Usual binary product `(,)` serves as a tensor product in $$(Hask, (,), ())$$, hence it satisfies associativity law and these products can be written without too much parens. Haskell has this notion --- there are tuples of length bigger than 2. But each of them is implemented separately in GHC and it doesn't allow tuples of length above some finite number.

This number is big enough for most applications, but here we need it only to notice that it is not a notion Haskell uses for parens-free presentation of finite products. Used notion is the following:
```haskell
data P3 = P3 a b c
```
This type is naturally isomorphic to `(a,b,c)` and is an example of product type.

Dually $$Hask$$ has all finite coproducts (initial object is set to be empty coproduct), which follows from existence of `Either`. Again from monoidal structure $$(Hask, Either, Void)$$ we derive that parens-free notation is legal.

In Haskell coproducts are written as
```haskell
data C3 = Cons1 a | Cons2 b | Cons3 c
```
This is an example of sum type.

How consider two types:
```haskell
type Mix1 = (a, Either b c)
```
and
```haskell
type Mix2 = Either (a, b) (a, c)
```
It's easy to see that they are canonically isomorphic, let's write isomorphism down.
```haskell
iso :: (Mix1 -> Mix2, Mix2 -> Mix1)
iso = (mix1toMix2, mix2toMix1)

mix1toMix2 :: Mix1 -> Mix2
mix1toMix2 (a, Left b) = Left (a,b)
mix1toMix2 (a, Right c) = Right (a,c)

mix2toMix1 :: Mix2 -> Mix1
mix2toMix1 (Left (a,b)) = (a, Left b)
mix2toMix1 (Right (a,c)) = (a, Right c)
```
This isomorphism inductively generalises to arbitrary finite coproducts.

We have that tensor product `(,)` distributes with finite coproducts.

Assume we have arbitrary symmetric monoidal category with tensor product distributive over finite coproducts (frequently denoted as _symmetric monoidal category with finite coproducts_).
Consider classes of isomorphic types and denote binary product as $$\cdot$$, binary coproduct as $$+$$, terminal object as $$1$$, and initial object as $$0$$.

Laws of monoidal categories we have built can be rewritten as follows:
- $$(a+b)+c=a+(b+c)$$
- $$0 + a = a = a + 0$$
- $$a + b = b + a$$
- $$(a \cdot b) \cdot c = a \cdot (b \cdot c)$$
- $$1 \cdot a = a = a \cdot 1$$
- $$a \cdot b = b \cdot a$$

Distributivity gives us equation $$a \cdot (b + c) = a \cdot b + a \cdot c$$.

For any object $$a$$ consider $$0 \cdot a$$. **Proposition:** it satisfies universal property of initial object.
Proof by reference to [ncatlab: Proposition 2.2](https://ncatlab.org/nlab/show/distributive+category). Hence $$0 \cdot a = 0$$.

For category of finite sets this construction yields semiring of natural numbers, as Qiaochu Yuan [points](https://math.stackexchange.com/questions/2582771/relationship-between-algebraic-data-types-and-the-set-of-real-numbers#comment5333054_2582817) out. This is an easy exercise and we can compare it with notation of finite ordinals used to compactly denote finite products at the start of the post.

We have also just shown that objects in $$Hask$$ as a symmetric monoidal category with finite coproducts form a commutative semiring.

So Haskell types do. This construction seems to me a good answer to question why algebraic types are algebraic.

Actually we have not constructed algebraic types since we excluded recursive types. And there is a valid question if given semigroup structure extends to the whole class of algebraic types. But we did not rely on nature of elements while proving general statement, so there is nothing to raise a problem. Recursive types deserve at least a separate post.
