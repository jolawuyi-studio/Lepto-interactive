<!--
  MeetYesica.svelte — "Meet Yesica & the Harrison Street Community"

  ════════════════════════════════════════════════════════════
  GOOGLE DOC USAGE
  ════════════════════════════════════════════════════════════
  Wrap your story text for this section in block shortcode tags:

      [[MeetYesica]]
      ## Meet Yesica
      For residents on Harrison Street...
      [[/MeetYesica]]

  ════════════════════════════════════════════════════════════
  WHAT LIVES WHERE
  ════════════════════════════════════════════════════════════
  Google Doc  →  Heading, paragraphs, attribution
  This file   →  Photos, audio path, video path, pull quotes,
                 snapshot data, all layout & interactivity

  ════════════════════════════════════════════════════════════
  MEDIA PLACEHOLDERS
  ════════════════════════════════════════════════════════════
  Audio  →  Replace `/audio/yesica-clip.mp3` in the script below
            with your actual interview clip. Create the folder
            static/audio/ and drop the file there.
  Video  →  `lepto moving.mp4` is the current placeholder.
            Replace with your encampment footage when ready.
  Photos →  All photos are already wired from static/.
            Update `photos` array captions as needed.
-->

<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import { browser } from '$app/environment';
  import { fade } from 'svelte/transition';
  import { base } from '$app/paths';

  // Story text from the Google Doc block shortcode
  export let bodyHtml: string = '';

  // ── Photo gallery ─────────────────────────────────────────
  // All photos currently in static/. Update captions as reporting develops.
  const photos = [
    {
      src: `${base}/8thstreet-01.JPG`,
      alt: 'Harrison Street encampment area, Berkeley',
      caption: 'Harrison Street became the center of Berkeley\'s leptospirosis alert in January 2026.'
    },
    {
      src: `${base}/8thstreet-02.JPG`,
      alt: 'Harrison Street, Berkeley',
      caption: 'The area near Harrison Street is home to an unhoused encampment of roughly 38–40 people.'
    },
    {
      src: `${base}/DSCF4079.JPG`,
      alt: 'Scene from Harrison Street encampment',
      caption: 'Residents describe the encampment as a community built over years of shared experience.'
    },
    {
      src: `${base}/DSCF4089.JPG`,
      alt: 'Harrison Street encampment',
      caption: 'For many residents, the first fear was not the disease itself, but displacement.'
    },
    {
      src: `${base}/DSCF4091.JPG`,
      alt: 'Details from the encampment',
      caption: 'Some residents have lived at the encampment for years, forming tight-knit networks.'
    },
    {
      src: `${base}/DSCF4096.JPG`,
      alt: 'Harrison Street encampment detail',
      caption: 'Residents say consistent sanitation support from the city has been hard to come by.'
    },
    {
      src: `${base}/DSCF4104.JPG`,
      alt: 'Harrison Street, January 2026',
      caption: 'The encampment includes residents with pets — dogs and cats — who also faced potential exposure.'
    },
    {
      src: `${base}/DSCF4120.JPG`,
      alt: 'Harrison Street encampment, Berkeley',
      caption: 'Yesica, a Homeless Union leader, tried to communicate the health alert to encampment residents.'
    },
    {
      src: `${base}/DSCF4128.JPG`,
      alt: 'Harrison Street encampment, January 2026',
      caption: 'Residents described self-organizing to keep food away from rats and clean their own areas.'
    },
    {
      src: `${base}/DSCF4137.JPG`,
      alt: 'Harrison Street environment',
      caption: 'Open lots and alleyways near the encampment were among areas where rodent activity was reported.'
    }
  ];

  // ── Pull quotes ───────────────────────────────────────────
  const pullQuotes = [
    {
      text: '“Nobody was really talking to us.”',
      attr: '— Yesica, Homeless Union leader'
    },
    {
      text: '“People were more afraid of being evicted — asking, where are we going to go next?”',
      attr: '— Yesica'
    },
    {
      text: '“If we don’t get support from the city to take care of the garbage, the pile just attracts all the rats.”',
      attr: '— Yesica'
    }
  ];

  // ── Snapshot data ─────────────────────────────────────────
  const snapshot = [
    { label: 'Estimated residents', value: '38–40' },
    { label: 'Pets',                value: 'Dogs and cats' },
    { label: 'Main concern',        value: 'Displacement' },
    { label: 'Unresolved issue',    value: 'Sanitation and communication' }
  ];

  // ── Precomputed waveform bar heights (avoids re-render jitter) ──
  const waveBars = Array.from({ length: 24 }, () =>
    Math.round(20 + Math.random() * 80)
  );

  // ── Gallery state ─────────────────────────────────────────
  let activePhoto = 0;

  function prevPhoto() {
    activePhoto = (activePhoto - 1 + photos.length) % photos.length;
  }
  function nextPhoto() {
    activePhoto = (activePhoto + 1) % photos.length;
  }

  // ── Audio player state ────────────────────────────────────
  let audioEl: HTMLAudioElement | null = null;
  let isPlaying = false;
  let audioProgress = 0;
  let audioCurrentTime = 0;
  let audioDuration = 0;

  function toggleAudio() {
    if (!audioEl) return;
    isPlaying ? audioEl.pause() : audioEl.play();
  }

  function onTimeUpdate() {
    if (!audioEl) return;
    audioCurrentTime = audioEl.currentTime;
    audioDuration    = audioEl.duration || 0;
    audioProgress    = audioDuration ? (audioCurrentTime / audioDuration) * 100 : 0;
  }

  function seekAudio(e: MouseEvent) {
    if (!audioEl || !audioDuration) return;
    const bar  = e.currentTarget as HTMLElement;
    const rect = bar.getBoundingClientRect();
    audioEl.currentTime = ((e.clientX - rect.left) / rect.width) * audioDuration;
  }

  function fmt(s: number) {
    const m = Math.floor(s / 60);
    return `${m}:${String(Math.floor(s % 60)).padStart(2, '0')}`;
  }

  // ── Scroll reveal ─────────────────────────────────────────
  let quoteEls:   (HTMLElement | null)[] = new Array(pullQuotes.length).fill(null);
  let quoteVis:   boolean[]              = new Array(pullQuotes.length).fill(false);
  let snapshotEl: HTMLElement | null = null;
  let snapshotVis = false;
  let videoWrapEl: HTMLElement | null = null;
  let videoVis    = false;
  let observer: IntersectionObserver | null = null;

  onMount(() => {
    if (!browser) return;
    observer = new IntersectionObserver(
      (entries) => {
        for (const e of entries) {
          if (!e.isIntersecting) continue;
          const qi = quoteEls.indexOf(e.target as HTMLElement);
          if (qi !== -1) { quoteVis[qi] = true; quoteVis = [...quoteVis]; }
          if (e.target === snapshotEl)  snapshotVis = true;
          if (e.target === videoWrapEl) videoVis    = true;
        }
      },
      { threshold: 0.18 }
    );
    quoteEls.forEach(el => el && observer?.observe(el));
    if (snapshotEl)  observer.observe(snapshotEl);
    if (videoWrapEl) observer.observe(videoWrapEl);
  });

  onDestroy(() => observer?.disconnect());
