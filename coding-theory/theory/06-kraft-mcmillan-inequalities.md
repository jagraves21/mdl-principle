# Codeword Lengths and the Kraft&ndash;McMillan Inequalities

Building upon the ideas of prefix-free codes and codeword lengths, we now explore the constraints that ensure unambiguous decoding. This will allow us to connect codeword lengths to probabilities and, ultimately, to the minimum description length (MDL) principle.

## 1. Kraft's Theorem (Prefix-Free Codes)

The Kraft inequality provides a necessary and sufficient condition on the codeword lengths of prefix-free codes.

**Theorem (Kraft).**  Let $`X`$ and $`Y`$ be alphabets with $`|Y| = D`$, and let

```math
L_{1}, L_{2}, \dots, L_{m} \in \mathbb{N}
```

be a set of positive integers. There exists a prefix-free code $`C: X \to Y^{*}`$ with codeword lengths exactly $`L_{1}, \dots, L_{m}`$ if and only if

```math
\sum_{i=1}^{m} D^{-L_{i}} \le 1.
```

Equivalently, using the length function $`\ell(x) = |C(x)|`$,

```math
\sum_{x \in X} |Y|^{-\ell(x)} \le 1.
```

### Interpretation

- Each term $`D^{-L_{i}}`$ corresponds to the fraction of the full $`D`$-ary universal prefix tree occupied by a codeword of depth $`L_{i}`$.
- The inequality ensures that the total "occupied space" in the code space tree does not exceed available capacity.
- When the bound is satisfied, we can assign codewords so that no codeword is a prefix of any other, guaranteeing instantaneous and unique decoding.

### Consequences

- **Necessity:** Every prefix-free code must satisfy the Kraft inequality.
- **Sufficiency:** Any set of integer lengths satisfying the inequality can be realized by an explicit construction of a prefix-free code (e.g., using a greedy tree assignment procedure).

Thus, Kraft's Theorem completely characterizes the length sequences that are attainable by prefix-free coding schemes. This result forms the bridge between _abstract length assignments_ and _actual code constructions_, enabling the design of optimal codes once desired lengths have been chosen&mdash;most notably those derived from symbol probabilities in entropy coding and MDL theory.

## 2. McMillan Inequality (Uniquely Decodable Codes)

While Kraft's Theorem characterizes prefix-free codes, we can ask a more general question: what constraints are placed on uniquely decodable codes, which may not be prefix-free? The answer is given by **McMillan's inequality**.

**Theorem (McMillan).**  Let $`C: X \to Y^{*}`$ be a uniquely decodable code over an alphabet of size $`|Y| = D`$, with codeword lengths $`L_{1}, L_{2}, \dots, L_{m}`$. Then

```math
\sum_{i=1}^{m} D^{-L_{i}} \le 1.
```

Equivalently, using the length function $`\ell(x) = |C(x)|`$,

```math
\sum_{x \in X} |Y|^{-\ell(x)} \le 1.
```

### Key Points

- **Necessity:** Any uniquely decodable code, whether prefix-free or not, must satisfy this inequality.  
- **Difference from Kraft:** Unlike Kraft's Theorem, McMillan's result is **necessary but not sufficient** for uniquely decodable codes. That is to say, satisfying the inequality does not guarantee that a code with those lengths exists; it only constrains what is possible.  
- **Intuition:** The inequality ensures that the "code space" is not overfilled. Even when codewords can appear as prefixes of others (so long as the code is still uniquely decodable), the total fraction of the universal prefix tree cannot exceed 1.

### Implications

- Prefix-free codes are a special case of uniquely decodable codes; for them, Kraft's inequality is both necessary and sufficient.  
- McMillan's inequality extends the length-space constraints to all uniquely decodable codes, providing a fundamental limit on what codeword lengths are possible.  

This result forms a bridge to _entropy coding_ and _optimal code design_, since any code aiming to minimize expected length must obey these constraints.

## 3. From Uniquely Decodable Codes to Prefix-Free Codes

A remarkable property in coding theory is that **any set of codeword lengths achievable by a uniquely decodable code can also be realized by a prefix-free code**. That is, if we have a uniquely decodable code $`C : X \to Y^{*}`$ that is not prefix-free, we can always find a prefix-free code $`C^{\prime} : X \to Y^{*}`$ such that 

```math
\ell(C(x_{i})) = \ell(C^{\prime}(x_{i})) \quad \text{for all } x_i \in X.
```

To see why this must be the case, suppose we have a uniquely decodable code $`C : X \to Y^{*}`$. Then, by the McMillan inequality, the codeword lengths of $`C`$ satisfy

```math
\sum_{i=1}^{m} |Y|^{-L_i} \le 1.
```

Since the Kraft inequality is both **necessary and sufficient** for prefix-free codes, it follows that there must exist a prefix-free code $`C^{\prime} : X \to Y^{*}`$ with the same set of codeword lengths:

```math
\ell(C(x_{i})) = \ell(C^{\prime}(x_{i})) \quad \text{for all } x_{i} \in X.
```

The Kraft&ndash;McMillan inequalities serve as a bridge between general uniquely decodable codes and practical prefix-free constructions, thus allowing us to focus on prefix-free codes rather than the more general uniquely decodable codes.

## 4. Connecting Probabilities to Codeword Lengths

### Shannon's Insight

Claude Shannon's 1948 paper, _A Mathematical Theory of Communication_, founded the field of **information theory**. One of his fundamental insights was that the probability of a symbol determines how many bits are _unavoidably required_ to represent it. Shannon showed that any prefix-free or uniquely decodable code assigning codeword lengths $`L_{i}`$ to source symbols with probabilities $`p_{i}`$ must satisfy an average-length constraint bounded below by the source entropy:

```math
H(X) = -\sum_{i=1}^n p_{i} \log_2 p_{i}.
```

From this result follows the idea of an **ideal codeword length**. If codeword lengths could be chosen continuously (rather than restricted to integers), the optimal assignment would be

```math
L_{i} \approx -\log_{2} p_{i}.
```

This choice makes each codeword length proportional to the _information content_ (or "surprisal") of the source symbol $`x_{i}`$. Consequently:

- More probable symbols receive shorter codewords.
- Less probable symbols receive longer codewords.

Shannon further showed that no coding scheme can achieve an expected length smaller than the entropy, but that prefix-free codes exist whose expected length is within one bit of this ideal bound. This establishes the fundamental inequality

```math
H(X) \le \bar{L} < H(X) + 1,
```

where the expected codeword length is

```math
\bar{L} = \sum_{i=1}^{n} p_{i} L_{i} = \sum_{x \in X} p(x)\,\ell(x).
```

This result forms the theoretical basis of efficient source coding. Practical coding methods, most notably Huffman coding, construct integer-length prefix codes that approximate the ideal lengths $`-\log_{2} p_{i}`$ as closely as possible, minimizing the expected message length subject to the Kraft&ndash;McMillan constraints.

Beyond data compression, Shannon's principle also underlies the MDL framework. In MDL, models or hypotheses that assign higher probability to observed data correspond to shorter descriptions, making learning equivalent to finding the explanation that yields the most compact encoding of the data.

## 6. Implications for MDL

- Prefix codes ensure unambiguous decoding and instantaneous reading.
- Codeword lengths, represented by the length function $`\ell`$, directly reflect _information content_.
- The uniquely decodable / prefix-free equivalence allows us to focus exclusively on prefix-free codes for analyzing optimal lengths.
- Assigning codeword lengths based on probabilities allows us to minimize expected description length.

