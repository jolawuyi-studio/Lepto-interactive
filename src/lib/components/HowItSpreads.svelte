<!--
  HowItSpreads.svelte — Section 2: "How Leptospirosis Spreads"

  USAGE IN GOOGLE DOC:
  Add this single line wherever you want Section 2 to appear:

      [[HowItSpreads]]

  That's it. After running `npm run build:extract-google-doc` the full
  interactive section will render in that position.

  To update any text, edit this file directly.
-->

<script lang="ts">
  import { fade } from 'svelte/transition';

  const steps = [
    {
      number: '01',
      label: 'Rats & Animals',
      heading: 'Infected animals carry the bacteria',
      body: 'Rats, dogs, and other animals can carry Leptospira bacteria without showing any signs of illness. In the Berkeley case, both rats and two dogs near Harrison Street tested positive for the bacteria.'
    },
    {
      number: '02',
      label: 'Urine',
      heading: 'Bacteria shed through urine',
      body: 'Infected animals shed Leptospira bacteria through their urine. This is the primary way the bacteria enters the surrounding environment — often without any visible sign of contamination.'
    },
    {
      number: '03',
      label: 'Water & Soil',
      heading: 'Contaminated water and soil',
      body: 'The bacteria can survive in water, wet soil, and damp surfaces for days or weeks — especially after rain. Puddles, flooded areas, and garbage-filled zones near encampments can all become exposure sites.'
    },
    {
      number: '04',
      label: 'Human Exposure',
      heading: 'How people and pets are exposed',
      body: 'People and pets are typically exposed through skin contact — especially through cuts or abrasions — or by ingesting contaminated water. Dogs that walk or play in standing water face heightened risk.'
    },
    {
      number: '05',
      label: 'Symptoms',
      heading: 'Symptoms that are easy to miss',
      body: 'Symptoms appear 2 to 30 days after exposure. Early signs — fever, chills, muscle aches, headache — resemble the flu or food poisoning, making leptospirosis difficult to detect without a specific blood or urine test.'
    }
  ];

  let activeIndex = 0;
</script>

