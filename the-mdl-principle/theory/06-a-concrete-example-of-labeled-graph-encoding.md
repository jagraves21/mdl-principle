# 6. A Concrete Example of Labeled Graph Encoding

To make the discussion in the previous chapter more concrete, we consider a small labeled graph and walk through its encoding and decoding, as well as the computation of its description length. This demonstrates how the three-stage encoding (graph dimensions, structure, and labels) operates in practice, and shows why calculating the description length is easier than performing full encoding and decoding.

For the remainder of this chapter, let $`G = (V, E, \ell_{V}, \ell_{E})`$ be a labeled graph with

```math
V = \{a, b, c, d\}, \quad
E = \{\{a,b\}, \{b,c\}, \{b,d\}, \{c,d\}\},
```

where the vertex labeling $`\ell_{V}`$ is

```math
a \mapsto \alpha, \quad
b \mapsto \beta, \quad
c \mapsto \beta, \quad
d \mapsto \beta,
```

and the edge labeling $`\ell_{E}`$ is

```math
\{a,b\} \mapsto \phi, \quad
\{b,c\} \mapsto \pi, \quad
\{b,d\} \mapsto \pi, \quad
\{c,d\} \mapsto \tau.
```

## 6.1. Encoding the Labeled Graph

We illustrate the three-stage encoding procedure in detail in the following sections. For clarity and readability, we insert spaces between the different components of the codeword to visually separate them. These spaces are not part of the actual codeword. In reality, the encoder outputs a single concatenated bitstring, and the decoder parses it without access to any visual separators

### 6.1.1. Encoding The Graph Dimensions

In most real-world datasets, the number of vertices is neither extremely small nor astronomically large. Graphs typically contain hundreds, thousands, or millions of vertices. This range is too large for Elias gamma coding to be efficient, but too small to benefit from Elias omega coding. Elias delta coding provides a balanced choice for encoding $`|V|`$ and $`|E|`$.

Since $`|E|`$ may be zero and Elias codes require strictly positive integers, we encode $`|E| + 1`$ to ensure positivity.

For our example, $`|V| = 4`$ and $`|E| = 4`$. The Elias delta codes are:

- $`|V| = 4 \mapsto 01100`$
- $`|E| + 1 = 5 \mapsto 01101`$

Thus, the graph dimensions are encoded as:

```math
01100 \;\; 01101
```

### 6.1.2. Encoding The Graph Structure

The graph has $`|V| = 4`$ vertices, giving $`4! = 24`$ potential adjacency matrices. However, with 2 automorphisms, the number of distinct adjacency matrices is

```math
\frac{4!}{2} = 12.
```

The adjacency matrix with the smallest rank under lexicographic ordering of the row-wise concatenated entries is:

```math
\begin{pmatrix}
0 & 0 & 0 & 1 \\
0 & 0 & 1 & 1 \\
0 & 1 & 0 & 1 \\
1 & 1 & 1 & 0
\end{pmatrix}.
```

This corresponds to two distinct vertex orderings:

```math
(a, c, d, b) \quad \text{and} \quad (a, d, c, b).
```

Its upper triangular part is

```math
\begin{pmatrix}
~ & 0 & 0 & 1 \\
~ & ~ & 1 & 1 \\
~ & ~ & ~ & 1 \\
~ & ~ & ~ & ~
\end{pmatrix},
```

with a row-wise concatenation of $`001111`$. There are $`\binom{6}{4} = 15`$ binary strings of length 6 with four 1's; coincidentally, this string is the first lexicographically, so its rank is 0.

To represent all 15 possibilities, a fixed-length code of length $`\lceil \log_{2} 15 \rceil = 4`$ is required. And so, the graph structure is encoded as:

```math
0000
```

### 6.1.3. Encoding The Graph Labels

As previously mentioned, there are two vertex orderings that yield the canonical adjacency matrix:

```math
(a, c, d, b) \quad \text{and} \quad (a, d, c, b).
```

We select the ordering that gives the smaller lexicographic label sequence.

