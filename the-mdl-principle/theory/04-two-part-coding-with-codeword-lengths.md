# 4. Two-Part Coding with Codeword Lengths

In the previous chapter, we introduced a concrete two-part code to encode both the polynomial model and the data. While this explicit coding approach is instructive, reasoning directly with bits and integer codes can be cumbersome and somewhat arbitrary. In this chapter, we abstract away the details of the actual code and reason directly in terms of codeword lengths, laying the foundation for refined MDL and probabilistic approaches.

The key idea is that once we have a valid code, we can work with the lengths of the codewords instead of the literal binary strings themselves. This simplifies analysis and model comparison while preserving the core MDL principle.

## 4.1. From Concrete Codes to Codeword Lengths

Recall that the total description length of a model $`M`$ and dataset $`D`$ in the two-part coding framework is

```math
L_{\text{MDL}}(M, D) = L(M) + L(D \mid M),
```

where:

- $`L(M)`$ is the number of bits needed to encode the model, and
- $`L(D \mid M)`$ is the number of bits needed to encode the data given the model.

In the previous chapter, these description lengths were obtained from explicit integer codes used to encode the degree, coefficients, and residuals. In practice, however, we often do not need to construct the binary strings themselves. Instead, it is sufficient to reason directly about the _lengths_ assigned to each component of the description.

Each part of the description is associated with a length function corresponding to a valid prefix-free code for that component:

- The degree of the polynomial is assigned a length $`L_{d}(d)`$.
- Each coefficient is assigned a length $`L_{a}(a_{i})`$.
- Each residual is assigned a length $`L_{r}(r_{i})`$.

These length functions can come from different codes. For instance, the Elias gamma code could be used for the degree of the polynomial, while the Elias delta code could be used for the coefficients. What matters is not the specific encoding procedure, but that each set of lengths corresponds to a valid prefix-free code. Each length function must satisfy the Kraft&ndash;McMillan inequality for its domain, ensuring that a corresponding prefix-free code _exists_, even if we do not construct it.

Under this abstraction, we no longer focus on how the binary strings are constructed. Instead, we treat description length as a mathematical quantity that behaves like a codeword length. The total description length can then be written as

```math
L(M) = L_{d}(d) + \sum_{i=0}^{d} L_{a}(a_{i}), \quad
L(D \mid M) = \sum_{i=1}^{n} L_{r}(r_{i}),
```

so that

```math
L_{\text{MDL}}(M, D) = L_{d}(d) + \sum_{i=0}^{d} L_{a}(a_{i}) + \sum_{i=1}^{n} L_{r}(r_{i}).
```

Notice that this expression is _exactly the same_ as the one obtained using our concrete two-part code. The difference is that we no longer need to commit to a specific integer code such as Elias delta or gamma; all that matters is that the codewords are uniquely decodable. This formulation separates the _structure_ of the MDL objective from the particular codes used to realize it. Later, we will see that the need to choose these length functions carefully, and the arbitrariness involved in doing so, motivates the development of refined MDL methods.

## 4.2. Uniquely Decodable Codes and the Kraft Inequality

When reasoning in terms of codeword lengths, we only require that the underlying code be uniquely decodable, so that the encoded description can be recovered without ambiguity. However, by the Kraft&ndash;McMillan theorem, for every uniquely decodable code that is not prefix-free, there exists a prefix-free code with the *same codeword lengths*. And so, we can restrict attention to prefix-free codes, without loss of generality, when reasoning abstractly about description lengths.

The codeword lengths of a prefix-free code satisfy the Kraft inequality:

```math
\sum_{x \in X} 2^{-L(x)} \le 1,
```

where $`X`$ denotes the set of objects being encoded (such as degrees, coefficients, or residuals). Conversely, any set of lengths satisfying this inequality can be realized as the codeword lengths of some prefix-free code. This allows us to reason entirely in terms of codeword lengths while remaining consistent with actual encodings. In practice, we can therefore compare models using their implied description lengths without ever constructing the binary code itself.

