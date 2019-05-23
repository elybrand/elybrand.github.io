--- 
layout: single 
title: Blind Deconvolution
author_profile: true
---

## Motivation
I remember as a first year student at UGA I was enrolled in a multivariable mathematics
class. At the time that class was impossibly difficult for me, but looking back I'm 
grateful for enrolling in it. A lot of key ideas that I would see later in my academic
career had seeds that were planted during that first year. The professor of that course,
Ted Shifrin, who is a good friend and mentor of mine now, is famously known by his students for incorporating geometry to a mathematical discussion whenever he finds the chance to do so. An extreme example of this is demonstrated by an Abstract Algebra text he authored titled "Abstract Algebra: A Geometric Approach." 

I remember working on some homework problems involving conic sections and coming across
a problem which outlined the mathematics behind what are known as "[whispering galleries](https://www.mathematicalsynergistics.com/gallery/2d-whispering-gallery/)."
Effectively these are rooms in the shape of an ellipse where two speakers can whisper to 
one another from across the room and hear each other perfectly clear. This is possible due
to the nature of how sound waves propagate and reflect off surfaces. If you click on the
link, you'll see a neat gif illustrating this phenomenon. 

It's worth re-emphasizing that whispering galleries only works due to the very special geometry of the room. Anyone who has tried having a conversation with a friend or colleague from across any normal room knows that a whisper likely isn't likely going to be heard. On the other extreme, one can imagine being inside a large, vacant room and shouting towards the other person. The problem with this latter scenario is that, depending on the material that the room is composed of, you're likely going to hear a lot of echos which makes discerning what was said difficult. 

If you'll humor me, imagine someone gives you a recording of someone talking in some unknown room where there are lots of echoes and reverberations and asks you what the person was saying. You might be able to piece together bits of what was said but if the recording is so poor due to the echoes you can imagine that the recording might be entirely uninterpretable. This problem of getting rid of echoes is formally known as *blind deconvolution*. 

The above example may seem artificial but, in fact, the scope of problems which can be classified as blind deconvolution is expansive. One important example is medical and astronomical imaging where the goal is to deblur a noisy image. Another example is communications engineering such as for the Internet of Things and for 5G networks where multiple devices are communicating with a single base station.

## A Mathematical Model
Let's return to the example of a person speaking to a single microphone in a room with poor acoustic design. As the person speaks, the sound waves from the speech emanate across the room, echoing off walls, and after some time reach the microphone. Let's call the function encoding the amplitude of the sound wave of speech \\(x(\cdot)\\), where the argument of the function is time.

[//]: # (Insert a diagram of the person speaking in the room.)

Now, if the microphone and the person were in a room with no wall then the microphone at time \\(t\\) would simply hear \\(x(t)\\) or perhaps a delayed signal \\(x(t-\tau)\\) accounting for the time it takes for the sound wave to reach the microphone. When we add walls, the picture is much more complicated. 

When sound waves echoe off a surface they will attenuate or dampen as a function of the material they echo off of. Let's model that by multiplication by some scalar, that is, after reflecting off of some fixed wall the echoed sound wave is now \\(a \cdot x(\cdot)\\). So if the person and the microphone were in an open space with one wall nearby then at time \\(t\\) the microphone would hear the sound travelling directly from the speaker \\(x(t)\\) but also the sound coming from the echo off the wall of an earlier part of speech \\(a \cdot x(t-\tau)\\), where the shift comes from the extra time it takes the sound wave to travel the extra distance from the speaker to the wall and the wall to the microphone. In other words, if we call the amplitude of the soundwave received by the microphone at time \\(t\\) \\(y(t)\\), we'd have \\(y(t) = x(t) + ax(t-\tau)\\).

Now when there are multiple walls there are many ways for the sound wave \\(x(\cdot)\\) to echo off and reflect back to the microphone. In fact, if there were no attenuation for echoing off a wall then the sound wave could echo off an arbitrarily large number of walls before reaching the microphone. In effect, that means at time \\(t\\) the microphone would receive

\\[ \begin{align}
y(t) = \sum\_{i=1}^{n(t)} a\_i x(t - \tau\_i).
\end{align}\\]

I'm a digital signal processor, so let's assume that we've discretized time that that our original signal \\(x(\cdot)\\) is now represented by a vector in \\(\mathbb{R}^L\\). If we store our attenuation coefficients, or our filter, in a vector \\(a \in \mathbb{R}^L\\), then we can rewrite the equation above as

\\[ \begin{align}
y\_j = \sum\_{i=1}^{L} a\_i x\_{i\ominus j} = a * x,
\end{align}\\]

where \\(\ominus\\) denotes circulant subtraction modulo \\(L\\) and \\(*\\) denotes circulant convolution.

So this explains part of the reason why this problem is called blind deconvolution. The reason for the world "blind" is because a priori *we don't know what either \\(a\\) or \\(x\\) is*! We don't know \\(a\\) because we don't have information regarding the composition of the room, and we don't know \\(x\\) because that's the speech we're trying to retrieve from the recording \\(y\\).

