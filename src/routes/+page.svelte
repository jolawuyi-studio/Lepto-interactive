<script lang="ts">
  import rawBlocks from '$lib/doc.blocks.json';
  import DocRenderer from '$lib/components/DocRenderer.svelte';
  import type { Block } from '$lib/components/DocRenderer.svelte';
  import TheAlert from '$lib/components/TheAlert.svelte';
  import HowItSpreads from '$lib/components/HowItSpreads.svelte';
  import MeetYesica from '$lib/components/MeetYesica.svelte';
  // Aliased to PascalCase so Svelte treats it as a component, not an HTML <map> element
  import MapEmbed from '$lib/components/map.svelte';
  const blocks = rawBlocks as Block[];
</script>



<!-- ── SECTION 1: The Alert ── -->
<TheAlert />


<!-- DO NOT EDIT (except to modify classes). This will become code imported from Google Doc. -->
<div class="container">
  <div class="row justify-content-center">
    <div class="col-12 col-sm-10 col-lg-8 col-xxl-6">

      <!-- ── Story intro ── -->
      <div class="story-intro">
        <p class="byline">By Yusuf Akinpelu, Emely Bonilla and Joshua Olawuyi</p>
        <p>On January 12th, 2026, the City of Berkeley Public Health Department issued a health alert warning of a potential Leptospirosis outbreak. At the time, several rats and two dogs had tested positive for the bacteria Leptospira, which can be fatal if untreated.</p>
        <p>Harrison Street is not home to the first Leptospira outbreak — there have been hundreds of cases popping up throughout the United States at a surprising rate. Many of the cases have been traced to encampments, which have conditions where Leptospira can thrive. Without proper interference the country's most vulnerable demographic can be at the forefront of an outbreak. These are who have been impacted in Berkeley.</p>
      </div>

      <!-- TEMPORARY: Remove once [[MeetYesica]]...[[/MeetYesica]] is in
           your Google Doc and you've run `npm run build:extract-google-doc`. -->
      <MeetYesica />

      <!-- TEMPORARY: HowItSpreads is hard-coded here for testing.
           Once you add [[HowItSpreads]] to your Google Doc and run
           `npm run build:extract-google-doc`, remove this line and
           let DocRenderer handle it from the blocks below. -->
      <HowItSpreads />

      <!-- TEMPORARY: Remove once [[map]] is in your Google Doc and you've
           run `npm run build:extract-google-doc`. -->
      <MapEmbed />

      <DocRenderer {blocks} />
    </div>
  </div>
</div>


<style>
  .story-intro {
    width: 100vw;
    margin-left: calc(50% - 50vw);
    background: linear-gradient(to bottom, #080808 0%, #0f0f0f 100%);
    padding: 4.5rem clamp(1.5rem, 10vw, 20vw) 4rem;
    border-top: 2px solid rgba(255, 200, 100, 0.35);
  }

  .story-intro p {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 1.05rem;
    line-height: 1.9;
    color: rgba(220, 220, 220, 0.78);
    margin: 0 auto 1.4rem;
    max-width: 680px;
  }

  .story-intro p:first-child {
    font-size: 1.15rem;
    line-height: 1.85;
    color: rgba(255, 255, 255, 0.92);
  }

  .story-intro p:last-child {
    margin-bottom: 0;
  }

  .byline {
    font-family: 'Lato', system-ui, sans-serif;
    font-size: 0.82rem;
    font-weight: 700;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    color: rgba(255, 200, 100, 0.8);
    max-width: 680px;
    margin: 0 auto 2rem !important;
  }
</style>


<!-- Hard-code any custom code that should appear AFTER Google Doc below here. -->
<footer class="container-fluid bg-dark text-white p-5">
  <p class="text-center">See template on <a class="text-white" href="https://github.com/jrue/multimedia-template-2026" target="_blank">Github</a></p>
</footer>