</script>


<section class="meet-yesica" aria-labelledby="yesica-heading">

  <!-- ── Section header ── -->
  <header class="yesica-header">
    <p class="yesica-kicker">Harrison Street</p>
    <h2 id="yesica-heading" class="yesica-title">Meet the community</h2>
  </header>

  <!-- ══════════════════════════════════════════════════════
       PHOTO GALLERY
       ══════════════════════════════════════════════════════ -->
  <div class="gallery-section">

    <!-- Main photo with fade on change -->
    <div class="gallery-main" role="img" aria-label={photos[activePhoto].alt}>
      {#key activePhoto}
        <img
          src={photos[activePhoto].src}
          alt={photos[activePhoto].alt}
          in:fade={{ duration: 380 }}
        />
      {/key}

      <!-- Prev / Next arrows -->
      <button class="gallery-arrow gallery-prev" on:click={prevPhoto} aria-label="Previous photo">‹</button>
      <button class="gallery-arrow gallery-next" on:click={nextPhoto} aria-label="Next photo">›</button>

      <!-- Counter badge -->
      <span class="gallery-counter" aria-live="polite">{activePhoto + 1} / {photos.length}</span>
    </div>

    <!-- Caption -->
    <p class="gallery-caption">{photos[activePhoto].caption}</p>

    <!-- Thumbnail strip — horizontal scroll -->
    <div class="gallery-thumbs" role="group" aria-label="Photo thumbnails">
      {#each photos as photo, i (i)}
        <button
          class="thumb-btn"
          class:active={activePhoto === i}
          aria-pressed={activePhoto === i}
          aria-label="Photo {i + 1}: {photo.alt}"
          on:click={() => (activePhoto = i)}
        >
          <img src={photo.src} alt="" loading="lazy" />
        </button>
      {/each}
    </div>

  </div>

  <!-- ══════════════════════════════════════════════════════
       CONTEXT TEXT
       ══════════════════════════════════════════════════════ -->
  <div class="context-text">
    <p>Dr. Gabriel Treuba, the director of microbiology at the University of San Francisco de Quito and a public health advocate for the country of Ecuador, described that globally, lepto is treated as an afterthought by many governments. Researchers who study this illness share a sense of frustration due to its constant dismissal and suspect that this is because of the demographic that Leptospirosis usually affects.</p>
    <blockquote>
      <p>"The problem is mainly in the poor neighborhoods. Places where they do not have places for sewage, places where the contact with rats is very intense. Vaccinating the animals is the only way that you can prevent the disease. The problem is when you get into poor people that don't have any money for the vaccine — that is not provided by the government [they] have to buy it."</p>
      <cite>— Dr. Gabriel Trueba</cite>
    </blockquote>
    <p>According to Dr. Trueba cases are often reported after periods of rainfall, but there is little understanding of how long the bacteria truly survive. However, we do know that the rising cases in the United States are due to climate change, but the disease has only been prevalent in communities that lack proper sewage systems and conditions where vermin can thrive like the unhoused encampments.</p>
  </div>

  <!-- ══════════════════════════════════════════════════════
       STORY BODY: text (left) + sidebar (right)
       ══════════════════════════════════════════════════════ -->
  <div class="yesica-body">

    <!-- Story text from Google Doc -->
    <div class="yesica-story">
      {#if bodyHtml}
        <!-- eslint-disable-next-line svelte/no-at-html-tags -->
        {@html bodyHtml}
      {:else}
        <figure class="yesica-portrait">
          <img src="{base}/DSCF4079.JPG" alt="Yesica Prado, Berkeley Homeless Union leader" />
          <figcaption>Yesica Prado at the Harrison Street encampment, Berkeley.</figcaption>
        </figure>
        <p>Yesica Prado, a leader with the Berkeley Homeless Union and a resident advocate at the Harrison Street encampment, has been closely following the public health alert warning about leptospirosis risk in the area. As someone who helps organize services and communicate information to residents, she sees the alert as both a warning and a source of concern within the community.</p>
        <p>While she acknowledges the seriousness of the disease risk, Yesica says many residents are worried about how the alert could affect their safety and stability. For her, the issue is not only about bacteria or public health guidance, but about how warnings translate into action that directly affects people living outdoors.</p>
      {/if}
    </div>

    <!-- Sidebar: pull quotes + audio player -->
    <aside class="yesica-sidebar">

      <!-- Pull quote 1 -->
      <blockquote
        class="pull-quote"
        class:visible={quoteVis[0]}
        bind:this={quoteEls[0]}
      >
        <p class="quote-text">{pullQuotes[0].text}</p>
        <cite class="quote-attr">{pullQuotes[0].attr}</cite>
      </blockquote>

      <!-- ── Audio player ── -->
      <div class="audio-card">
        <p class="audio-kicker">Listen</p>
        <p class="audio-label">Yesica on the health alert</p>

        <!--
          REPLACE: Drop your interview clip into static/audio/
          and update the src below to `/audio/your-file.mp3`
          The player will show as inactive until a real file is provided.
        -->
        <!-- svelte-ignore a11y-media-has-caption -->
        <audio
          bind:this={audioEl}
          src="{base}/yesica-clip.mp3"
          on:timeupdate={onTimeUpdate}
          on:play={() => (isPlaying = true)}
          on:pause={() => (isPlaying = false)}
          on:ended={() => { isPlaying = false; audioProgress = 0; audioCurrentTime = 0; }}
        ></audio>

        <!-- Waveform visualization -->
        <div class="waveform" aria-hidden="true">
          {#each waveBars as h, j (j)}
            <span
              class="wave-bar"
              class:playing={isPlaying}
              style="height:{h}%; animation-delay:{j * 0.04}s"
            ></span>
          {/each}
          <!-- Playhead overlay -->
          <div class="waveform-progress" style="width:{audioProgress}%"></div>
        </div>

        <!-- Controls row -->
        <div class="audio-controls">
          <button
            class="audio-play"
            class:playing={isPlaying}
            on:click={toggleAudio}
            aria-label={isPlaying ? 'Pause' : 'Play interview clip'}
          >
            {#if isPlaying}
              <span aria-hidden="true">⏸</span>
            {:else}
              <span aria-hidden="true">▶</span>
            {/if}
          </button>

          <!-- Seekbar -->
          <!-- svelte-ignore a11y-click-events-have-key-events -->
          <!-- svelte-ignore a11y-no-static-element-interactions -->
          <div class="seekbar" on:click={seekAudio} title="Click to seek">
            <div class="seekbar-fill" style="width:{audioProgress}%"></div>
          </div>

          <span class="audio-time">
            {audioDuration ? `${fmt(audioCurrentTime)} / ${fmt(audioDuration)}` : '0:00'}
          </span>
        </div>
      </div>

      <!-- Pull quote 2 -->
      <blockquote
        class="pull-quote"
        class:visible={quoteVis[1]}
        bind:this={quoteEls[1]}
      >
        <p class="quote-text">{pullQuotes[1].text}</p>
        <cite class="quote-attr">{pullQuotes[1].attr}</cite>
      </blockquote>

    </aside>
  </div>

  <!-- ══════════════════════════════════════════════════════
       BOTTOM ROW: pull quote 3 + snapshot card
       ══════════════════════════════════════════════════════ -->
  <div class="yesica-bottom">

    <blockquote
      class="pull-quote pull-quote--large"
      class:visible={quoteVis[2]}
      bind:this={quoteEls[2]}
    >
      <p class="quote-text">{pullQuotes[2].text}</p>
      <cite class="quote-attr">{pullQuotes[2].attr}</cite>
    </blockquote>

    <aside
      class="snapshot-card"
      class:visible={snapshotVis}
      bind:this={snapshotEl}
      aria-label="Encampment snapshot"
    >
      <p class="snapshot-kicker">Encampment snapshot</p>
      <ul class="snapshot-list">
        {#each snapshot as row (row.label)}
          <li class="snapshot-row">
            <span class="snapshot-label">{row.label}</span>
            <span class="snapshot-value">{row.value}</span>
          </li>
        {/each}
      </ul>
      <p class="snapshot-source">Estimates from Yesica at time of reporting.</p>
    </aside>

  </div>

  <!-- ══════════════════════════════════════════════════════
       CLOSING TEXT
       ══════════════════════════════════════════════════════ -->
  <div class="context-text">
    <p>Leptospirosis is a zoonotic disease that primarily affects animals and is found predominantly in tropical countries in Africa, Asia, and Central and South America, but it is now becoming more common in the United States. In 2014, the CDC declared Leptospirosis a National Notifiable Disease, which means the disease is closely monitored and that positive cases must be reported to the department. Notifiable diseases pose threats to public health due to their potential to cause an outbreak or an epidemic. Diseases like Leptospirosis need to be addressed adequately and with a sense of urgency.</p>
    <p>According to an article published by the National Library of Medicine, Leptospirosis is a difficult disease to track, both in terms of illness and from a bacterial standpoint. Symptoms can appear anywhere from a week to six weeks after exposure, and the severity can range from chills and fever to organ failure. The disease can often be mistaken for a cold or the flu.</p>
  </div>

  <!-- ══════════════════════════════════════════════════════
       VIDEO
       ══════════════════════════════════════════════════════ -->
  <div
    class="video-section"
    class:visible={videoVis}
    bind:this={videoWrapEl}
  >
    <div class="video-inner">
      <p class="video-kicker">Harrison Street</p>
      <h3 class="video-heading">Meet Erin</h3>

      <p class="video-intro">Erin, a veteran who has been unhoused for nearly a decade, has been living in Berkeley for about ______ years. About 3 years ago, he had been diagnosed with Leptospirosis living in an encampment on Ashby in Berkeley. Erin describes the symptoms as the worst pain of his life.</p>

      <!--
        REPLACE: swap `lepto moving.mp4` with your encampment interview
        or B-roll footage. Drop the file into static/ and update the src.
      -->
      <!-- svelte-ignore a11y-media-has-caption -->
      <video
        src="{base}/lepto%20moving.mp4"
        controls
        playsinline
        preload="metadata"
        aria-label="Footage from Harrison Street encampment — Meet Erin"
      ></video>

      <p class="video-caption">
        Footage from Harrison Street, Berkeley.
        <span class="video-note">Replace with Erin's interview footage when available.</span>
      </p>
    </div>
  </div>

  <!-- ══════════════════════════════════════════════════════
       DR. TRI DO
       ══════════════════════════════════════════════════════ -->
  <div class="context-text">
    <p>Dr. Tri Do is the Medical Director of Alameda County Health Care for the Homeless Housing and Homelessness Services, a company that assists both the county and the city with emergency public health issues. Throughout this bacterial outbreak, Alameda County Health provided biweekly symptom screenings for the encampment.</p>
    <p>During symptom screening, Dr. Do reported that about three people had vague symptoms, but none that would indicate a positive Leptospirosis test. Alameda County Health is still conducting symptom screenings in hopes of providing consistent care for the unhoused community.</p>
    <blockquote>
      <p>"Homeless people deserve to have housing and great healthcare. That's why we do what we do."</p>
      <cite>— Dr. Tri Do</cite>
    </blockquote>
  </div>

</section>


<style>
  /* ── Full-bleed dark section ────────────────────────────── */
  .meet-yesica {
    width: 100vw;
    margin-left: calc(50% - 50vw);
    background: #0f0f0f;
    color: #e8e8e8;
    padding-bottom: 5rem;
  }

  /* ── Section header ──────────────────────────────────────── */
  .yesica-header {
    max-width: 1100px;
    margin: 0 auto;
    padding: 4rem clamp(1.5rem, 5vw, 4rem) 2rem;
    border-bottom: 1px solid rgba(255,255,255,0.08);
  }

  .yesica-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.7rem;
    font-weight: 700;
    letter-spacing: 0.22em;
    text-transform: uppercase;
    color: rgba(255, 200, 100, 0.85);
    margin-bottom: 0.5rem;
  }

  .yesica-title {
    font-family: 'Crimson Text', Garamond, Georgia, serif;
    font-size: clamp(2.2rem, 5vw, 3.5rem);
    font-weight: 400;
    color: #ffffff;
    line-height: 1.1;
    margin: 0;
  }

  /* ── Photo gallery ───────────────────────────────────────── */
  .gallery-section {
    max-width: 1100px;
    margin: 0 auto;
    padding: 2.5rem clamp(1.5rem, 5vw, 4rem) 0;
  }

  .gallery-main {
    position: relative;
    width: 100%;
    aspect-ratio: 3 / 2;
    background: #1a1a1a;
    overflow: hidden;
    border-radius: 4px;
  }

  .gallery-main img {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
  }

  /* Prev / Next arrows */
  .gallery-arrow {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    z-index: 2;
    background: rgba(0,0,0,0.5);
    color: #fff;
    border: none;
    width: 2.8rem;
    height: 2.8rem;
    border-radius: 50%;
    font-size: 1.5rem;
    line-height: 1;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background 0.2s;
  }

  .gallery-arrow:hover { background: rgba(0,0,0,0.75); }
  .gallery-prev { left: 0.8rem; }
  .gallery-next { right: 0.8rem; }

  .gallery-arrow:focus-visible {
    outline: 2px solid rgba(255,200,100,0.8);
    outline-offset: 2px;
  }

  /* Counter badge */
  .gallery-counter {
    position: absolute;
    bottom: 0.75rem;
    right: 0.85rem;
    background: rgba(0,0,0,0.55);
    color: rgba(255,255,255,0.85);
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.7rem;
    letter-spacing: 0.1em;
    padding: 0.2rem 0.55rem;
    border-radius: 20px;
    pointer-events: none;
  }

  .gallery-caption {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.78rem;
    font-style: italic;
    color: rgba(255,255,255,0.5);
    line-height: 1.55;
    margin: 0.6rem 0 1rem;
    min-height: 2.4em;
  }

  /* Thumbnail strip */
  .gallery-thumbs {
    display: flex;
    gap: 0.4rem;
    overflow-x: auto;
    padding-bottom: 0.35rem;
    scrollbar-width: thin;
    scrollbar-color: rgba(255,255,255,0.15) transparent;
  }

  .thumb-btn {
    flex-shrink: 0;
    width: 72px;
    height: 54px;
    padding: 0;
    border: 2px solid transparent;
    border-radius: 3px;
    overflow: hidden;
    cursor: pointer;
    background: #222;
    opacity: 0.5;
    transition: opacity 0.2s, border-color 0.2s;
  }

  .thumb-btn img { width: 100%; height: 100%; object-fit: cover; display: block; }
  .thumb-btn.active  { border-color: rgba(255,200,100,0.85); opacity: 1; }
  .thumb-btn:hover:not(.active) { opacity: 0.8; }
  .thumb-btn:focus-visible { outline: 2px solid rgba(255,200,100,0.8); outline-offset: 2px; }

  /* ── Context text (between gallery and story body) ─────────── */
  .context-text {
    max-width: 720px;
    margin: 3rem auto 0;
    padding: 0 clamp(1.5rem, 5vw, 4rem);
  }

  .context-text p {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 1rem;
    line-height: 1.85;
    color: rgba(232, 232, 232, 0.85);
    margin: 0 0 1.25rem;
  }

  .context-text blockquote {
    margin: 1.75rem 0;
    padding: 1rem 1.5rem;
    border-left: 3px solid rgba(255, 200, 100, 0.7);
    background: rgba(255, 255, 255, 0.04);
    border-radius: 0 4px 4px 0;
  }

  .context-text blockquote p {
    font-family: 'Crimson Text', Garamond, Georgia, serif;
    font-size: clamp(1.1rem, 2vw, 1.3rem);
    font-style: italic;
    color: rgba(255, 255, 255, 0.92);
    line-height: 1.6;
    margin: 0 0 0.65rem;
  }

  .context-text blockquote cite {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.72rem;
    font-style: normal;
    font-weight: 700;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: rgba(255, 200, 100, 0.75);
  }

  /* ── Story body grid ─────────────────────────────────────── */
  .yesica-body {
    max-width: 1100px;
    margin: 3rem auto 0;
    padding: 0 clamp(1.5rem, 5vw, 4rem);
    display: grid;
    grid-template-columns: 1fr 380px;
    gap: 3.5rem;
    align-items: start;
  }

  @media (max-width: 900px) {
    .yesica-body { grid-template-columns: 1fr; gap: 2rem; }
  }

  /* ── Yesica portrait (placeholder state only) ───────────── */
  .yesica-portrait {
    margin: 0 0 1.5rem;
    border-radius: 4px;
    overflow: hidden;
  }

  .yesica-portrait img {
    width: 100%;
    display: block;
    object-fit: cover;
    max-height: 420px;
    border-radius: 4px;
  }

  .yesica-portrait figcaption {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.75rem;
    font-style: italic;
    color: rgba(255,255,255,0.45);
    margin-top: 0.45rem;
    line-height: 1.5;
  }

  /* ── Story text (Google Doc HTML) ───────────────────────── */
  .yesica-story :global(h2),
  .yesica-story :global(h3) {
    font-family: 'Roboto Slab', serif;
    font-weight: 500;
    color: #ffffff;
    line-height: 1.2;
    margin: 0 0 1rem;
  }

  .yesica-story :global(h2) { font-size: clamp(1.4rem, 3vw, 2rem); }
  .yesica-story :global(h3) { font-size: clamp(1.1rem, 2vw, 1.4rem); }

  .yesica-story :global(p) {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 1rem;
    line-height: 1.8;
    color: rgba(232,232,232,0.85);
    margin: 0 0 1.1rem;
  }

  .yesica-story :global(blockquote) {
    margin: 1.5rem 0;
    padding: 0.75rem 1.25rem;
    border-left: 3px solid rgba(255,200,100,0.7);
    background: rgba(255,255,255,0.04);
    border-radius: 0 4px 4px 0;
  }

  .yesica-story :global(blockquote p) {
    font-family: 'Crimson Text', Garamond, Georgia, serif;
    font-size: clamp(1.1rem, 2vw, 1.25rem);
    font-style: italic;
    color: rgba(255,255,255,0.9);
    margin: 0;
    line-height: 1.55;
  }



  /* ── Sidebar ─────────────────────────────────────────────── */
  .yesica-sidebar {
    display: flex;
    flex-direction: column;
    gap: 1.75rem;
    position: sticky;
    top: 1.5rem;
  }

  @media (max-width: 900px) {
    .yesica-sidebar { position: static; }
  }

  /* ── Pull quotes ─────────────────────────────────────────── */
  .pull-quote {
    margin: 0;
    padding: 1.4rem 1.5rem;
    border-left: 3px solid rgba(255,200,100,0.7);
    background: rgba(255,255,255,0.04);
    border-radius: 0 6px 6px 0;
    opacity: 0;
    transform: translateX(14px);
    transition: opacity 0.6s ease, transform 0.6s ease;
  }

  .pull-quote.visible {
    opacity: 1;
    transform: translateX(0);
  }

  .quote-text {
    font-family: 'Crimson Text', Garamond, Georgia, serif;
    font-size: clamp(1.15rem, 2.2vw, 1.35rem);
    font-style: italic;
    line-height: 1.5;
    color: #ffffff;
    margin: 0 0 0.6rem;
  }

  .quote-attr {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.72rem;
    font-style: normal;
    font-weight: 700;
    letter-spacing: 0.1em;
    color: rgba(255,200,100,0.75);
    text-transform: uppercase;
  }

  /* Large pull quote variant used in the bottom row */
  .pull-quote--large .quote-text {
    font-size: clamp(1.3rem, 2.8vw, 1.7rem);
  }

  /* ── Audio player card ───────────────────────────────────── */
  .audio-card {
    background: rgba(255,255,255,0.05);
    border: 1px solid rgba(255,255,255,0.1);
    border-radius: 6px;
    padding: 1.1rem 1.3rem 1rem;
  }

  .audio-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.65rem;
    font-weight: 700;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    color: rgba(255,200,100,0.8);
    margin: 0 0 0.2rem;
  }

  .audio-label {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.88rem;
    color: rgba(255,255,255,0.75);
    margin: 0 0 0.85rem;
    line-height: 1.4;
  }

  /* Waveform */
  .waveform {
    position: relative;
    display: flex;
    align-items: center;
    gap: 2px;
    height: 36px;
    margin-bottom: 0.75rem;
    overflow: hidden;
  }

  .wave-bar {
    flex: 1;
    background: rgba(255,255,255,0.2);
    border-radius: 1px;
    transform-origin: bottom;
    transition: background 0.3s;
  }

  .wave-bar.playing {
    background: rgba(255,200,100,0.7);
    animation: wavePulse 0.6s ease-in-out infinite alternate;
  }

  @keyframes wavePulse {
    from { transform: scaleY(0.6); }
    to   { transform: scaleY(1.0); }
  }

  /* Playhead tint overlay */
  .waveform-progress {
    position: absolute;
    left: 0; top: 0; bottom: 0;
    background: rgba(255,200,100,0.12);
    pointer-events: none;
    transition: width 0.1s linear;
  }

  /* Controls row */
  .audio-controls {
    display: flex;
    align-items: center;
    gap: 0.65rem;
  }

  .audio-play {
    width: 2rem;
    height: 2rem;
    border-radius: 50%;
    border: 1.5px solid rgba(255,200,100,0.7);
    background: transparent;
    color: rgba(255,200,100,0.9);
    font-size: 0.75rem;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
    transition: background 0.2s;
  }

  .audio-play:hover,
  .audio-play.playing { background: rgba(255,200,100,0.15); }
  .audio-play:focus-visible { outline: 2px solid rgba(255,200,100,0.8); outline-offset: 2px; }

  .seekbar {
    flex: 1;
    height: 3px;
    background: rgba(255,255,255,0.15);
    border-radius: 2px;
    cursor: pointer;
    position: relative;
  }

  .seekbar-fill {
    height: 100%;
    background: rgba(255,200,100,0.75);
    border-radius: 2px;
    transition: width 0.1s linear;
  }

  .audio-time {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.68rem;
    color: rgba(255,255,255,0.4);
    letter-spacing: 0.05em;
    white-space: nowrap;
    flex-shrink: 0;
  }

  /* ── Video section ───────────────────────────────────────── */
  .video-section {
    max-width: 1100px;
    margin: 3.5rem auto 0;
    padding: 0 clamp(1.5rem, 5vw, 4rem);
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.65s ease, transform 0.65s ease;
  }

  .video-section.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .video-inner {
    border-top: 1px solid rgba(255,255,255,0.08);
    padding-top: 2.5rem;
  }

  .video-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.7rem;
    font-weight: 700;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    color: rgba(255,200,100,0.8);
    margin: 0 0 0.4rem;
  }

  .video-heading {
    font-family: 'Roboto Slab', serif;
    font-size: clamp(1.2rem, 2.5vw, 1.6rem);
    font-weight: 500;
    color: #ffffff;
    margin: 0 0 1.25rem;
  }

  .video-intro {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 1rem;
    line-height: 1.85;
    color: rgba(232, 232, 232, 0.85);
    margin: 0 0 1.5rem;
  }

  .video-section video {
    width: 100%;
    display: block;
    border-radius: 4px;
    background: #111;
    aspect-ratio: 16 / 9;
    object-fit: cover;
  }

  .video-caption {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.78rem;
    font-style: italic;
    color: rgba(255,255,255,0.45);
    margin: 0.55rem 0 0;
    line-height: 1.55;
  }

  .video-note {
    display: block;
    font-style: normal;
    color: rgba(255,200,100,0.5);
    margin-top: 0.2rem;
  }

  /* ── Bottom row: large quote + snapshot ─────────────────── */
  .yesica-bottom {
    max-width: 1100px;
    margin: 3.5rem auto 0;
    padding: 0 clamp(1.5rem, 5vw, 4rem);
    display: grid;
    grid-template-columns: 1fr 340px;
    gap: 3rem;
    align-items: start;
    border-top: 1px solid rgba(255,255,255,0.08);
    padding-top: 3rem;
  }

  @media (max-width: 900px) {
    .yesica-bottom { grid-template-columns: 1fr; gap: 2rem; }
  }

  /* ── Snapshot card ───────────────────────────────────────── */
  .snapshot-card {
    background: rgba(255,255,255,0.05);
    border: 1px solid rgba(255,255,255,0.1);
    border-top: 3px solid rgba(255,200,100,0.7);
    border-radius: 0 0 6px 6px;
    padding: 1.2rem 1.4rem 1.1rem;
    opacity: 0;
    transform: translateY(16px);
    transition: opacity 0.6s ease, transform 0.6s ease;
  }

  .snapshot-card.visible { opacity: 1; transform: translateY(0); }

  .snapshot-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.68rem;
    font-weight: 700;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: rgba(255,200,100,0.8);
    margin: 0 0 0.9rem;
  }

  .snapshot-list {
    list-style: none;
    margin: 0;
    padding: 0;
    display: flex;
    flex-direction: column;
    gap: 0;
  }

  .snapshot-row {
    display: flex;
    justify-content: space-between;
    align-items: baseline;
    gap: 0.75rem;
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.85rem;
    line-height: 1.4;
    padding: 0.55rem 0;
    border-bottom: 1px solid rgba(255,255,255,0.07);
  }

  .snapshot-row:last-child { border-bottom: none; }
  .snapshot-label { color: rgba(255,255,255,0.55); flex-shrink: 0; }
  .snapshot-value { font-weight: 600; color: #ffffff; text-align: right; }

  .snapshot-source {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.68rem;
    color: rgba(255,255,255,0.3);
    font-style: italic;
    margin: 0.75rem 0 0;
  }

  /* ── Mobile overrides ────────────────────────────────────── */
  @media (max-width: 600px) {
    .gallery-main { aspect-ratio: 4 / 3; }
    .thumb-btn { width: 56px; height: 42px; }
    .yesica-sidebar { position: static; }
  }
</style>
