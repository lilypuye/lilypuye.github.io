---
title: What we talk about when we talk about Ray Tracing?
author: puye
date: 2023-09-06 21:20:00 +0800
categories: [Graphics]
tags: [Raytracing, HWRT, GI]
pin: false
comments: true
math: true
image: /assets/img/post/Raytracing.jpg
---

Let's provide a concise overview of ray tracing. We won't delve into subjects like Monte Carlo and stratified sampling, PBRT, BRDF, rendering equations, denoising techniques, or intricate SDF concepts. In essence, we'll stick to the fundamentals.

So, what is Raytracing?

# Raytracing Algorithm Framework

Ray tracing primarily serves as an algorithmic framework. Within this framework, numerous algorithms exist. They are not mutually exclusive and can be employed for various stages of ray tracing or in combination.


## Ray Marching

Within the ray tracing process, one of the most intricate tasks involves computing intersections between rays and the scene. When the scene comprises simple geometric shapes, accurately determining intersections through analytical expressions is relatively straightforward. However, scenes often feature complex models, making precise analytical intersection calculations challenging. If a method exists to easily ascertain whether a point "p" lies within the scene model, we can progressively move along the ray, step by step, checking if each point penetrates the model's interior. This approach allows us to identify the intersection between the scene and the model.

![Raytracing](https://pic3.zhimg.com/v2-0f1141e381947c58e45b1dfac25dbd12_r.jpg)

This is also a crucial application of Signed Distance Functions (SDF) in Ray tracing. With the help of SDF, we can determine the distance to march along a ray while ensuring that no intersections occur with the scene. This approach significantly minimizes unnecessary computational steps.

Ray marching finds extensive applications in real-time rendering, including volumetric clouds, volumetric fog, Screen Space Global Illumination (SSGI), Screen Space Reflections (SSR), and various screen-space effects.


![Raytracing](https://pic2.zhimg.com/v2-37cdd9deaa5a8d808b69290366659f3d_r.jpg)
![Raytracing](https://pic4.zhimg.com/v2-fd75f8885065b26a00dc5a5509113437_r.jpg)


## Path Tracing

Path Tracing is regarded as the quintessential Ray tracing algorithm. When we mention that specific offline rendering tools incorporate Ray tracing, it typically implies that they employ Path Tracing.

![Raytracing](https://pic4.zhimg.com/v2-0b0cb837a56a2f7fe824045f4567607b_r.jpg)

Path Tracing entails the process of originating a ray from the camera directed towards each pixel, subsequently computing multiple interactions, which may involve scattering, refraction, and reflection, occurring between the ray and objects within the scene. It ultimately tracks the ray's trajectory back to a light source, constituting a valid path. After aggregating all potential scenarios, it yields an exceptionally precise rendering result for the scene. When materials are intricate, and there's a need to trace various rough diffuse reflections in different directions, the computational workload can be quite substantial.

Path Tracing can be further categorized into Unidirectional Path Tracing and Bidirectional Path Tracing. Bidirectional Path Tracing involves emitting rays from both the camera and the light source into the scene, identifying intersections with objects, and linking these two intersection points. If there are no obstructions along this connecting line, then a valid path is established. This approach helps circumvent numerous fruitless traversals in path tracing, where rays may exceed the maximum reflection count without reaching a light source.

## Photon Mapping
![Raytracing](https://pic2.zhimg.com/v2-b5e39717002be3eb3e13ef281a335ac1_r.jpg)

Photon Mapping encompasses the process of emitting rays from a light source and positioning photons at the locations where these rays intersect with the scene. These photons are subsequently gathered and employed for illuminating the scene during the relighting process.

## Cone Tracing

![Raytracing](https://pic4.zhimg.com/v2-19c80ecde4e7758cbccaaea3b88c115b_r.jpg)
As for Cone Tracing, as the name implies, it doesn't involve emitting rays during the tracing process; instead, it utilizes cones to locate intersections. This approach notably reduces the number of rays that need to be traced. Cone tracing is often employed in conjunction with voxelized representations of the scene. There are also other applications, such as combining Cone Tracing with Signed Distance Functions to estimate soft shadows.

These algorithms, particularly Path Tracing and Photon Mapping, are commonly employed for Global Illumination calculations. However, the Ray Tracing framework can be applied to a wide array of other tasks beyond those mentioned earlier, including its use in occlusion culling algorithms and more.

# Render Pipeline
Ray tracing is also a rendering pipeline, and when we talk about the "Ray tracing pipeline," it signifies a concept that differs from the traditional rasterization pipeline.

![Raytracing](https://pic3.zhimg.com/v2-34cbdb004015ebffaa4772c717ba530a_r.jpg)

The two pipelines follow entirely distinct approaches. The rasterization pipeline involves discretizing triangles onto the screen, conducting depth sorting, and subsequently performing certain lighting calculations. Conversely, the Ray tracing pipeline encompasses casting a ray for each pixel, documenting the intersections between rays and triangles, calculating barycentric coordinates, UV mapping, and then conducting lighting computations. Naturally, in the Ray tracing pipeline, it's not limited to a single ray emission; it often proceeds with secondary rays based on the intersections found by primary rays. Ray tracing pipelines can handle various tasks such as global illumination, shadow calculations, reflection computations, and more, utilizing the outcomes of multiple ray intersections. For instance, a common technique in the rasterization pipeline, Shadowmap, can be replaced by casting rays towards the light source to ascertain whether a point is in shadow or not.


![Raytracing](https://pic1.zhimg.com/v2-023d827482225ae038e33f1a99bf9758_r.jpg)


Is the quality of the Ray tracing pipeline always better than the rasterization pipeline? I really like the comparison below; which one is the result of rasterization, and which one is the result of the Ray tracing pipeline?

![Raytracing](https://pic3.zhimg.com/v2-05b12c61b9a05da6b371beb837627c52_r.jpg)
（http://www.realtimerendering.com/erich/AnIntroductoryTourOfInteractiveRendering.pdf
http://web.cs.wpi.edu/~emmanuel/courses/cs563/S10/talks/wk10_p1_steve_Mirror_Reflection.pdf）

Typically, most individuals would assume that the image on the left is the result of the rasterization pipeline, and the one on the right is the outcome of the Ray tracing pipeline. However, in reality, the image on the left is actually the product of Ray tracing because it encompasses an infinite reflection effect between mirrors, which is challenging to achieve within a rasterization pipeline.

In a general sense, scenes requiring real-time rendering, such as games, tend to opt for the rasterization pipeline, whereas scenes allowing for slower, offline rendering are more inclined to utilize the Ray tracing pipeline.

So, is the rasterization pipeline always faster than the Ray tracing pipeline? It's commonly believed that real-time rendering aligns better with the rasterization pipeline. However, in my experiments, I've observed that if we exclude Secondary Rays and solely focus on Primary Rays—meaning each pixel performs just one intersection calculation, samples textures, and computes lighting, akin to a rasterized PBR shader—the frame rate can actually experience a slight improvement. This is because the Ray tracing pipeline doesn't need to concern itself with occlusion culling or the overhead of Drawcall submissions. If even the most intricate aspect of Ray tracing, which involves the cost of multiple reflections with Secondary Rays, remains deactivated, it's challenging to definitively declare which pipeline performs better.

Nonetheless, my testing environment is notably ideal, featuring a simple scene and neglecting any considerations for translucent rendering or post-processing techniques like TAA. When solely relying on ray intersection results and lighting computations without depth information and other factors, many additional effects become intricate to manage. Consequently, when we mention Hybrid pipelines these days, they often entail employing Ray tracing to generate a G-buffer and subsequently applying other effects based on it.

# Hardware Ray Tracing (HWRT)

Today, with the progress of hardware technology, when we mention that a particular AAA title incorporates Ray tracing, it commonly signifies the utilization of hardware ray tracing (HWRT).

![Raytracing](https://pic4.zhimg.com/v2-99c2b2f6e9c45e77513c632b4b4825fb_r.jpg)

Nvidia's Pascal architecture, particularly the GTX 10 series, did have the capability to handle ray tracing, but it lacked a dedicated RT core for accelerated ray tracing computations. Starting with the Turing architecture in the 20 series, Nvidia introduced RT cores, leading to the transition to the RTX series, which includes the RTX 30 series based on the Ampere architecture and the 40 series based on the Ada Lovelace architecture. Other manufacturers have also implemented hardware support for ray tracing, but how well do their offerings perform?

Let's examine this chart released by Imagination. Disregarding Imagination's Level 4 and Level 5, and focusing on the initial levels, we can gain a rough understanding of the accomplishments of various manufacturers.

![Raytracing](https://pic2.zhimg.com/v2-d16238cae71dd1d969f46ed316541bb9_r.jpg)

Imagination's classification outlines various levels of ray tracing support:

Level 1 (e.g., Apple and Mali): At this level, ray tracing is primarily software-based, lacking dedicated hardware acceleration designed specifically for ray tracing calculations. It relies on general-purpose computing.

Level 2 (e.g., AMD): Hardware at this level incorporates specialized acceleration for ray-box and ray-triangle intersection calculations. This means there are hardware features intended to enhance the performance of these fundamental ray tracing operations.

Level 3 (e.g., Nvidia RTX 30 series): Level 3 hardware accelerates both the management and traversal of the BVH (Bounding Volume Hierarchy) structure, which is crucial for efficient ray tracing.

Level 3.5 (e.g., Nvidia RTX 40 series): This level extends the capabilities of Level 3 by effectively addressing thread coherence issues. Achieving good thread coherence and sorting during BVH traversal is a significant feature at this level.

What is the issue of thread coherence?

![Raytracing](https://pic4.zhimg.com/v2-5a013dcd3d5d5a33a2935ba8f2a057e7_r.jpg)

Parallel rays, after reflecting or refracting through real materials, frequently disperse in entirely distinct directions. Alternatively, contemplate a group of parallel rays, where one, denoted as A, intersects with a model, while another, named B, does not. Consequently, the subsequent tracing calculations for A and B become entirely incongruous. Such disparities can pose significant challenges for the parallel architecture of GPUs.

## Raytracing API
To make use of hardware-accelerated ray tracing, you must utilize supported ray tracing APIs. Some examples include DX12's ray tracing API (DXR), along with Metal Ray tracing and Vulkan Ray tracing, among others.

I conducted a small survey, and I found that many of my peers have dabbled in ray tracing demos. However, most of them typically begin with CPU-based implementations in languages like C++. For example, the well-known tutorial series "Ray Tracing In One Weekend" frequently starts with CPU-based implementations.

![Raytracing](https://pic3.zhimg.com/v2-676ae687fbcf10dc3b541f0370dfb5f2_r.jpg)
Most tutorials usually begin with the basics, defining concepts such as rays and geometry. They then cover topics like scene management, accelerating ray-object intersections, random sampling, handling ray intersections, managing materials, and more. Obtaining a visually pleasing and gratifying image often requires several minutes or even longer.

Fewer students have experience with hardware-accelerated ray tracing. However, utilizing Ray tracing APIs significantly simplifies the process because you don't need to handle complex tasks like scene management and ray-object intersections on your own. For instance, consider Unity's DXR demo, where placing an elephant into a refrigerator involves just three steps:

1. Add all the Renderers that need to be traced into an AccelerationStructure.
![Raytracing](https://pic4.zhimg.com/v2-9d34d26a27e5bf5d7699cba53e81fcd7_r.jpg)

2. Write a shader to emit rays.
![Raytracing](https://pic1.zhimg.com/v2-1e8c343d06de0c44bda3ffd9165d12ec_r.jpg)

3. In the shader of renderers, handle what happens when rays intersect, such as performing lighting calculations or emitting the next ray.
![Raytracing](https://pic4.zhimg.com/v2-f0ff0f7d239111f61629c6e45ccc57a3_r.jpg)


It's indeed remarkably straightforward! The image below demonstrates the comparison using Unity's HDRP after integrating hardware-accelerated ray tracing.

![Raytracing](https://pic1.zhimg.com/v2-c7595f0b9443a8a27b8af3d46201845c_r.jpg)

Of course, it also comes with various bugs and crashes.

![Raytracing](https://pic3.zhimg.com/v2-11a9f695c618be9a7de46ea41d8ef142_r.jpg)


# Ray Tracing and Global Illumination

Global Illumination (GI) takes into account both direct and indirect lighting effects resulting from multiple reflections and refractions of light within a scene. At first glance, it might seem quite similar to the concept of Ray tracing.

![Raytracing](https://pic4.zhimg.com/v2-432b07f55e4e2174d045004a3b71ad3f_r.jpg)

What's the relationship between Ray tracing and Global Illumination (GI)? Someone may not differentiate between the two and think of them as the same thing. For instance, some might say, "We don't need to worry about a GI solution for our PC version; we can just use Ray tracing."

From the previous explanations, it's evident that Ray tracing and GI are distinct concepts. Ray tracing is not only an algorithm framework but can also refer to a rendering pipeline, and it's not solely used for GI solutions. In GI solution design and optimization, Path Tracing is often used initially to establish a Ground Truth, which guides algorithm optimization. GI solutions also frequently involve Ray tracing processes; for example, the VXGI solution employs Cone Tracing. Leveraging hardware-accelerated ray tracing is also a common approach.

The recent popular GI solution in UE5, Lumen, involves many steps that use Signed Distance Fields (SDF) to accelerate the Ray Marching process for tracing. 

![Raytracing](https://pic2.zhimg.com/v2-4c71f2364785fa22cdf4c18a1bf5bbcd_r.jpg)

Lumen supports using hardware ray tracing (HWRT) to optimize effects like specular reflections, but it also allows for not using HWRT.

So, does GI always require Ray Tracing algorithms? Not necessarily. For instance, the classical Radiosity algorithm divides the scene into multiple patches, estimates the indirect diffuse lighting influence between patches based on distance and angles, and then constructs a large equation. By solving this equation, it calculates the indirect diffuse for the entire scene. Ray tracing is just one approach to achieve GI, but there are other techniques to compute as well.

![Raytracing](https://pic4.zhimg.com/v2-f853e03bf428786b37cbbd663229113b_r.jpg)

# HWRT in Hogwarts Legacy

![Raytracing](https://pic1.zhimg.com/v2-54720138c1cd4a995108470a7ea59828_r.jpg)


In the article [Ray tracing on AMD's RDNA 2/3, and Nvidia's Turing and Pascal](https://chipsandcheese.com/2023/03/22/raytracing-on-amds-rdna-2-3-and-nvidias-turing-and-pascal/) there is a frame capture analysis of Hogwarts Legacy's Acceleration Structure. It can be observed that Hogwarts essentially employs a brute-force approach by including the entire scene, including dynamic objects, within the Acceleration Structure for hardware ray tracing.

![Raytracing](https://pic3.zhimg.com/v2-5a91b5768579d79392d700be8110a872_r.jpg)

![Raytracing](https://pic1.zhimg.com/v2-9aec7eef193ec48d64587db4e30949cc_r.jpg)




 Let's reference some comparative screenshots:

![Raytracing](https://pic1.zhimg.com/v2-4067e70e42db606ad495729617d8eed4_r.jpg)

![Raytracing](https://pic3.zhimg.com/v2-c7cae64166e0881637c59b89418d421e_r.jpg)

![Raytracing](https://pic1.zhimg.com/v2-506ff77cd4d1fe2d9cdd52542fd1adb8_r.jpg)



The improvements in reflections and shadows are notably pronounced. The Ambient Occlusion (AO) component also seems to create a more natural transition within the cabinet.

# In closing

Ray tracing is no silver bullet. While it can certainly enhance visual quality, it's not a one-size-fits-all solution. In practice, implementing ray tracing in a project can introduce a diverse set of challenges that require significant time and effort to overcome. Moreover, it may even face the risk of being perceived as a mere gimmick, inviting user criticism such as "it's better when it's off." There's still much work ahead! After all, Unity comes at a cost!

![Raytracing](https://pic2.zhimg.com/v2-2b0cee448d3971a9ecd778b6834cb7fd_r.jpg)





