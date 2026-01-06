# 2. From Descriptions to Codes

In the previous chapter, the MDL principle was introduced as a general criterion for learning based on description length, and two complementary interpretations of description length were presented: an _algorithmic_ view based on Kolmogorov complexity and a _statistical_ view based on explicit coding schemes. Both perspectives share the same conceptual foundation (that compression is learning), but differ in how descriptions are represented and evaluated.

In this chapter, we move beyond that high-level view and make the notion of description length precise. We address the computability limitations of algorithmic MDL and show how statistical MDL can approximate the idealized algorithmic formulation.

Once we choose to formalize descriptions using explicit coding schemes, description lengths become codeword lengths and are therefore subject to strict structural constraints. This shift from informal descriptions to descriptions realized through codes helps transition MDL from a guiding principle into a rigorous mathematical theory.

## 2.1. Algorithmic MDL as an Ideal Reference

Algorithmic MDL provides a conceptually clean formulation of description length. By identifying the description of data with the shortest program that generates it, Kolmogorov complexity captures the intrinsic regularity of a dataset independently of any modeling assumptions. In this sense, algorithmic MDL represents an _ideal reference_: the shortest possible description of the data.

> Note: The additive constant depends on the choice of universal Turing machine (i.e., programming language). Different machines may encode the same program differently, but the difference is bounded by a fixed constant independent of the dataset.

However, this idealization comes at a fundamental cost; Kolmogorov complexity is uncomputable, and there exists no general algorithm that, given an arbitrary dataset, can determine the length of its shortest generating program. As a result, algorithmic MDL cannot be used directly as a practical model selection criterion. Nevertheless, it helps to formalize what MDL ultimately seeks to approximate: the shortest effective description of the data. Practical implementations of the MDL principle can be seen as attempts to approximate Kolmogorov complexity using restricted, computable descriptions.

## 2.2. Statistical MDL as a Computable Approximation
Statistical MDL provides a practical, computable approximation to algorithmic MDL by restricting attention to a specific class of descriptions in the form of encodings defined by explicit coding schemes. Rather than allowing arbitrary programs, descriptions are constructed using well-defined codes that assign binary strings to models, parameters, and data.

In statistical MDL, **descriptions are codes**. A description of an object, whether it is a model, a set of parameters, or the data given a model, is represented as a binary string produced by a coding scheme. The **description length** is simply the length of that binary string. This formal identification of descriptions with codes allows statistical MDL to be fully computable, while still capturing the essence of the algorithmic notion that shorter descriptions correspond to simpler, more efficient explanations of the data.

The MDL objective

```math
L_{\text{MDL}}(M) = L(M) + L(D \mid M)
```

is interpreted as the total length of a binary encoding that first describes the model and then describes the data given that model. Under this framework, the lengths $`L(M)`$ and $`L(D \mid M)`$ can be explicitly computed or approximated, making model comparison feasible in practice.

Two important consequences follow from this restriction:

- **Computability:** Unlike algorithmic MDL, code lengths can be explicitly computed or approximated, so statistical MDL can be applied to real datasets and practical models.
- **Approximation of Algorithmic MDL:** By choosing sufficiently expressive coding schemes, statistical MDL can closely approximate the algorithmic notion of description length. This will be shown in the next section.

Having introduced statistical MDL as a computable approach for descriptions, we now turn to a deeper analysis of how statistical codes relate to algorithmic descriptions. The next section presents the formal correspondence between the shortest program and sufficiently expressive statistical codes, providing a theoretical foundation for why statistical MDL can serve as a practical approximation of algorithmic MDL.

## 2.3. Connecting Statistical MDL to Algorithmic MDL

The relationship between algorithmic and statistical MDL can be formalized as follows:

**Theorem (Algorithmic&ndash;Statistical MDL Correspondence):** Let $`K(D)`$ denote the Kolmogorov complexity of a dataset $`D`$, and let $`L_{\text{stat}}(D)`$ denote the length of $`D`$ under a well-defined code. Then, up to an additive constant $`c`$ independent of $`D`$,

```math
L_{\text{stat}}(D) = K(D) + c
```

_if and only if_ the statistical code is sufficiently expressive to simulate a universal Turing machine.

The theorem formalizes why statistical MDL can serve as a practical approximation of algorithmic MDL. The key idea is that shortest programs and sufficiently rich statistical codes are essentially _interconvertible up to a small, fixed overhead_.

- The shortest program for a dataset can be viewed as an encoding of the data (programs are data). The decoder is simply an interpreter capable of executing the program (i.e., the encoding), which in turn reconstructs the dataset.

- Conversely, if you start with the shortest statistical code, you can turn it into a program by attaching a fixed decoder that reconstructs the data by simply interpreting the code.

Since both directions involve only a fixed overhead that is independent of the actual dataset, the length of the shortest program and the length of the shortest code differ by at most a fixed constant. This explains why statistical MDL, which uses computable codes, can approximate the idealized algorithmic MDL.

## 2.4. Descriptions as Prefix-Free Codes

In statistical MDL, descriptions become binary encodings. However, simply using any code is not enough; the encoded model and data must be recoverable exactly from their binary representation. And so, the codes must be _uniquely decodable_. Furthermore, as a consequence of the Kraft&ndash;McMillan theorem, any set of codeword lengths achievable by a uniquely decodable code can also be realized by a prefix-free code. For this reason, descriptions are normally restricted to prefix-free codes.

When viewing descriptions as encodings, description lengths are no longer just penalties; they are codeword lengths and therefore obey strict mathematical constraints. In particular, prefix-free codes impose nontrivial limits on the lengths they can assign. Specifically, for a code $`C : X \to Y^{*}`$ with codeword lengths $`L(x) = |C(x)|`$ for $`x \in X`$, the **Kraft&ndash;McMillan inequality** must hold:

```math
\sum_{x \in X} 2^{-L(x)} \leq 1.
```

This inequality plays an important role in statistical MDL. It formalizes the intuition that short descriptions are limited resources: assigning a very short description to one object forces longer descriptions elsewhere. As a result, model complexity penalties cannot be chosen arbitrarily; any valid statistical MDL criterion must correspond to a set of code lengths that satisfies this constraint.

Conversely, the Kraft&ndash;McMillan theorem guarantees that any collection of lengths satisfying the inequality corresponds to some prefix-free code. This allows MDL to reason directly in terms of description lengths, without having to realize a particular binary encoding.

## 2.5. Models and Data as Joint Descriptions

Equivalently, this two-part construction defines

Under statistical MDL, the description of a dataset is typically constructed as a two-part code. One part encodes the model, and the second encodes the data in a way that depends upon the model. The total description length is therefore written as

```math
L(M, D) = L(M) + L(D \mid M),
```

which corresponds operationally to first decoding the model and then decoding the data using the model-specific code.

At this stage, we do not yet specify how models, parameters, or residuals are encoded. The only requirement is that both components correspond to valid code lengths. This ensures that the resulting description is unambiguous and exactly decodable, while leaving room for different modeling and coding choices that will be introduced later.

With this foundation in place, we are now ready to see how the MDL principle can be applied in practice. The next chapter demonstrates this through a concrete example in which we select a polynomial to model a dataset, balancing model complexity and fit using code lengths.

