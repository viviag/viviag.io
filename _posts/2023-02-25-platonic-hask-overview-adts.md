---
layout: post
title:  "Platonic Hask overview: algebraic data types as a corollary of monoidal structure"
tags: [hask]
usemathjax: true
---

_[To index of the series](https://viviag.io/tagged/hask/)_

#### Algebraic data types

We have only considered binary products and coproducts. We can consider a family of products of arbitrary arity defining them as representing objects of $$\Pi_{i \in I}\operatorname{Hom}(\_,A_i)$$ for finite ordinals $$I$$. This definition has a problem with $$I = 0$$, we treat this case specially and define the empty product as the terminal object. Universal properties of these objects can be drawn easily from the binary case.

$$Hask$$ has all the finite products, for instance, product `(a,(b,c))` satisfy the universal property. This is an instance of a general statement that a category with binary products and terminal objects has all finite products.

Usual binary product `(,)` serves as a tensor product in $$(Hask, (,), ())$$, hence it satisfies associativity law and these products can be written without too many parens. Haskell has this notion --- there are tuples of length bigger than 2. But each of them is implemented separately in GHC and it doesn't allow tuples of length above some finite number.

This number is big enough for most applications, but here we need it only to notice that it is not a notion Haskell uses for the parens-free presentation of finite products. The used notion is the following:
```haskell
data P3 = P3 a b c
```
This type is naturally isomorphic to `(a,b,c)` and is an example of a product type.

Dually $$Hask$$ has all finite coproducts (initial object is set to be empty coproduct), which follows from the existence of `Either`. Again from monoidal structure $$(Hask, Either, Void)$$ we derive that parens-free notation is legal.

In Haskell, coproducts are written as
```haskell
data C3 = Cons1 a | Cons2 b | Cons3 c
```
This is an example of a sum type.

Now consider two types:
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
This isomorphism inductively generalizes to arbitrary finite coproducts.

We have that tensor product `(,)` distributes with finite coproducts.

Assume we have an arbitrary symmetric monoidal category with tensor product distributive over finite coproducts (frequently denoted as _symmetric monoidal category with finite coproducts_).
Consider classes of isomorphic types and denote binary product as $$\cdot$$, binary coproduct as $$+$$, terminal object as $$1$$, and initial object as $$0$$.

The laws of monoidal categories we have built can be rewritten as follows:
- $$(a+b)+c=a+(b+c)$$
- $$0 + a = a = a + 0$$
- $$a + b = b + a$$
- $$(a \cdot b) \cdot c = a \cdot (b \cdot c)$$
- $$1 \cdot a = a = a \cdot 1$$
- $$a \cdot b = b \cdot a$$

Distributivity gives us equation $$a \cdot (b + c) = a \cdot b + a \cdot c$$.

**Proposition:** For any object $$a$$ $$0 \cdot a = 0$$. I.e. $$0 \cdot a$$ is an initial object.

**Proof:** Consider $$\operatorname{Hom}(0 \cdot a, b)$$ for some $$b$$. It is non-empty due to existence of the unique composition $$0 \cdot a \xrightarrow{\pi_0} \to b$$. Let $$f, g \in \operatorname{Hom}(0 \cdot a, b)$$. Consider $$(0 \cdot a) + (0 \cdot a) = (0 + 0) \cdot a = 0 \cdot a$$. There is a unique morphism from $$0$$ to $$0$$, hence inclusions $$i_1$$ and $$i_2$$ to the left and right summands coincide. Consider $$f + g : (0 \cdot a) + (0 \cdot a) \to b + b$$. Compositions $$(f + g) . i_1$$ and $(f + g) . i_2$ are equal as $b + 0 = 0 + b$. But $$(f + g) . i_1$ = f + 0 = f$ and $$(f + g) . i_2 = 0 + g = g$$.

Reference: [ncatlab: Proposition 2.2](https://ncatlab.org/nlab/show/distributive+category).

For the category of finite sets, this construction yields the semiring of natural numbers, as Qiaochu Yuan [points](https://math.stackexchange.com/questions/2582771/relationship-between-algebraic-data-types-and-the-set-of-real-numbers#comment5333054_2582817) out. This is an easy exercise and we can compare it with the notation of finite ordinals used to compactly denote finite products at the start of the post.

We have also just shown that isomorphism classes of objects in $$Hask$$ as a symmetric monoidal category with finite coproducts form a commutative semiring.

So do Haskell types. This construction seems to be a good answer to the question of why algebraic types are algebraic.

Actually, we have not constructed algebraic types since we excluded recursive types. And there is a valid question if the given semigroup structure extends to the whole class of algebraic types. But we did not rely on the nature of elements while proving a general statement, so there is nothing to raise a problem. Recursive types deserve at least a separate post.
