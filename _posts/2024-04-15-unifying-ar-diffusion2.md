---
layout: distill
title: Bridging Autoregressive and Diffusion Models for Robust Sequence Generation
description: Exploring a novel framework that unites autoregressive and diffusion-based approaches with hyperschedules, hybrid noising, and an adaptive correction sampler for superior sequence generation.
date: 2025-04-07
authors:
  - name: Nima Fathi
    url: "https://nimafathi.github.io"
    affiliations:
      name: "ServiceNow, Montreal"
      url: "https://www.servicenow.com"
---

<d-contents>
  <nav class="l-text figcaption">
    <h3>Contents</h3>
    <div><a href="#introduction">Introduction</a></div>
    <div><a href="#background-motivation">Background &amp; Motivation</a></div>
    <div><a href="#unified-framework">The Unified Framework</a></div>
    <div><a href="#hyperschedules">Hyperschedules</a></div>
    <div><a href="#hybrid-noising">Hybrid Noising Processes</a></div>
    <div><a href="#adaptive-correction">Adaptive Correction Sampler (ACS)</a></div>
    <div><a href="#experimental-evaluation">Experimental Evaluation</a></div>
    <div><a href="#efficiency-attention">Efficiency via Attention Masks</a></div>
    <div><a href="#conclusion">Conclusion and Future Work</a></div>
    <div><a href="#table-of-figures">Table of Figures</a></div>
    <div><a href="#references">References</a></div>
  </nav>
</d-contents>

## Introduction {#introduction}

Recent advances in sequence generation have largely focused on two dominant paradigms: the efficiency of autoregressive (AR) models (e.g., GPT [1]) and the robust, iterative refinement of diffusion-based models [2]. However, each approach has its limitations—AR models propagate errors without revision, while diffusion models, despite their error-correcting capabilities, suffer from slower inference. In this post, we present our unified framework that combines the strengths of both paradigms through innovative techniques such as hyperschedules, hybrid noising, and an Adaptive Correction Sampler (ACS). By incorporating additional insights from our full paper and the corresponding appendix, we aim to deliver a comprehensive guide that takes roughly 15 minutes to read.

## Background & Motivation {#background-motivation}

Autoregressive models (e.g., GPT [1]) generate text one token at a time, offering low latency but with the inherent risk of cumulative errors. Diffusion models, in contrast, iteratively refine noisy inputs to correct errors—albeit at a higher computational cost [2]. Our work bridges these two strategies, allowing for dynamic correction mechanisms and improved overall generation efficiency. This unification not only mitigates the shortcomings of each paradigm but also opens up new avenues for practical applications.

## The Unified Framework {#unified-framework}

Our approach introduces a continuum between traditional autoregressive decoding and iterative diffusion through:
- **Hyperschedules:** Customized noise schedules that vary per token, providing a smooth transition between AR and diffusion modalities.
- **Hybrid Noising Processes:** A blend of masking and random perturbations to train models in recognizing and correcting errors.
- **Adaptive Correction Sampler (ACS):** A novel inference algorithm that revisits and refines token decisions, thereby reducing the risk of error propagation.

## Hyperschedules {#hyperschedules}

Standard diffusion models apply a uniform noise level across tokens. In our approach, hyperschedules assign a unique schedule to each token position, enabling a balance between the rigid structure of AR generation and the adaptability of diffusion. Our experiments (see Figure 1) highlight various designs—including “Quenched AR,” “Flat,” “Block,” and “Slide Annealing”—each with its own trade-offs in performance and efficiency.

<figure>
  <img src="/assets/img/unifying_ar_diff/hyperschedules.png" alt="Hyperschedule Examples">
  <figcaption>Figure 1: Various hyperschedule designs that customize noise levels per token, bridging AR and diffusion generation.</figcaption>
</figure>

## Hybrid Noising Processes {#hybrid-noising}

