---
layout: post
title: "Minimal weights of cyclic codes"
tags: [math-gm]
usemathjax: true
---

I have recently attended perfect course in coding theory by [Valery
Gritsenko](https://www.hse.ru/en/org/persons/143457702). And being
inspired by it I want to tell a story related to both algebra and
hardware engineering.

This post does not cover encoding and decoding of cyclic codes, existing
implementations, and many mathematical aspects of codes. Maybe I will
write more on this topic.

Let's start with a practical problem. People or machines frequently have
to send messages. They have to use some physical data transmission
channel. Any physical channel is error-prone. Hence we have to be able
to clean message from errors. One of adopted solutions is
error-correcting codes.

#### Linear codes: additive structure

Most of the information now is transmitted in binary form. So our
message is a sequence of bits. Let's split a message by chunks of length
$$n$$. We will call subset of all words of length $$n$$ a code of length $$n$$.

Binary word of length $$n$$ is an element in $$\mathbb{F}_2^n$$ --- $$n$$-dimensional vector space
over field with two elements $$0$$ and $$1$$. Multiplication by scalars is trivial
over $$\mathbb{F}_2$$. Hence the only operation we obtain by introducing this structure
is addition. For simplicity we require all codes we consider to be
closed under addition --- sum of any two words in a code must lie in a
code. Such codes are called **linear**.

#### Correcting codes: metric structure

Now each of our codes is a subspace in $$\mathbb{F}_2^n$$. Denote number of nonzero
coordinates of vector $$a$$ as $$w(a)$$. This function is called weight.

Using this function we can define **Hamming distance**. Let $$a$$ and $$b$$ be
arbitrary words in $$\mathbb{F}_2^n$$. Then Hamming distance $$d(a,b)$$ is defined as $$w(a+b)$$. It is
symmetric by definition and is zero if and only if arguments coincide.

Note that

$$w(a+b) = w(a) + w(b) - \#\{i:\;a_i = b_i = 1\}$$.

Last summand can be written as $$w(a \cdot b)$$ with $$a$$ and $$b$$ multiplied coordinate-wise.
We can check triangle inequality:

Let $$a,b,c$$ be three vectors.
Then
$$w(a+b) + w(b+c) = w(a) + 2w(b) + w(c) - w(a \cdot b) - w(b \cdot c)$$

and
$$w(a+c) = w(a) + w(c) - w(a \cdot c)$$.

Difference
$$w(a+b) + w(b+c) - w(a+c) = 2w(b) - w(a \cdot b) - w(b \cdot c) + w(a \cdot c)$$

is strictly non-negative since
$$\forall x\; w(b) \geq w(b \cdot x)$$
.

Thus Hamming distance turns $$\mathbb{F}_2^n$$ into a metric space $$\mathbb{B}_2^n$$. Metric induces metric topology of open balls, we shall use these terms.

We are ready to define correcting codes. Consider a message $$m$$ of length $$n$$
with errors. It is a vector. And it has a distance from code subspace it
was expected to be in defined as minimum of distances to words in a
code. If where is a single closest word $$r$$ we say (assuming errors in used
channel are rare) that $$r$$ is a correction of $$m$$. Hence code $$C$$ corrects $$e$$ errors
if every word in $$C$$ has a closed ball with center in it and radius $$e$$ with no
other words of $$C$$ inside (including boundary).

Let $$d$$ be a minimal distance between words in $$C$$. This condition is
equivalent to $$e = \operatorname{floor}(\frac{d}{2})$$. Since $$w(a+b) = w(a+c+b+c)$$ we can isometrically shift code by any vector.
Hence $$d = \min_{a \neq 0 \in C}w(a)$$.

#### Cyclic codes: multiplicative structure

Every vector space has a basis. We want to compute minimal weight of a
code and we already can do it given a basis. Every word in a vector
space over $$\mathbb{F}_2$$ can be identified with characteristic function of its
decomposition into basis vectors so we can map over all words of length
equal to dimension of the code without using any complex operations.
This algorithm is more effective than the one we give in the end since
its complexity depends on dimension of the code and not space itself.

But we want to introduce another approach.

Consider ring $$R = \mathbb{F}[x]/(x^n-1)$$. It is a vector space over $$\mathbb{F}_2$$ with finite dimension $$n$$. Any two vector spaces of same dimension are isomorphic, so $$R \cong \mathbb{F}_2^n$$. This
isomorphism $$\phi$$ is given by identifying polynomial with vector of its
coefficients.

Consider codes generated by linear combinations of cyclic shifts of a
single vector $$g$$. They are called **cyclic codes**. Cyclic shifts form a
cyclic group and are generated by a single one-positional shift $$\sigma$$. In $$\mathbb{F}_2[x]$$ $$\phi(\sigma(g)) = x \cdot \phi(g)$$ by construction. It is a straightforward check that this relation holds in $$R$$.

$$x$$ is a basis of $$R$$ as a finitely generated $$\mathbb{F}_2$$-algebra. Hence every element $$f \cdot \phi(g)$$ is a sum $$\phi(g) \cdot \sum x^i$$ for some 's. Hence cyclic codes are in 1:1 correspondence to ideals
in $$R$$ generated by single polynomial. From general theory it is known that $$R$$
is a principal ideal domain, hence these are all ideals.

Immediate consequence of this form of cyclic codes is easy encoding ---
to encode word in cyclic code we only have to multiply it by generating
polynomial. It is just an example of why this presentation is useful.

#### Brute-force computation of minimal weight

There are several bounds on minimal weight. And for many codes,
especially for best codes (I use this adjective for Hamming codes and
simplicial codes) it is exactly known. But we are interested in
brute-force algorithm. We have
[one](https://gist.github.com/viviag/19183263c868494d43fe28883d7e462f)
from linear algebra and it has the best performance in most cases. But
let's use polynomial presentation.

Idea is to filter list of all polynomials of degree below $$n$$ over $$\mathbb{F}_2$$ by a
condition of being in $$C$$ and then compute lengths. Unlike when we use
linear structure we have no fast way to list all polynomials in a code.

The most interesting question is how to represent a polynomial.

Naive representation is to represent polynomial as list of degrees of
monomials and compute modulo $$x^n-1$$ by using Euclid's algorithm. Algorithm is
[here](https://gist.github.com/viviag/4961e87e7db567f0d2fd86bc664388ae).

This algorithm runs about a week on code of length $$31$$ with generating
polynomial of degree $$16$$ (of dimension $$15$$). For reference linear algorithm
succeeds in $$3$$ seconds, this is obvious cost of choice of parameter which
controls complexity. In linear algorithm we have to evaluate length of
each of $$2^{16}$$ words. In polynomial algorithm we have to list code first by
checking any of $$2^{31}$$ polynomials if they belong to $$C$$.

But recall that $$\phi$$ is isomorphism of vector spaces. Hence we can store
polynomial as a bit word. It cannot reduce complexity asymptotically but
does it give us a better measures of single operation?

And it does.

Sum of vectors over $$\mathbb{F}_2$$ is computationally a simple xor. It is implemented on
hardware level and is fast.

Multiplication on a monomial of degree $$d$$ is a bitwise shift on $$d$$ bits. Hence
multiplication of $$f$$ and $$g$$ can be performed in single cycle in at maximum $$\operatorname{deg}(f) \cdot \operatorname{deg}(g)$$ hardware-level operations --- one multiple for shifts, another for
additions since we iterate by another polynomial.

Modulo is the hardest of three. Consider $$f \operatorname{mod} g$$. $$f \operatorname{mod} g = \sum_i a_ix^i \operatorname{mod} g$$ where $$x^i$$ are monomials of $$f$$. $$x^i \operatorname{mod} g = x^i$$ if
degree of $$x^i$$ is less than degree of $$g$$. If degrees are equal, modulo is equal
to $$x^i + g$$. Otherwise divide $$i$$ by $$t = \operatorname{deg}(g)$$.

We obtain
$$i = qt + r$$ and $$x^i \operatorname{mod} g = x^{tq} \operatorname{mod} g + x^r \operatorname{mod} g = (g+x^i)^t + x^r \operatorname{mod} g$$.

Polynomial at the end has
degree strictly less than initial hence we can divide it again and
eventually compute result.

Implementation is
[here](https://gist.github.com/viviag/fcd540145d914c09d9c5edf3df288885).
It runs about six times faster than the naive one.

#### Postscript

Implementation of operations with binary polynomials of a level of bits
is an important side-effect. Latest algorithm can be easily rewritten in
C and be run on specific hardware. And these operations are important
not only for discrete signal transmission but are also crucial for all
symmetric cryptography.
