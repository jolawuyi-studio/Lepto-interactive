<!--
  map.svelte — Juxtapose before/after image slider

  USAGE IN GOOGLE DOC:
      [[map]]

  To swap the embed: publish your slider at knightlab.com/juxtapose,
  then replace the `src` value in the iframe below.
  To update the headline, intro, or caption, edit the text here.
-->

<!-- Section heading -->
<div class="map-section-heading">
  <h2 class="map-section-title">It Is Becoming a Larger Issue</h2>
</div>

<!-- Intro text after heading -->
<div class="map-intro-text">
  <p>Harrison Street is not home to the first Leptospira outbreak — there have been hundreds of cases popping up throughout the United States at a surprising rate. Just last year, the Chicago Health Department reported 6 cases of Leptospirosis, resulting in one death. The cases were in Logan Square and West Town, communities with a large unhoused population.</p>
</div>

<!-- Header: sits inside the article column (normal flow) -->
<div class="map-header">
  <p class="map-kicker">Visual Comparison</p>
  <h2 class="map-heading">Harrison Street: Then and Now</h2>
  <!-- REPLACE: update this intro sentence to match your story -->
  <p class="map-intro">
    Drag the slider to compare conditions along Harrison Street before and
    after the leptospirosis alert was issued.
  </p>
</div>

<!-- Full-bleed embed: breaks out of the article column -->
<div class="juxtapose-outer">
  <div class="juxtapose-ratio">
    <div class="drag-hint" aria-hidden="true">
      <span class="drag-arrow">←</span>
      Drag to compare
      <span class="drag-arrow">→</span>
    </div>
    <iframe
      title="Before and after comparison of Harrison Street"
      frameborder="0"
      class="juxtapose"
      src="https://cdn.knightlab.com/libs/juxtapose/latest/embed/index.html?uid=6376e67e-3ecb-11f1-ba1b-0e6f42328d7d"
      allowfullscreen
    ></iframe>
  </div>
</div>

<!-- Caption: back in the article column -->
<!-- REPLACE: update this caption and source line -->
<p class="map-caption">
  Harrison Street, Berkeley.
  <span class="map-source">Source: Knight Lab Juxtapose / reporting by Akinpelu, Bonilla, Olawuyi.</span>
</p>

<style>
  /* ── Section opening heading ────────────────────────────── */
  .map-section-heading {
    padding-top: 3.5rem;
    margin-bottom: 0.5rem;
  }

  .map-section-title {
    font-family: 'Crimson Text', Garamond, Georgia, serif;
    font-size: clamp(2rem, 5vw, 3.2rem);
    font-weight: 400;
    color: #1a1a1a;
    line-height: 1.15;
    margin: 0;
  }

  /* ── Intro text ─────────────────────────────────────────── */
  .map-intro-text {
    margin: 1rem 0 0;
  }

  .map-intro-text p {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 1.05rem;
    line-height: 1.85;
    color: #333;
    margin: 0;
  }

  /* ── Section header ──────────────────────────────────────── */
  .map-header {
    padding-top: 2.5rem;
    margin-bottom: 1.25rem;
    border-top: 1px solid #e0e0e0;
  }

  .map-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.72rem;
    font-weight: 700;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: #888;
    margin-bottom: 0.5rem;
  }

  .map-heading {
    font-family: 'Roboto Slab', serif;
    font-size: clamp(1.5rem, 3.5vw, 2.1rem);
    font-weight: 500;
    color: #1a1a1a;
    line-height: 1.2;
    margin-bottom: 0.65rem;
  }

  .map-intro {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.97rem;
    line-height: 1.65;
    color: #555;
    max-width: 560px;
    margin: 0;
  }

  /* ── Full-bleed embed ────────────────────────────────────── */
  /* calc(50% - 50vw) shifts left by (half viewport - half column).
     This reliably centers a 100vw element regardless of the column
     width, and works correctly when rendered inside Bootstrap's col. */
  .juxtapose-outer {
    width: 100vw;
    margin-top: 1.5rem;
    margin-left: calc(50% - 50vw);
  }

  /* ── Adaptive container ──────────────────────────────────── */
  /* height: clamp(min, preferred, max)
     - preferred = 65vh scales continuously with any screen size
     - min = 260px  prevents collapse on tiny phones / landscape mobile
     - max = 780px  stops it dominating a large monitor
     No breakpoints needed — this adapts fluidly to every screen. */
  .juxtapose-ratio {
    position: relative;
    width: 100%;
    height: clamp(400px, 85vh, 1000px);
    background: #111;
    overflow: hidden;
  }

  .juxtapose-ratio iframe {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    display: block;
  }

  /* "Drag to compare" hint — fades out after 3 s via animation */
  .drag-hint {
    position: absolute;
    bottom: 1rem;
    left: 50%;
    transform: translateX(-50%);
    z-index: 2;
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.35rem 0.9rem;
    background: rgba(0, 0, 0, 0.55);
    color: rgba(255, 255, 255, 0.9);
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.75rem;
    letter-spacing: 0.08em;
    border-radius: 20px;
    pointer-events: none;
    white-space: nowrap;
    animation: hint-fade 3s ease 1.5s forwards;
  }

  .drag-arrow {
    font-size: 0.85rem;
    opacity: 0.7;
  }

  @keyframes hint-fade {
    0%   { opacity: 1; }
    100% { opacity: 0; }
  }

  /* ── Caption ─────────────────────────────────────────────── */
  .map-caption {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.78rem;
    font-style: italic;
    color: #777;
    line-height: 1.55;
    margin: 0.6rem 0 2.5rem;
  }

  .map-source {
    display: block;
    font-style: normal;
    color: #aaa;
    margin-top: 0.2rem;
  }
</style>
