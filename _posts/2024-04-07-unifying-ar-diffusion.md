---
layout: distill
title: Unifying Autoregressive and Diffusion Models for Better Sequence Generation
description: Exploring our novel framework that unifies autoregressive and diffusion-based sequence generation through hyperschedules and hybrid noising processes.
date: 2024-04-07
authors:
  - name: Nima Fathi
    url: "https://nimafathi.github.io"
    affiliations:
      name: "ServiceNow, Montreal"
      url: "https://www.servicenow.com"
---

<d-title>
<h1>Unifying Autoregressive and Diffusion Models for Better Sequence Generation</h1>
<p>Exploring our novel framework that unifies autoregressive and diffusion-based sequence generation through hyperschedules and hybrid noising processes.</p>
</d-title>

<d-byline></d-byline>

<d-article>
<d-contents>
  <nav class="l-text figcaption">
    <h3>Contents</h3>
    <div><a href="#introduction">Introduction</a></div>
    <div><a href="#background">Background</a></div>
    <div><a href="#hyperschedules">Hyperschedules</a></div>
    <div><a href="#hybrid-noising">Hybrid Noising</a></div>
    <div><a href="#adaptive-correction">Adaptive Correction</a></div>
    <div><a href="#results">Results</a></div>
    <div><a href="#efficiency">Efficiency</a></div>
    <div><a href="#conclusion">Conclusion</a></div>
  </nav>
</d-contents>

<h2 id="introduction">Introduction</h2>

<p>Imagine combining the best of two powerful approaches: <strong>autoregressive (AR) models</strong>—like GPT—which generate sequences efficiently token by token, and <strong>diffusion models</strong>, known for their robustness in iterative refinement. In our recent work presented at the DeLTa Workshop (ICLR 2025), we made this happen. Here's how.</p>

<h2 id="background">Background: The Best of Both Worlds</h2>

<p>Autoregressive models like GPT are efficient but can't fix their mistakes. Diffusion models can correct themselves iteratively, but at a computational cost. Could we have the efficiency of AR with the robustness of diffusion?</p>

<p>We discovered that these seemingly different models actually belong to the same family. AR models can be viewed as an extreme case of diffusion, using a very particular noising schedule.</p>

<h2 id="hyperschedules">Introducing Hyperschedules: Bridging AR and Diffusion</h2>

<p>To unify AR and diffusion, we introduce <strong>hyperschedules</strong>, unique noise schedules that vary per token position. Traditional diffusion models uniformly apply noise, but our hyperschedules allow each token to have its own noising pattern, smoothly transitioning from AR-like generation to fully iterative diffusion.</p>

<figure>
  <img src="/assets/img/unifying_ar_diff/hyperschedules.png" alt="Hyperschedule Examples">
  <figcaption>Examples of hyperschedules. From left to right: Quenched AR, Flat, Block, Slide Annealing.</figcaption>
</figure>

<p>These schedules let us generate sequences token by token (like AR) or iteratively (like diffusion)—or anywhere in between.</p>

<h2 id="hybrid-noising">Hybrid Noising: Teaching Models to Self-Correct</h2>

<p>Diffusion models traditionally use either "absorb" (replacing tokens with a MASK) or "uniform" (randomly changing tokens). We introduced a <strong>hybrid noising process</strong> that combines both methods, allowing our models to detect and correct mistakes during generation.</p>

<figure>
  <img src="/assets/img/unifying_ar_diff/hybrid_noising.png" alt="Hybrid Noising Illustration">
  <figcaption>Hybrid noising introduces small controlled errors (red), guiding the model to learn corrections.</figcaption>
</figure>

<h2 id="adaptive-correction">Adaptive Correction Sampler: Fixing Mistakes at Inference Time</h2>

<p>To fully leverage our hybrid noising, we introduced the <strong>Adaptive Correction Sampler (ACS)</strong>, a novel inference algorithm that lets models revisit and correct previously generated tokens. ACS significantly enhances sequence quality by reducing cumulative errors.</p>

<h2 id="results">How Does It Perform? State-of-the-Art Results</h2>

<p>We tested our model on several language modeling benchmarks, outperforming existing diffusion-based methods in terms of perplexity and quality-diversity trade-offs.</p>

<figure>
  <table>
    <thead>
      <tr>
        <th>Method</th>
        <th>PTB</th>
        <th>WikiText</th>
        <th>Lambada</th>
        <th>Pubmed</th>
        <th>Arxiv</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Transformer (Sahoo et al.)</td>
        <td>82.1</td><td>25.8</td><td>51.3</td><td>49.0</td><td>41.7</td>
      </tr>
      <tr>
        <td>MDLM (Sahoo et al.)</td>
        <td>91.0</td><td>33.2</td><td>48.3</td><td>43.1</td><td>37.9</td>
      </tr>
      <tr>
        <td><strong>Hybrid Model (ours)</strong></td>
        <td><strong>89.9</strong></td><td><strong>30.0</strong></td><td><strong>45.4</strong></td><td><strong>41.2</strong></td><td><strong>37.3</strong></td>
      </tr>
    </tbody>
  </table>
  <figcaption>Performance comparison across different benchmarks</figcaption>
</figure>

<figure>
  <img src="/assets/img/unifying_ar_diff/perplexity_mauve.png" alt="Quality-Diversity Trade-offs">
  <figcaption>Our hybrid models achieve better quality-diversity trade-offs compared to other diffusion models.</figcaption>
</figure>

<h2 id="efficiency">Efficient Attention Masks for Faster Inference</h2>

<p>Our hyperschedules also support KV-caching-compatible attention masks, making our method computationally efficient. This efficiency brings diffusion-based language models closer to the practical performance of AR models, crucial for real-world applications.</p>

<h2 id="conclusion">Conclusion: A Unified Future</h2>

<p>By unifying autoregressive and diffusion models through hyperschedules, hybrid noising, and adaptive sampling, we open exciting avenues for future research—especially for complex, reasoning-intensive tasks like code generation and logical reasoning.</p>

<p>Read the full paper <a href="https://arxiv.org">here</a>.</p>

<p>Stay tuned for more exciting updates!</p>

</d-article>

<d-appendix>
<d-footnote-list></d-footnote-list>
<d-citation-list></d-citation-list>
</d-appendix> 