Using the upper triangle of the adjacency matrix and the vertex ordering $`(a, c, d, b)`$, we obtain an edge ordering of

```math
(\{a,b\}, \{c,d\}, \{b,c\}, \{b,d\}),
```

with vertex and edge labels:

```math
(\alpha, \beta, \beta, \beta) \quad \text{and} \quad (\phi, \pi, \pi, \tau).
```

Vertex ordering $`(a, d, c, b)`$ corresponds to an edge ordering of

```math
(\{a,b\}, \{c,d\}, \{b,d\}, \{b,c\}),
```

with vertex and edge labels:

```math
(\alpha, \beta, \beta, \beta) \quad \text{and} \quad (\phi, \pi, \tau, \pi).
```

Given the ordering $`\alpha < \beta < \phi < \pi < \tau`$, we choose $`(a, c, d, b)`$, as it produces the smaller lexicographic sequence of labels.

To encode the individual vertex labels, we define a fixed-length binary code of length $`\lceil \log_{2} 2 \rceil = 1`$:

```math
\alpha \mapsto 0, \qquad
\beta \mapsto 1.
```

To encode the individual edge labels, we define a prefix-free binary code:

```math
\phi \mapsto 10, \qquad
\pi \mapsto 0, \qquad
\tau \mapsto 11.
```

And so, encoding the vertex label sequence gives

```math
(\alpha, \beta, \beta, \beta)
\;\mapsto\;
0\,1\,1\,1,
```

while encoding the edge label sequence gives

```math
(\phi, \pi, \pi, \tau)
\;\longmapsto\;
10\;0\;0\;11.
```

After concatenating the vertex and edge label encodings, the full label encoding is:

```math
0 \; 1 \; 1 \; 1 \; 10 \; 0 \; 0 \; 11.
```

### 6.1.4. The Final Codeword

Putting each of these pieces together gives us a final codeword of

```math
01100 \;\; 01101 \; 0000 \; 0 \; 1 \; 1 \; 1 \; 10 \; 0 \; 0 \; 11.
```

## 6.2. Decoding the Labeled Graph

We now demonstrate how a decoder reconstructs the original labeled graph from the codeword

```math
011000110100000111100011.
```

### 6.2.1. Decoding The Graph Dimensions

The decoder begins by decoding the number of vertices $`|V|`$ and the number of edges as $`|E| + 1`$ from the first part of the bitstring using Elias delta decoding. Since the Elias delta code is a prefix-free code, both values can be determined unambiguously, without any need to look ahead.

The prefix

```math
01100
```

decodes to $`|V| = 4`$.

After removing these bits, the next prefix

```math
01101
```

decodes to $`|E| + 1 = 5`$, giving $`|E| = 4`$.

### 6.2.2. Decoding The Graph Structure

After decoding the dimensions, the remaining part of the codeword is

```math
00000111100011,
```

which encodes the graph structure as well as the vertex and edge labels.

At this stage, the decoder knows that the graph has 4 vertices and 4 edges. It also knows that the upper-triangular portion of the adjacency matrix contains

```math
\binom{4}{2} = 6
```

entries. The next

```math
\left \lceil \log_{2} \binom{6}{4} \right \rceil = 4
```

bits of the codeword form a fixed-length encoding of the rank of the upper-triangular binary string among all length-6 strings with exactly 4 non-zero entries. These 4 bits are

```math
0000,
```

which corresponds to rank 0. Among all

```math
\binom{6}{4} = 15
```

such strings, the lexicographically smallest (i.e., the one with rank 0) is

```math
001111.
```

The decoder places these entries row-wise into the upper-triangular portion of the adjacency matrix:

```math
\begin{pmatrix}
~ & 0 & 0 & 1 \\
~ & ~ & 1 & 1 \\
~ & ~ & ~ & 1 \\
~ & ~ & ~ & ~
\end{pmatrix}.
```

To obtain the full adjacency matrix, we place zeros on the diagonal (since the graph has no self-loops) and reflect the upper-triangular entries to the lower triangle. This gives

