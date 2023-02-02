---
title: Introduction to Spherical Harmonics
author: puye
date: 2023-01-26 21:55:00 +0800
categories: [Graphics, Tutorial]
tags: [SH, Tutorial]
pin: false
comments: true
math: true
---

This article tries to explain **Spherical Harmonics** in simple words without mathematical terms. And how SH is applied to describe the Radiometric/Photometric.

SH, Spherical Harmonics, is just a set of Basic Functions. I will not explain here how SH is deduced.

If you have learned Taylor Expansion or Fourier Transform in Primary School, you will be familiar with Basic Functions. If your Primary School skipped that, it doesn't matter.

Take Polynomial Basis Functions for example:

$ y_{0} = 1 $

$ y_{1} = x$

$ y_{2} = x^2$

$ y_{3} = x^3$

....

Trigonometric Basis Functions for another example:

$y_{0} = 1 $

$y_{1} = sin(x)$

$y_{2} = sin(2x)$

$y_{3} = sin(4x)$

....

![TheBaseFunc](https://pic1.zhimg.com/v2-ab9c4891e80f3ac3f68713e74e16fde4_r.jpg)


`

Or you can make up any basic functions you like:

$y_{0} = 555$

$y_{1} = \frac{1}{x} + tan(x)$

$y_{2} = x^3 - 666$

$y_{3} = sin(4x)$

....

With definitions of Basic Functions, we can describe any function as a weighted sum of Basic Functions.

e.g.

$y \approx 0.1 y_0 + 0.3 y_1 + 0.8 y_2 + 0.001 y_3+...$

Here the original function

$y = f(x)$

is split into a set of coefficients:

0.1, 0.3, 0.8, 0.001, ...

Generally, more Basic Function Coefficients mean higher accuracy to represent the original function.

It's like lossy compression. Both you and I have a Codebook written with predefined Basic Functions. When you need to send a function to me, you need only send several coefficients. I can reconstruct the original function, with some loss of accuracy.

Take the Square Wave Function for example. The black function cannot be exactly represented by some Sine Basic Functions. However, more Sine Basic Functions make the simulation closer.

![TheBaseFunc](https://pic2.zhimg.com/v2-87770114c85764fece35bfb23e2c00b5_r.jpg)

The functions above are in 2D Orthogonal Coordinates. When it comes to Polar Coordinates, examples are like:


$ r_0 = 1 $ （blue）

$r_1 = cos θ $ （green）

$r_2 = sin θ$ （purple）

$r3 = ...$

![TheBaseFunc](https://pic2.zhimg.com/v2-9c39b11a8583211922f25dab5720f3c9_r.jpg)

3D Orthogonal Coordinates are similar. The original function may be like this:

![TheBaseFunc](https://pic1.zhimg.com/v2-9af6a90dc9574af5b23adc4c005d59b4_r.jpg)

The Basic Functions may be like this:

$z_0 = f_0(x, y) $

$z_1 = f_1(x, y) $

$z_2 = f_2(x, y) $

...

At last, we come to the Spherical Coordinates.

The functions of Spherical Coordinates are like this:

![TheBaseFunc](https://pic4.zhimg.com/v2-17b532bdb46a0d30b9eaf0e45f2df733_b.jpg)


If we don't use the distance to express the function value but use the color luminance to express it, images are like this:

![TheBaseFunc](https://pic1.zhimg.com/v2-663f3ac6abb50526a2019acb0c1b28b0_r.jpg)

Basic Functions on Spherical Coordinates are like this:

$r_0 = f_0(θ，φ) $

$r_1 = f_1(θ，φ) $

$r_2 = f_2(θ，φ) $

...

The most famous Basic Function of Spherical Coordinates is Spherical Harmonics. It has a lot of good properties like Orthogonality and Rotation Invariance(which will be explained in other articles). Orthogonality means every Basic Function is independent, and cannot equal a weighted sum of other Basic Functions.

The SH Basic Functions are like this(Blue for positive, yellow for negative). Anybody who tried to learn SH may have seen this:


![TheBaseFunc](https://pic3.zhimg.com/v2-1ea9c5bac926b47e7410a4a73a91070a_r.jpg)


SH Expressions are:

![TheBaseFunc](https://pic1.zhimg.com/v2-096dacfb295b80bd42ccab4e07512c3c_b.jpg)


The feeling at first glance is like this:

![TheBaseFunc](https://pic3.zhimg.com/v2-6540b01fe06220d21a78870a89a46e06_b.jpg)

It is easier if we go back to the Polar Coordinates. 2D SH may seem like this (Blue for positive, yellow for negative):

（Coefficients are not accurate, just for schematic purposes）

![TheBaseFunc](https://pic3.zhimg.com/v2-a23b03aaed71ad57e133211ef4d19afe_r.jpg)

Things become easy then. Does this lobe seem like the 3D SH Basic Functions?

(Small Question: Why only 2 Basic Functions in the third row ?)

Let's imagine a function in Polar Coordinates like this:

![TheBaseFunc](https://pic4.zhimg.com/v2-2942f917515e32af8d65d75ea85cc27b_r.jpg)

It can be represented by:

$r = 0.5 + 0.1 cos θ + 0.07 sin θ + 0.05 cos θ sin θ + 0.3(2cos^2θ - 1)$

With only coefficients, this function is compressed into:

0.5， 0.1， 0.07， 0.05， 0.3

Now we get SH coefficients in Spherical Coordinates.

With more SH coefficients, we can express the original spherical function with higher accuracy.

![TheBaseFunc](https://pic2.zhimg.com/v2-447fa3cffce97c4d95fd924c3e4ce5b9_r.jpg)

Usually, the second or third order of SH is used to express illumination. 2nd order SH needs 4 coefficients. 3rd order needs 9 coeffeicients.

![TheBaseFunc](https://pic4.zhimg.com/v2-411c4a303d19cd6ce40427c044f21817_b.jpg)

If 2nd order SH is applied on 3 colors RGB, we will need 4*3 = 12 coefficients.

![TheBaseFunc](https://pic3.zhimg.com/v2-288c6cb3e93e89bc4084945d3ae2225a_r.jpg)

If 3rd order SH is applied on 3 colors RGB, we will need 9 * 3 = 27 coefficients.

With a series of SH coefficients for every Probe, the illumination of the position can be described approximately.

Why not use higher order of SH? On one hand, it brings in higher pressure for storage and calculation, especially in games. 3rd order is often enough for smooth environment lighting. On the other hand, higher-order of SH comes with artifacts, and artists may consider them as bugs.


![TheBaseFunc](https://pic4.zhimg.com/v2-70396fd2f0a9c3d2842a0c1dae9bfadf_r.jpg)

Are there Spherical Basic Functions easier to understand? Yes, Spherical Gaussian may be introduced next time.

