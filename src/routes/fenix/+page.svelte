<script>
  import { onMount } from "svelte";
  import { ProgressRadial } from '@skeletonlabs/skeleton';

  var frame = false;

  onMount(async () => {
    checkAlive();
  });

  async function checkAlive() {
    try {
      const response = await fetch(
        "http://localhost:8083/#/mcdu",
      );
      //console.log("Server There");
      frame = true;
    } catch (error) {
      //console.log("Failed to fetch data:", error);
    }
  }

  setInterval(checkAlive, 2000);

  let iframeWidth = window.innerWidth;
  let iframeHeight = window.innerHeight;

  /*
  // Update dimensions on window resize
  window.addEventListener("resize", () => {
    iframeWidth = window.innerWidth;
    iframeHeight = window.innerHeight;
  });
*/



</script>

{#if frame}
  <iframe
    title="MCDU"
    src="http://localhost:8083/#/mcdu"
    width={iframeWidth}
    height={iframeHeight}
    style="border: none;"
  />
  {:else}
  
  <div class="h-screen flex items-center justify-center">
    
    <ProgressRadial class="w-56" />
  </div>
{/if}