```math
\begin{pmatrix}
0 & 0 & 0 & 1 \\
0 & 0 & 1 & 1 \\
0 & 1 & 0 & 1 \\
1 & 1 & 1 & 0
\end{pmatrix}.
```

At this point, the decoder has recovered

```math
V^{\prime} = \{1, 2, 3, 4\} \text{ and } E^{\prime} = \{\{1,4\}, \{2,3\}, \{2,4\}, \{3,4\}\}.
```

### 6.2.3. Decoding The Graph Labels

The remaining part of the code word is

```math
0111100011,
```

which encodes the vertex and edge labels. The order in which the labels are decoded is determined by the canonical adjacency matrix. The vertices are indexed as

```math
(1, 2, 3, 4),
```

and the edges are listed in the order

```math
(\{1,4\}, \{2,3\}, \{2,4\}, \{3,4\}),
```

corresponding to their positions in the row-wise concatenation of the upper triangle of the adjacency matrix. This ensures that each vertex and edge label is assigned to the correct element of the graph.

Since vertex labels were encoded using a fixed-length code of length 1 defined by

```math
0 \mapsto \alpha, \qquad 1 \mapsto \beta,
```

the next 4 bits

```math
0111
```

decode to

```math
(\alpha, \beta, \beta, \beta),
```

and are assigned to vertices

```math
(1, 2, 3, 4),
```

respectively.

The edges were encoded using a prefix-free code defined by

```math
10 \mapsto \phi,
\qquad 0 \mapsto \pi,
\qquad 11 \mapsto \tau.
```

And so, the remaining 6 bits

```math
100011
```

decode as

```math
(\phi, \pi, \pi, \tau),
```

corresponding to edges

```math
(\{1,4\}, \{2,3\}, \{2,4\}, \{3,4\}).
```

Thus, the labelings are fully reconstructed as

```math
\ell_{V^{\prime}}: 1 \mapsto \alpha, \; 2 \mapsto \beta, \; 3 \mapsto \beta, \; 4 \mapsto \beta,
```

and

```math
\ell_{E^{\prime}}: \{1,4\} \mapsto \phi, \; \{2,3\} \mapsto \pi, \; \{2,4\} \mapsto \pi, \; \{3,4\} \mapsto \tau.
```

### 6.2.4. The Reconstructed Labeled Graph

After decoding the structure and labels, the decoder has reconstructed a graph with vertex set

```math
V^{\prime} = \{1, 2, 3, 4\}
```

and edge set

```math
E^{\prime} = \{\{1,4\}, \{2,3\}, \{2,4\}, \{3,4\}\}.
```

The corresponding vertex labeling function is

```math
\ell_{V^{\prime}}: 1 \mapsto \alpha, \; 2 \mapsto \beta, \; 3 \mapsto \beta, \; 4 \mapsto \beta,
```

and the edge labeling function is

```math
\ell_{E^{\prime}}: \{1,4\} \mapsto \phi, \; \{2,3\} \mapsto \pi, \; \{2,4\} \mapsto \pi, \; \{3,4\} \mapsto \tau.
```

Note that the original graph had vertex set

```math
V = \{a, b, c, d\}
```

and edge set

```math
E = \{\{a,b\}, \{b,c\}, \{b,d\}, \{c,d\}\}.
```

Strictly speaking, the reconstructed graph $`G^{\prime} = (V^{\prime}, E^{\prime}, \ell_{V^{\prime}}, \ell_{E^{\prime}})`$ is _not equal_ to the original graph $`G = (V, E, \ell_{V}, \ell_{E})`$, because the vertex sets are different. Instead, $`G^{\prime}`$ is _isomorphic_ to $`G`$.

In graph theory, the specific labels of the vertices are often arbitrary, and what matters is the adjacency structure and the mapping of labels to vertices and edges. From this perspective, $`G^{\prime}`$ perfectly preserves all the information from $`G`$; the adjacency relationships and the vertex and edge labels are identical up to a relabeling of the vertices. Therefore, the decoding procedure has successfully reconstructed the graph without any loss of information.

