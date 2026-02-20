# 5. A Description Method for Labeled Graphs

In this chapter, we consider the problem of calculating the description length of a labeled graph. We define a code over the set of all labeled graphs and show that, in practice, it is much easier to calculate the length of a graph's description (i.e., codeword) than to actually compute the codeword.

## 5.1. Defining the Graph Model

Graph theory terminology tends to be highly inconsistent across the literature, and different sources adopt slightly different definitions. For the purposes of this chapter, we adopt a precise definition of a labeled graph to fix the assumptions required for calculating description lengths unambiguously.

Formally, a labeled graph $`G = (V, E, \ell_{V}, \ell_{E})`$ is an ordered 4-tuple, where:

- $`V = \{v_{1}, \dots, v_{n}\}`$ is a non-empty set of elements called **vertices**,
- $`E \subseteq \{\{u,v\} : u,v \in V, u \neq v\}`$ is a set of unordered pairs of vertices called **edges**,
- $`\ell_{V} : V \to \mathcal{L}_{V}`$ is a function that assigns **labels to vertices** from a finite alphabet $`\mathcal{L}_{V}`$, and
- $`\ell_{E} : E \to \mathcal{L}_{E}`$ is a function that assigns **labels to edges** from a finite alphabet $`\mathcal{L}_{E}`$.

This definition ensures that all subsequent constructions (such as encoding the graph structure) are unambiguous and consistent.

## 5.2. A Three-Stage Encoding Scheme

To calculate the description length of a labeled graph, we adopt a three-stage encoding approach to serve as our description. This decomposition separates the problem into manageable parts, allowing us to encode the graph efficiently while reflecting the contributions of different components to the total description length:

1. **First we encode the graph dimensions** by specifying the number of vertices $`|V|`$ and the number of edges $`|E|`$.
2. **Next we encode the graph structure** by representing the adjacency relationships between vertices.
3. **Finally we encode the graph labels** by assigning codewords to all vertex and edge labels.

Each stage builds on the previous one: first we fix the size of the graph, then we encode its connectivity, and finally we encode the information carried by the labels. This staged approach simplifies both the calculation and the analysis of the total description length.

## 5.2.1. Encoding the Graph Dimensions

To encode the dimensions of the graph, we first encode the number of vertices $`|V|`$ and the number of edges $`|E|`$. This can be done using an integer code, such as the Elias gamma, delta, or omega code. By definition, $`|V|`$ is positive, but $`|E|`$ may be zero since we allow graphs with no edges. Because many integer codes are defined only for positive integers, we may need to encode $`|E| + 1`$ rather than $`|E|`$ to ensure that the input to the integer code is always positive.

## 5.2.2. Encoding the Graph Structure

Once the number of vertices and edges has been encoded, we can focus on encoding the actual structure of the graph. A natural representation of the structure of a simple graph is its $`0-1`$ adjacency matrix.

Because the vertices of an unlabeled graph have no intrinsic ordering, a single graph may correspond to many different adjacency matrices, depending on the vertex ordering used to index the matrix. To ensure that the encoding is a well-defined function of the graph (i.e., the same graph always yields the same codeword), we must select a _canonical adjacency matrix_.

For now, suppose that a canonical adjacency matrix has already been fixed. Since the underlying unlabeled graph is simple (i.e., no loops or parallel edges), the adjacency matrix is a symmetric, binary matrix with 0's on the main diagonal. Therefore, it is sufficient to encode only the upper triangle (excluding the diagonal), which contains

```math
\frac{|V|(|V|-1)}{2}
```

entries, exactly $`|E|`$ of which are equal to $`1`$.

By representing the upper triangle of the adjacency matrix as a binary string, we can compute its _rank_ under a fixed total ordering. The details of this are provided later; for now, it is sufficient to know that the rank is an integer between

```math
0 \text{ and } \binom{\frac{|V|(|V|-1)}{2}}{|E|}-1.
```

This integer is then encoded using a fixed-length code.

> **Note:** One might wonder why we do not simply concatenate the upper triangle of the adjacency matrix into a raw binary string and use it as the codeword. Using a fixed-length code over all possible upper-triangle binary strings with exactly $`|E|`$ ones is more efficient, especially for very sparse or very dense graphs. When $`|E|`$ is very small or very large relative to $`|V|`$, the number of valid binary strings is much smaller than the total number of entries in the upper triangle, reducing the uncertainty about the graph structure and allowing for a more concise encoding than a straightforward binary string.

### 5.2.2.1. The Canonical Adjacency Matrix

To ensure that the encoding of a graph is unique, we must select a _canonical adjacency matrix_ from the set of all adjacency matrices corresponding to the same unlabeled graph. The canonical adjacency matrix serves as a representative that is independent of the vertex ordering.

