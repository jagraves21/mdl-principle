# What is a Code?

In coding theory, a **code** is a systematic way to represent information from one set of symbols using another set of symbols. Codes allow us to efficiently store, transmit, and manipulate data.

## 1. Basic Definition

A **code** is a mapping $C : X \to Y^{*}$ where

- $X$ is the **source alphabet** (i.e., the set of symbols you want to represent).
- $Y$ is the **code alphabet** (i.e., the symbols used to form codewords).
- $Y^{*}$ is the set of all finite-length strings over $Y$.
- $C(x)$ is the **codeword** corresponding to source symbol $x \in X$.

Informally, a code assigns a string of symbols (from $Y$) to every symbol in $X$. If $Y=\{0,1\}$, then $C$ is said to be a binary code.

## 2. Fixed-Length vs. Variable-Length Codes

Suppose we want to transmit a large piece of text where some symbols appear much more often than others. Using a fixed-length binary code means each symbol takes the same number of bits, even if some symbols are very common. Variable-length codes take advantage of **symbol probabilities**:

$$\text{Average message length} = \sum_{x \in \mathcal{X}} p(x) \cdot L(x)$$

- $p(x)$ = probability of symbol $x$
- $L(x)$ = length of codeword for $x$

By assigning shorter codewords to high-probability symbols, we **minimize the expected message length**. This principle is the foundation of **Huffman coding** and underpins the **Minimum Description Length (MDL) principle**, where shorter codes correspond to simpler or more likely explanations of data.

## 3. Examples

### 3.1 ASCII (Fixed-Length)

- Source alphabet: letters A–Z, digits 0–9, punctuation.
- Code alphabet: `{0, 1}` (bits).
- Codewords: 7 or 8 bits each.
- Example:
  - $A \mapsto 01000001$
  - $B \mapsto 01000010$

### 3.2 Morse Code (Variable-Length)

- Source alphabet: letters and digits.
- Code alphabet: $\{\cdot, -\}$ (dots and dashes).

- Example:
  - $A \mapsto \cdot\,-$  
  - $B \mapsto - \cdot\,\cdot\,\cdot$  
  - $E \mapsto \cdot$

## 4. Why Codes Matter

Codes are not just for compression. They also provide a **measure of information**:

- In MDL, the length of a codeword corresponds to the **cost or complexity** of representing a symbol or dataset.
- Shorter codes correspond to more probable or simpler events; longer codes correspond to less probable or more complex events.

This connection between **coding and information** is the foundation for MDL and other information-theoretic methods.

## 5. Next Steps

After understanding what a code is, the next topics to explore are:

- **Unique decodable codes** – ensuring a code can be interpreted correctly.
- **Instantaneous and prefix codes** – enabling immediate decoding.

- **Example codes** – unary, Elias, Fibonacci, and Huffman codes.

These codes help to bridge basic definitions to practical coding methods used in MDL.