## 6.3. Calculating The Description Length

We will now demonstrate how to calculate the description length of $`G`$ without needing to realize any part of the codeword. Recall that the total description length of $`G`$ is given by

```math
L(G) = L(|V|) + L(|E|) + \left\lceil \log_{2} \binom{\frac{|V|(|V|-1)}{2}}{|E|} \right\rceil + \sum_{v \in V} L(\ell_{V}(v)) + \sum_{e \in E} L(\ell_{E}(e)).
```

Here,

- $`L(|V|)`$ and $`L(|E|)`$ are the description lengths of the number of vertices and edges under the associated integer codes used to encode them,

- $`\left\lceil \log_{2} \binom{\frac{|V|(|V|-1)}{2}}{|E|} \right\rceil`$ is the number of bits needed to encode the canonical adjacency matrix, and

- $`\sum_{v \in V} L(\ell_{V}(v))`$ and $`\sum_{e \in E} L(\ell_{E}(e))`$ are the bits required to encode all vertex and edge labels, respectively.

### 6.3.1. The Graph Dimensions

The description length of the graph dimensions is simply the sum of the lengths of the Elias delta codes for $`|V|`$ and $`|E|+1`$. We can compute their lengths directly using the standard formula for Elias delta coding. For a positive integer $`n`$, the length of its Elias delta code is

```math
L_{\delta}(n) = \lfloor \log_{2} n \rfloor + 2 \lfloor \log_{2} (\lfloor \log_{2} n \rfloor + 1) \rfloor + 1.
```

Applying this formula, we obtain

```math
\begin{aligned}
L(|V|) &= L_{\delta}(4) \\
&= \lfloor \log_{2} 4 \rfloor + 2 \lfloor \log_{2} (\lfloor \log_{2} 4 \rfloor + 1) \rfloor + 1 \\
&= 2 + 2 \cdot \lfloor \log_{2} 3 \rfloor + 1 \\
&= 2 + 2 \cdot 1 + 1 \\
&= 5.
\end{aligned}
```

and

```math
\begin{aligned}
L(|E|)
&= L_{\delta}(4 + 1) \\
&= \lfloor \log_{2} 5 \rfloor + 2 \lfloor \log_{2} (\lfloor \log_{2} 5 \rfloor + 1) \rfloor + 1 \\
&= 2 + 2 \cdot \lfloor \log_{2} 3 \rfloor + 1 \\
&= 2 + 2 \cdot 1 + 1 \\
&= 5.
\end{aligned}
```

Thus, the total description length of the graph dimensions is 10 bits.

### 6.3.2. The Graph Structure

Next, we compute the description length of the graph structure. Recall that the upper triangle of any adjacency matrix (excluding the main diagonal) has

```math
\frac{|V|(|V|-1)}{2}
```

entries, of which $`|E|`$ are 1. The number of possible ways to choose which entries are 1's is given by

```math
\binom{\frac{|V|(|V|-1)}{2}}{|E|}.
```

For our example, with $`|V| = 4`$ and $`|E| = 4`$, this gives

```math
\binom{\frac{|V|(|V|-1)}{2}}{|E|} = \binom{6}{4} = 15.
```

If we let $`L_{\text{fixed}}(N)`$ denote the codeword length of a fixed-length code over $`N`$ elements, a fixed-length code over 15 elements requires

```math
L_{\text{fixed}} \left( \binom{\frac{|V|(|V|-1)}{2}}{|E|} \right)
= \left\lceil \log_{2} \binom{\frac{|V|(|V|-1)}{2}}{|E|} \right\rceil
= \lceil \log_{2} 15 \rceil
= 4
```

bits. Thus, the total description length of the graph structure is 4 bits.

### 6.3.3. The Graph Labels

The vertex and edge labels are encoded separately. We used a fixed-length code of length 1 for the vertex labels. Thus,

```math
\sum_{v \in V} L(\ell_{V}(v)) = \sum_{v \in V} 1 = |V| = 4.
```

