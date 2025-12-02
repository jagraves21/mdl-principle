# Uniquely Decodable Codes

Before diving into formal definitions, let's look at two example codes:

## 1. Motivation

### Example 1: A Uniquely Decodable Code

Let $`X = \{a, b, c\}`$ be a source alphabet, and consider the binary code $`C_{1}`$:

- $`C_{1}(a) = 0`$
- $`C_{1}(b) = 01`$
- $`C_{1}(c) = 11`$

This code is _uniquely decodable_. That is, any concatenation of codewords from $`C_{1}`$ can be decoded in exactly one way.
_(This can be shown using the Sardinas&ndash;Patterson Algorithm, which is introduced later.)_

### Example 2: A Non-Uniquely Decodable Code

Now consider the code $`C_{2}`$:

- $`C_{2}(a) = 0`$
- $`C_{2}(b) = 1`$
- $`C_{2}(c) = 01`$

This code is _not uniquely decodable_. For example, the sequence $`01`$ could correspond to either:

- $`C_{2}(a)C_{2}(b) = 01`$
- $`C_{2}(c) = 01$`

This ambiguity motivates the need for uniquely decodable codes, thus ensuring messages can be decoded unambiguously.

### Example 3: Morse Code

In the Morse code alphabet:

- $`A \mapsto \cdot\ -`$
- $`E \mapsto \cdot`$
- $`F \mapsto \cdot\ \cdot\ -\ \cdot`$

Morse code is _not uniquely decodable_ since the sequence

```math
\cdot\ \cdot\ -\ \cdot
```

could be decoded as either

```math
E\,A\,E \quad \text{or} \quad F.
```

To resolve this ambiguity, Morse code uses pauses of varying lengths between dots and dashes (symbols within a letter), between letters, and between words. Including these pauses effectively introduces more than two "symbols" in practice. While the basic signals are binary (dot and dash), the timing of pauses acts like additional symbols, making Morse code behave more like a 5-ary code in terms of transmission.

With these examples in mind, we can now formally define uniquely decodable codes.

## 2. Definition of Uniquely Decodable Codes

Formally, a code $`C : X \to Y^{*}`$ is **uniquely decodable** if any finite concatenation of its codewords can be decoded in exactly one way.

In other words, no two different sequences of source symbols produce the same concatenated codeword sequence. This ensures that any finite concatenation of codewords can be decoded in exactly one way.

## 3. Why Uniquely Decodable Codes Matter

Uniquely decodable codes are important for several reasons:

1. **Correctness:** Decoding is reliable; each message has only one interpretation. This prevents ambiguity when reconstructing the original message.

2. **Compression:** All practical variable-length codes, such as Huffman codes, are uniquely decodable. Without unique decodability, compressed data could become ambiguous and meaningless.

3. **Minimum Description Length (MDL) Connection:** In information theory, the length of a codeword reflects the information content of a symbol. Ambiguity breaks this link; if a sequence can be decoded in multiple ways, we cannot reliably measure or compare information content.

4. **Practical Reliability:** In data transmission or storage, uniquely decodable codes ensure that messages can always be restored correctly, even in complex or variable-length coding scenarios.

## 4. Special Types of Uniquely Decodable Codes

Among uniquely decodable codes, two important subclasses are **prefix-free codes** and **instantaneously decodable codes**. Understanding these helps in practical encoding and decoding.

- **Instantaneously decodable codes** allow each codeword to be decoded immediately as it is read, without waiting for additional symbols. This makes real-time decoding possible.

- **Prefix-free codes** are codes in which no codeword is a prefix of any other codeword. Because of this property, prefix-free codes are always uniquely decodable and are therefore widely used in practice.

In fact, these two concepts are essentially equivalent as every prefix-free code is instantaneously decodable and vice versa. This makes prefix-free codes particularly convenient for practical coding schemes like Huffman coding.

By focusing on prefix-free codes, we gain both theoretical guarantees of unique decodability and practical simplicity in decoding. We will explore them in detail in the next section.

