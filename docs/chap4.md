# What Else Should You Know About the DFT/FFT?

## Uncertainty Principle

In signal processing and quantum physics alike, the **uncertainty principle** states that

> A signal $x(t)$ and its Fourier transform $X(f)$ cannot both be sharply localized.  If $\delta t$ denotes the effective duration of the signal and $\delta f$ its effective bandwidth, then
$$
\Delta t \,\Delta f \;\ge\; \tfrac{1}{4\pi}
$$
> meaning you can trade time resolution for frequency resolution, but never improve both simultaneously beyond this bound.  
>
This is often summarized as: "A signal cannot be both time-limited and band-limited."
Practically, a shorter pulse spreads its spectrum, while a narrowband tone must last longer.

<p align = 'center'><img src="Figure/figure_uncertainty_principle.png" width="100%"/></p>
<p align = 'center'><i>Top: Gaussian pulses with narrow (purple) to wide (blue) time span; Bottom: Their Fourier coefficient with wide to narrow frequency bandwidth. </i></p>

The lower bound of the time–bandwidth product is achieved by Gaussian functions, which are optimal in the sense that they minimize the product $\Delta t \Delta f$.

Here the central time $t_0$ and bandwidth $f_0$ are defined as the first moments of the energy density $|x(t)|^2$ and $|X(f)|^2$, respectively, while $\Delta t$ and $\Delta f$ are their standard deviations. The integrals are taken over all time and frequency, respectively.
$$
t_0 = \frac{\int t\,|x(t)|^{2} \, dt}{\int |x(t)|^{2} \, dt},
\qquad
\Delta t = \sqrt{\frac{\int (t-t_0)^{2}\,|x(t)|^{2} \, dt}{\int |x(t)|^{2} \, dt}}
$$

$$
f_0 = \frac{\int f\,|X(f)|^{2} \, df}{\int |X(f)|^{2} \, df},
\qquad
\Delta f = \sqrt{\frac{\int (f-f_0)^{2}\,|X(f)|^{2} \, df}{\int |X(f)|^{2} \, df}}
$$

This trade-off is a fundamental limitation of the Fourier transform and is mathematically expressed through time–bandwidth products. It implies that short-duration signals must occupy a wide frequency range, while narrow-band signals cannot be sharply confined in time—a concept closely related to Heisenberg’s uncertainty principle in quantum mechanics.

One direct application of this principle is the choice of the window length in the Short-Time Fourier Transform (STFT). A longer window improves frequency resolution but reduces time resolution, while a shorter window does the opposite. The optimal choice depends on the specific characteristics of the signal being analyzed.

<div STYLE="page-break-after: always;"></div>

## Gibbs Phenomenon

Although the Fourier transform can perfectly reconstruct **<u>discrete</u>** signal points, it struggles to represent analytic functions with sharp discontinuities. This is because a finite number of sinusoids cannot fully capture the infinite-frequency behavior near a jump, leading to **the Gibbs phenomenon**—persistent overshoot and ringing near discontinuities. 

<p align = 'center'>
<img src="Figure/figure_gibbs.png" width="100%"/>
</p>
The variation of the signal concentrates sharply localized, which means the bandwidth should be infinitely wide. Such a transient signal has a Fourier coefficient propotional to ${(2k+1)}^{-1}$ and $\delta f$ can not converged.

Despite increasing the number of harmonics, the overshoot becomes narrower but remains at a constant: ~8.95%. 
$$
\frac{1}{\pi}\int_0^\pi \frac{\mathrm{sin} u}{u} \mathrm{d}u \approx 0.08949\ldots
$$
This is a fundamental limitation of the Fourier basis, not a numerical flaw, and in practice, techniques like windowing, filtering, or using alternative bases such as wavelets are employed to mitigate its effects.

## Convolution Theorem

In Fourier analysis, the **Convolution Theorem** says that convolving two signals in time becomes multiplying their spectra:
$$
\mathcal{F}\{\,x(t)*y(t)\,\}=X(f)\cdot Y(f),
\qquad
\mathcal{F}^{-1}\{X(f)*Y(f)\}=x(t)\cdot y(t),
$$
with the definition of convolution operator ($*$):
$$
(x*y)(t)=\int_{-\infty}^{\infty}x(\tau)\,y(t-\tau)\,d\tau
$$
The convolution theorem states how operations performed on a signal in the time domain or in the frequency domain will affect that signal in its dual domain. 

