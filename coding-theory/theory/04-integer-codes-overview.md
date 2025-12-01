# Integer Codes Overview

Integer codes are **binary, prefix-free codes** designed to represent integers efficiently. They are widely used when encoding sequences of numbers **without knowing the probability distribution in advance**; encoding methods that take advantage of known probabilities will be discussed later.

We will first introduce the concept of **codeword lengths**, then explore common integer codes, and finally discuss **universal codes**.

## 1. Codeword Lengths

For a code $C: X \to Y^{*}$ over an alphabet $Y$:

- Each source symbol $x_{i} \in X$ is assigned a **codeword** $C(x_{i})$.
- Define the **length function**

$$\ell : X \to \mathbb{N}, \qquad \ell(x_{i}) = |C(x_{i})|.$$

We write $L_{i} := \ell(x_{i})$ for the length of the codeword associated with symbol $x_{i}$.

For a sequence (i.e., message)

$$\mathbf{x} = x_{1}, x_{2}, \dots, x_{n} \in X^{*},$$

the code is applied by concatenation:

$$C(\mathbf{x}) = C(x_{1})\,C(x_{2})\,\cdots C(x_{n}).$$

The **total encoded length** is obtained by the additive extension of the length function:

$$\ell(\mathbf{x}) = \sum_{j=1}^{n} \ell(x_{j}) = \sum_{j=1}^{n} |C(x_{j})| = \sum_{j=1}^{n} L_{j}.$$

Thus, the description length of any message is simply the sum of the lengths of its individual symbol codewords.

The goal is to design a code $C$ whose induced length function $\ell(x) = |C(x)|$ ensures **unique decoding** while minimizing the **expected message length**. Later, we’ll see how these lengths can be chosen based on symbol probabilities to achieve efficient encoding.

## 2. Common Integer Codes

There are several important integer coding schemes, each with its own strategy for representing integers as sequences of bits. Understanding these codes in terms of their **codeword lengths** helps us see how efficiently they encode small versus large integers. In the following sections, we will introduce some widely used codes and examine how their codeword lengths grow with the integer $n$.

### 2.1 Unary Code

The unary code is one of the simplest codes for non-negative integers. It is a variable-length, prefix-free code.

For a non-negative integer $n$, the unary code $u(n)$ is constructed as follows:

- Output $n$ ones followed by a single zero.

**Example codewords (0&ndash;10):**

| $n$ | $u(n)$      | $\ell(n)$ |
|-----|-------------|-----------|
| 0   | 0           | 1         |
| 1   | 10          | 2         |
| 2   | 110         | 3         |
| 3   | 1110        | 4         |
| 4   | 11110       | 5         |
| 5   | 111110      | 6         |
| 6   | 1111110     | 7         |
| 7   | 11111110    | 8         |
| 8   | 111111110   | 9         |
| 9   | 1111111110  | 10        |
| 10  | 11111111110 | 11        |

Notice that the length of the unary codeword grows **linearly** with $n$ ($\ell(n) = n + 1$). This makes it simple but inefficient for large integers, as codewords become very long.

### 2.2 Elias Gamma Code

The Elias gamma code is a **prefix-free code** for encoding positive integers.

For a positive integer $n$, the Elias gamma code $\gamma(n)$ is constructed as follows:

1. Let $b$ be the binary representation of $n$ without leading zeros.
2. Let $L$ be the number of bits in $b$.
3. Output $(L-1)$ zeros followed by $b$.

**Example codewords (1&ndash;10):**

| $n$ | $\gamma(n)$ | $\ell(n)$ |
|-----|-------------|-----------|
| 1   | 1           | 1         |
| 2   | 0 10        | 3         |
| 3   | 0 11        | 3         |
| 4   | 00 100      | 5         |
| 5   | 00 101      | 5         |
| 6   | 00 110      | 5         |
| 7   | 00 111      | 5         |
| 8   | 000 1000    | 7         |
| 9   | 000 1001    | 7         |
| 10  | 000 1010    | 7         |

*Note: In the above table, spaces within the codewords separate the unary prefix from the binary remainder for readability; they are **not** part of the actual codewords.*

The Elias gamma code is efficient for small integers, and its codeword length

$$\ell(n) = 2 \lfloor \log_2 n \rfloor + 1$$

grows **logarithmically** with $n$.

### 2.3 Elias Delta Code

The Elias delta code is a **prefix-free code** designed to efficiently encode positive integers, improving on Elias gamma for larger values.

For a positive integer $n$, the Elias delta code $\delta(n)$ is constructed as follows:

1. Let $b$ be the binary representation of $n$ without leading zeros.
2. Let $L$ be the number of bits in $b$.
3. Output $\gamma(L)$ followed by $b$ without its leading $1$.

**Example codewords (1&ndash;10):**

| $n$ | $\delta(n)$ | $\ell(n)$ |
|-----|-------------|-----------|
| 1   | 1           | 1         |
| 2   | 010 0       | 4         |
| 3   | 010 1       | 4         |
| 4   | 011 00      | 5         |
| 5   | 011 01      | 5         |
| 6   | 011 10      | 5         |
| 7   | 011 11      | 5         |
| 8   | 00100 000   | 8         |
| 9   | 00100 001   | 8         |
| 10  | 00100 010   | 8         |

*Note: In the above table, spaces within the codewords separate the gamma-coded length $\gamma(L)$ from the binary remainder $b$ for readability; they are **not** part of the actual codewords.*

The Elias delta code is more efficient than Elias gamma for larger integers, with codeword length  

