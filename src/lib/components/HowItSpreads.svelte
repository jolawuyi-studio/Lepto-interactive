<!--
  HowItSpreads.svelte — Scroll-driven transmission explainer

  ════════════════════════════════════════════════════════════
  GOOGLE DOC USAGE
  ════════════════════════════════════════════════════════════
  Option A — standalone shortcode (heading + paragraphs live
  in the Google Doc as normal text above it):

      [[HowItSpreads]]

  Option B — block shortcode (intro text comes from Google Doc):

      [[HowItSpreads]]
      ## How Leptospirosis Spreads
      Leptospirosis is often invisible at first...
      [[/HowItSpreads]]

  ════════════════════════════════════════════════════════════
  IMAGES
  ════════════════════════════════════════════════════════════
  1. Create the folder:  static/images/lepto/
  2. Add your five PNG files with these exact names:
       step-1-rat.png
       step-2-urine.png
       step-3-rain-soil.png
       step-4-human-entry.png
       step-5-symptoms.png
  3. Each `image` path in the steps array below already
     points there — no other changes needed.

  To update step labels or short text, edit the `steps` array.
  Long story paragraphs should live in the Google Doc, not here.
-->

<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import { browser } from '$app/environment';
  import { fade } from 'svelte/transition';
  import { base } from '$app/paths';

  // Intro text from the Google Doc when used as a block shortcode.
  // Leave empty when using Option A (standalone shortcode).
  export let bodyHtml: string = '';

  // ── Transmission steps ────────────────────────────────────
  // Edit shortLabel and text here.
  // Replace each `image` path once your PNGs are in static/images/lepto/
  const steps = [
    {
      shortLabel: 'Rats & Animals',
      image: `${base}/1-rat.png`,
      alt: 'Illustration of a rat as a reservoir host for Leptospira bacteria',
      text: 'Rats can carry Leptospira bacteria without immediately appearing sick.'
    },
    {
      shortLabel: 'Urine',
      // Filename has a space — encoded as %20 for safe URL usage
      image: `${base}/2-rat%20urine.png`,
      alt: 'Illustration showing bacteria shed through animal urine',
      text: 'The bacteria leave the body through urine and can contaminate the ground.'
    },
    {
      shortLabel: 'Rain & Soil',
      // Filename has a space — encoded as %20
      image: `${base}/3-rain%20drops.png`,
      alt: 'Illustration of rainwater spreading bacteria through soil, mud and puddles',
      text: 'Rainwater can move bacteria through soil, mud, puddles and drains.'
    },
    {
      shortLabel: 'Human Exposure',
      image: `${base}/4-human.png`,
      alt: 'Illustration of human exposure points: eyes, nose, mouth and broken skin',
      text: 'People can be exposed when contaminated water or soil reaches the mouth, eyes, nose or broken skin.'
    },
    {
      shortLabel: 'Symptoms',
      // Filename has a space — encoded as %20
      image: `${base}/5-sick%20human.png`,
      alt: 'Illustration of a person showing flu-like symptoms: fever, chills, muscle aches',
      text: 'Symptoms can look like flu or food poisoning, making the disease difficult to recognize early.'
    }
  ];

  let activeStep = 0;
  let showCycle = false;

  // Precomputed raindrop data — step 3 "Rain & Soil" effect
  const raindrops = Array.from({ length: 55 }, () => ({
    left:     parseFloat((Math.random() * 100).toFixed(1)),
    delay:    parseFloat((-(Math.random() * 2.5)).toFixed(2)),
    duration: parseFloat((0.4 + Math.random() * 0.6).toFixed(2)),
    opacity:  parseFloat((0.2 + Math.random() * 0.45).toFixed(2)),
    height:   8 + Math.round(Math.random() * 16)
  }));

  let stepEls: (HTMLElement | null)[] = new Array(steps.length).fill(null);
  let cycleEl: HTMLElement | null = null;
  let observer: IntersectionObserver | null = null;

  onMount(() => {
    if (!browser) return;

    // Fires when a step or the cycle section crosses the middle band
    // of the viewport (rootMargin clips top and bottom 35%)
    observer = new IntersectionObserver(
      (entries) => {
        for (const entry of entries) {
          if (!entry.isIntersecting) continue;

          const idx = stepEls.indexOf(entry.target as HTMLElement);
          if (idx !== -1) activeStep = idx;
          if (entry.target === cycleEl) showCycle = true;
        }
      },
      { rootMargin: '-35% 0px -35% 0px', threshold: 0 }
    );

    stepEls.forEach(el => el && observer?.observe(el));
    if (cycleEl) observer.observe(cycleEl);
  });

  onDestroy(() => observer?.disconnect());
