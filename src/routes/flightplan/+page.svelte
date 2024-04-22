<script lang="ts">
  import { settings } from "$lib/settings";
  import { onMount } from "svelte";


  let data:any;
  var pdfLink:any;

  onMount(async () => {
    // Code to execute on page load
    console.log("Hello World")
    try {
      const response = await fetch(
        "https://www.simbrief.com/api/xml.fetcher.php?username=" +
          settings.simbriefUsername +
          "&json=1",
      );
      data = await response.json();
    } catch (error) {
      console.error("Failed to fetch data:", error);
    }
    //console.log(data);
    pdfLink = data.files.directory + data.files.pdf.link;
    console.log("Link: " + pdfLink);
  });

  let iframeWidth = window.innerWidth;
let iframeHeight = window.innerHeight;

  // Update dimensions on window resize
  window.addEventListener('resize', () => {
      iframeWidth = window.innerWidth;
      iframeHeight = window.innerHeight;
    });
</script>


<iframe
src={pdfLink}
width={iframeWidth}
height={iframeHeight}
style="border: none;"
/>