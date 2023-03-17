---
title: Introduction to Spherical Harmonics
author: puye
date: 2023-02-05 21:55:00 +0800
categories: [Graphics, Tutorial]
tags: [SH, Tutorial]
pin: false
comments: true
math: true
image: https://picx.zhimg.com/v2-4908d0a071c9df111a1fe00ee0ab5bbd_r.jpg
---

This article aims to explain **Spherical Harmonics** in simple terms without using mathematical jargon. It also discusses how **SH** is applied in **Radiometric/Photometric** contexts.

**SH**, or **Spherical Harmonics**, is just a set of basis functions. I won't go into the details of how **SH** is derived here.

If you've learned about **Taylor Expansion** or **Fourier Transform** in elementary school, you'll be familiar with basis functions. If your elementary school didn't cover that, it's not a problem.

Consider **Polynomial Basis Functions** as an example:

$ y_{0} = 1 $

$ y_{1} = x $

$ y_{2} = x^2 $

$ y_{3} = x^3 $

....

**Trigonometric Basis Functions** for another example:

$y_{0} = 1 $

$y_{1} = sin(x)$

$y_{2} = sin(2x)$

$y_{3} = sin(4x)$

....

![TheBaseFunc](https://pic1.zhimg.com/v2-ab9c4891e80f3ac3f68713e74e16fde4_r.jpg)


Or you can create any basis function you prefer:

$y_{0} = 555$

$y_{1} = \frac{1}{x} + tan(x)$

$y_{2} = x^3 - 666$

$y_{3} = sin(4x)$

....

With definitions of Basis Functions, we can represent any function as a weighted sum of these Basis Functions.

e.g.

$y \approx 0.1 y_0 + 0.3 y_1 + 0.8 y_2 + 0.001 y_3+...$

Here the original function

$y = f(x)$

is split into a set of coefficients:

0.1, 0.3, 0.8, 0.001, ...

In general, having more coefficients for the basis functions leads to a higher accuracy in representing the original function.

Think of it like lossy compression. Both you and I have a codebook containing predefined basis functions. When you need to send a function to me, you only need to send a few coefficients. I can then reconstruct the original function, albeit with some loss of accuracy.

Take the **Square Wave Function** as an example. The black function cannot be precisely represented by a combination of **Sine Basis Functions**. However, using more **Sine Basis Functions** results in a closer approximation.

![TheBaseFunc](https://pic2.zhimg.com/v2-87770114c85764fece35bfb23e2c00b5_r.jpg)

The functions above are in **2D Cartesian Coordinates**. When it comes to **Polar Coordinates**, examples are as follows:

$ r_0 = 1 $ （blue）

$r_1 = cos θ $ （green）

$r_2 = sin θ$ （purple）

$r3 = ...$

![TheBaseFunc](https://pic2.zhimg.com/v2-9c39b11a8583211922f25dab5720f3c9_r.jpg)

**3D Cartesian Coordinates** are similar. The original function might look like this:

![TheBaseFunc](https://pic1.zhimg.com/v2-9af6a90dc9574af5b23adc4c005d59b4_r.jpg)

The Basis Functions might look like this:

$z_0 = f_0(x, y) $

$z_1 = f_1(x, y) $

$z_2 = f_2(x, y) $

...

Finally, we arrive at the **Spherical Coordinates**.

The functions for **Spherical Coordinates** look like this:

![TheBaseFunc](https://pic4.zhimg.com/v2-17b532bdb46a0d30b9eaf0e45f2df733_b.jpg)


If we use color luminance instead of distance to represent the function value, the images would look like this:

![TheBaseFunc](https://pic1.zhimg.com/v2-663f3ac6abb50526a2019acb0c1b28b0_r.jpg)

Basis Functions on **Spherical Coordinates** are like this:

$r_0 = f_0(θ，φ) $

$r_1 = f_1(θ，φ) $

$r_2 = f_2(θ，φ) $

...


The most well-known basis function of **Spherical Coordinates** is **Spherical Harmonics**. It possesses numerous beneficial properties, such as **Orthogonality** and **Rotation Invariance** (which will be explained in other articles). **Orthogonality** implies that each basis function is independent and cannot be represented as a weighted sum of other basis functions.

The **SH** basis functions look like this (blue for positive, yellow for negative). Anyone who has attempted to learn **SH** has likely encountered this:


![TheBaseFunc](https://pic3.zhimg.com/v2-1ea9c5bac926b47e7410a4a73a91070a_r.jpg)


**SH Expressions** are:

![TheBaseFunc](https://pic1.zhimg.com/v2-096dacfb295b80bd42ccab4e07512c3c_b.jpg)


At first glance, it feels like this:

![TheBaseFunc](https://pic3.zhimg.com/v2-6540b01fe06220d21a78870a89a46e06_b.jpg)

It's easier if we return to the **Polar Coordinates**. **2D SH** might look like this (Blue for positive, yellow for negative):

(Coefficients are not accurate, they're just for illustrative purposes)

![TheBaseFunc](https://pic3.zhimg.com/v2-a23b03aaed71ad57e133211ef4d19afe_r.jpg)

Things become easy then. Do these lobes resemble the 3D **SH** Basis Functions?

(Small Question: Why are there only 2 Basis Functions in the third row?)

Let's imagine a function in Polar Coordinates like this:

![TheBaseFunc](https://pic4.zhimg.com/v2-2942f917515e32af8d65d75ea85cc27b_r.jpg)

It can be represented as:

$r = 0.5 + 0.1 cos θ + 0.07 sin θ + 0.05 cos θ sin θ + 0.3(2cos^2θ - 1)$

With just the coefficients, this function is compressed into:

0.5， 0.1， 0.07， 0.05， 0.3

Now we obtain **SH coefficients** in **Spherical Coordinates**.

With more **SH coefficients**, we can represent the original spherical function with greater accuracy.

![TheBaseFunc](https://pic2.zhimg.com/v2-447fa3cffce97c4d95fd924c3e4ce5b9_r.jpg)

Typically, the second or third order of **SH** is used to represent illumination. The 2nd order requires 4 coefficients, while the 3rd order requires 9 coefficients.

![TheBaseFunc](https://pic4.zhimg.com/v2-411c4a303d19cd6ce40427c044f21817_b.jpg)

If 2nd order **SH** is applied to 3 colors RGB, we will need 4 * 3 = 12 coefficients.

![TheBaseFunc](https://pic3.zhimg.com/v2-288c6cb3e93e89bc4084945d3ae2225a_r.jpg)

If 3rd order **SH** is applied to 3 colors RGB, we will need 9 * 3 = 27 coefficients.

With a series of **SH** coefficients for each Probe, the illumination at a given position can be approximated.

Why not use a higher order of SH? On one hand, it increases the storage and computational demands, especially in games. 3rd order is often sufficient for smooth environmental lighting. On the other hand, higher-order **SH** can introduce artifacts, which artists may perceive as bugs.


![TheBaseFunc](https://pic4.zhimg.com/v2-70396fd2f0a9c3d2842a0c1dae9bfadf_r.jpg)

Are there **Spherical Basis Functions** easier to understand? Yes, **Spherical Gaussian** may be introduced next time.