</script>


<section class="how-spreads" aria-label="How leptospirosis spreads">

  <!-- ── Section heading ── -->
  <div class="spreads-heading-block">
    <p class="spreads-kicker">Transmission</p>
    <h2 class="spreads-heading">How Does Leptospirosis Spread?</h2>
  </div>

  <!-- ── Optional intro from Google Doc ── -->
  {#if bodyHtml}
    <div class="spreads-doc-intro">
      <!-- eslint-disable-next-line svelte/no-at-html-tags -->
      {@html bodyHtml}
    </div>
  {/if}

  <!-- ── Scroll-driven two-column body ── -->
  <div class="spreads-body">

    <!-- LEFT: scroll column — one panel per step -->
    <div class="spreads-scroll">
      {#each steps as step, i (i)}
        <div class="step-panel" bind:this={stepEls[i]}>

          <!-- Text card (visible on all screen sizes) -->
          <div class="step-card" class:active={activeStep === i}>
            <span class="step-num" aria-label="Step {i + 1}">0{i + 1}</span>
            <h3 class="step-label">{step.shortLabel}</h3>
            <p class="step-desc">{step.text}</p>
          </div>

          <!-- Inline image: only shown on mobile -->
          <div class="step-img-mobile" class:rain-host={i === 2} aria-hidden="true">
            {#if i === 2}
              <div class="rain-overlay" aria-hidden="true">
                {#each raindrops as drop, ri (ri)}
                  <span
                    class="raindrop"
                    style="left:{drop.left}%; animation-delay:{drop.delay}s; animation-duration:{drop.duration}s; opacity:{drop.opacity}; height:{drop.height}px"
                  ></span>
                {/each}
              </div>
            {/if}
            <img src={step.image} alt={step.alt} loading="lazy" />
          </div>

        </div>
      {/each}
    </div>

    <!-- RIGHT: sticky image panel — desktop only, aria-hidden
         because the same images appear inline on mobile       -->
    <div class="spreads-sticky-col" aria-hidden="true">
      <div class="spreads-sticky">

        <!-- Rain overlay — only visible on step 3 (Rain & Soil) -->
        {#if activeStep === 2}
          <div class="rain-overlay" aria-hidden="true" transition:fade={{ duration: 500 }}>
            {#each raindrops as drop, ri (ri)}
              <span
                class="raindrop"
                style="left:{drop.left}%; animation-delay:{drop.delay}s; animation-duration:{drop.duration}s; opacity:{drop.opacity}; height:{drop.height}px"
              ></span>
            {/each}
          </div>
        {/if}

        <!-- Image fades on activeStep change via Svelte {#key} -->
        {#key activeStep}
          <div class="sticky-img-wrap" in:fade={{ duration: 320 }}>
            <img
              src={steps[activeStep].image}
              alt={steps[activeStep].alt}
            />
          </div>
        {/key}

        <!-- Step label beneath the image -->
        <p class="sticky-step-label">{steps[activeStep].shortLabel}</p>

        <!-- Progress dots -->
        <div class="sticky-dots" role="presentation">
          {#each steps as step, i (i)}
            <span
              class="dot"
              class:active={activeStep === i}
              title={step.shortLabel}
            ></span>
          {/each}
        </div>

      </div>
    </div>

  </div>

  <!-- ── Final cycle view ── -->
  <!-- Observed by IntersectionObserver; reveals when user reaches here -->
  <div class="cycle-section" bind:this={cycleEl}>
    <div class="cycle-inner" class:visible={showCycle}>

      <p class="cycle-kicker">The Full Cycle</p>
      <h3 class="cycle-heading">How leptospirosis moves through an environment</h3>

      <div class="cycle-flow">
        {#each steps as step, i (i)}
          <!-- Each node staggers in via transition-delay -->
          <div
            class="cycle-node"
            style="transition-delay: {showCycle ? `${i * 0.12}s` : '0s'}"
          >
            <div class="cycle-img">
              <img src={step.image} alt={step.alt} loading="lazy" />
            </div>
            <p class="cycle-label">{step.shortLabel}</p>
          </div>

          {#if i < steps.length - 1}
            <span class="cycle-arrow" aria-hidden="true">→</span>
          {/if}
        {/each}
      </div>

    </div>
  </div>

</section>


<style>
  /* ── Full-bleed dark section ────────────────────────────── */
  .how-spreads {
    width: 100vw;
    margin-left: calc(50% - 50vw);
    background: #0d1117;
    color: #f0f0f0;
  }

  /* ── Section heading ────────────────────────────────────── */
  .spreads-heading-block {
    max-width: 680px;
    margin: 0 auto;
    padding: 4rem clamp(1.5rem, 5vw, 3rem) 2rem;
  }

  .spreads-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.7rem;
    font-weight: 700;
    letter-spacing: 0.22em;
    text-transform: uppercase;
    color: rgba(255, 200, 100, 0.85);
    margin: 0 0 0.6rem;
  }

  .spreads-heading {
    font-family: 'Crimson Text', Garamond, Georgia, serif;
    font-size: clamp(2rem, 5vw, 3.2rem);
    font-weight: 400;
    color: #ffffff;
    line-height: 1.15;
    margin: 0;
  }

  /* ── Google Doc intro block ─────────────────────────────── */
  .spreads-doc-intro {
    max-width: 680px;
    margin: 0 auto;
    padding: 4rem clamp(1.5rem, 5vw, 3rem) 2.5rem;
  }

  .spreads-doc-intro :global(h2),
  .spreads-doc-intro :global(h3) {
    font-family: 'Roboto Slab', serif;
    font-weight: 500;
    color: #ffffff;
    line-height: 1.2;
    margin-bottom: 0.75rem;
  }

  .spreads-doc-intro :global(h2) { font-size: clamp(1.5rem, 3.5vw, 2.2rem); }
  .spreads-doc-intro :global(h3) { font-size: clamp(1.1rem, 2.5vw, 1.5rem); }

  .spreads-doc-intro :global(p) {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 1.05rem;
    line-height: 1.75;
    color: rgba(240, 240, 240, 0.8);
    margin: 0 0 1rem;
  }

  /* ── Two-column scroll body ─────────────────────────────── */
  .spreads-body {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }

  /* ── LEFT scroll column ─────────────────────────────────── */
  /* Each panel is at least one viewport tall so there's
     enough scroll distance for the sticky image to register */
  .step-panel {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 4rem clamp(2rem, 5vw, 4rem);
  }

  /* Text card */
  .step-card {
    max-width: 400px;
    opacity: 0.28;
    transform: translateX(-10px);
    transition: opacity 0.45s ease, transform 0.45s ease;
  }

  .step-card.active {
    opacity: 1;
    transform: translateX(0);
  }

  .step-num {
    display: block;
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.68rem;
    font-weight: 700;
    letter-spacing: 0.22em;
    text-transform: uppercase;
    color: rgba(255, 200, 100, 0.85);
    margin-bottom: 0.6rem;
  }

  .step-label {
    font-family: 'Roboto Slab', serif;
    font-size: clamp(1.2rem, 2.5vw, 1.65rem);
    font-weight: 500;
    color: #ffffff;
    line-height: 1.2;
    margin: 0 0 0.75rem;
  }

  .step-desc {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: clamp(0.95rem, 1.8vw, 1.05rem);
    line-height: 1.75;
    color: rgba(240, 240, 240, 0.78);
    margin: 0;
  }

  /* Mobile inline image — hidden on desktop */
  .step-img-mobile {
    display: none;
  }

  /* ── RIGHT sticky column ─────────────────────────────────── */
  /* Must be position:relative so the sticky child can scroll
     within its bounds */
  .spreads-sticky-col {
    position: relative;
    border-left: 1px solid rgba(255, 255, 255, 0.06);
  }

  .spreads-sticky {
    position: sticky;
    top: 0;
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 2.5rem;
    gap: 1.25rem;
  }

  .sticky-img-wrap {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    flex: 1;
  }

  .sticky-img-wrap img {
    max-width: 100%;
    max-height: 55vh;
    object-fit: contain;
    /* Fallback tint while PNG loads */
    background: transparent;
  }

  .sticky-step-label {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.78rem;
    font-weight: 700;
    letter-spacing: 0.14em;
    text-transform: uppercase;
    color: rgba(255, 200, 100, 0.8);
    margin: 0;
  }

  /* Progress dots */
  .sticky-dots {
    display: flex;
    gap: 0.5rem;
  }

  .dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.2);
    transition: background 0.3s ease, transform 0.3s ease;
  }

  .dot.active {
    background: rgba(255, 200, 100, 0.9);
    transform: scale(1.4);
  }

  /* ── Cycle view ─────────────────────────────────────────── */
  .cycle-section {
    padding: 4.5rem clamp(1.5rem, 6vw, 5rem) 5rem;
    border-top: 1px solid rgba(255, 255, 255, 0.08);
  }

  .cycle-inner {
    max-width: 1100px;
    margin: 0 auto;
    opacity: 0;
    transform: translateY(28px);
    transition: opacity 0.7s ease, transform 0.7s ease;
  }

  .cycle-inner.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .cycle-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.7rem;
    font-weight: 700;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    color: rgba(255, 200, 100, 0.8);
    margin: 0 0 0.5rem;
  }

  .cycle-heading {
    font-family: 'Roboto Slab', serif;
    font-size: clamp(1.1rem, 2.5vw, 1.55rem);
    font-weight: 500;
    color: #ffffff;
    margin: 0 0 2.5rem;
    max-width: 580px;
  }

  .cycle-flow {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    gap: 0.5rem;
  }

  /* Each node fades and scales in with a stagger delay */
  .cycle-node {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.6rem;
    opacity: 0;
    transform: scale(0.82) translateY(12px);
    transition: opacity 0.5s ease, transform 0.5s ease;
  }

  .cycle-inner.visible .cycle-node {
    opacity: 1;
    transform: scale(1) translateY(0);
  }

  .cycle-img {
    width: clamp(90px, 10vw, 130px);
    height: clamp(90px, 10vw, 130px);
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba(255, 255, 255, 0.06);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 50%;
    padding: 0.85rem;
  }

  .cycle-img img {
    width: 100%;
    height: 100%;
    object-fit: contain;
  }

  .cycle-label {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.72rem;
    font-weight: 600;
    letter-spacing: 0.06em;
    color: rgba(240, 240, 240, 0.8);
    text-align: center;
    margin: 0;
    max-width: 90px;
  }

  /* Arrow between cycle nodes */
  .cycle-arrow {
    font-size: 1.3rem;
    color: rgba(255, 200, 100, 0.5);
    /* Push arrow down to align with image centers */
    margin-bottom: 1.8rem;
    flex-shrink: 0;
  }

  /* ── Rain effect (Step 3: Rain & Soil) ─────────────────── */
  .rain-overlay {
    position: absolute;
    inset: 0;
    overflow: hidden;
    pointer-events: none;
    z-index: 3;
  }

  .raindrop {
    position: absolute;
    top: -5%;
    width: 1.5px;
    background: linear-gradient(
      to bottom,
      transparent,
      rgba(140, 210, 255, 0.75) 30%,
      rgba(100, 180, 255, 0.55) 70%,
      transparent
    );
    border-radius: 0 0 2px 2px;
    animation: rainfall linear infinite;
  }

  @keyframes rainfall {
    from { transform: rotate(8deg) translateY(0); }
    to   { transform: rotate(8deg) translateY(130vh); }
  }

  /* Mobile rain host needs a positioning context */
  .rain-host {
    position: relative;
    overflow: hidden;
    border-radius: 8px;
  }

  /* ── Mobile layout ──────────────────────────────────────── */
  @media (max-width: 768px) {
    /* Collapse to single column */
    .spreads-body {
      grid-template-columns: 1fr;
    }

    /* Hide desktop sticky panel */
    .spreads-sticky-col {
      display: none;
    }

    /* Show inline image per step */
    .step-img-mobile {
      display: flex;
      justify-content: center;
      margin-top: 1.5rem;
    }

    .step-img-mobile img {
      max-width: 220px;
      max-height: 200px;
      object-fit: contain;
    }

    /* Remove dim/slide effect — all cards fully visible on mobile */
    .step-card {
      opacity: 1;
      transform: none;
      transition: none;
      max-width: 100%;
    }

    /* Shorter panels on mobile (no full-screen scroll needed) */
    .step-panel {
      min-height: unset;
      padding: 2.5rem 1.5rem;
      border-bottom: 1px solid rgba(255, 255, 255, 0.07);
    }

    /* Cycle: stack vertically on small screens */
    .cycle-flow {
      flex-direction: column;
      align-items: flex-start;
      gap: 0.25rem;
    }

    .cycle-arrow {
      transform: rotate(90deg);
      margin: 0.1rem 0 0.1rem clamp(35px, 4.5vw, 50px);
    }

    .cycle-node {
      flex-direction: row;
      align-items: center;
      gap: 1rem;
    }

    .cycle-img {
      width: 64px;
      height: 64px;
      flex-shrink: 0;
    }

    .cycle-label {
      max-width: unset;
      text-align: left;
      font-size: 0.85rem;
    }
  }
</style>
