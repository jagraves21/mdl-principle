# 4. Crude MDL and Its Limitations

In the previous chapter, we saw how the MDL principle can be applied to a concrete problem in polynomial regression. This involved constructing an _explicit, two-part code_ to encode both the model and the data. While instructive, this approach has several inherent limitations and is sometimes referred to as **crude MDL**. In this chapter, we examine these limitations and discuss how they motivate the development of refined MDL.

## 4.1. What Is Crude MDL?

Crude MDL refers to a straightforward, explicit coding-based implementation of the MDL principle:

- The model is encoded first (i.e., $`L(M)`$), usually as a sequence of integers (e.g., polynomial degree and coefficients).
- The data is encoded next (i.e., $`L(D \mid M)`$), often as residuals or deviations from the model.
- The total description length is simply the sum of these two parts:

```math
L_{\text{MDL}}(D, M) = L(M) + L(D \mid M)
```

The key characteristic of crude MDL is _direct, literal encoding_; it relies on manually choosing a code for every component of the description. Even when crude MDL is applied using only codeword lengths, these lengths implicitly define a valid underlying code. All of the limitations of crude MDL (e.g., arbitrary choices in parameter encoding, dependence on discretization, and inefficiency relative to refined approaches) still apply in this case.

In our polynomial regression example, we had to choose a code for each component:

- The degree $`d`$ of the polynomial was encoded using an integer code.
- The coefficients were quantized, mapped to positive integers, and then encoded.
- The residuals were similarly quantized and encoded.

This approach works well as a pedagogical example, but it introduces several _arbitrary design choices_, such as the selection of an integer code.

## 4.2. Limitations of Crude MDL

Crude MDL suffers from multiple limitations, primarily due to its reliance on explicit, hand-picked codes.

### 4.2.1. Arbitrary Choice of Integer Codes

In the polynomial example:

- The degree could be encoded using any of the Elias codes or even a unary code.
- The coefficients and residuals could be encoded using different integer codes, depending on assumptions about their magnitudes.

These choices _directly affect the resulting description length_, introducing subjectivity. Two equally valid models may have different code lengths simply because a different integer code was chosen. For instance, while the Elias Omega code grows asymptotically more slowly than the Elias Delta code, the Delta code is shorter for moderately small integers. This effect is even more pronounced for residuals, which can take a wide range of values. Depending on how they are encoded, one model may appear shorter than another, not because it fits the data better, but simply because of the choice of code.

> The MDL principle seeks to measure model quality objectively, but crude MDL can make the measurement **arbitrary**.

### 4.2.2. Precision and Scaling

Real-valued data and model parameters must be rounded to finite precision:

- Different scaling factors lead to different integers for encoding.
- This affects code lengths and thus the total MDL score.
- The "best" model could change if a different precision is chosen.

Crude MDL is therefore _sensitive to implementation details_, even though the underlying data or model structure has not changed.

### 4.2.3. Encoding the Input Data

In the polynomial example, as part of our two-part code:

- We encoded the $`x_{i}`$ values as part of $`L(D \mid M)`$.
- The $`x_{i}`$ values do not depend on the model.
- Including them in the code adds a constant term to $`L(D \mid M)`$ that does not depend on the model $`M`$ or its residuals.

This example illustrates a general issue with crude MDL, where the code becomes unnecessarily complex simply because we require that a description of the data be able to reproduce the data without error.

### 4.2.4. Lack of Canonical or Minimal Representation

Because crude MDL relies on hand-picked codes:

- There is no guarantee that the description length is _minimal_ within the model class; this is one of the issues with algorithmic MDL that statistical MDL attempts to address.
- Additive constants (e.g., from different integer codes, rounding schemes, or choices in parameter discretization) can obscure the true differences between models, making some models appear artificially shorter or longer than others.

As a result, model selection may be unreliable and could depend heavily on arbitrary design choices. This highlights the importance of developing canonical or universal coding schemes in statistical MDL, which aim to reduce or eliminate the influence of such arbitrary decisions and provide a more consistent basis for comparing models.

## 4.3. Implications

The limitations discussed above highlight several important points. Crude MDL is excellent for illustration and intuition, as it shows explicitly how a model can compress data. However, its reliance on arbitrary coding choices makes it unsuitable for principled model comparison in many practical settings. These issues motivate the need for a _refined MDL_ principle, providing a canonical way to define description lengths that depends only on the model class and the data, rather than on arbitrary coding conventions.

## 4.4. Summary

Key Takeaways:

- **Crude MDL encodes the model and data explicitly** to provide a direct implementation that illustrates how compression works in practice.
- **Crude MDL relies on arbitrary choices of integer codes, scaling factors, and rounding** which introduce subjectivity into the resulting description lengths.
- **Some parts of the data, such as the inputs, may be encoded unnecessarily** adding constant terms that do not affect model comparison.
- **There is no guarantee that code lengths reflect a canonical or minimal description** because crude MDL requires exact data reproduction, introducing additive constants that depend on coding conventions rather than on the model class itself.

These limitations motivate refined MDL, which seeks intrinsic, model-class-dependent code lengths that remove arbitrariness while preserving the core principle that _compression is learning_.