## 4.3. Reasoning Over Codeword Lengths

So far, we have discussed abstract codeword lengths without committing to a specific code. To see why reasoning about lengths alone can be useful, consider a simpler scenario: encoding a symbol from a finite set $`X`$ of $`n`$ items.

### 4.3.1. Fixed-Length Codes for Finite Sets

If all symbols in $`X`$ are equally likely, a natural approach is to use a fixed-length code. Given $`n`$ items, we need $`\lceil \log_{2} n \rceil`$ bits to represent each symbol. Each symbol is therefore assigned

```math
L(x) = \lceil \log_{2} n \rceil
```

bits. Note that a fixed-length code is always prefix-free.

**Example:** Suppose we want to encode the days of the week. There are $`n = 7`$ symbols. Using a fixed-length code, each day can be encoded in $`\lceil \log_{2} 7 \rceil = 3`$ bits. This shows that reasoning about lengths alone is often sufficient: we do not need the actual binary codewords to know their length.

> Standard ASCII is a fixed-length 7-bit encoding that represents 128 characters (including English letters, digits, and punctuation) as bit strings whose numerical values range from 0 to 127.

### 4.3.2. Probabilities and Shannon's Optimal Code Lengths

We can do better if we know the probabilities of the symbols. Let $`p(x)`$ denote the probability of symbol $`x`$ in $`X`$. According to Shannon's source coding theorem, the optimal codeword length for $`x`$ is

```math
L(x) \approx -\log_{2} p(x),
```

up to rounding to integers. More probable symbols get shorter codewords, and less probable symbols get longer ones, minimizing the expected total description length.

**Example:** Consider encoding letters in English text. The letter `e` occurs roughly 13% of the time, whereas `z` occurs less than 1%. Assigning lengths proportional to $`-\log_{2} p(x)`$ allows us to compress English text more efficiently than a fixed-length code. While Huffman coding could generate concrete codewords, we can compute the codeword length of each letter directly using Shannon's formula without constructing the binary codewords.

### 4.3.3. Why This Doesn't Solve the Polynomial Case

For polynomial regression, this probabilistic reasoning does not directly apply:

- Coefficients and residuals are real numbers, not symbols from a finite set.
- There is no natural, predefined probability distribution over these quantities.

Thus, while fixed-length and Shannon-optimal codes are useful for discrete symbols, additional mechanisms are needed to handle real-valued data and models. These mechanisms are developed in later chapters using refined MDL and probabilistic modeling, which assign effective code lengths to continuous parameters and residuals.

## 4.4. Fractional Codeword Lengths and Average Optimality

When reasoning about codeword lengths abstractly, we often allow fractional lengths rather than rounding to the nearest integer. For example, in English text, the letter `e` occurs with probability $`p(\text{e}) \approx 0.13`$. The corresponding Shannon-optimal codeword length is

```math
L(e) = -\log_{2} p(\text{e}) \approx 2.94 \text{ bits}.
```

Allowing fractional lengths is convenient because it corresponds to the _average codeword length_ of an optimal prefix-free code. Formally, if we consider a set of symbols $`X = \{ x_{i} \}`$ with associated probabilities $`p(x_{i})`$, the expected codeword length is

```math
\mathbb{E}[L(X)] = \sum_{x_{i} \in X} p(x_{i}) \, L(x_{i}) = \sum_{x_{i} \in X} p(x_{i}) \, (-\log_{2} p(x_{i})).
```

Any actual code must assign integer-length codewords, but rounding introduces only a small overhead (at most 1 bit per codeword). For large collections of symbols, the average length per symbol approaches this expected value, making fractional lengths a faithful and practical abstraction for analyzing compression and description length.

Going forward, we will allow fractional bits when reasoning about abstract codes. Treating codeword lengths as real numbers simplifies analysis without affecting the essence of the MDL principle.

