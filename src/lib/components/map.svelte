<!--
  map.svelte — Juxtapose before/after image slider

  USAGE IN GOOGLE DOC:
      [[map]]

  To swap the embed: publish your slider at knightlab.com/juxtapose,
  then replace the `src` value in the iframe below.
  To update the headline, intro, or caption, edit the text here.
-->

<<<<<<< Updated upstream
<div class="map-dark-section"></div>
<!-- Section heading -->
<div class="map-section-heading">
  <h2 class="map-section-title">It Is Becoming a Larger Issue</h2>
</div>
=======
<div class="map-section">
>>>>>>> Stashed changes

  <!-- Section heading + intro text -->
  <div class="map-text-block">
    <p class="map-kicker">United States</p>
    <h2 class="map-section-title">It Is Becoming a Larger Issue</h2>
    <p class="map-body">Harrison Street is not home to the first Leptospira outbreak — there have been hundreds of cases popping up throughout the United States at a surprising rate. Just last year, the Chicago Health Department reported 6 cases of Leptospirosis, resulting in one death. The cases were in Logan Square and West Town, communities with a large unhoused population.</p>

<<<<<<< Updated upstream
<!-- Header: sits inside the article column (normal flow) -->
<div class="map-header">
  <p class="map-kicker">Visual Comparison</p>
  <h2 class="map-heading">The United States: Then and Now</h2>
  <!-- REPLACE: update this intro sentence to match your story -->
  <p class="map-intro">
    Drag the slider to compare the amount of cases throughout the country in 2025 and until now in 2026.
  </p>
</div>

<!-- Full-bleed embed: breaks out of the article column -->
<div class="juxtapose-outer">
  <div class="juxtapose-ratio">
    <div class="drag-hint" aria-hidden="true">
      <span class="drag-arrow">←</span>
      Drag to compare
      <span class="drag-arrow">→</span>
=======
    <div class="map-sub-header">
      <p class="map-sub-kicker">Visual Comparison</p>
      <h3 class="map-sub-heading">Harrison Street: Then and Now</h3>
      <p class="map-sub-intro">
        Drag the slider to compare conditions along Harrison Street before and
        after the leptospirosis alert was issued.
      </p>
>>>>>>> Stashed changes
    </div>
  </div>

  <!-- Full-bleed embed -->
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
        width="100%"
        src="https://cdn.knightlab.com/libs/juxtapose/latest/embed/index.html?uid=050f885c-438f-11f1-ba1b-0e6f42328d7d"
      ></iframe>
    </div>
  </div>

  <!-- Caption -->
  <div class="map-text-block">
    <p class="map-caption">
      Harrison Street, Berkeley.
      <span class="map-source">Source: Knight Lab Juxtapose / reporting by Akinpelu, Bonilla, Olawuyi.</span>
    </p>
  </div>

</div>


