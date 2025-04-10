---
layout: distill
title: Unifying Autoregressive and Diffusion Models for Better Sequence Generation
description: Exploring our novel framework that unifies autoregressive and diffusion-based sequence generation through hyperschedules and hybrid noising processes.
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
    <div><a href="#background">Background</a></div>
    <div><a href="#hyperschedules">Hyperschedules</a></div>
    <div><a href="#hybrid-noising">Hybrid Noising Processes</a></div>
    <div><a href="#adaptive-correction">Adaptive Correction Sampler (ACS)</a></div>
    <div><a href="#results">Experimental Results</a></div>
    <div><a href="#efficiency">Efficiency via Attention Masks</a></div>
    <div><a href="#conclusion">Conclusion and Future Work</a></div>
  </nav>
</d-contents>

## Introduction

Imagine combining the efficiency of **autoregressive (AR) models**—like GPT—which generate sequences token by token—with the robustness of **diffusion models**, renowned for their iterative refinement and error correction. At the DeLTa Workshop (ICLR 2025), we presented a unified framework that brings these two approaches together. This post dives deeply into our methods and results, detailing how hyperschedules, hybrid noising, and adaptive correction yield significant improvements in sequence generation.

## Background

AR models generate sequences one token at a time, predicting each token based on its predecessors. While this method is computationally efficient, errors cannot be revised once generated. Diffusion models, by contrast, generate sequences by gradually refining random noise, which allows for iterative corrections but is typically slower and more computationally expensive. Notably, AR models can be considered a special case of diffusion models with an extreme noising schedule—this insight paves the way for our unification.

## Hyperschedules

To bridge AR and diffusion models, we introduce **hyperschedules**—custom noise schedules assigned to individual token positions. Traditional diffusion models apply noise uniformly across tokens; hyperschedules allow us to vary the noise level per token, creating a smooth continuum between strict autoregression and full diffusion.

<figure>
  <img src="/assets/img/unifying_ar_diff/hyperschedules.png" alt="Hyperschedule Examples">
  <figcaption>Figure 1: Examples of hyperschedules: Quenched AR, Flat, Block, Slide Annealing.</figcaption>
</figure>

This flexibility enables our models to generate sequences either token-by-token (as in AR) or iteratively (as in diffusion), or even intermediate hybrid modes.

## Hybrid Noising Processes

Diffusion models often rely on two noising schemes:
- **Absorbing process**: where tokens are replaced with a special MASK token.
- **Uniform process**: where tokens are substituted randomly.

Our **hybrid noising process** blends these two methods, introducing controlled errors (illustrated in red) while masking other tokens. This train-the-model-to-correct strategy facilitates robust sequence generation even when initial predictions are uncertain.

<figure>
  <img src="/assets/img/unifying_ar_diff/hybrid_noising.png" alt="Hybrid Noising Illustration">
  <figcaption>Figure 2: Hybrid noising introduces controlled errors (red tokens), guiding the model to learn corrections during generation.</figcaption>
</figure>

We explore two variants:
- **γ-Hybrid**: employing an analytical approach based on SEDD.
- **ε-Hybrid**: using a more straightforward weighted cross-entropy formulation as seen in MDM frameworks.

## Adaptive Correction Sampler (ACS)

Building on hybrid noising, the **Adaptive Correction Sampler (ACS)** is our novel inference algorithm that allows the model to revisit and revise previous token decisions. Unlike traditional AR models—which do not modify past outputs—ACS adaptively corrects errors based on model confidence, reducing cumulative generation errors and yielding higher-quality sequences.

## Experimental Results