The edge labels were encoded using a prefix-free code defined by

```math
\phi \mapsto 10,
\quad \pi \mapsto 0,
\quad \tau \mapsto 11.
```

The edges of the graph, in no particular order, are

```math
(\{a,b\}, \{b,c\}, \{b,d\}, \{c,d\}),
```

with corresponding labels

```math
(\phi, \pi, \pi, \tau).
```

The lengths of the encoded edge labels are

```math
L(\phi) = 2,
\quad L(\pi) = 1,
\quad L(\pi) = 1,
\quad L(\tau) = 2.
```

Summing these lengths, the total description length of the edge labels is

```math
\sum_{e \in E} L(\ell_{E}(e)) = 2 + 1 + 1 + 2 = 6.
```

Therefore, the total description length of all labels is

```math
\sum_{v \in V} L(\ell_{V}(v)) + \sum_{e \in E} L(\ell_{E}(e)) = 4 + 6 = 10 \text{ bits}.
```

### 6.3.4. Total Description Length

Finally, the total description length of the graph is the sum of the three components:

```math
\begin{aligned}
L(G) &= L(|V|) + L(|E| + 1) + \left\lceil \log_{2} \binom{\frac{|V|(|V|-1)}{2}}{|E|} \right\rceil + \sum_{v \in V} L(\ell_{V}(v)) + \sum_{e \in E} L(\ell_{E}(e)) \\
&= 5 + 5 + 4 + 4 + 6 \\
&= 24 \text{ bits}.
\end{aligned}
```

This 24-bit total matches the length of the codeword

```math
011000110100000111100011
```

computed above. Moreover, determining this length required only simple calculations based on the graph’s properties, making it much easier and faster than producing the full codeword itself.

## 6.4. Fractional Description Length

In the previous section, the description length was calculated by reasoning about the existence of a coding scheme with the required properties, without explicitly constructing the full codeword. Because realizable binary codes must have integer lengths, this required the use of floor and ceiling operations.

In this section, we recompute the description length of the example graph while allowing fractional bits wherever appropriate. Conceptually, we assume the existence of codes whose lengths match the real-valued information quantities exactly, as suggested by Shannon’s source coding theorem, rather than restricting ourselves to integer-length realizations.

### 6.4.1. The Graph Dimensions Revisited

Previously, we used the exact Elias delta code lengths

```math
L_{\delta}(n) = \lfloor \log_{2} n \rfloor + 2 \lfloor \log_{2} (\lfloor \log_{2} n \rfloor + 1) \rfloor + 1.
```

If we ignore the floor operations and treat the logarithms as real-valued quantities, this yields the smooth approximation

```math
L_{\delta}(n) = \log_{2} n + 2 \log_{2} (\log_{2} n) + 1.
```

For our example, $`|V| = 4`$ and $`|E|+1 = 5`$, the description length of the graph dimensions in the idealized setting now becomes

```math
\begin{aligned}
L(|V|) + L(|E|)
&= L_{\delta}(4) + L_{\delta}(5) \\
&= \left[ \log_{2} 4 + 2 \log_{2} (\log_{2} 4) + 1 \right] + \left[ \log_{2} 5 + 2 \log_{2} (\log_{2} 5) + 1 \right] \\
&= \log_{2} 5 + 2 \log_{2} (\log_{2} 5) + 6 \\
&\approx 10.75 \text{ bits}.
\end{aligned}
```

For comparison, the actual integer-length code resulted in an integer-description length of 10 bits.

### 6.4.2. The Graph Structure Revisited

The graph structure was encoded using a fixed-length code over all possible upper-triangular binary strings with $`|E|`$ non-zero entries. The codeword length of a fixed-length code over $`N`$ elements is given by

```math
L_{\text{fixed}}(N) = \left\lceil \log_{2} N \right\rceil,
```

which can be relaxed to

```math
L_{\text{fixed}}(N) = \log_{2} N
```

in the idealized setting. And so, for our example with $`|V| = 4`$ and $`|E| = 4`$, the description length of the graph structure now becomes

