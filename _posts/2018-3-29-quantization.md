---
layout: single
title: Analog to Digital Conversion & \\(\Sigma\Delta\\) Quantization
author_profile: true
---

A necessary step in the signal processing pipeline involves taking analog signals, or, mathematically speaking,
real valued functions defined on \\(\mathbb{R}\\) and representing them digitally as bit strings. We take this for
granted when we listen to a song on our phone or watch a movie on our computer. It's the kind of thing that some mathematicians
like to ignore and leave for the engineers to figure out. And yet, there's a lot of serious math that goes on
behind the scenes to make sure that song you're listening to doesn't go haywire as your phone reads through the bitstream
encoding it.

To set things up, suppose we have a function \\(f: [-T, T] \to \mathbb{R}\\) which represents 
some natural signal we'd like to encode. Since humans can only see wavelengths between
\\(390\\) and \\(700\\) nm and can only hear frequencies between 20 and 20,000 Hz it's safe
to assume \\(f\\) is *bandlimited*, or that it's Fourier transform is compactly supported
in some set \\([-\Omega, \Omega]\\). There is a classic result known as the [Shannon-Nyquist](https://en.wikipedia.org/wiki/Nyquist%E2%80%93Shannon_sampling_theorem) 
sampling theorem which states that \\(f\\) can be encoded without *distortion*, or error, given samples
\\( \\{f(x\_i)\\} \\) sampled at a rate of \\( (2\Omega)^{-1} \\). In other words, if we were to think in terms of
seconds as our unit of time, we'd need \\((2\Omega)^{-1}\\) samples per second for perfect reconstruction. Specifically, the theorem
states that
\\[f(t) = \sum_{n\in\mathbb{Z}}f\left(\frac{n\pi}{\Omega}\right) \text{sinc}(\Omega t - n\pi),\\]
where \\(\text{sinc}(x) = \frac{\sin(x)}{x}\\). It can be shown that this lower bound on the sampling
rate is sharp. That is, there exist bandlimited functions which cannot be reconstructed by this scheme if the sampling
rate is less than \\((2\Omega)^{-1}\\). It is for this reason that this critical threshold rate is referred to as the
Nyquist rate. Practically speaking, most sensors sample at a rate above the Nyquist rate to prevent aliasing and increase resolution.

It's worth mentioning that this reconstruction scheme is far from optimal. The reason being that
sinc decays like \\(x^{-1}\\), so any error in measuring \\( f\left(\frac{n\pi}{\Omega}\right)\\)
is going to accumulate even at higher frequencies. There is a rich field of math which explores similar
ways of reconstructing signals called [frame theory](https://en.wikipedia.org/wiki/Frame_(linear_algebra)).
In particular, [Gabor frames and wavelets](https://en.wikipedia.org/wiki/Overcompleteness) are great examples of how frame theory has come to shape
modern day signal processing.

What's important is that we've reduced a real-valued function down to (finitely many) point
samples, i.e. a vector over \\(\mathbb{R}\\). In some sense, this is why in fields like compressed
sensing the signals of interest are always vectors instead of continuous functions. Generalizing the discussion above,
in place of pointwise evaluations we suppose we have *linear* measurements \\( y = Ax \\), where
\\( x \in \mathbb{R}^N \\), and \\( A \in \mathbb{R}^{m\times N} \\) is some linear map. 
We're still not done quantizing, since these measurements take on values
in the continuum. One way or another, we'll need to estimate \\(y\\) from vectors in a discrete set, sometimes
called an *alphabet*, \\(\mathcal{A}\\). The natural first guess is to make a uniformly spaced grid over
\\( [-B,B] \\) for some chosen \\(B\\) and just round each component of \\(y\\).

The above scheme is often referred to as Memoryless Scalar Quantization (MSQ). We'll see in a bit why it's called memoryless. 
Now, if we want our grid to have resolution \\( \delta \\), we'll need \\( m\log\left(\frac{B}{\delta}\right) \\) bits.
The number of bits is sometimes called the *rate*. For fixed \\(m\\) and letting \\( \mathcal{R} = m\log\left(\frac{B}{\delta}\right)\\), we find
that the quantization error for one component is bounded by \\( \delta = B 2^{-\frac{\mathcal{R}}{m}}\\). In other words, the distortion from quantizing using MSQ decays
exponentially with the rate. So we might think this is the best we could do and call it a day. What if, however, we were working
with a circuit which had low storage capacity and could only expend, say, 8 bits. Are we stuck with a mediocre distortion bound?

Fortunately the answer to the above question is no. Just as oversampling improves resolution in analog to digital conversion 
increasing \\(m\\) will lead to lower distortion in recovering \\(x\\). [Goyal and coauthors showed](http://ieeexplore.ieee.org/abstract/document/650985/) 
that for MSQ the error in reconstructing \\(x\\) as a function of \\(m\\) cannot decay faster than \\(O(m^{-1})\\). There are, however, quantization schemes which, like MSQ, enjoy
an exponentially decreasing relationship between distortion and rate but also have a distortion which decays like \\(O(m^{-r})\\) where
\\(r\\) is any positive integer. Unlike MSQ these encoding schemes "remember" quantization error.

These other quantization schemes fall under the category of [noise shaping](https://en.wikipedia.org/wiki/Noise_shaping).
I will focus particularly on \\(\Sigma\Delta\\) quantization. Whereas MSQ quantizes \\(y\_i\\) simply based on the value
that \\(y\_i\\) takes, \\(\Sigma\Delta\\) quantizers feature a state variable \\(u\\) which encodes the quantization
error of the previous \\(r\\) components of \\(y\\) and uses this state variable to quantize the values of \\(y\\).
Given a fixed positive integer \\(r\\), an alphabet \\(\mathcal{A}\\), and a function \\(\rho\_r: \mathbb{R} \to \mathbb{R}\\),
\\(y\_i\\) is quantized as
\\[ \begin{align}
q\_i &= Q\_{\mathcal{A}}(\rho\_r(u\_{i-1},..., u\_{i-r}, y\_{i},..., y\_{i-r+1})) \newline
(\Delta^r u)\_i &= y\_i - q\_i 
\end{align}
\\]
where \\( (\Delta u)\_i = u\_i - u\_{i-1}\\) is the difference operator and \\(Q\_{\mathcal{A}}\\) rounds to the nearest
point in \\(\mathcal{A}\\). For logistical reasons, one needs to choose \\(\rho\_r\\) carefully to ensure that 
\\( \\|u\\|\_{\infty} \leq C \\). [Daubechies and DeVore](https://services.math.duke.edu/~ingrid/publications/annals-v158-n2-p09.pdf)
proved this holds for a particular family of \\(\rho\_r\\), namely
\\[ \rho\_r(u\_{i-1},..., u\_{i-r}, y\_{i},..., y\_{i-r+1}) = y\_i + \sum\_{j=1}^{r} (-1)^{j-1} {r\choose j} u\_{i-j}. \\]
This is all rather opaque, but for the simple case of \\(r = 1\\), solving the recurrence relation for the \\(u\\) terms
yields
\\[ \begin{align}
q\_i = Q\_{\mathcal{A}}\left(\sum\_{j=1}^{r+1} y\_{i-j+1} - \sum\_{j=1}^{r} q\_{i-j} \right)
\end{align}\\]
and for \\( r = 2 \\) we have
\\[ \begin{align}
q\_i = Q\_{\mathcal{A}}\left(\sum\_{j=1}^{r+1} j y\_{i-j+1} - \sum\_{j=1}^{r} (j+1) q\_{i-j} \right)
\end{align}\\]
As a mathematician, I am tempted to interpret this in terms of quantizing the difference of \\(0^{th}\\)
moments for \\(r = 1\\) and the difference in means for \\(r = 2\\). Unfortunately this explanation is disingenuous
and doesn't hold up for \\(r > 2\\). It appears to be the case that the coefficients in the summands are 
[simplicial numbers](http://oeis.org/wiki/Simplicial_polytopic_numbers) which, to the best of my knowledge, have no
important applications in quadrature rules or estimating moments/cumulants. 

I think engineers view this in terms of high-pass and low-pass filters. Namely, if the difference between \\(y\_{i-1}\\) 
and \\(q_{i-1}\\) is significant due to quantization error, the rate of change of \\(\sum\_{j=1}^{r+1} y\_{i-j+1} - \sum\_{j=1}^{r} q\_{i-j}\\) isn't likely
to be as large as that of \\(y\_{i-1} - q\_{i-1} = (\Delta^r u)\_{i-1}\\). Appealing to mathematical intuition, this is simply because integration
is a global operator whereas differentiation is a local operator and therefore victim to the tiniest of perturbations. As such, \\(q\\)
is going to be composed of lower frequencies compared to the quantization error encoded in \\(u\\). Pushing error into higher frequencies is, after all,
the name of the game in noise shaping.

Long story short, noise shaping techniques like \\(\Sigma\Delta\\) enjoy favorable distortion decay
as you oversample. Namely, if you use the above \\(r^{th}\\) order \\(\Sigma\Delta\\) scheme with the an appropriately chosen alphabet
(see Section 2 in [this paper](https://arxiv.org/pdf/1306.4549.pdf) for a more thorough treatment; importantly, the one bit alphabet and uniform grids
as mentioned above are included as examples), then the reconstruction
error (in Euclidean norm) for recovering \\(x\in \mathbb{R}^N\\) from \\(q\\) decays like \\(O(m^{-r})\\). 
Let me repeat: this is true even in the extreme case where your alphabet is \\(\\{-1,1\\}\\), i.e. 
*one bit \\(\Sigma\Delta\\) quantization preserves information 
about the length of the vector \\(y\\)* while MSQ clearly fails to do so.

If by now you're on the \\(\Sigma\Delta\\) bandwagon and want to incorporate it into any of your projects, feel free to use some 
[MATLAB code](https://github.com/elybrand/SigmaDelta) I wrote. For recent applications of \\(\Sigma\Delta\\) quantization
in compressed sensing, I'll shamelessly promote [this work](https://arxiv.org/abs/1709.09803) on recovering low rank matrices with quantization 
and [this work](https://arxiv.org/abs/1801.08639) which uses \\(\Sigma\Delta\\) quantization with structured random matrices in compressed sensing.