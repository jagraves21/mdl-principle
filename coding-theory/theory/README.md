# Coding Theory Background

This directory provides a brief introduction to coding theory concepts needed to understand and apply the Minimum Description Length (MDL) principle.

The goal is not to present a full information‐theoretic treatment of coding but to supply the minimal background needed to understand and build descriptions used as part of the MDL principle.

## Essential Topics

These files contain the core material needed to work with the MDL principle. Together they define what descriptions (codes) are, the types of codes allowed, and how integers are encoded in actual descriptions.

**[01-what-is-a-code.md](01-what-is-a-code.md)** &mdash; Defines codes and codewords, explains message encoding via concatenation, and introduces the connection between code length and description cost in MDL.

**[02-uniquely-decodable-codes.md](02-uniquely-decodable-codes.md)** &mdash; Introduces uniquely decodable codes, explaining why codes must be unambiguous, and shows how prefix-free codes provide a practical subclass.

**[03-prefix-free-codes.md](03-prefix-free-codes.md)** &mdash; Covers prefix-free (instantaneously decodable) codes, their equivalence to instantaneous decoding, and their role in efficient, unambiguous message encoding.

**[04-integer-codes-overview.md](04-integer-codes-overview.md)** &mdash; Introduces binary, prefix-free integer codes and universal codes, explaining how positive integers can be efficiently encoded without knowing probabilities.

Understanding these four topics provides a solid grasp of how descriptions are constructed and compared within the MDL framework.

## Supportive Topics

These topics provide intuition, visualization, and additional theory. They are _optional_ for applying MDL but helpful for deeper understanding.

**[05-the-code-space-tree.md](05-the-code-space-tree.md)** &mdash; Introduces the code space tree as a geometric representation of prefix-free codes and illustrates how codeword lengths are constrained.

**[06-kraft-mcmillan-inequalities.md](06-kraft-mcmillan-inequalities.md)** &mdash; Covers the Kraft and McMillan inequalities, showing constraints on codeword lengths and connections to probabilities, MDL, and entropy coding.

**[07-huffman-codes.md](07-huffman-codes.md)** &mdash; Explains Huffman coding for constructing optimal prefix codes from known symbol probabilities, minimizing expected codeword length, and illustrating links to MDL.

## Not Required

The following topic is _not needed_ to apply the MDL principle:

**[08-sardinas-patterson.md](08-sardinas-patterson.md)** &mdash; Introduces the Sardinas–Patterson algorithm to test whether any finite code is uniquely decodable.

