# What is a Code?

In coding theory, a **code** is a systematic way to represent information from one set of symbols using another set of symbols. Codes allow us to efficiently store, transmit, and manipulate data.

## 1. Basic Definition

A **code** is a mapping $`C : X \to Y^{*}`$ where

- $`X`$ is the **source alphabet** (i.e., the set of symbols you want to represent).
- $`Y`$ is the **code alphabet** (i.e., the symbols used to form codewords).
- $`Y^{*}`$ is the set of **all finite-length strings** over $`Y`$.
- $`C(x)`$ is the **codeword** corresponding to source symbol $`x \in X`$.

Informally, a code assigns a string of symbols (from $`Y`$) to every symbol in $`X`$. If $`Y=\{0,1\}`$, then $`C`$ is said to be a binary code.

## 1.1 Encoding Messages via Concatenation

A **messsage** is a finite sequence of source symbols $`x_{1} x_{2} \cdots x_{n} \in X^{*}`$. Once we have a code $`C: X \to Y^{*}`$, we can encode a message by **juxtaposing (concatenating) the codewords** of each source symbol in order.

For example, let

- $`C(a) = 0`$
- $`C(b) = 01`$
- $`C(c) = 1`$

Then the message $`ab`$ is encoded as:

```math
C(a)C(b) = 0\_01 \rightarrow 001.
```

Similarly, $`bc`$ is encoded as:

```math
C(b)C(c) = 01\_1 \rightarrow 011.
```

_Note: The underscores in the above examples are included **only for readability** to separate codewords; they are not part of the actual code or encoded message._

## 2. Fixed-Length vs. Variable-Length Codes

Suppose we wish to transmit a long message in which some source symbols occur much more frequently than others. The **average message length** of a code is the expected number of code symbols per source symbol and is given by

```math
\bar{L} \;=\; \sum_{x \in X} p(x)\,L(x),
```

where $`p(x)`$ is the probability of the source symbol $`x`$, and $`L(x)`$ is the number of code symbols in the codeword assigned to $`x`$.

A **fixed-length code** assigned the same number of code symbols to every source symbol. And so, if $`L(x)=m`$ for every source symbol $`x`$, then $`\bar{L}=m`$. Fixed-length codes are simple but cannot exploit variation in symbol probabilities.

A **variable-length code** assigns codewords of different lengths to different source symbols. By assigning shorter codewords to more probable symbols (and longer codewords to less probable ones), we can reduce $`\bar{L}`$ below what any fixed-length code achieves for the same source distribution.

By assigning shorter codewords to high-probability symbols, we _minimize the expected message length_. This principle is the foundation of Huffman coding and underpins the Minimum Description Length (MDL) principle, where shorter codes correspond to simpler or more likely explanations of data.

## 3. Examples

### 3.1 ASCII (Fixed-Length)

- Source alphabet: letters A&ndash;Z, digits 0&ndash;9, punctuation.
- Code alphabet: $`\{0, 1\}`$ (bits).
- Codewords: 7 or 8 bits each.
- Example:
  - $`A \mapsto 01000001`$
  - $`B \mapsto 01000010`$

### 3.2 Morse Code (Variable-Length)

- Source alphabet: letters and digits.
- Code alphabet: $`\{\cdot, -\}`$ (dots and dashes).
- Example:
  - $`A \mapsto \cdot\ -`$
  - $`B \mapsto - \cdot\ \cdot\ \cdot`$
  - $`E \mapsto \cdot`$

## 4. Why Codes Matter

Codes are not just for compression. They also provide a measure of information:

- In MDL, the length of a codeword corresponds to the _cost or complexity_ of representing a symbol or dataset.
- Shorter codes correspond to more probable or simpler events; longer codes correspond to less probable or more complex events.

Codes are also essential in communication and storage systems. For example, digital data sent over the internet or stored on a hard drive is often encoded with codes that allow error detection and correction. This ensures that messages can be transmitted reliably even in the presence of noise or data corruption.

## 5. Next Steps

Next, we explore essential concepts and techniques for constructing and analyzing codes, covering decodability, efficient encoding, codeword structure, and tools for visualizing and verifying code properties.

- **Uniquely decodable codes**  &mdash; ensuring a code can be interpreted correctly.
- **Instantaneous and prefix-free codes**  &mdash; enabling immediate decoding.
- **Codeword lengths and universal integer codes**  &mdash; measuring codeword length and efficient encoding of integers without prior knowledge of probabilities.
- **Universal prefix and code space trees** &mdash; visualizing which parts of the code space are used and how codewords are organized.
- **Kraft&ndash;McMillan inequalities**  &mdash; determining feasible codeword lengths and their connection to probabilities.
- **Sardinas&ndash;Patterson Algorithm**  &mdash; a method to test whether a code is uniquely decodable.

These topics provide a complete theoretical and practical foundation for constructing and analyzing codes, bridging basic definitions to applications in compression and the MDL principle.