```math
\begin{aligned}
L_{\text{fixed}} \left( \binom{\frac{|V|(|V|-1)}{2}}{|E|} \right)
&= \log_{2} \binom{6}{4} \\
&= \log_{2} 15 \\
&\approx 3.91 \text{ bits}.
\end{aligned}
```

Again, for comparison, the actual integer-length code resulted in an integer-description length of 4 bits.

### 6.4.3. The Graph Labels Revisited

We previously used a fixed-length code of 1 bit to encode the vertex labels. Given that there are only two distinct labels, in the idealized setting we have that

```math
L_{\text{fixed}}(2) = \log_{2} 2.
```

But since

```math
\log_{2} 2 = \left\lceil \log_{2} 2 \right\rceil,
```

the fractional codeword lengths will actually agree with the integer codeword lengths, and nothing really changes here; the description length of the vertex labels in the idealized setting remains

```math
\sum_{v \in V} L_{\text{fixed}}(2)
= \sum_{v \in V} \log_{2} 2
= |V|
= 4.
```

> Note that the empirical distribution of vertex labels is
>
> ```math
> p(\alpha) = \tfrac{1}{4},
> \qquad p(\beta) = \tfrac{3}{4}.
> ```
>
> Given the size of the graph, this is probably not a good estimate of the vertex label probabilities. Regardless, for this specific graph, a variable length code assigning fractional codeword lengths of
>
> ```math
> L(\alpha) = -\log_{2} \tfrac{1}{4} = 2
> ```
> and
> ```math
> L(\beta) = -\log_{2} \tfrac{3}{4} \approx 0.42
> ```
>
> would result in a smaller total description length.

The empirical distribution of the edge labels is

```math
p(\phi) = \tfrac{1}{4},
\qquad p(\pi) = \tfrac{1}{2},
\qquad p(\tau) = \tfrac{1}{4}.
```

Using the empirical distribution of edge labels, the ideal fractional code lengths are given by Shannon’s formula

```math
L(\ell) = -\log_2 p(\ell).
```

For the present graph,

```math
p(\phi)=\tfrac14,\quad
p(\pi)=\tfrac12,\quad
p(\tau)=\tfrac14,
```

which yields

```math
L(\phi)=2,\qquad
L(\pi)=1,\qquad
L(\tau)=2.
```

These coincide with the integer code lengths used previously. Hence the total edge-label description length in the idealized setting is

```math
\sum_{e\in E} -\log_2 p(\ell_E(e)) =
2+1+1+2 =
6.
```

And so, the total description length of the labels in the idealized setting is 10, matching the integer-length code result.

### 6.4.4. Total Fractional Description Length

Summing all idealized components:

- **Dimensions:** $`\log_{2} 5 + 2 \log_{2} (\log_{2} 5) + 6`$ bits
- **Structure:** $`\log_{2} 15`$ bits
- **Labels:** 10 bits

results in an idealized description length of approximately 24.66 bits.

### 6.4.5. Observations on the Fractional Length

In this small graph, the idealized fractional description length slightly exceeds the integer-length code. This happened because encoding the graph dimensions introduced a fractional increase in description length due to the nature of the Elias delta code. At the same time, there are so few vertices and edges to label, and the empirical probabilities of the labels happen to be exact reciprocals of powers of 2. As a result, the fixed-length and variable-length codes for these labels matched the idealized fractional lengths, leaving no opportunity for the fractional lengths to be shorter.

In more realistic graphs, with many vertices, edges, and a larger variety of labels, the label probabilities are typically not exact reciprocals of powers of two. In such cases, fractional code lengths can differ from integer code lengths, often resulting in shorter idealized descriptions. Rounding differences in codeword lengths accumulate across the labels, especially when many vertices and edges are labeled, making the fractional-length calculation a more accurate reflection of the true information content.

This example illustrates that, while small graphs can produce quirks in idealized description lengths, the fractional-length approach remains useful for reasoning about the information-theoretic limits of encoding.

