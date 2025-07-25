## Appendix

## Weighted Summation

In spectral analysis—whether it’s the Fourier transform, a filter, an interpolator, or a wavelet transform—the core operation is always a weighted summation. By designing appropriate weights, we amplify the information we care about and suppress what we don’t, allowing us to distill an N-point signal into concise, abstract, and generalizable features. As a result, nearly every outcome of spectral analysis exhibits additive properties—for example, the noise spectrum follows a chi-square distribution. From another viewpoint, weighted summation is essentially a convolution; by the convolution theorem, it is equivalent to multiplication in the dual domain. This insight also suggests alternative ways to construct kernels in spectral analysis, such as the FBSP wavelet in the continuous wavelet transform, which is obtained via spectral-domain interpolation.

When you are lost in the mathematical derivation, remember that the core operation is always a weighted summation. This perspective can help you understand the underlying principles and guide your implementation.

## NDArray Shaping

When create the `numpy.ndarray`, arrange the dimension in the sequence of `Frequency -> Time -> Dimension(s) -> Channel(s)`. This arrangement is  nothing but my personal preference. One of the reason is the STFT and wavelet package by default return the matrix in the order of `Frequency -> Time`.    In this convention, one should use `np.stack([a, b], axis = -1)` when combining two channels. 

## Naming Convention

Naming the variable with the format of `noun_adjective` like `spec_complex` for the complex spectral matrix.



### Jargon Sheet

|      Notation      |   Variable Name   |                         Explanation                          |
| :----------------: | :---------------: | :----------------------------------------------------------: |
|     $(t), (f)$     |      `t, f`       |       Continuous input, frequency ($f$) or time ($t$)        |
|     $[t], [f]$     |      `t, f`       |                        Discrete input                        |
|      $\omega$      |      `omega`      |             Angular frequency, $\omega = 2\pi f$             |
|       $x(t)$       |         -         |      Continuous signal $x$ as a function of time ($t$)       |
|  $x[n] = x(nf_S)$  |       `sig`       | An even sample of $x(t)$, the intensity is termed as ***Amplitude*** |
| $X[k]=X(kT^{-1})$  |      `coef`       | Fourier Transform Coefficient of $x(t)$, the intensity is termed as ***Magnitude*** |
|   $\mathcal{F}$    |         -         |                  Fourier Transform Operator                  |
|        $N$         |        `N`        |                        Signal length                         |
|        Arb         |         -         |                        Arbitrary Unit                        |
|       $w[n]$       |     `window`      |                       Window Function                        |
|    $\hat{B}_i$     |      `coef`       |                     Fourier Coefficient                      |
|      $S_{ij}$      |      `spec`       |                       Spectral Matrix                        |
|         -          |      `*_wf`       |                       Wave Frame (WF)                        |
|         -          |      `*_ma`       |                     Moving Average (MA)                      |
|         -          | `*_lh` and `*_rh` |            Left Handed (LH) and Right Handed (RH)            |
| $\mathrm{\Delta}t$ |       `dt`        |                       Sampling Period                        |
|       $f_s$        |       `fs`        |                      Sampling Frequency                      |
|   $\mathrm{Amp}$   |       `amp`       |                          Amplitude                           |
|                    |                   |                                                              |
|                    |                   |                                                              |
|                    |                   |                                                              |
|                    |                   |                                                              |
|                    |                   |                                                              |
|                    |                   |                                                              |
|                    |                   |                                                              |


## Sliding Window

`numpy.lib.stride_tricks.sliding_window_view(x, window_shape, axis=None, *, subok=False, writeable=False)` provides the function for re-organizing the signal into several sub-chunk. This function can only give a stride of one. For a customized stride, you need to use `numpy.lib.stride_tricks.as_strided(x, shape=None, strides=None, subok=False, writeable=True)`. This function can be unsafe and crash your program.  

The `bottleneck` package, which is safer and more efficient,  is more suggested for common usage of moving windows, like moving-average and moving-maximum. The following code shows how to use the `bottleneck` functions and their expected results.

```python
# -------------------------------
# Method 1: sliding_window_view (fixed stride = 1)
# -------------------------------
window_size = 4
windows_stride1 = sliding_window_view(sig, window_shape=window_size)

# -------------------------------
# Method 2: as_strided (custom stride, use with caution!)
# -------------------------------
custom_stride = 2
n_windows = (len(sig) - window_size) // custom_stride + 1
shape = (n_windows, window_size)
strides = (sig.strides[0] * custom_stride, sig.strides[0])

windows_custom = as_strided(sig, shape=shape, strides=strides)

# -------------------------------
# Method 3: bottleneck (safe, NaN-aware window functions)
# -------------------------------
print("\n[3] bottleneck window functions:")
ma = bn.move_mean(sig, window=3, min_count=1)
mm = bn.move_max(sig, window=3, min_count=1)
std = bn.move_std(sig, window=3, min_count=1)

```

## Table of Common Distributions

Please keep in mind that all signals contain uncertainty. If you want to start a theoretical derivation on some signal properties, consider it from the perspective of probability. 

<p align = 'center'><img src="Figure/figure_table_of_distributions.png" width="60%"/></p>
<p align = 'center'><i>Table of Common Distributions</i></p>

