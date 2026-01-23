# 8. The Sardinas&ndash;Patterson Algorithm

Prefix-free codes are always uniquely decodable and relatively easy to verify. However, many codes are uniquely decodable even if they are not prefix-free. Determining unique decodability is important because, in communication systems and data compression, we need to ensure that encoded messages can always be decoded unambiguously. The _Sardinas&ndash;Patterson algorithm_ provides a systematic way to check whether a code is uniquely decodable, even when it is not prefix-free, helping us design efficient and reliable coding schemes.

## 8.1. Problem Statement

Let $`C: X \to Y^{*}`$ be a code mapping symbols from a source alphabet $`X`$ to strings over a target alphabet $`Y`$.

The goal is to determine whether $`C`$ is uniquely decodable. Recall that a code is uniquely decodable if any concatenation of codewords can be decoded in only one way. Conversely, if there exists a string in $`Y^{*}`$ that can be formed by concatenating codewords corresponding to different sequences of source symbols, the code is not uniquely decodable.

## 8.2. Detecting a Non-Uniquely Decodable Code

Consider the binary code $`C : X \rightarrow Y^{*}`$ defined by

- $`a \mapsto 01`$
- $`b \mapsto 10`$
- $`c \mapsto 101`$
- $`d \mapsto 11101`$

If this code weren’t uniquely decodable, there would be two different sequences of codewords

```math
w = x_{1} x_{2} \dots x_{n} \quad \text{and} \quad w^{\prime}  = x_{1}^{\prime} x_{2}^{\prime} \dots x_{m}^{\prime}
```

that produce the same concatenated string:

```math
w = w^{\prime} \quad \text{but} \quad (x_{1}, \dots, x_{n}) \neq (x_{1}^{\prime}, \dots, x_{m}^{\prime}).
```

If the first codewords were the same ($`x_{1} = x_{1}^{\prime}`$), we could just remove them and look at the suffixes. So let’s assume $`x_{1} \neq x_{1}^{\prime}`$.

Looking at the codewords, the only way two sequences could start differently but overlap is if the first codewords are $`10`$ and $`101`$. That is to say,

```math
x_{1} = C(b) = 10 \quad \text{and} \quad x_{1}^{\prime} = C(c) = 101.
```

Then we have

```math
w = 10\, x_{2} \dots \quad \text{and} \quad \quad w^{\prime} = 101\, x_{2}^{\prime} \dots
```

For these two strings to match, $`x_{2}`$ must start with a $`1`$. If we take $`x_{2} = C(c) = 101`$, we have

```math
w = 10101\dots \quad \text{and} \quad w^{\prime} = 101\dots
```

And now, if we take $`x_{2}^{\prime} = C(a) = 01`$, we get

```math
w = 10101\dots \quad \text{and} \quad w^{\prime} = 10101 \ldots
```

At this point, $`w`$ and $`w^{\prime}`$ have the same prefix, namely $`10101`$. So we have found a string that can be obtained from two different sequences of codewords: $`C(b)C(c)`$ and $`C(c)C(a)`$. This systematic search for overlapping codewords is the inspiration for the Sardinas&ndash;Patterson algorithm.

## 8.3. The Sardinas&ndash;Patterson Algorithm

It is worth noting that the Sardinas&ndash;Patterson algorithm applies only to finite codes. Determining whether an infinite code (i.e., one with an infinite source alphabet) is uniquely decodable is, in general, not computable, and other techniques are required.

### 8.3.1. Preliminaries

Let $`C: X \to Y^{*}`$ be a _finite_ code, where $`X`$ is the source alphabet and $`Y`$ is the code alphabet.

Define $`S_{1}`$ as the set of all _non-empty suffixes_ obtained when one codeword has another codeword as a prefix:

```math
S_{1} = \{ w \in Y^{*} \mid x_{i}, x_{j} \in \mathrm{Im}(C),\; x_{i} = x_{j} w,\; w \neq \varepsilon \}.
```