A simple and widely used approach is as follows ([Knuth, 2011](#knuth2011)):

1. Consider all possible adjacency matrices obtained by permuting the vertices.
2. Interpret each adjacency matrix as a binary string by concatenating all of its rows (or columns) in order.
3. Choose the adjacency matrix whose binary string is _lexicographically smallest_. Equivalently, this is the adjacency matrix with the smallest integer value when the binary string is interpreted as a number.

This procedure guarantees that every unlabeled graph corresponds to exactly one canonical adjacency matrix. By fixing the canonical adjacency matrix in this way, we remove any ambiguity arising from different vertex orderings when encoding the structure of the graph.

### 5.2.2.2. Ranking

The process of **ranking** provides a one-to-one correspondence between a set of combinatorial objects (e.g., adjacency matrices or their upper triangles) and the integers from $`0`$ to $`N-1`$, where $`N`$ is the number of combinatorial objects under consideration. The reverse process, called **unranking**, maps an integer back to its corresponding object.

A common example is the set of binary strings of length $`n`$ with exactly $`k`$ ones. There are

```math
\binom{n}{k}
```

such strings. By fixing an ordering (for instance, _lexicographic order_), each string can be assigned a unique integer, called its **rank**, between $`0`$ and $`\binom{n}{k}-1`$. This mapping allows any object in the set to be represented efficiently as a fixed-length code of

```math
\log_2 \binom{n}{k}
```

bits.

We now apply this general notion of ranking to the upper triangle of an adjacency matrix. For a simple graph with $`|V|`$ vertices and $`|E|`$ edges, the upper triangle contains

```math
n = \frac{|V|(|V|-1)}{2}
```

entries, exactly

```math
k = |E|
```

of which are ones. By concatenating the rows (or columns) of the upper triangle (excluding the diagonal), we obtain a binary string that can be ranked as described above. The resulting integer rank provides a compact, unique representation of the graph structure once the adjacency matrix has been fixed.

> **Note:** Ranking is a purely combinatorial step that does not depend on the matrix being canonical. Its purpose is to represent the adjacency structure compactly, so that any fixed adjacency matrix can be encoded and later reconstructed unambiguously.

## 5.2.3. Encoding the Graph Labels

To encode the vertex and edge labels, we first impose a fixed total ordering on the vertices and edges. The vertex ordering could simply follow the order used to index the canonical adjacency matrix. The edge ordering could follow the sequence of non-zero entries in the upper triangle of the canonical adjacency matrix when concatenated row-wise or column-wise. However, if more than one possible ordering of the vertices yields the same canonical adjacency matrix, ties must be broken. This can be done by selecting an ordering that produces the smallest sequence of vertex and edge labels under some fixed total ordering, such as lexicographical order.

Once the orderings are fixed, we encode the labels by traversing the vertices in order, followed by the edges in order. This can be done using a fixed-length code, or, if the label probabilities are known, we can leverage Shannon’s source coding theorem to assign each label a codeword length equal to the negative logarithm of its probability:

```math
L(\ell) = -\log_{2} p(\ell).
```

This approach ensures that the labels are encoded consistently with the chosen orderings and allows the description length to reflect the actual uncertainty in the labels.

## 5.3. Computational Costs

This section contrasts the computational effort required to calculate the description length of a labeled graph with the effort required to encode and decode the graph itself. While calculating the description length is straightforward, actual encoding involves significant combinatorial complexity, primarily due to canonicalization.

Throughout this section, let $`G = (V, E, \ell_{V}, \ell_{E})`$ be a labeled graph.

### 5.3.1. Calculating Description Lengths

The total description length of $`G`$ is given by

```math
L(G) = L(|V|) + L(|E|) + \log_{2} \binom{\frac{|V|(|V|-1)}{2}}{|E|} + \sum_{v \in V} L(\ell_{V}(v)) + \sum_{e \in E} L(\ell_{E}(e)).
```

Here,

- $`L(|V|)`$ and $`L(|E|)`$ are the description lengths under the integer codes used to encode the number of vertices and edges,

- $`\log_{2} \binom{\frac{|V|(|V|-1)}{2}}{|E|}`$ is the number of bits needed to encode the canonical adjacency matrix, and

- $`\sum_{v \in V} L(\ell_{V}(v))`$ and $`\sum_{e \in E} L(\ell_{E}(e))`$ are the bits required to encode all vertex and edge labels, respectively.

This formulation treats the graph as a single object $`G`$ and emphasizes that the description length is fully determined by the number of vertices, the number of edges, the canonical adjacency matrix, and the vertex and edge labels. Importantly, we can compute $`L(G)`$ without ever computing the actual codeword for $`G`$, as long as the integer code lengths for $`|V|`$ and $`|E|`$ can be calculated directly.

> **Note:** It is important to observe that calculating $`L(G)`$ is straightforward and can be done quickly.

### 5.3.2. Computing Codewords of Labeled Graphs

Encoding a labeled graph under our encoding scheme requires computing a canonical adjacency matrix, which is equivalent to solving a graph isomorphism problem. The steps are as follows:

1. **Encode the number of vertices and edges** using the chosen integer codes to produce codewords for $`|V|`$ and $`|E|`$.
2. **Select a canonical adjacency matrix** to represent the graph structure for encoding.
3. **Rank the upper triangle** of the canonical adjacency matrix according to a fixed total ordering.
4. **Encode the rank of the upper triangle** using a fixed-length code.
5. **Encode vertex and edge labels** following a fixed total ordering.

The primary difficulty in this process is computing the canonical adjacency matrix. This requires considering all possible vertex permutations to identify the lexicographically smallest adjacency matrix. The graph isomorphism is in **NP**, but not known to be NP-complete ([Garey & Johnson, 1979](#garey1979)), and quasipolynomial-time algorithms exist ([Babai, 2016](#babai2016)). For small to medium graphs, practical algorithms such as *nauty* ([McKay & Piperno, 2014](#mckay2014)) efficiently compute canonical forms, but for large graphs, canonicalization remains the main computational bottleneck.

Once the canonical adjacency matrix is determined, ranking the upper triangle and encoding the labels can be performed efficiently. For many natural total orderings of the binary strings (e.g., lexicographic), ranking can be done in polynomial time in the number of entries in the upper triangle ([Knuth, 2011](#knuth2011)).

### 5.3.3. Computing Labeled Graphs from Codewords

Decoding a labeled graph from its codeword is significantly easier than encoding. Since the codeword explicitly specifies the graph structure and labels, the procedure is straightforward:

1. **Decode the graph dimensions** using the integer codes to recover $`|V|`$ and $`|E|`$.
2. **Reconstruct the adjacency matrix** by decoding the rank of the upper triangle and converting it back into the binary string representing the upper triangle; the full adjacency matrix is then completed by symmetry.
3. **Decode vertex and edge labels** in the fixed orderings established during encoding.

No search over vertex permutations is required because the adjacency matrix in the codeword is already canonical. All decoding steps can be done in polynomial time.

### 5.3.4. Comparing Computational Complexity

The asymmetry between calculating description lengths and encoding and decoding labeled graphs arises from a fundamental difference in what must be done in each case:

- **Calculating $`L(G)`$** requires only evaluating explicit combinatorial formulas determined by the number of vertices, edges, and labels. The binomial term and label sums can be computed directly, without any search over vertex permutations. The overall cost grows essentially linearly with $`|V| + |E|`$ and remains feasible even for very large graphs.

- **Encoding a labeled graph** is computationally intensive because it requires selecting a canonical adjacency matrix. This involves finding a permutation of vertices that produces the lexicographically smallest adjacency matrix, which is equivalent to solving a graph isomorphism problem. Although the space of possible vertex permutations has size $`|V|!`$, the best known worst-case algorithms for graph isomorphism run in quasipolynomial time. In practice, canonicalization is often efficient for small to medium graphs, but it can still become a computational bottleneck for large instances.

- **Decoding a labeled graph** from a codeword is easier than encoding. The codeword already specifies the canonical adjacency matrix and label sequences, so no search over permutations is required. Reconstruction can be performed in polynomial time by reversing the ranking and label encoding procedures.

In summary, calculating description lengths is a direct operation based on formulas, encoding requires a combinatorial search, and decoding involves structured but efficient reconstruction. This creates a pronounced asymmetry between formula-based evaluation and the algorithmic requirements of encoding and decoding.

## 5.4. References

- <a id="babai2016"></a>Babai, L. *Graph Isomorphism in Quasipolynomial Time*. [arXiv:1512.03547](https://arxiv.org/abs/1512.03547), 2016.
- <a id="garey1979"></a>Garey, M. R., & Johnson, D. S. *Computers and Intractability: A Guide to the Theory of NP-Completeness*. [ACM Digital Library](https://dl.acm.org/doi/book/10.5555/574848), 1979.
- <a id="mckay2014"></a>McKay, B. D., & Piperno, A. *Practical Graph Isomorphism, II*. Journal of Symbolic Computation, 60 (2014), pp. 94–112. [https://doi.org/10.1016/j.jsc.2013.09.003](https://doi.org/10.1016/j.jsc.2013.09.003)
- <a id="knuth2011"></a>Knuth, D. E. *The Art of Computer Programming, Volume 4A: Combinatorial Algorithms, Part 1*. Addison-Wesley, 2011. [ACM Digital Library](https://dl.acm.org/doi/10.5555/1984890)