We now understand that windowing (time-domain multiplication) actually broadens and shapes the spectrum exactly as predicted by the convolution theorem.

Unlike the continuous version, the discrete convolution theorem inherently links point-wise multiplication in the DFT domain to **circular (convolution-mod-N)** in the time domain—so to obtain ordinary linear convolution you must first zero-pad the sequences beyond their combined length.

According to the convolution theorem, performing convolution via the FFT cuts the computational cost from $O(N^{2})$ to $O\bigl(N\log N\bigr)$.

## More Practical Properties

A super powerful property of Fourier transform is that:
$$
\mathcal{F}\left[\frac{\mathrm{d}}{\mathrm{d}t}x(t)\right]=(i2\pi f)\cdot X(f)
$$

which can be easily proved by doing derivative to the both sides of the inverse Fourier transform:
$$
\begin{align}
\frac{\mathrm{d}}{\mathrm{d}t}[x(t)]&=\int_{-\infty}^{+\infty} X(f) (i2\pi f)e^{i 2 \pi f t} \mathrm{d}f\\
&=\int_{-\infty}^{+\infty} \left[(i2\pi f)\cdot X(f)\right] e^{i 2 \pi f t} \mathrm{d}f\\
&=\mathcal{F}^{-1}\left[(i2\pi f)\cdot X(f)\right]
\end{align}
$$

It can be denoted as 
$$
{{\mathrm{d}}/{\mathrm{d}t}}\leftrightarrow i 2\pi f
$$

One can also extend this property to
$$
({{\mathrm{d}/}{\mathrm{d}t}})^n\leftrightarrow (i 2\pi f)^n
$$

In plasma physics, the conventional way to express the electromagnetic field.

It should be noted that this derivation property change a little bit for discrete Fourier transform:

$$
\begin{align}
\frac{\Delta x(t)}{\Delta  t}&=\int_{-\infty}^{+\infty}X(f) \frac{e^{2\pi i f (t+\Delta t)}-e^{2\pi i f t}}{\Delta t} \mathrm{d}f\\
&=\mathcal{F}^{-1}[\frac{e^{2\pi if \Delta t} - 1}{\Delta t}\cdot X(f)]
\end{align}
$$

| Property                   | Continuous-Time Fourier Transform (FT)                 | Discrete Fourier Transform (DFT/FFT)                         |
| -------------------------- | ------------------------------------------------------ | :----------------------------------------------------------- |
| Linearity                  | $\mathcal{F}\{a\,x(t) + b\,y(t)\} = a\,X(f) + b\,Y(f)$ | $\mathrm{DFT}\{a\,x[n] + b\,y[n]\} = a\,X[k] + b\,Y[k]$      |
| Time Shift                 | $x(t - t_0) \rightarrow X(f)\,e^{-j 2\pi f t_0}$       | $x[n - n_0] \rightarrow X[k]\,e^{-j 2\pi k n_0 / N}$         |
| Frequency Shift            | $x(t)\,e^{j 2\pi f_0 t} \rightarrow X(f - f_0)$        | $x[n]\,e^{j 2\pi k_0 n / N} \rightarrow X[(k - k_0)\bmod N]$ |
| Time-Domain Convolution    | $x(t) * h(t) \rightarrow X(f)\,H(f)$                   | $x[n] \circledast h[n] \rightarrow X[k]\,H[k]$ (circular)    |
| Time-Domain Multiplication | $x(t)\,h(t) \rightarrow X(f) * H(f)$                   | $x[n]\,h[n] \rightarrow X[k] * H[k] / N$                     |
| Derivative (Time Domain)   | $\frac{d^n x(t)}{dt^n} \rightarrow (j 2 \pi f)^n X(f)$ | $\Delta^n x[n] \rightarrow X[k] \cdot (e^{j 2 \pi k / N} - 1)^n$ |
| Conjugate Symmetry         | $x(t) \in \mathbb{R} \Rightarrow X(-f) = X^*(f)$       | $x[n] \in \mathbb{R} \Rightarrow X[N - k] = X^*[k]$          |
| Parseval's Theorem         | $\int |x(t)|^2\,dt = \int |X(f)|^2\,df$                | $\sum |x[n]|^2 = \frac{1}{N} \sum |X[k]|^2$                  |
| Spectral Periodicity       | $X(f)$ not periodic                                    | $X[k]$ is periodic with period $N$                           |
| Periodic Input Duality     | Periodic $x(t) \Rightarrow$ discrete $X(f)$            | Periodic $x[n] \Rightarrow$ sparse $X[k]$                    |