Here, $`\mathrm{Im}(C)`$ denotes the image of $`C`$ (i.e., the codebook of $`C`$).

Intuitively, $`S_{1}`$ contains the _first potential ambiguities_ in the code. If a codeword $`x_{i}`$ begins with another codeword $`x_{j}`$, then the remainder of $`x_{i}`$ after removing $`x_{j}`$ is included in $`S_{1}`$. These are exactly the strings that need to be tracked to check for unique decodability.

For $`k \ge 1`$, define a sequence of sets $`\{ S_{k} \}_{k \ge 1}`$ recursively as follows:

```math
\begin{aligned}
S_{k+1} =& \{ w \mid x \in \mathrm{Im}(C),\; s \in S_{k},\; s = x w \} \\
& \cup \{ w \mid x \in \mathrm{Im}(C),\; s \in S_{k},\; x = s w \}.
\end{aligned}
```

Intuitively, $`S_{k+1}`$ contains all non-empty suffixes that could create ambiguities after removing a codeword from the start of a string in $`S_{k}`$, or removing a string in $`S_{k}`$ from the start of a codeword.

If a concatenation of codewords can be interpreted in multiple ways, a suffix will eventually reduce to the empty string, indicating ambiguity.

### 8.3.2. The Algorithm

1. Compute $`S_{1}`$.
2. Iteratively compute $`S_{k+1}`$:
   * If $`S_{k+1}`$ contains $`\varepsilon`$, the code is **not uniquely decodable**.
   * If $`S_{k+1} = S_{k}`$, the code is **uniquely decodable**.

## 8.4. Examples

We now apply the Sardinas&ndash;Patterson algorithm to a few concrete examples.

### 8.4.1. An Example of a Uniquely Decodable Code

Let $`C`$ be a code with codebook

```math
D = \{ 0, 01, 011 \}.
```

We begin by finding the suffixes of overlapping codewords. Note that

- $`0`$ is a prefix of both $`01`$ and $`011`$, yielding suffixes $`1`$ and $`11`$.
- $`01`$ is a prefix of $`011`$, yielding suffix $`1`$.

And so, $`S_{1} = \{ 1, 11 \}`$.

Next, we compute $`S_{2}`$ from $`S_{1}`$ using the recursive definition:

```math
\begin{aligned}
S_{2} =& \{ w \mid x \in D,\ s \in S_{1},\ s=xw \} \\
&\cup \{ w \mid x \in D,\ s \in S_{1},\ x=sw \}.
\end{aligned}
```

Considering cases where a codeword is a prefix of a string in $`S_{1}`$:

- For $`s = 1`$ and $`s = 11`$, no codeword in $`D`$ is a prefix of either string, since all codewords in $`D`$ begin with $`0`$. Thus, no suffixes arise from this case.

Considering cases where a string in $`S_{1}`$ is a prefix of a codeword:

- For $`s = 1`$ and $`s = 11`$, neither string is a prefix of any codeword in $`D`$. Thus, no suffixes arise from this case either.

Thus, $`S_{2} = \varnothing`$ and $`S_{3} = S_{2}`$. Since $`\varepsilon \notin S_{k}`$ and the sets have stabilized ($`S_{2} = S_{3}`$), the code $`C`$ is uniquely decodable according to the Sardinas&ndash;Patterson algorithm.

### 8.4.2. An Example of a Non-Uniquely Decodable Code

Let $`C`$ be a code with codebook

```math
D = \{ 01, 10, 101, 11101 \}.
```

Following the Sardinas&ndash;Patterson Algorithm, we compute the successive sets of suffixes:

```math
S_{1} = \{ 1 \}, \quad S_{2} = \{ 0, 01, 1101 \}, \quad \text{and} \quad S_{3} = \{ \varepsilon, 1 \}.
```

Since $`S_{3}`$ contains $`\varepsilon`$, the code $`C`$ is not uniquely decodable, according to the Sardinas&ndash;Patterson algorithm.