Our unified models were evaluated on several benchmarks, outperforming prior diffusion-based methods and established autoregressive baselines. Below is a performance comparison:

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
        <td>25.8</td><td>51.3</td><td>49.0</td><td>41.7</td>
      </tr>
      <tr>
        <td>SEDD (Lou et al.)</td>
        <td>36.0</td><td>48.9</td><td>45.4</td><td>40.0</td>
      </tr>
      <tr>
        <td>MDLM (Sahoo et al.)</td>
        <td>33.2</td><td>48.3</td><td>43.1</td><td>37.9</td>
      </tr>
      <tr>
        <td>BD3-LM (Arriola et al.)</td>
        <td>31.3</td><td>50.0</td><td>42.5</td><td>39.2</td>
      </tr>
      <tr>
        <td><strong>γ-Hybrid (Ours)</strong></td>
        <td>30.0</td><td><strong>45.4</strong></td><td>46.6</td><td>40.6</td>
      </tr>
      <tr>
        <td><strong>ε-Hybrid (Ours)</strong></td>
        <td>32.5</td><td>50.2</td><td><strong>41.2</strong></td><td><strong>37.8</strong></td>
      </tr>
    </tbody>
  </table>
  <figcaption>Table 1: Performance comparison across benchmarks. Bold indicates best performance.</figcaption>
</figure>


We further investigate the trade-off between sequence generation quality and diversity by analyzing generative perplexity against token-level entropy and MAUVE scores, as visualized in the following figure:

<figure>
  <img src="/assets/img/unifying_ar_diff/perplexity_mauve.png" alt="Quality-Diversity Trade-offs">
  <figcaption>Figure 3: Hybrid models achieve better quality-diversity trade-offs compared to baseline diffusion models.</figcaption>
</figure>
 Our hybrid configurations consistently achieve superior positions on both Pareto frontiers, indicating enhanced generation fluency, coherence, and diversity compared to baselines. 
The observed balance in generation quality and diversity establish a new state-of-the-art in diffusion-based sequence generation.

## Efficiency via Attention Masks

Efficiency is paramount for real-world deployment, and our approach facilitates this through advanced attention mechanisms. Our hyperschedules support **KV-caching-compatible attention masks**, which allow us to:
- **Reuse settled tokens** (already generated and fixed) via KV-caching.
- **Focus computations on active tokens** (currently being updated).

Our framework is flexible—it supports both unmasking the MASK token (typical in masked language models) and predicting the next token (autoregressive style), echoing the strategies proposed in DiffuLLaMA. This dual capability enables the model to operate in diverse inference modes depending on the task.

<figure>
  <img src="/assets/img/unifying_ar_diff/transformer_aligned.jpg" alt="Attention Mask: Aligned Configuration" style="max-width:45%;">
  <figcaption>Figure 4: Transformer-based sequence generator with an <strong>ALIGNED</strong> attention configuration, akin to masked language models.</figcaption>
</figure>

<figure>
  <img src="/assets/img/unifying_ar_diff/transformer_shifted.jpg" alt="Attention Mask: Shifted Configuration" style="max-width:45%;">
  <figcaption>Figure 5: Transformer-based sequence generator in a <strong>SHIFTED</strong> configuration, resembling autoregressive models.</figcaption>
</figure>

<figure>
  <img src="/assets/img/unifying_ar_diff/attention_mask.jpg" alt="Training Attention Mask">
  <figcaption>Figure 6: Example training attention mask for efficient token updates under both aligned and shifted configurations, highlighting the split between settled and active tokens.</figcaption>
</figure>

From Appendix B, our models partition tokens into three components during generation:
1. **Settled tokens**: Finalized outputs that can be cached.
2. **Active tokens**: Those being actively updated.
3. **Worthless tokens**: Tokens not yet generated or not relevant to the current context.

This tailored attention mechanism reduces the effective computational load, thereby enhancing inference speed without sacrificing generation quality.

## Conclusion and Future Work

By unifying autoregressive and diffusion models through the innovations of hyperschedules, hybrid noising, and the Adaptive Correction Sampler, we achieve significant improvements in both sequence quality and efficiency. Our model is flexible enough to support multiple inference modes—whether unmasking tokens or predicting future tokens—making it highly adaptable to varied applications.

Future research directions include:
- Exploring a broader range of hyperschedule designs.
- Extending the hybrid noising approach to new domains such as code generation.
- Deepening theoretical understanding of noise scheduling and its impact on model performance.

For further details, please refer to our full paper on [arXiv](https://arxiv.org/html/2504.06416v1).

Stay tuned for more updates as we continue to push the boundaries of sequence generation!

<d-appendix>
<d-footnote-list></d-footnote-list>
<d-citation-list></d-citation-list>
</d-appendix>