--- 
layout: single
author_profile: true
---

I am a fourth year mathematics Ph.D. candidate at the University
of California, San Diego. [Rayan Saab](http://www.math.ucsd.edu/~rsaab/) is my adviser.
I received my bachelor’s degree in mathematics at the University of Georgia in 2015.
For anyone who is interested, here is a copy of my [CV]({{ "/assets/files/CV.pdf" | absolute_url }}).

### Research Interests
My research interests lay primarily in digital signal processing, and quantization. 
Classical sampling theory is predicated on reconstructing bandlimited functions from analog (i.e. non-quantized)
samples sampled at a rate exceeding the [Nyquist rate](https://en.wikipedia.org/wiki/Nyquist%E2%80%93Shannon_sampling_theorem).
In practice though, these measurements must be quantized in order to be stored and processed on computers.
Moreover, there are many instances where taking too many samples is expensive such as in medical imaging, for example.
A central goal of my research is to study how to approximately reconstruct structured signals embedded in a high dimensional space
from very few quantized measurements. Importantly, I am interested in quantization schemes where the error incurred from
quantization [decays polynomially or exponentially fast](https://elybrand.github.io//quantization/) with respect to the number of measurements.

The advent of compressed sensing taught the mathematics and engineering communities that the most effective sampling
schemes to use when undersampling (sampling below the Nyquist rate or extrinsic dimension of the set of signals in question) structured signals are random. As a result, I am also interested in random matrix theory.
In the non-asymptotic case, I'm interested in random matrix ensembles as seen in the compressed sensing literature as 
well as non-linear random measurement operators which appear in the phase retrieval and blind deconvolution settings.
I am also interested in the asymptotic case, where one uses tools from free probability
to study the empirical spectral distributions of random linear operators. The latter case appears
in modeling large scale communication networks with random network topologies, as well as in MIMO systems.

In the spring of 2017 the signal processing group at UCSD went through recent advancements in deep learning
from the perspective of applied harmonic analysis. I've written a [blog post]({{ "/sparse_net/" | absolute_url }}) about one of the papers
we read if you're looking for a nice introduction.

As an undergraduate, I worked with [Jason Cantarella](https://www.jasoncantarella.com/wordpress/)
on computational knot theory.