<section class="how-it-spreads" aria-labelledby="spreads-heading">

  <!-- Section intro -->
  <div class="spreads-intro">
    <p class="spreads-kicker">Explainer</p>
    <h2 id="spreads-heading" class="spreads-heading">How Leptospirosis Spreads</h2>
    <p class="spreads-intro-text">
      Leptospirosis is often invisible at first. The bacteria can move through animals,
      water, soil and human contact before anyone knows they have been exposed.
    </p>
  </div>

  <!-- Transmission chain — five clickable steps -->
  <div class="chain">
    {#each steps as step, i}
      <!-- Accessible button; aria-pressed tracks active state -->
      <button
        class="step-btn"
        class:active={activeIndex === i}
        aria-pressed={activeIndex === i}
        on:click={() => (activeIndex = i)}
      >
        <span class="step-number" aria-hidden="true">{step.number}</span>
        <span class="step-label">{step.label}</span>
      </button>

      <!-- Arrow connector between steps, hidden on mobile -->
      {#if i < steps.length - 1}
        <span class="chain-arrow" aria-hidden="true">›</span>
      {/if}
    {/each}
  </div>

  <!-- Progress bar: thin line filling as active step advances -->
  <div class="chain-progress" aria-hidden="true">
    <div
      class="chain-progress-fill"
      style="width: {((activeIndex) / (steps.length - 1)) * 100}%"
    ></div>
  </div>

  <!-- Explanation panel: fades in when active step changes -->
  {#key activeIndex}
    <div class="step-panel" in:fade={{ duration: 220 }}>
      <p class="panel-counter" aria-label="Step {activeIndex + 1} of {steps.length}">
        Step {activeIndex + 1} / {steps.length}
      </p>
      <h3 class="panel-heading">{steps[activeIndex].heading}</h3>
      <p class="panel-body">{steps[activeIndex].body}</p>

      <!-- Navigation arrows inside the panel -->
      <div class="panel-nav">
        <button
          class="nav-btn"
          disabled={activeIndex === 0}
          on:click={() => (activeIndex -= 1)}
          aria-label="Previous step"
        >← Prev</button>
        <button
          class="nav-btn"
          disabled={activeIndex === steps.length - 1}
          on:click={() => (activeIndex += 1)}
          aria-label="Next step"
        >Next →</button>
      </div>
    </div>
  {/key}

</section>

<style>
  /* ── Section wrapper ─────────────────────────────────────── */
  .how-it-spreads {
    margin: 3.5rem 0 4rem;
    padding: 2.5rem 0 0;
    border-top: 1px solid #e0e0e0;
  }

  /* ── Intro block ─────────────────────────────────────────── */
  .spreads-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.72rem;
    font-weight: 700;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: #888;
    margin-bottom: 0.6rem;
  }

  .spreads-heading {
    font-family: 'Roboto Slab', serif;
    font-size: clamp(1.5rem, 3.5vw, 2.1rem);
    font-weight: 500;
    color: #1a1a1a;
    margin-bottom: 0.75rem;
    line-height: 1.2;
  }

  .spreads-intro-text {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 1rem;
    line-height: 1.65;
    color: #555;
    max-width: 560px;
    margin-bottom: 2rem;
  }

  /* ── Transmission chain ──────────────────────────────────── */
  .chain {
    display: flex;
    flex-direction: row;
    align-items: center;
    flex-wrap: wrap;
    gap: 0.35rem;
    margin-bottom: 0.5rem;
  }

  .step-btn {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.35rem;
    padding: 0.7rem 1rem;
    min-width: 90px;

    background: #f2f2f2;
    border: 1.5px solid transparent;
    border-radius: 6px;
    cursor: pointer;

    transition: background 0.2s ease, border-color 0.2s ease, color 0.2s ease,
      transform 0.15s ease;

    /* Reset browser button styles */
    font-family: inherit;
    color: #444;
  }

  .step-btn:hover:not(.active) {
    background: #e8e8e8;
    border-color: #ccc;
    transform: translateY(-1px);
  }

  .step-btn:focus-visible {
    outline: 2px solid #1a1a1a;
    outline-offset: 2px;
  }

  /* Active step: filled dark */
  .step-btn.active {
    background: #1a1a1a;
    border-color: #1a1a1a;
    color: #fff;
    transform: translateY(-1px);
  }

  .step-number {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.65rem;
    font-weight: 700;
    letter-spacing: 0.1em;
    opacity: 0.6;
  }

  .step-btn.active .step-number {
    opacity: 0.7;
  }

  .step-label {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.8rem;
    font-weight: 600;
    text-align: center;
    line-height: 1.3;
  }

  /* Arrow between steps — hidden on small screens */
  .chain-arrow {
    font-size: 1.3rem;
    color: #bbb;
    line-height: 1;
    user-select: none;
    flex-shrink: 0;
  }

  @media (max-width: 600px) {
    .chain {
      gap: 0.5rem;
    }

    .chain-arrow {
      display: none;
    }

    .step-btn {
      flex-direction: row;
      min-width: unset;
      width: 100%;
      justify-content: flex-start;
      gap: 0.75rem;
      text-align: left;
    }

    .step-label {
      text-align: left;
    }
  }

  /* ── Progress bar ────────────────────────────────────────── */
  .chain-progress {
    height: 2px;
    background: #e8e8e8;
    border-radius: 2px;
    margin: 0.75rem 0 1.5rem;
    overflow: hidden;
  }

  .chain-progress-fill {
    height: 100%;
    background: #1a1a1a;
    border-radius: 2px;
    transition: width 0.35s ease;
  }

  /* ── Explanation panel ───────────────────────────────────── */
  .step-panel {
    background: #fafafa;
    border: 1px solid #e8e8e8;
    border-left: 3px solid #1a1a1a;
    border-radius: 0 6px 6px 0;
    padding: 1.4rem 1.6rem 1.2rem;
  }

  .panel-counter {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.7rem;
    font-weight: 700;
    letter-spacing: 0.14em;
    text-transform: uppercase;
    color: #999;
    margin-bottom: 0.5rem;
  }

  .panel-heading {
    font-family: 'Roboto Slab', serif;
    font-size: clamp(1rem, 2.5vw, 1.2rem);
    font-weight: 500;
    color: #1a1a1a;
    margin-bottom: 0.6rem;
    line-height: 1.3;
  }

  .panel-body {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.97rem;
    line-height: 1.7;
    color: #444;
    margin-bottom: 1.1rem;
  }

  /* ── Prev / Next navigation inside panel ────────────────── */
  .panel-nav {
    display: flex;
    gap: 0.6rem;
  }

  .nav-btn {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.78rem;
    font-weight: 600;
    letter-spacing: 0.04em;
    color: #1a1a1a;
    background: none;
    border: 1.5px solid #ccc;
    border-radius: 4px;
    padding: 0.3rem 0.8rem;
    cursor: pointer;
    transition: background 0.15s ease, border-color 0.15s ease;
  }

  .nav-btn:hover:not(:disabled) {
    background: #f0f0f0;
    border-color: #aaa;
  }

  .nav-btn:disabled {
    opacity: 0.35;
    cursor: default;
  }

  .nav-btn:focus-visible {
    outline: 2px solid #1a1a1a;
    outline-offset: 2px;
  }
</style>
