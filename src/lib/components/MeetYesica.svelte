<!--
  MeetYesica.svelte — Section 2: "Meet Yesica"

  ════════════════════════════════════════════════════════════
  GOOGLE DOC USAGE
  ════════════════════════════════════════════════════════════
  Wrap the story text for this section in the Google Doc like this:

      [[MeetYesica]]

      ## Meet Yesica

      For residents on Harrison Street, the leptospirosis alert
      was not only about disease...

      "Nobody was really talking to us," she said.

      ...all remaining paragraphs for this section...

      [[/MeetYesica]]

  Everything between the tags becomes the left-column story text.
  The media gallery and snapshot card on the right are managed here.

  ════════════════════════════════════════════════════════════
  WHAT LIVES WHERE
  ════════════════════════════════════════════════════════════
  Google Doc  →  Section heading, paragraphs, quotes, bylines
  This file   →  Media URLs, captions, snapshot data, layout
-->

<script lang="ts">
  import { onMount, onDestroy } from 'svelte';
  import { browser } from '$app/environment';
  import { base } from '$app/paths';

  // Story text injected from the Google Doc block shortcode content.
  // It will be empty when testing without running the extraction script.
  export let bodyHtml: string = '';

  // ── Media gallery ─────────────────────────────────────────
  // Add files to the static/ folder, then update src and thumb below.
  // Paths must start with / and must NOT include the word "static".
  // Use `${base}/filename` so the path works on GitHub Pages too.
  const media = [
    {
      type: 'image' as const,
      // REPLACE: First Harrison Street environment photo
      src:   `${base}/8thstreet-01.JPG`,
      thumb: `${base}/8thstreet-01.JPG`,
      alt:   'Harrison Street encampment area',
      caption: 'Harrison Street has been home to an unhoused encampment and became the focus of Berkeley\'s leptospirosis alert.'
    },
    {
      type: 'image' as const,
      // REPLACE: Photo of Yesica or other residents
      src:   `${base}/8thstreet-02.JPG`,
      thumb: `${base}/8thstreet-02.JPG`,
      alt:   'Yesica, an unhoused resident and Homeless Union leader',
      caption: 'Yesica, an unhoused resident and Homeless Union leader, says residents were confused by the warning and worried about displacement.'
    },
    {
      type: 'video' as const,
      // REPLACE: Ambient video from Harrison Street
      src:   `${base}/lepto moving.mp4`,
      // REPLACE: A still frame or poster image for the video thumbnail
      thumb: `${base}/hero-fallback.jpg`,
      alt:   'Ambient video from Harrison Street',
      caption: 'Ambient video from Harrison Street shows the physical environment residents described during interviews.'
    }
  ];

  // ── Snapshot card data ────────────────────────────────────
  // Edit these values here if the reported numbers change.
  const snapshot = [
    { label: 'Estimated residents', value: '38–40' },
    { label: 'Pets',                value: 'Dogs and cats' },
    { label: 'Main concern',        value: 'Displacement' },
    { label: 'Unresolved issue',    value: 'Sanitation and communication' }
  ];

  let activeIndex = 0;

  // ── Scroll reveal ─────────────────────────────────────────
  let sidebarEl: HTMLElement | null = null;
  let sidebarVisible = false;
  let observer: IntersectionObserver | null = null;

  onMount(() => {
    if (!browser || !sidebarEl) return;
    observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting) {
          sidebarVisible = true;
          observer?.disconnect();
        }
      },
      { threshold: 0.08 }
    );
    observer.observe(sidebarEl);
  });

  onDestroy(() => observer?.disconnect());
</script>

