# Huffman Coding and Prefix Trees

Suppose we have a set of symbols $X = \{x_{1}, x_{2}, \dots, x_{n}\}$ with known probabilities $p(x_{i})$. We want to assign prefix-free codewords $C(x_{i})$ the code source symbols $x_{i}$ that minimizes the expected codeword length

$$\bar{L} = \sum_{x \in X} p(x)\,L(x).$$

Several algorithms have been developed to solve this problem. In the following sections, we present three well-known approaches: a general greedy algorithm, Shannon–Fano coding, and the optimal Huffman coding algorithm. While all three of these methods build codewords according to symbol probabilities, they differ in efficiency, simplicity, and optimality.

## 2. Tree-Based Greedy Construction of Prefix-Free Codes

A constructive, greedy approach for building prefix-free codes can be derived from Kraft's theorem. In this method, we first determine the desired codeword lengths (typically based on symbol probabilities). Once we have a given a set of desired codeword lengths that satisfy Kraft's inequality, we can assign codewords iteratively in a prefix tree, always choosing the leftmost available node at the appropriate depth. By marking the assigned node and its entire subtree as used, this method ensures that no codeword is a prefix of another, producing a valid prefix-free code with the specified lengths.

### Algorithm

1. Sort the codeword lengths in non-decreasing order.  
2. Initialize the full universal prefix tree.  
3. For each codeword length $l_i$:
    1. Find the leftmost available node at depth $l_i$ whose ancestors have not been assigned as codewords.  
    2. Assign the codeword corresponding to the path from the root to this node.  
    3. Mark the entire subtree rooted at this node as used, so no future codeword can occupy it (ensuring the prefix-free property).  
4. Return the set of assigned codewords.

In practice, determining optimal lengths from probabilities is a crucial step. However, thanks to Shannon's source coding theorem, we know that the approximately optimal codeword length for a symbol with probability $p_{i}$ is

$$L(x_{i}) = \lceil−log_{D}\,p(x_{i})\rceil,$$

where $D$ is the number of code symbols.

Using Shannon's formula lets us move directly from probabilities to lengths, making the tree construction step a straightforward way to generate valid prefix-free codewords.

### Example

Suppose we want a binary, prefix-free code for the source symbols $X = \{a, b, c, d\}$ with probabilities

* $p(a) = 0.5$
* $p(b) = 0.25$
* $p(c) = 0.125$$
* $p(d) = 0.125$$

Using Shannon’s approximation for binary codes ($D=2$):

$$L(x_{i}) \approx \lceil−log_{2}\,p(x_{i})\rceil,$$

we get the codeword lengths:

* $L(a) \approx \lceil-\log_{2} 0.5\rceil = 1$
* $L(b) \approx \lceil-\log_{2} 0.25\rceil = 2$
* $L(c) \approx \lceil-\log_{2} 0.25\rceil = 3$
* $L(c) \approx \lceil-\log_{2} 0.25\rceil = 3$

These lengths satisfy Kraft’s inequality, so a prefix-free code  must exist with codewords having the given lengths.

The following illustration shows how the codewords are selected from the universal prefix tree:

![Universal Prefix Tree](../../assets/images/greedy-code-construction.gif)