To harness the benefits of both masking and randomness, we introduce a hybrid noising process:
- **Absorbing Process:** Gradually replaces tokens with a dedicated MASK token.
- **Uniform Process:** Randomly substitutes tokens with alternative tokens.

We explore two variants:
- **γ-Hybrid:** An approach based on an analytical framework inspired by SEDD [3].
- **ε-Hybrid:** A formulation utilizing weighted cross-entropy similar to MDM frameworks [4].

This combined strategy (illustrated in Figure 2) trains the model to progressively correct its own errors during sequence generation.

<figure>
  <img src="/assets/img/unifying_ar_diff/hybrid_noising.png" alt="Hybrid Noising Illustration">
  <figcaption>Figure 2: Controlled hybrid noising that integrates token masking with random perturbations to enable effective error correction [3,4].</figcaption>
</figure>

## Adaptive Correction Sampler (ACS) {#adaptive-correction}

Traditional AR models do not allow revisiting earlier decisions. Our Adaptive Correction Sampler (ACS) breaks this limitation by iteratively refining token outputs. ACS leverages model confidence to adjust past predictions, ensuring minor mistakes do not cascade into major errors over the entire sequence. This iterative sampling is particularly valuable for generating longer and more coherent sequences.

## Experimental Evaluation {#experimental-evaluation}

We evaluated our unified models on benchmarks including WikiText, Lambada, Pubmed, and ArXiv. The key experimental findings are:
- **Enhanced Generation Quality:** Both γ-Hybrid and ε-Hybrid configurations show significantly improved perplexity and MAUVE scores (refer to Figure 3).
- **Improved Trade-offs:** Our model achieves superior quality-diversity trade-offs compared to baseline approaches, as quantified in the performance table below [3–5].

<figure>
  <table>
    <thead>
      <tr>
        <th>Method</th>
        <th>WikiText</th>
        <th>Lambada</th>
        <th>Pubmed</th>
        <th>Arxiv</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Transformer (Sahoo et al.)</td>
        <td>25.8</td>
        <td>51.3</td>
        <td>49.0</td>
        <td>41.7</td>
      </tr>
      <tr>
        <td>SEDD (Lou et al.)</td>
        <td>36.0</td>
        <td>48.9</td>
        <td>45.4</td>
        <td>40.0</td>
      </tr>
      <tr>
        <td>MDLM (Sahoo et al.)</td>
        <td>33.2</td>
        <td>48.3</td>
        <td>43.1</td>
        <td>37.9</td>
      </tr>
      <tr>
        <td>BD3-LM (Arriola et al.)</td>
        <td>31.3</td>
        <td>50.0</td>
        <td>42.5</td>
        <td>39.2</td>
      </tr>
      <tr>
        <td><strong>γ-Hybrid (Ours)</strong></td>
        <td>30.0</td>
        <td><strong>45.4</strong></td>
        <td>46.6</td>
        <td>40.6</td>
      </tr>
      <tr>
        <td><strong>ε-Hybrid (Ours)</strong></td>
        <td>32.5</td>
        <td>50.2</td>
        <td><strong>41.2</strong></td>
        <td><strong>37.8</strong></td>
      </tr>
    </tbody>
  </table>
  <figcaption>Table 1: Comparative performance across benchmarks. Bold indicates the best results [3–5].</figcaption>
</figure>

<figure>
  <img src="/assets/img/unifying_ar_diff/perplexity_mauve.png" alt="Quality-Diversity Trade-offs">
  <figcaption>Figure 3: Analysis of perplexity versus MAUVE scores showing enhanced quality-diversity trade-offs in our hybrid configurations [3,4].</figcaption>
</figure>

## Efficiency via Attention Masks {#efficiency-attention}

An integral part of our framework is optimizing computational efficiency through advanced attention mechanisms. Our attention design leverages:
- **KV-Caching:** Reuse of finalized (settled) tokens to minimize redundant computations [6].
- **Active vs. Settled Tokens:** Dynamic partitioning of tokens into:
  1. **Settled Tokens:** Finalized outputs eligible for caching.
  2. **Active Tokens:** Tokens under current update.
  3. **Worthless Tokens:** Tokens yet to be generated or irrelevant to the current context.

