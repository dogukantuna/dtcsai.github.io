---
title: Highly Efficient DL-primitives for GPU kernels
date: 2021-10-27
permalink: /posts/2021/10/highly-efficient-deep-learning-primitives-for-gpu-kernels/
tag: 
    - deep learning
pin: true
---

To be honest, I always find GPU programming incredibly fascinating because it expands our capabilities in every field of science, engineering, and technology. Since the past decade, GPUs have become an essential part of providing processing power for high-performance computing applications over the recent years. This progress has been ongoing for years and currently as we all know GPUs have gained the ability to do some truly incredible things, but they have recently gotten a lot of attention due to extreme deep learning and crypto mining needs, in addition to gaming. That's why programming a GPU has turned into a really important thing and has always been an important task to make better GPU code optimizations, development of specialized GPU kernels/cores, graphics/video/image/signal processing, etc. Aside from its importance, programming GPUs has always been too difficult. Hardware providers, open-source groups, startups, individuals have developed various GPU programming interfaces/parallel programming architectures to make this coding attempt more cleaner, easier. Today, we have several high-level programming languages, libraries, models, or architectures that provide access to the GPU directly for accelerating specific sets of problems and algorithms. A few of these are CUDA, Triton, OpenCL, OpenACC, Nyuzi Processor, emu… 

However, if you're looking for more awesome GPU programming projects, please take look at [this](https://medium.com/r/?url=https%3A%2F%2Fawesomeopensource.com%2Fprojects%2Fgpu-computing). 

I just want to discuss two of them in this blog post: CUDA and Triton.

Anyway, let's get it for a trip now to neural networks and learn about why efficient, specialized GPU kernels are used so heavily in the deep learning field. We all know that GPUs are well-suited units for parallel computing. Which is why deep learning uses them. Due to its unique requirements, deep learning requires a lot of computational power to run on and may require the use and customization of many different techniques, architectures, and technologies atop almost all of its processes. This type of customization requirement is most often needed in GPU code development for tiled neural network computations. However, especially in the context of GPU optimizations for DL, a developer or researcher should be familiar with all of these concepts and their essential technologies. Learning advanced technologies is becoming more difficult, especially in GPU/DL development. If you are not familiar with how you can use these advanced computing techniques and must perform, it will take some time to learn and perform because the concepts are complex and programming is rigorous. 

But this is where CUDA and Triton comes into play.

NVIDIA's CUDA (or Compute Unified Device Architecture) is a model that's been here since 2007, but the OpenAI's Triton 1.0 is pretty new, actually here for since 2019 and open-sourced in July 2021 with introduction. The philosophy behind this clearly explains why Triton is such an amazing alternative to the CUDA software platform. CUDA is legendary, okay, but Triton is also too. The most important feature of Triton is that: it allows developers to focus on the high-level logic of their parallel code by automating optimizations. This feature takes miles away from all the complexity of making GPU programming with CUDA and helps you to save your time and effort tremendously.

Already OpenAI confirms this with the following words according to their blog posts:

> Although a variety of systems have recently emerged to make this process easier, we have found them to be either too verbose, lack flexibility, or generate code noticeably slower than our hand-tuned baselines,

Programming GPUs directly in CUDA, according to Tillet and the team, is just too difficult. For example, writing native kernels, or functions, for GPUs "can be surprisingly difficult due to the many intricacies of GPU programming," Tillet and the team wrote in the post. Especially, "GPUs remain incredibly challenging to optimize for locality and parallelism," as the Triton documentation website explains.

Also OpenAI scientist Philippe Tillet said the aim is to become a viable alternative to CUDA for deep learning, according to Analytics India Magazine.
> It is for machine learning researchers and engineers who are unfamiliar with GPU programming despite having good software engineering skills.

So, Triton's main goal is to be broadly applicable and therefore does not automatically schedule work across SMs — leaving some important algorithmic considerations (e.g. tiling, inter-SM synchronization) to the discretion of developers.

The modern architecture of today's GPUs can be divided into three parts.

![](https://cdn.openai.com/triton/assets/gpu-architecture.svg)

- DRAM (Dynamic Random Access Memory): DRAM is a type of random access memory, and semiconductor memory that stores each bit of data in a memory cell and uses it for program code needed by the computer processor and deletes it after the process is done.

- SRAM (Static Random Access Memory): SRAM is a random access memory that holds data bits and programs into its memory cell until the computer’s power is being supplied.

- ALU (Arithmetic logic unit): ALU is the part of a computer processor that carries out the operation (arithmetic or logical) on the operands according to the command from the computer.

Each of these parts must be considered when optimizing CUDA code:

- Memory transfer from DRAM must be fused into large transactions to leverage the large bus width of modern memory interfaces.

- Data is manually uploaded to SRAM before being re-used and managed to minimize shared memory bank conflicts upon retrieval.

- Computations must be partitioned and scheduled carefully, both across and within streaming multiprocessors (SMs), to promote instruction/thread-level parallelism and leverage special-purpose ALUs (Tensor Cores)

What sets Triton apart from CUDA is that it perfects and automates the following optimizations:

- Memory coalescing

- Shared memory management

- Scheduling (within SMs)

Example implementation of Triton:

```python
import triton
import triton.language as tl


@triton.jit
def Triton_softmax(Y, X, stride_xm, stride_ym, M, N, **meta):
    
    m = tl.program_id(0)
    n = tl.arange(0, meta['BLOCK'])
    X = X + m * stride_xm + n
    x = tl.load(X, mask=n < N, other=-float('inf'))
    z = x - tl.max(x, axis=0)
    numerator = tl.exp(z)
    denominator = tl.sum(numerator, axis=0)
    y = numerator / denominator
    Y = Y + m * stride_ym + n
    tl.store(Y, y, mask=n < N)
```

The compiler currently uses block-level data-flow analysis, a technique used for scheduling iteration-block statically based on the control-and data-flow structure of the target program, to solve the challenge of scheduling. Triton aims to provide an open-source environment to write fast code at a higher productivity than CUDA, but also with higher flexibility than other existing domain-specific languages. 

Triton, in my opinion, is an excellent example of the fundamental need for automation, even for the most critical optimizations. Productivity is a rare hack! Let's use it!

If you want to participate, adding a few great content, including the official introduction and paper, below:

[Introducing Triton: Open-Source GPU Programming for Neural Networks](https://openai.com/blog/triton/)

[Triton: An Intermediate Language and Compiler for Tiled Neural Network Computations](http://www.eecs.harvard.edu/~htk/publication/2019-mapl-tillet-kung-cox.pdf)

[Move Over CUDA: OpenAI Releases Triton For GPU Developers](https://analyticsindiamag.com/how-is-openais-triton-different-from-nvidia-cuda/)

[How Is OpenAI’s Triton Different From NVIDIA CUDA?](https://analyticsindiamag.com/how-is-openais-triton-different-from-nvidia-cuda/)

[Hands-On Guide To Triton By OpenAI](https://analyticsindiamag.com/hands-on-guide-to-triton-by-openai/)
