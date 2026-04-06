# 7. Crude MDL and Its Limitations

In previous chapters, we saw how the MDL principle can be applied to concrete problems such as polynomial regression and measuring the description length of a graph. In the former, we constructed an _explicit, two-part code_ to encode both the model and the data, whereas in the latter, we reasoned about a code without needing to define it explicitly. While these approaches are instructive, they have inherent limitations and are sometimes collectively referred to as _crude MDL_. In this chapter, we examine these limitations and discuss how they motivate the development of refined MDL.

## 7.1. What Is Crude MDL?

Crude MDL refers to a simple, often explicit, coding-based implementation of the MDL principle.  

- In some cases, crude MDL uses an _explicit code_, where both the model and the data are encoded literally. We saw this in the case of polynomial regression.

- However, crude MDL does not require a fully specified code. It is often sufficient to know what the lengths of codewords would be without defining the exact mapping. We saw how this could be done to measure the description length of labeled graphs without defining an explicit code.

The total description length in crude MDL is usually expressed as the sum of model and data parts:

```math
L_{\text{MDL}}(D, M) = L(M) + L(D \mid M)
```

The key characteristic of crude MDL is that it relies on a direct, often simplified notion of code length. This makes it easy to apply but introduces several limitations, such as arbitrary choices in parameter encoding, dependence on discretization, and inefficiency compared to refined MDL approaches.  

In practice, crude MDL can serve as a pedagogical tool or a first approximation, even when the underlying code is only partially specified or entirely implicit.

## 7.2. Limitations of Crude MDL

Crude MDL suffers from several important limitations. These issues arise primarily because the approach relies on manually designed codes and ad hoc modeling choices. While crude MDL is useful for building intuition, these limitations can make it unsuitable as a general-purpose model selection principle.

### 7.2.1. Arbitrary Choice of Integer Codes

In the polynomial example:

- The degree could be encoded using any of the Elias codes or even a unary code.  
- The coefficients and residuals could be encoded using different integer codes, depending on assumptions about their magnitudes.

These choices directly affect the resulting description length, introducing subjectivity. Two equally valid models may have different code lengths simply because a different integer code was chosen. For instance, while the Elias Omega code grows asymptotically more slowly than the Elias Delta code, the Delta code is shorter for moderately small integers. This effect is even more pronounced for residuals, which can take a wide range of values. Depending on how they are encoded, one model may appear shorter than another, not because it fits the data better, but simply because of the choice of code.

In the labeled graph example, we described the graph structure using its adjacency matrix and selected a code that resulted in shorter description lengths for sparse graphs (and, as a side effect, for dense graphs as well). This choice was based on the assumption that most real-world graphs are sparse. While this decision was guided by experience, it remains fundamentally arbitrary; another choice of code could lead to different comparisons between models.

> The MDL principle seeks to measure model quality objectively, but crude MDL can make this measurement _arbitrary_.

### 7.2.2. Dependence on Parameter Discretization

Another limitation arises when models contain real-valued parameters. In order to encode such parameters using a discrete code, they must first be quantized.

For instance, in the polynomial regression example:

- The coefficients are real numbers.
- To encode them, we must first round them to some finite precision.
- The rounded values are then mapped to integers and encoded.

However, the choice of precision is itself arbitrary. If the precision is too coarse, the model may not fit the data well. If it is too fine, the parameter description length increases unnecessarily.

Thus, the resulting description length depends not only on the model and the data, but also on the chosen discretization scheme. Different discretization choices can lead to different model rankings.

In some situations, this issue is mitigated by the way the data is measured. For example, measurements are often recorded with a fixed number of significant digits, which naturally determines an appropriate precision for encoding the parameters and residuals. In such cases, the discretization level is dictated by the measurement process itself rather than by an arbitrary modeling choice. However, this is not always the case. In many settings, there may be no clear domain-based justification for selecting a particular precision (e.g., when working with derived quantities or abstract parameters that are not directly tied to the measurement process). When this happens, the discretization must be chosen somewhat arbitrarily, and the resulting description lengths can depend on that choice.

### 7.2.3. Inefficiency of Two-Part Codes

Crude MDL typically relies on two-part codes, where the model is encoded first and the data is encoded afterward:

```math
L_{\text{MDL}}(D, M) = L(M) + L(D \mid M)
```

While conceptually simple, this approach is not always optimal. By encoding the model separately from the data, the two-part scheme fails to exploit all available structure in the joint description.

More efficient codes often exist that directly encode the data while implicitly representing the model. These codes can achieve shorter overall description lengths than any explicit two-part decomposition.

As a result, crude MDL may over-penalize models and produce suboptimal model selections.

### 7.2.4. Lack of Theoretical Invariance

Ideally, a model selection principle should not depend on arbitrary implementation details such as the choice of encoding scheme or parameterization. However, crude MDL lacks this invariance.

Different but equivalent representations of the same model can lead to different description lengths simply because the encoding scheme changes. As a consequence, the outcome of crude MDL may depend on how the model is described rather than on the intrinsic complexity of the model itself.

These limitations motivate the development of refined MDL methods, which aim to remove arbitrary coding choices and provide a more principled and theoretically grounded approach to model selection.

## 7.3. Implications

The limitations discussed above highlight several important points. Crude MDL is excellent for illustration and intuition, as it shows explicitly how a model can compress data. However, its reliance on arbitrary coding choices makes it unsuitable for principled model comparison in many practical settings. These issues motivate the need for a _refined MDL_ principle, providing a canonical way to define description lengths that depends only on the model class and the data, rather than on arbitrary coding conventions.

## 7.4. Summary

Key Takeaways:

- **Crude MDL encodes the model and data explicitly** to provide a direct implementation that illustrates how compression works in practice.
- **Crude MDL relies on arbitrary choices of integer codes, scaling factors, and rounding** which introduce subjectivity into the resulting description lengths.
- **Some parts of the data, such as the inputs, may be encoded unnecessarily** adding constant terms that do not affect model comparison.
- **There is no guarantee that code lengths reflect a canonical or minimal description** because crude MDL requires exact data reproduction, introducing additive constants that depend on coding conventions rather than on the model class itself.

These limitations motivate refined MDL, which seeks intrinsic, model-class-dependent code lengths that remove arbitrariness while preserving the core principle that _compression is learning_.