This design supports two distinct inference modes:
- **Aligned** (Masked Language Model Style): (See Figure 4)
- **Shifted** (Autoregressive Style): (See Figure 5)

Additionally, Figure 6 demonstrates the training attention mask that effectively partitions tokens, contributing to significant improvements in efficiency.

<figure>
  <img src="/assets/img/unifying_ar_diff/transformer_aligned.jpg" alt="Attention Mask: Aligned Configuration" style="max-width:45%;">
  <figcaption>Figure 4: Aligned attention configuration that enables efficient KV-caching in the model [6].</figcaption>
</figure>

<figure>
  <img src="/assets/img/unifying_ar_diff/transformer_shifted.jpg" alt="Attention Mask: Shifted Configuration" style="max-width:45%;">
  <figcaption>Figure 5: Shifted attention configuration suited for autoregressive generation [6].</figcaption>
</figure>

<figure>
  <img src="/assets/img/unifying_ar_diff/attention_mask.jpg" alt="Training Attention Mask">
  <figcaption>Figure 6: Example training attention mask partitioning tokens into settled, active, and worthless groups [6].</figcaption>
</figure>

## Conclusion and Future Work {#conclusion}

By unifying the autoregressive and diffusion paradigms through hyperschedules, hybrid noising, and our Adaptive Correction Sampler (ACS), we demonstrate significant improvements in both sequence fluency and computational efficiency. Our flexible approach supports diverse inference strategies—from masked token predictions to autoregressive generation—paving the way for novel applications and further research.

**Future Research Directions:**
- Investigating more nuanced hyperschedule designs for finer-grained control.
- Adapting the hybrid noising strategy to new domains such as code generation.
- Conducting extensive studies on the trade-offs between quality and efficiency in real-world applications.

For further details, please refer to our full paper on [arXiv](https://arxiv.org/abs/2504.06416).

Stay tuned for more updates as we continue to push the boundaries of sequence generation!

## Table of Figures {#table-of-figures}

| Figure No. | Title                         | Description                                                                                      |
|------------|-------------------------------|--------------------------------------------------------------------------------------------------|
| 1          | Hyperschedules                | Shows various hyperschedule designs that customize noise levels per token.                   |
| 2          | Hybrid Noising                | Depicts the hybrid noising process integrating token masking and random perturbations [3,4].       |
| 3          | Quality-Diversity Trade-offs  | Compares perplexity and MAUVE scores to demonstrate improved quality-diversity trade-offs [3,4].  |
| 4          | Transformer (Aligned)         | Visualizes the aligned attention mask configuration that leverages KV-caching [6].                |
| 5          | Transformer (Shifted)         | Shows the shifted attention configuration suited for autoregressive generation [6].               |
| 6          | Training Attention Mask       | Demonstrates token partitioning during training into settled, active, and worthless tokens [6].   |

## References {#references}

1. **Radford, A., et al. (2019).** *Language Models are Unsupervised Multitask Learners.* OpenAI.  
2. **Ho, J., Jain, A., & Abbeel, P. (2020).** *Denoising Diffusion Probabilistic Models.* Advances in Neural Information Processing Systems.  
3. **Lou, et al.** *Discrete Diffusion Modeling by Estimating the Ratios of the Data Distribution* International Conference on Machine Learning.  
4. **Sahoo, et al.** *Simple and Effective Masked Diffusion Language Models.* Conference on Neural Information Processing Systems.  
5. **Arriola, et al.** *Block Diffusion: Interpolating Between Autoregressive and Diffusion Language Models* International Conference on Learning Representations.   
6. **Vaswani, A., et al. (2017).** *Attention Is All You Need.* Advances in Neural Information Processing Systems.