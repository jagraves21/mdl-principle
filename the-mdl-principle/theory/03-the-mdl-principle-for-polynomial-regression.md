# 3. The MDL Principle for Polynomial Regression

In this chapter, we illustrate the MDL principle using a concrete example, polynomial regression, where the goal is to select an appropriate polynomial model for a dataset. This example demonstrates how MDL naturally balances model complexity and data fit using explicit code lengths, without relying on probabilistic assumptions or standard model selection criteria such as the Bayesian Information Criterion (BIC) or the Akaike Information Criterion (AIC).

## 3.1. The Setup

Suppose we have a dataset of $`n`$ paired samples,

```math
D = \{(x_{i}, y_{i})\}_{i=1}^{n}.
```

We want to model the data using a polynomial,

```math
P(x) = a_{0} + a_{1} x + a_{2} x^{2} + \dots + a_{d} x^{d},
```

where $`d`$ is the degree and $`(a_{0}, \dots, a_{d})`$ are the coefficients.

Under the MDL principle, the optimal polynomial model is the one that minimizes the **total description length**

```math
L_{\text{MDL}}(P) = L(P) + L(D \mid P),
```

where $`L(P)`$ denotes the number of bits required to encode the model, and $`L(D \mid P)`$ denotes the number of bits required to encode the data given the model.

## 3.2. Encoding the Model

Recall that the first term in the total description length, namely $`L(P)`$, represents the number of bits required to encode the polynomial. A polynomial is uniquely determined by its coefficients. However, to decode a sequence of coefficients unambiguously, the decoder must know in advance how many coefficients belong to the polynomial. Otherwise, there is no principled way to determine where the model description ends and where the remainder of the message begins (i.e., the part of the message encoding the data given the model). The number of coefficients is determined by the degree of the polynomial. And so, encoding a polynomial reduces to encoding two components: the degree and the coefficients themselves.

Encoding the degree is straightforward. We can use any suitable integer code, such as the Elias delta code. One subtlety is that many integer codes (like the Elias delta code) are defined only for positive integers, whereas a polynomial may have degree 0 (i.e., a constant polynomial). To handle this, we simply encode $`d+1`$ instead of $`d`$.

Encoding the coefficients is more subtle. Real-valued coefficients cannot be represented exactly, since they may require infinite precision (e.g., $`\sqrt{2}`$). This is not a practical problem, since the dataset is already stored with finite precision, making it unnecessary to encode coefficients to infinite precision. We therefore choose a fixed precision and round all coefficients accordingly. Multiplying by the chosen precision scale and truncating the fractional part converts each coefficient into an integer.

Because these integers may be zero or negative, we must use an encoding that can represent all integer values. A standard approach is to map the integers to the positive integers using a one-to-one correspondence, and then encode the result using a code for positive integers. For example, the mapping function

```math
f(n) = \begin{cases}
2n & n \ge 0 \\
-2n - 1 & n < 0
\end{cases}
```

assigns a unique positive integer to every integer. Once this mapping is applied, the resulting positive integers can be encoded using any standard integer code, such as Elias delta or gamma codes.

In this way, both the degree and the coefficients are converted into a sequence of integers that can be efficiently encoded, giving a concrete realization of $`L(P)`$ in bits.

## 3.3. Encoding the Data Given the Model

Once a model has been fixed, we need a way to encode the dataset given the model in order to define the second term of the total description length, $`L(D \mid P)`$. A natural approach is to encode both the input values $`x_{i}`$ and the residuals $`r_{i} = y_{i} - \hat{y}_{i}`$, where $`\hat{y}_{i}`$ are the model's predictions.

We begin by encoding the number of observations $`n`$ using a standard integer code (e.g., Elias delta or gamma code). Then, both the input values $`x_{i}`$ and the residuals $`r_{i}`$ can be encoded in a manner analogous to the polynomial coefficients. We begin by rounding each residual to a fixed precision, then scale and map them to positive integers using a one-to-one correspondence. These integers can then be encoded using a standard integer code, such as the Elias delta or gamma code.

In this way, both the input values and residuals are converted into sequences of integers that can be efficiently encoded, providing a concrete realization of $`L(D \mid P)`$ in bits.

## 3.4. Recovering the Data From the Code

Operationally, the dataset can be recovered from its encodings as follows:

**Decode the Model $`P`$**:

1. Decode the degree $`d`$ of the polynomial using the chosen integer code. If the degree was encoded as $`d+1`$ to handle zero, subtract 1 to recover the actual degree.  
2. Use the value of $`d`$ to decode $`d+1`$ integers using the chosen integer code and convert them to coefficients:  
   - Decode each mapped positive integer for the coefficient.  
   - Invert the one-to-one mapping to recover signed integers.  
   - Divide by the scaling factor used during encoding to recover the coefficient at the specified precision.  

**Decode the Data Using the Model**:

1. Decode the number of observations $`n`$ using the chosen integer code.  
2. Use the value of $`n`$ to decode $`n`$ integers using the chosen integer code and convert them to the inputs $`x_{i}`$:  
   - Decode each mapped positive integer for $`x_{i}`$.  
   - Invert the one-to-one mapping to recover signed integers.  
   - Divide by the scaling factor used during encoding to recover $`x_{i}`$ at the specified precision.  
3. Use the value of $`n`$ to decode $`n`$ integers using the chosen integer code and convert them to residuals $`r_{i}`$:  
   - Decode each mapped positive integer for $`r_{i}`$.  
   - Invert the one-to-one mapping to recover signed integers.  
   - Divide by the scaling factor used during encoding to recover $`r_{i}`$ at the specified precision.  
4. Reconstruct the original outputs $`y_{i}`$ using the model predictions:  
```math
y_{i} = \hat{y}_{i} + r_{i}, \quad \text{where } \hat{y}_{i} = P(x_{i}).
```

In this way, both the model and the data are fully recoverable from their encoded representations, providing a concrete realization of the total description length $`L_{\text{MDL}}(P) = L(P) + L(D \mid P)`$ in bits.

## 3.5. Discussion and Limitations

In the preceding sections, we described how to encode both the model and the dataset given the model using explicit integer codes, including the inputs $`x_{i}`$ and residuals $`r_{i}`$. Encoding the input values $`x_{i}`$ is necessary to fully reconstruct the dataset from the code. In practice, however, encoding the $`x_{i}`$ values is not strictly necessary for model selection. The number of bits required to encode the $`x_{i}`$'s does not depend on the model $`P`$ or on how well it fits the data. Therefore, it contributes only a constant overhead to $`L(D \mid P)`$, which cancels out when comparing candidate models. For this reason, many MDL examples focus solely on encoding the residuals.

Despite providing a concrete illustration of the MDL principle, our approach has several limitations. One important issue is the choice of integer codes. Different parts of the description naturally call for different coding strategies:

- **The degree of the polynomial** is typically small, so a compact code such as the Elias gamma code is appropriate.  
- **The magnitudes of the coefficients, residuals, and data values** ($`x_{i}`$, $`y_{i}`$) are unknown a priori. For small integers, the Elias gamma code works well; for medium-sized integers, the Elias delta code is preferable; for extremely large integers, the Elias omega code may be more efficient.  

Because these magnitudes are not known in advance, the choice of integer code is essentially arbitrary. This arbitrariness introduces an element of subjectivity into the total description length, and in some cases may lead to suboptimal model selection if the coding scheme is poorly matched to the data.

These issues highlight limitations of the explicit, two-stage coding approach used in this chapter and motivate more refined MDL constructions that reduce the arbitrariness associated with manually choosing coding schemes. These refinements will be introduced in later chapters.

