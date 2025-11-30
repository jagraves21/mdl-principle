# Prefix-Free Codes (Instantaneous Codes)

Previously, we noted that we want messages to be decoded without ambiguity; therefore, we use **uniquely decodable codes**. Another desirable property is that decoding should be efficient.

One particularly useful property is **instantaneous decoding**, which means that each symbol can be decoded as soon as its codeword is read, without the need to look ahead at subsequent symbols.

## 1. Instantaneous Decoding

A code is **instantaneously decodable** if the decoder can determine the end of each codeword immediately upon reading it.

In other words, there is never any need to read additional code symbols to decide how to decode the current source symbol.

## 2. A Code That Is *Not* Instantaneously Decodable

Consider the code $C_{1}$ given by:

- $a \mapsto 0$
- $b \mapsto 01$

Suppose the decoder begins reading the message

$$0001.$$

After reading the first symbol $0$, there is ambiguity:

- The codeword could correspond to $a$, since $0$ is a valid codeword.
- Alternatively, it might be the beginning of $b$, since $01$ is also a valid codeword.

The decoder must therefore read the next symbol before it can decide how to decode the current source symbol.

Hence, this code:

- Is **not instantaneous**, because decoding requires look-ahead.
- Is still **uniquely decodable**, since no complete message can be parsed in more than one way (this fact will be established later).

## 3. An Instantaneously Decodable Code

Now consider the code $C_{2}$ defined by:

- $a \mapsto 0$
- $b \mapsto 10$
- $c \mapsto 110$
- $d \mapsto 1110$

In this case, $C_2$ is instantaneously decodable. To see this, observe that decoding a message requires only counting the number of 1's that appear before a 0.

- If zero 1's are found before the 0, the codeword corresponds to $a$.
- If one 1 is found before the 0, the codeword corresponds to $b$.
- If two 1's are found before the 0, the codeword corresponds to $c$.
- If three 1's are found before the 0, the codeword corresponds to $d$.

Once the 0 is read, the decoder immediately knows which symbol has been encoded. No lookahead is needed at any step. Therefore, this code is instantaneously decodable.

## 4. Prefix-Free Codes

A code is said to be **prefix-free** (or simply a **prefix code**) if no codeword is a prefix of any other codeword.

Let’s see how this definition applies to our earlier examples.

### Non-Instantaneous Code:

Recall the code $C_{1}$ from before where

- $a \mapsto 0$
- $b \mapsto 01$

Here $0$ is a prefix of $01$, and so this code is not prefix-free.

### Prefix-Free Code:

Now recall the code $C_{2}$ from above with

- $a \mapsto 0$
- $b \mapsto 10$
- $c \mapsto 110$
- $d \mapsto 1110$

Since no codeword appears at the beginning of any other codeword, this code is prefix-free.

## 5. Equivalence of Prefix-Free and Instantaneous Codes

The structural property of being prefix-free and the operational property of being instantaneously decodable are exactly the same, as stated in the following theorem:

**Theorem:** A code is instantaneously decodable *if and only if* it is prefix-free.

**Proof (Sketch):**

$(\Rightarrow)$ If a code is prefix-free, then no codeword is the beginning of another. Therefore, when reading a message from left to right, the decoder can determine the end of each codeword immediately upon reading it's last symbol. And so, no lookahead is needed, hence the code is instantaneously decodable.

$(\Leftarrow)$ If a code is instantaneously decodable, the decoder can always determine the end of a codeword without reading further symbols. If any codeword were a prefix of another, the decoder would have to read additional symbols to decide where the codeword ends, contradicting instantaneous decodability. Hence, the code must be prefix-free.

Thus the two notions describe *the same class of codes*, just from different perspectives:

- **Prefix-free** &mdash; a structural description of the code.
- **Instantaneous decoding** &mdash; an operational description of the decoding process.

## 6. Connection to MDL

Prefix-free codes are fundamental to the **Minimum Description Length (MDL)** principle:

-   They ensure that concatenated codewords can be decoded without ambiguity or delay.
-   They make total description length additive:

    $$L(\text{message})=\sum_{i} L(\text{codeword}_{i}).$$
-   Standard MDL coding schemes (such as **unary**, **Elias gamma**, and **Elias delta** codes) are all prefix-free codes.

## 7. Why Codeword Lengths Matter

So far, we’ve seen that **prefix-free codes** let us decode messages instantly and without ambiguity. But another natural question arises:

**How long should each codeword be?**

- If some codewords are very long, transmitting messages becomes inefficient.
- If all codewords are short, we might run into conflicts that prevent the code from being prefix-free.

This balance between **efficiency** and **decodability** is crucial. To formalize it, we need a way to describe **which sets of codeword lengths are even possible** for a prefix-free code.

The **Kraft-McMillan inequality** provides exactly that: a simple condition that any collection of codeword lengths must satisfy to correspond to a prefix-free code.

