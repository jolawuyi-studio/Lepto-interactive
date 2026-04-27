<script lang="ts">
  import rawBlocks from '$lib/doc.blocks.json';
  import DocRenderer from '$lib/components/DocRenderer.svelte';
  import type { Block } from '$lib/components/DocRenderer.svelte';
  import TheAlert from '$lib/components/TheAlert.svelte';
<<<<<<< Updated upstream
  import map from '$lib/components/map.svelte';
=======
  import HowItSpreads from '$lib/components/HowItSpreads.svelte';
  import MeetYesica from '$lib/components/MeetYesica.svelte';
>>>>>>> Stashed changes
  const blocks = rawBlocks as Block[];
</script>



<!-- ── SECTION 1: The Alert ── -->
<TheAlert />


<!-- DO NOT EDIT (except to modify classes). This will become code imported from Google Doc. -->
<div class="container">
  <div class="row justify-content-center">
    <div class="col-12 col-sm-10 col-lg-8 col-xxl-6">
      <!-- TEMPORARY: HowItSpreads is hard-coded here for testing.
           Once you add [[HowItSpreads]] to your Google Doc and run
           `npm run build:extract-google-doc`, remove this line and
           let DocRenderer handle it from the blocks below. -->
      <HowItSpreads />

      <!-- TEMPORARY: Remove once [[MeetYesica]]...[[/MeetYesica]] is in
           your Google Doc and you've run `npm run build:extract-google-doc`. -->
      <MeetYesica />

      <DocRenderer {blocks} />
    </div>
  </div>
</div>


<!-- Hard-code any custom code that should appear AFTER Google Doc below here. -->
<footer class="container-fluid bg-dark text-white p-5">
  <p class="text-center">See template on <a class="text-white" href="https://github.com/jrue/multimedia-template-2026" target="_blank">Github</a></p>
</footer>