## Software Performance

The invention of the ***(Cooley–Tukey) Fast Fourier Transform (FFT) algorithm*** reduced the time complexity of DFT from $\mathcal{O}(N^2)$ to $\mathcal{O}(N\mathrm{log}N)$ by efficiently decomposing the DFT into smaller computations, i.e., [divide-and-conquer](https://en.wikipedia.org/wiki/Divide-and-conquer_algorithm).  

Most tutorials introduce the ***radix-2*** FFT, which splits the signal into ***two*** sub-signals with exactly the same length and requires the length of the signal to be an integer power of ***2***. This requirement is hard to satisfy in common applications without zero-padding, which actually includes unwanted modification of the original signal. To overcome that, ***radix-3*** and ***radix-5*** FFTs are developed and implemented. 

Still, the divide-and-conquer strategy fails when the signal length *N* consists of at least one big prime number factor (e.g, 10007) as the signal is hard to split. In that situation, the ***Bluestein's algorithm***, which is essentially a ***Chirping-Z transform***, is used. This algorithm takes the $\mathcal{F}$ operation as a convolution and then uses the *convolution theorem* in the calculation of DFT coefficients. The convolution property allows us to extend the signal length to a proper, highly composite number with zero-padding (denoted as *M*), but the coefficients and frequency resolution remain unchanged. The final time complexity of *Bluestein's algorithm* goes to $\mathcal{O}(N+M\mathrm{log}M)$, where the first term originates from the iterate all the frequency component.

<p align = 'center'>
<img src="Figure/figure_numpy_fft_performance.png" width="100%"/>
</p>



From the performance test, we observe that signals with prime-number lengths (dark red dots) often incur higher computational costs. For example:
$$
\begin{align}
N&=181=182-1=2^1\times\boxed{7^1\times13^1}-1\\
N&=197=198-1=2^1\times3^2\times\boxed{11^1}-1\\
\end{align}
$$
In contrast, signals with highly composite number lengths (dark blue dots), such as those with lengths being integer powers of 2, usually have the lowest computation time.

However, some prime numbers like: 
$$
\begin{align}
N&=191=192-1=2^6\times3^1-1\\
N&=199=200-1 = 2^3\times5^2-1
\end{align}
$$
can also exhibit relatively efficient performance due to their proximity to highly factorable numbers.

Modern implementation of the FFT algorithm, such as `pocketfft`, combines the above two methods (*Cooley–Tukey* and *Bluestein*). This *C++* package is used in both `numpy` and `scipy(1.4.0+)`  for their FFT implementation. Besides, `fftw`, which stands for the somewhat whimsical title of *"Fastest Fourier Transform in the West"*, is also very popular and used in the `fft/ifft` functions of *MATLAB*. Its *Python* implementation  can be found in the `pyfftw` package.

The `scipy.signal.fft` additionally provides an input parameter `workers:` *`int, optional`* to assign the maximum number of workers to use for parallel computation. If negative, the value wraps around from `os.cpu_count()`. For parallel computation, you need to input a batch of signals with shape of $N\times K$.

***<u>Reference</u>**:*

1. Cooley, James W., and John W. Tukey, 1965, “An algorithm for the machine calculation of complex Fourier series,” Math. Comput. 19: 297-301.
2. Bluestein, L., 1970, “A linear filtering approach to the computation of discrete Fourier transform”. IEEE Transactions on Audio and Electroacoustics. 18 (4): 451-455.
3. https://dsp.stackexchange.com/questions/24375/fastest-implementation-of-fft-in-c
4. https://www.fftw.org/

<div STYLE="page-break-after: always;"></div>