$$\ell(n) = \lfloor \log_2 n \rfloor + 2 \lfloor \log_2 (\lfloor \log_2 n \rfloor + 1) \rfloor + 1,$$

growing **slightly faster than $\log_2 n$ but much slower than linear**.

### 2.4 Fibonacci Code

The Fibonacci code is a **prefix-free code** that encodes positive integers using sums of non-consecutive Fibonacci numbers, known as the **Zeckendorf representation**.

**Note:** In this section, we define the Fibonacci sequence as $F_{1} = 1, F_{2} = 1, F_{3} = 2, \dots$, so the Zeckendorf representation of any positive integer uses Fibonacci numbers $F_{k}$ with $k \ge 2$.

#### 2.4.1 Zeckendorf Representation

The **Zeckendorf representation** expresses any positive integer $n$ as a sum of **non-consecutive Fibonacci numbers**:

$$n = F_{k_1} + F_{k_2} + \dots + F_{k_m}, \quad k_{1} > k_{2} > \dots > k_{m} \ge 2,$$

where no two consecutive Fibonacci numbers are used. This representation is **unique** for every positive integer.

A simple way to find it is to use a **greedy algorithm**:

1. Find the **largest Fibonacci number** $F_k$ that is less than or equal to $n$.  
2. Subtract $F_k$ from $n$.  
3. Repeat with the remaining number until it becomes zero.  
4. The sum of the selected Fibonacci numbers gives the Zeckendorf representation.

**Example:**  
To represent $n = 100$:
- Largest Fibonacci $\leq$ 100 is $F_{11} = 89$, remainder $11$  
- Largest Fibonacci $\leq$ 11 is $F_6 = 8$, remainder $3$  
- Largest Fibonacci $\leq$ 3 is $F_4 = 3$, remainder $0$  
- So $100 = F_{11} + F_6 + F_4$

#### 2.4.2 Constructing Fibonacci Codewords

For a positive integer $n$, the Fibonacci code $F(n)$ is constructed as follows:

1. Find the [**Zeckendorf representation**](https://en.wikipedia.org/wiki/Zeckendorf%27s_theorem) of $n$.  
2. Write a binary string where each position corresponds to a Fibonacci number in the sequence, placing a $1$ if that Fibonacci number is used in the sum and $0$ otherwise.  
3. Append an extra $1$ at the end to mark the **end of the codeword**.

**Example codewords (1&ndash;10):**

| $n$ | Zeckendorf Representation | $F(n)$ | $\ell(n)$ |
|-----|---------------------------|--------|-----------|
| 1   | $F_{2}$                   | 11     | 2         |
| 2   | $F_{3}$                   | 011    | 3         |
| 3   | $F_{4}$                   | 0011   | 4         |
| 4   | $F_{2}+F_{4}$             | 1011   | 4         |
| 5   | $F_{5}$                   | 00011  | 5         |
| 6   | $F_{2}+F_{5}$             | 10011  | 5         |
| 7   | $F_{3}+F_{5}$             | 01011  | 5         |
| 8   | $F_{6}$                   | 000011 | 6         |
| 9   | $F_{2}+F_{6}$             | 100011 | 6         |
| 10  | $F_{3}+F_{6}$             | 010011 | 6         |

The Fibonacci code is efficient and its codeword length grows roughly as  

$$\ell(n) \sim \log_{\phi} (\sqrt{5}n) + 2,$$

where $\phi$ is the golden ratio. This growth is **faster than $\log_{2}$ but still much slower than linear**, making it a good universal code for integers.

## 3. Universal Codes

A **universal code for positive integers** is a **prefix-free code**

$$C: \mathbb{Z}^{+} \to \{0,1\}^{*}$$

whose associated length function $\ell(n) = |C(n)|$ grows **sublinearly** with $n$:
  $$\ell(n) = o(n) \quad \text{as } n \to \infty.$$

Universal codes are **distribution-free** in the sense that their construction does not rely on any assumed probability distribution, making them suitable for applications where no prior is known.

All of the integer codes discussed previously, except for the unary code, are universal codes.

> **Note:** "Universal" in this context specifically refers to codes for **positive integers**, and not to general sequences or source distributions. In information theory, the term *universal coding* can also refer to codes that efficiently encode sequences from unknown distributions, which is a broader concept.

## 4. Other Notable Integer Codes

Several additional integer coding schemes are widely used in practice, often tailored for specific applications.

**[Elias Omega Coding](https://en.wikipedia.org/wiki/Elias_omega_coding)**: A **universal, prefix-free** recursive code that improves on Elias gamma and delta codes for larger integers.

**[Exp-Golomb Coding](https://en.wikipedia.org/wiki/Exponential-Golomb_coding)**: A family of **universal, prefix-free** codes used in video compression standards (e.g., H.264/AVC). It generalizes Elias gamma and delta codes and is efficient for encoding both small and large integers.

**[Levenshtein Coding](https://en.wikipedia.org/wiki/Levenshtein_coding)**: The **first universal code** for integers. Provides compact, **prefix-free** representations for all positive integers with simple encoding and decoding algorithms.

**[Golomb Coding](https://en.wikipedia.org/wiki/Golomb_coding)**: A generalization of Rice coding for geometrically distributed integers with arbitrary parameters. Golomb codes are **prefix-free** but **not universal**, and reduce to Rice codes for certain parameter choices.

**[Rice Coding](https://en.wikipedia.org/wiki/Golomb_coding#Rice_coding)**: A special case of Golomb coding optimized for geometrically distributed integers. **Prefix-free** but **not universal**, commonly used in lossless audio compression (e.g., FLAC) and efficient for data skewed toward smaller integers.

