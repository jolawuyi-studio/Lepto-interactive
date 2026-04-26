<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import { browser } from '$app/environment';

  const beats = [
    {
      label: 'Nov–Dec 2025',
      text: 'Samples were collected from rats and dogs near Harrison Street.'
    },
    {
      label: 'Jan. 12, 2026',
      text: 'Berkeley issued a public health alert.'
    },
    {
      label: 'Harrison Street',
      text: 'The warning centered around an area where unhoused residents live.'
    }
  ];

  let beatEls: (HTMLElement | null)[] = new Array(beats.length).fill(null);
  let visible: boolean[] = new Array(beats.length).fill(false);
  let observer: IntersectionObserver | null = null;

  onMount(() => {
    if (!browser) return;

    observer = new IntersectionObserver(
      (entries) => {
        for (const entry of entries) {
          const idx = beatEls.indexOf(entry.target as HTMLElement);
          if (idx !== -1 && entry.isIntersecting) {
            visible[idx] = true;
            visible = [...visible]; // trigger Svelte reactivity
          }
        }
      },
      { threshold: 0.25 }
    );

    for (const el of beatEls) {
      if (el) observer.observe(el);
    }
  });

  onDestroy(() => {
    observer?.disconnect();
  });
</script>

<section class="the-alert full-bleed">

  <!--
    STICKY BACKGROUND
    This element stays pinned at the top of the viewport while the user scrolls
    through the hero intro and the three timeline beat cards below it.
  -->
  <div class="alert-bg" role="img" aria-label="Harrison Street, Berkeley">
    <!--
      *** REPLACE THE IMAGE URL HERE ***
      1. Add your Harrison Street photo to the `static/` folder
         (e.g. static/harrison-street.jpg)
      2. Change the background-image value below to:
            url('/harrison-street.jpg')
      The current placeholder is the existing hero-fallback.jpg.
    -->
    <div
      class="alert-bg-img"
      style="background-image: url('/hero-fallback.jpg')"
    ></div>

    <!-- Dark gradient overlay — lighter at top, heavier at bottom for readability -->
    <div class="alert-bg-overlay"></div>
  </div>

  <!--
    SCROLLABLE CONTENT LAYER
    Pulled up over the sticky background with a negative margin.
    Everything here scrolls normally while the background stays pinned.
  -->
  <div class="alert-scroll-layer">

    <!-- ── HERO INTRO (fills the first viewport) ── -->
    <div class="alert-intro">
      <div class="alert-intro-inner">
        <p class="alert-dateline">January 12, 2026</p>
        <h1 class="alert-headline">
          Berkeley issued a health alert near Harrison Street.
        </h1>
        <p class="alert-sub">
          Rats and two dogs had tested positive for Leptospira bacteria.
          For residents living nearby, the alert marked the beginning of uncertainty.
        </p>
      </div>

      <!-- Animated scroll cue pinned to bottom of intro panel -->
      <div class="scroll-cue" aria-hidden="true">
        <span>Scroll to begin</span>
        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24"
          fill="none" stroke="currentColor" stroke-width="2"
          stroke-linecap="round" stroke-linejoin="round">
          <line x1="12" y1="5" x2="12" y2="19"></line>
          <polyline points="19 12 12 19 5 12"></polyline>
        </svg>
      </div>
    </div>

    <!-- ── TIMELINE BEATS ── -->
    <!-- Each card fades in when it enters the viewport (IntersectionObserver above) -->
    <div class="alert-beats">
      {#each beats as beat, i}
        <div class="beat-step" bind:this={beatEls[i]}>
          <!--
            transition-delay staggers each card slightly so they don't all
            animate at once if the user scrolls quickly past them.
          -->
          <div
            class="beat-card"
            class:visible={visible[i]}
            style="transition-delay: {visible[i] ? i * 0.08 : 0}s"
          >
            <span class="beat-label">{beat.label}</span>
            <p class="beat-text">{beat.text}</p>
          </div>
        </div>
      {/each}
    </div>

    <!-- Spacer so the sticky background has room to scroll away cleanly after the last beat -->
    <div class="alert-end-spacer"></div>

  </div>
</section>

<style>
  /* ── Section wrapper ─────────────────────────────────────── */
  .the-alert {
    position: relative;
  }

  /* ── Sticky background ───────────────────────────────────── */
  .alert-bg {
    position: sticky;
    top: 0;
    height: 100vh;
    width: 100%;
    overflow: hidden;
    z-index: 0;
  }

  .alert-bg-img {
    position: absolute;
    inset: 0;
    background-size: cover;
    background-position: center;
  }

  .alert-bg-overlay {
    position: absolute;
    inset: 0;
    background: linear-gradient(
      to bottom,
      rgba(0, 0, 0, 0.30) 0%,
      rgba(0, 0, 0, 0.55) 45%,
      rgba(0, 0, 0, 0.78) 100%
    );
  }

  /* ── Scrollable layer on top of sticky bg ────────────────── */
  .alert-scroll-layer {
    position: relative;
    z-index: 1;
    margin-top: -100vh; /* pulls layer back up over the sticky background */
  }

  /* ── Hero intro panel ────────────────────────────────────── */
  .alert-intro {
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 2rem 1.5rem;
    text-align: center;
    position: relative;
  }

  .alert-intro-inner {
    max-width: 720px;
  }

  .alert-dateline {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: clamp(0.72rem, 1.8vw, 0.9rem);
    font-weight: 400;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    color: rgba(255, 255, 255, 0.65);
    margin-bottom: 1.25rem;
  }

  .alert-headline {
    /* Uses the Crimson Text display font declared in app.scss */
    font-family: 'Crimson Text', Garamond, Georgia, serif;
    font-size: clamp(2rem, 5.5vw, 3.75rem);
    font-weight: 400;
    line-height: 1.15;
    color: #ffffff;
    margin-bottom: 1.5rem;
  }

  .alert-sub {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: clamp(1rem, 2.2vw, 1.2rem);
    font-weight: 300;
    line-height: 1.7;
    color: rgba(255, 255, 255, 0.82);
    max-width: 580px;
    margin: 0 auto;
  }

  /* Scroll cue: pinned to the bottom of the intro panel, gently bounces */
  .scroll-cue {
    position: absolute;
    bottom: 2.5rem;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.35rem;
    color: rgba(255, 255, 255, 0.55);
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.72rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    animation: nudge 2.2s ease-in-out infinite;
    pointer-events: none;
  }

  @keyframes nudge {
    0%, 100% { transform: translateX(-50%) translateY(0); }
    50%       { transform: translateX(-50%) translateY(6px); }
  }

  /* ── Timeline beat cards ─────────────────────────────────── */
  .alert-beats {
    padding: 0 1.5rem;
  }

  /* Each beat-step provides the scroll distance for one card reveal */
  .beat-step {
    min-height: 70vh;
    display: flex;
    align-items: center;
    padding: 4vh 0;
    /* Left-aligned on desktop, centered on mobile — mirrors newspaper column convention */
    max-width: 500px;
    margin-left: max(8vw, 1.5rem);
  }

  @media (max-width: 768px) {
    .beat-step {
      margin-left: auto;
      margin-right: auto;
    }
  }

  /* Card starts hidden and slightly below; .visible triggers the reveal */
  .beat-card {
    opacity: 0;
    transform: translateY(18px);
    transition: opacity 0.65s ease, transform 0.65s ease;

    border-left: 3px solid rgba(255, 255, 255, 0.5);
    padding: 1.1rem 1.4rem;
    border-radius: 0 3px 3px 0;

    /* Frosted-glass effect — works in all modern browsers */
    background: rgba(0, 0, 0, 0.35);
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
  }

  .beat-card.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .beat-label {
    display: block;
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.72rem;
    font-weight: 700;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    /* Warm amber accent — subtle, not distracting */
    color: rgba(255, 210, 120, 0.9);
    margin-bottom: 0.45rem;
  }

  .beat-text {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: clamp(1rem, 2.2vw, 1.15rem);
    font-weight: 300;
    line-height: 1.65;
    color: rgba(255, 255, 255, 0.92);
    margin: 0;
  }

  /* ── End spacer ──────────────────────────────────────────── */
  /* Gives the sticky background room to scroll away after the last card */
  .alert-end-spacer {
    height: 35vh;
  }
</style>