<style>
<<<<<<< Updated upstream
  /* ── Section opening heading ────────────────────────────── */
  .map-dark-section {
  background-color: #000 !important;
  color: #fff !important;
  padding: 4rem 0rem;
  position: relative;
  left: 50%;
  right: 50%;
  margin-left: -50vw;
  margin-right: -50vw;
  width: 100vw;
  } 
  
  .map-section-heading {
    padding-top: 3.5rem;
    margin-bottom: 0.5rem;
  }

  .map-section-title {
    font-family: 'Lato', Garamond, Georgia, serif;
    font-size: clamp(2rem, 5vw, 3.2rem);
    font-weight: 400;
    color: #ffffff;
    line-height: 1.15;
    margin: 0;
  }

  /* ── Intro text ─────────────────────────────────────────── */
  .map-intro-text {
    color: #dddddd;
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
    border-top: 1px solid #000000;
=======
  /* ── Full-bleed dark section ────────────────────────────── */
  .map-section {
    width: 100vw;
    margin-left: calc(50% - 50vw);
    background: #0d1117;
    color: #f0f0f0;
    padding-bottom: 3rem;
  }

  /* ── Text blocks inside section ─────────────────────────── */
  .map-text-block {
    max-width: 720px;
    margin: 0 auto;
    padding: 4rem clamp(1.5rem, 5vw, 3rem) 0;
>>>>>>> Stashed changes
  }

  /* ── Kicker ──────────────────────────────────────────────── */
  .map-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.72rem;
    font-weight: 700;
    letter-spacing: 0.22em;
    text-transform: uppercase;
    color: rgba(255, 200, 100, 0.85);
    margin: 0 0 0.6rem;
  }

  /* ── Main heading ────────────────────────────────────────── */
  .map-section-title {
    font-family: 'Crimson Text', Garamond, Georgia, serif;
    font-size: clamp(2rem, 5vw, 3.2rem);
    font-weight: 400;
    color: #ffffff;
    line-height: 1.15;
    margin: 0 0 1.25rem;
  }

  /* ── Body paragraph ──────────────────────────────────────── */
  .map-body {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 1.05rem;
    line-height: 1.85;
    color: rgba(220, 220, 220, 0.82);
    margin: 0;
  }

  /* ── Map sub-header ──────────────────────────────────────── */
  .map-sub-header {
    margin-top: 3rem;
    padding-top: 2rem;
    border-top: 1px solid rgba(255, 255, 255, 0.08);
  }

  .map-sub-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.68rem;
    font-weight: 700;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: rgba(255, 200, 100, 0.7);
    margin: 0 0 0.45rem;
  }

<<<<<<< Updated upstream
  .map-heading {
    font-family: 'Robloto Slab', serif;
    font-size: clamp(1.5rem, 3.5vw, 2.1rem);
=======
  .map-sub-heading {
    font-family: 'Roboto Slab', serif;
    font-size: clamp(1.3rem, 3vw, 1.8rem);
>>>>>>> Stashed changes
    font-weight: 500;
    color: #ffffff;
    line-height: 1.2;
    margin: 0 0 0.65rem;
  }

  .map-sub-intro {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.95rem;
    line-height: 1.65;
<<<<<<< Updated upstream
    color: #ffffff;
    max-width: 560px;
=======
    color: rgba(200, 200, 200, 0.7);
>>>>>>> Stashed changes
    margin: 0;
  }

  /* ── Full-bleed embed ────────────────────────────────────── */
<<<<<<< Updated upstream
 .juxtapose-outer {
  width: 100vw;
  margin-top: 1.5rem;
  margin-left: calc(50% - 50vw);
  background: #111;
}
=======
  .juxtapose-outer {
    width: 100%;
    margin-top: 1.75rem;
    background: #111;
  }
>>>>>>> Stashed changes

  .juxtapose-ratio {
    position: relative;
    width: 100%;
    aspect-ratio: 16 / 9;
    overflow: hidden;
  }

  @media (max-width: 600px) {
    .juxtapose-ratio { aspect-ratio: 4 / 3; }
  }

  .juxtapose-ratio iframe {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    display: block;
    border: none;
  }

  /* ── Drag hint ───────────────────────────────────────────── */
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
    color: rgba(225, 36, 36, 0.9);
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.75rem;
    letter-spacing: 0.08em;
    border-radius: 20px;
    pointer-events: none;
    white-space: nowrap;
    animation: hint-fade 3s ease 1.5s forwards;
  }

  .drag-arrow { font-size: 0.85rem; opacity: 0.7; }

  @keyframes hint-fade {
    0%   { opacity: 1; }
    100% { opacity: 0; }
  }

  /* ── Caption ─────────────────────────────────────────────── */
  .map-caption {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.78rem;
    font-style: italic;
    color: rgba(255, 255, 255, 0.35);
    line-height: 1.55;
    margin: 0.75rem 0 0;
  }

  .map-source {
    display: block;
    font-style: normal;
    color: rgba(255, 200, 100, 0.4);
    margin-top: 0.2rem;
  }
</style>