<section class="meet-yesica" aria-labelledby="yesica-heading">
  <div class="yesica-grid">

    <!-- ── LEFT: Story text from Google Doc ── -->
    <div class="yesica-story">
      {#if bodyHtml}
        {@html bodyHtml}
      {:else}
        <!--
          Placeholder shown only in development before the Google Doc
          extraction has been run. Add [[MeetYesica]]...[[/MeetYesica]]
          to your Google Doc and run `npm run build:extract-google-doc`.
        -->
        <div class="dev-placeholder">
          <p><strong>Story text placeholder</strong></p>
          <p>Add <code>[[MeetYesica]]</code> … <code>[[/MeetYesica]]</code> to your Google Doc,
            then run <code>npm run build:extract-google-doc</code>.</p>
        </div>
      {/if}
    </div>

    <!-- ── RIGHT: Media gallery + snapshot card ── -->
    <div
      class="yesica-sidebar"
      class:visible={sidebarVisible}
      bind:this={sidebarEl}
    >

      <!-- Media gallery -->
      <figure class="gallery">

        <!-- Main preview area -->
        <div class="gallery-main">
          {#if media[activeIndex].type === 'video'}
            <!-- svelte-ignore a11y-media-has-caption -->
            <video
              src={media[activeIndex].src}
              controls
              playsinline
              preload="metadata"
              aria-label={media[activeIndex].alt}
            ></video>
          {:else}
            <img
              src={media[activeIndex].src}
              alt={media[activeIndex].alt}
            />
          {/if}
        </div>

        <!-- Caption -->
        <figcaption class="gallery-caption">
          {media[activeIndex].caption}
        </figcaption>

        <!-- Thumbnail strip -->
        <div class="gallery-thumbs" role="group" aria-label="Media thumbnails">
          {#each media as item, i}
            <button
              class="thumb-btn"
              class:active={activeIndex === i}
              aria-pressed={activeIndex === i}
              aria-label="{item.type === 'video' ? 'Video' : `Photo ${i + 1}`}: {item.caption}"
              on:click={() => (activeIndex = i)}
            >
              <img src={item.thumb} alt="" aria-hidden="true" />
              {#if item.type === 'video'}
                <span class="thumb-play" aria-hidden="true">▶</span>
              {/if}
            </button>
          {/each}
        </div>

      </figure>

      <!-- Snapshot card -->
      <aside class="snapshot-card" aria-label="Encampment snapshot">
        <p class="snapshot-kicker">Encampment snapshot</p>
        <ul class="snapshot-list">
          {#each snapshot as row}
            <li class="snapshot-row">
              <span class="snapshot-label">{row.label}</span>
              <span class="snapshot-value">{row.value}</span>
            </li>
          {/each}
        </ul>
        <p class="snapshot-source">Based on estimates from Yesica at time of reporting.</p>
      </aside>

    </div>
  </div>
</section>

<style>
  /* ── Section wrapper ─────────────────────────────────────── */
  .meet-yesica {
    margin: 3rem 0 4rem;
    padding-top: 2.5rem;
    border-top: 1px solid #e0e0e0;
  }

  /* ── Two-column grid ─────────────────────────────────────── */
  .yesica-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 2.5rem;
    align-items: start;
  }

  @media (max-width: 768px) {
    .yesica-grid {
      grid-template-columns: 1fr;
      gap: 2rem;
    }
  }

  /* ── Story text (Google Doc content) ────────────────────── */
  /* All selectors use :global() because the HTML is injected
     dynamically via {@html bodyHtml} and won't carry Svelte's
     scoping attribute. */

  .yesica-story :global(h2),
  .yesica-story :global(h3) {
    font-family: 'Roboto Slab', serif;
    font-weight: 500;
    color: #1a1a1a;
    line-height: 1.2;
    margin: 0 0 1rem;
  }

  .yesica-story :global(h2) {
    font-size: clamp(1.5rem, 3.5vw, 2.1rem);
  }

  .yesica-story :global(h3) {
    font-size: clamp(1.1rem, 2.5vw, 1.4rem);
  }

  .yesica-story :global(p) {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 1rem;
    line-height: 1.75;
    color: #333;
    margin: 0 0 1.1rem;
  }

  /* Blockquotes: pull-quote style for direct speech formatted
     as a Quote block in Google Docs */
  .yesica-story :global(blockquote) {
    margin: 1.5rem 0;
    padding: 0.75rem 1.25rem;
    border-left: 3px solid #1a1a1a;
    background: #f9f9f9;
  }

  .yesica-story :global(blockquote p) {
    font-family: 'Crimson Text', Garamond, Georgia, serif;
    font-size: clamp(1.1rem, 2.5vw, 1.3rem);
    font-style: italic;
    color: #222;
    margin: 0;
    line-height: 1.55;
  }

  /* Captions: small text below images in the Google Doc */
  .yesica-story :global(figcaption),
  .yesica-story :global(.caption) {
    font-size: 0.8rem;
    color: #888;
    line-height: 1.5;
    margin-top: 0.35rem;
  }

  /* Dev placeholder */
  .dev-placeholder {
    background: #f5f5f5;
    border: 1px dashed #ccc;
    border-radius: 4px;
    padding: 1.5rem;
    color: #777;
    font-size: 0.9rem;
    line-height: 1.6;
  }

  .dev-placeholder code {
    background: #e8e8e8;
    padding: 0.1em 0.35em;
    border-radius: 3px;
    font-size: 0.85em;
  }

  /* ── Sidebar: scroll reveal ──────────────────────────────── */
  .yesica-sidebar {
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.65s ease, transform 0.65s ease;
    position: sticky;
    top: 1.5rem; /* stays in view as story text scrolls past it */
  }

  .yesica-sidebar.visible {
    opacity: 1;
    transform: translateY(0);
  }

  @media (max-width: 768px) {
    .yesica-sidebar {
      position: static; /* no sticky on mobile */
    }
  }

  /* ── Media gallery ───────────────────────────────────────── */
  .gallery {
    margin: 0 0 1.5rem;
  }

  .gallery-main {
    width: 100%;
    aspect-ratio: 3 / 2;
    background: #111;
    border-radius: 4px;
    overflow: hidden;
  }

  .gallery-main img,
  .gallery-main video {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
  }

  .gallery-caption {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.78rem;
    line-height: 1.55;
    color: #777;
    margin: 0.5rem 0 0.85rem;
    font-style: italic;
  }

  /* Thumbnail strip */
  .gallery-thumbs {
    display: flex;
    gap: 0.5rem;
  }

  .thumb-btn {
    position: relative;
    width: 72px;
    height: 54px;
    padding: 0;
    border: 2px solid transparent;
    border-radius: 3px;
    overflow: hidden;
    cursor: pointer;
    background: #ddd;
    flex-shrink: 0;
    transition: border-color 0.15s ease, opacity 0.15s ease;
  }

  .thumb-btn img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
  }

  .thumb-btn.active {
    border-color: #1a1a1a;
  }

  .thumb-btn:not(.active) {
    opacity: 0.6;
  }

  .thumb-btn:hover:not(.active) {
    opacity: 0.85;
    border-color: #aaa;
  }

  .thumb-btn:focus-visible {
    outline: 2px solid #1a1a1a;
    outline-offset: 2px;
  }

  /* Play icon overlay on video thumbnails */
  .thumb-play {
    position: absolute;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1rem;
    color: #fff;
    background: rgba(0, 0, 0, 0.45);
    pointer-events: none;
  }

  /* ── Snapshot card ───────────────────────────────────────── */
  .snapshot-card {
    background: #fafafa;
    border: 1px solid #e4e4e4;
    border-top: 3px solid #1a1a1a;
    border-radius: 0 0 4px 4px;
    padding: 1.1rem 1.3rem 1rem;
  }

  .snapshot-kicker {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.7rem;
    font-weight: 700;
    letter-spacing: 0.16em;
    text-transform: uppercase;
    color: #888;
    margin: 0 0 0.85rem;
  }

  .snapshot-list {
    list-style: none;
    margin: 0;
    padding: 0;
    display: flex;
    flex-direction: column;
    gap: 0.55rem;
  }

  .snapshot-row {
    display: flex;
    justify-content: space-between;
    align-items: baseline;
    gap: 0.75rem;
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.88rem;
    line-height: 1.4;
    border-bottom: 1px solid #ebebeb;
    padding-bottom: 0.55rem;
  }

  .snapshot-row:last-child {
    border-bottom: none;
    padding-bottom: 0;
  }

  .snapshot-label {
    color: #666;
    flex-shrink: 0;
  }

  .snapshot-value {
    font-weight: 600;
    color: #1a1a1a;
    text-align: right;
  }

  .snapshot-source {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.7rem;
    color: #aaa;
    margin: 0.75rem 0 0;
    font-style: italic;
  }
</style>
