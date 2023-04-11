---
title: Be a Product Manager! - An Example of Using GPT to Debug RTX Bugs


author: puye
date: 2023-04-11 23:38:00 +0800
categories: [Graphics]
tags: [GPT, RTX, debug]
pin: false
comments: true
math: true
image: /assets/img/post/ProductManager.png
---

Here's the Chinese version:

[这次我是产品经理---用GPT辅助查RTX的bug实例](https://zhuanlan.zhihu.com/p/620738881)

In this article, I will discuss how I successfully debugged some RTX code using Cursor.

Debugging DX12 RTX code can be quite challenging. While Renderdoc is a tool we are familiar with, it does not support RTX. We typically use Nsight or PIX, but their debugging capabilities are limited. We can only view information such as acceleration structure and hit table, and cannot debug step by step. (If you know of a way to do this, please let me know!)


![ProductManager](https://pic1.zhimg.com/80/v2-497be0aabdb514a94b22ef791dde87cc_720w.webp)

What can you do if you want to debug the details? You can output information such as the intersection results of the light rays, positions, and normals to a texture for debugging:

![ProductManager](https://pic3.zhimg.com/80/v2-bdfffa9cf5fc9b0e605544356d5a963a_720w.webp)

However, this method is still not very intuitive. While I can see what the RTX result looks like on the texture, it's difficult to determine the specific value of the light rays in a particular direction. Currently, I can only rely on logs, but using logs for output is even more unintuitive and it can be challenging to locate the correct log among a large number of logs.

For instance, I encountered a bug where the RTX result appeared mosaic on a flat stone.

![ProductManager](https://pic2.zhimg.com/80/v2-e1199dcb6cb9a11560565ee1017cbcc5_720w.webp)

In summary, the RTX accumulation result at certain positions is not continuous. Why is this happening?

What can we do? Why not let GPT help us write a debugging tool! For example, a tool like this:

![ProductManager](https://pic4.zhimg.com/80/v2-18b5acf1bd2cebdbad314f6ab148b37f_720w.webp)

In fact, this tool has gone through many iterations. I am like a product manager holding a small whip and constantly changing requirements, and GPT is like a hardworking programmer who is willing to do anything, and doesn't even ask me to sign a contract (laughs).


Me: Draw all the rays in every direction!

GPT: Done.

Me: It's too messy, let's not draw the rays that didn't hit anything!

GPT: Done. <b>(Used Raycast.hit to determine and help you clip the rays)</b>

Me: Add the number of hits for each ray!

GPT: Done. <b>(Displayed it using 3D UI)</b>

Me: Use different colors to distinguish between different materials that were hit, and count them separately!

GPT: Done. <b>(Used a dictionary to map materials to colors)</b>

Me: I can't see anything. Just read back the RTX result and display it on the ray color!

GPT: Done.

Me: The display is correct, but I can't see where the bug is. Display the accumulated color results too!

GPT: Done.

Me: Distinguishing colors by material is useless, let's delete it.

GPT: Done.

Me: Counting the number of ray hits is useless, let's delete it.

GPT: Done.

Me: Oh, by the way, add a snap function to snap to the integer point I need to debug!

GPT: Done.

Me: Don't write the tool too poorly, remember to clean up the garbage collection.

GPT: Done.


Perfect!

![ProductManager](https://pic2.zhimg.com/80/v2-e83d4898c6c93fbf38ccffb04abb6e29_720w.webp)

This tool is like having a metal detector, allowing you to easily detect the source of the problem.

![ProductManager](https://pic3.zhimg.com/80/v2-9a50f7d3d4d7033f5e92209246916a7a_720w.webp)

After completing this tool, the color of each ray in every direction represents the color of the indirect light coming from that direction. When I used the metal detector in the area where the mosaic appeared, I found that some directions should be able to receive indirect light normally, but the rays are displayed as black?


![ProductManager](https://pic1.zhimg.com/80/v2-6495d85ba5621eefe950f69190b667c8_720w.webp)

After modifying the RTX result displayed by the rays to return the texture color instead of the light color, all the colors appeared normal. Therefore, the issue must be with the second reflection calculation! Additionally, rays that are more parallel to the ground are less likely to have problems. Could it be because the second reflection cannot penetrate and hit the ground itself?

After considering this feature, I attempted to solve the problem by modifying the rayDescriptor.TMin to a larger value, and it worked!



![ProductManager](https://pic4.zhimg.com/80/v2-5d8013a3d19c597860eaa6d56de25c0f_720w.webp)

This time, using AI to write code is not just a gimmick. It's truly productive now.







