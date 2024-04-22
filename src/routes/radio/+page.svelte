<script>
  import { AppBar } from "@skeletonlabs/skeleton";
  import { SlideToggle } from "@skeletonlabs/skeleton";
  import { writable } from "svelte/store";

  let COM1_ACT_FREQ_L = 119;
  let COM1_ACT_FREQ_R = 525;
  let COM1_STBY_FREQ_L = 119;
  let COM1_STBY_FREQ_R = 525;

  let COM2_ACT_FREQ_L = 119;
  let COM2_ACT_FREQ_R = 525;
  let COM2_STBY_FREQ_L = 119;
  let COM2_STBY_FREQ_R = 525;

  let ACT_FREQ = 0;

  function handleClick() {
    alert("Button ID: " + buttonId);
    // Do whatever you want with the button ID
  }
</script>

<AppBar
  gridColumns="grid-cols-3"
  slotDefault="place-self-center"
  slotTrail="place-content-end"
>
  <svelte:fragment slot="lead">
    <button
      type="button"
      id="btn_COM1"
      class="btn btn-lg variant-filled-primary px-2 font-bold"
      on:click={handleClick}
    >
      COM1
    </button>
  </svelte:fragment>

  <div class="flex justify-center">
    <span class="badge variant-filled p-4">
      <h1 class="h1">
        {COM1_ACT_FREQ_L}.{COM1_ACT_FREQ_R}
      </h1>
    </span>

    <button type="button" class="btn btn-lg variant-filled-primary mx-1">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="1.5"
        stroke="currentColor"
        class="w-6 h-6"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          d="M16.023 9.348h4.992v-.001M2.985 19.644v-4.992m0 0h4.992m-4.993 0 3.181 3.183a8.25 8.25 0 0 0 13.803-3.7M4.031 9.865a8.25 8.25 0 0 1 13.803-3.7l3.181 3.182m0-4.991v4.99"
        />
      </svg>
    </button>

    <span class="badge variant-filled p-4">
      <h1 class="h1">
        {COM1_STBY_FREQ_L}.{COM1_STBY_FREQ_R}
      </h1>
    </span>
  </div>

  <svelte:fragment slot="trail">
    <button type="button" class="btn btn-lg variant-filled-primary">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="1.5"
        stroke="currentColor"
        class="w-6 h-6"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          d="M3.75 6A2.25 2.25 0 0 1 6 3.75h2.25A2.25 2.25 0 0 1 10.5 6v2.25a2.25 2.25 0 0 1-2.25 2.25H6a2.25 2.25 0 0 1-2.25-2.25V6ZM3.75 15.75A2.25 2.25 0 0 1 6 13.5h2.25a2.25 2.25 0 0 1 2.25 2.25V18a2.25 2.25 0 0 1-2.25 2.25H6A2.25 2.25 0 0 1 3.75 18v-2.25ZM13.5 6a2.25 2.25 0 0 1 2.25-2.25H18A2.25 2.25 0 0 1 20.25 6v2.25A2.25 2.25 0 0 1 18 10.5h-2.25a2.25 2.25 0 0 1-2.25-2.25V6ZM13.5 15.75a2.25 2.25 0 0 1 2.25-2.25H18a2.25 2.25 0 0 1 2.25 2.25V18A2.25 2.25 0 0 1 18 20.25h-2.25A2.25 2.25 0 0 1 13.5 18v-2.25Z"
        />
      </svg>
    </button>
  </svelte:fragment>
</AppBar>

<hr class="!border-t-8" />

<AppBar
  gridColumns="grid-cols-3"
  slotDefault="place-self-center"
  slotTrail="place-content-end"
>
  <svelte:fragment slot="lead">
    <button
      type="button"
      id="btn_COM2"
      class="btn btn-lg variant-filled-primary px-2 font-bold"
    >
      COM2
    </button>
  </svelte:fragment>

  <div class="flex justify-center">
    <span class="badge variant-filled p-4">
      <h1 class="h1">
        {COM2_ACT_FREQ_L}.{COM2_ACT_FREQ_R}
      </h1>
    </span>

    <button type="button" class="btn btn-lg variant-filled-primary mx-1">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="1.5"
        stroke="currentColor"
        class="w-6 h-6"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          d="M16.023 9.348h4.992v-.001M2.985 19.644v-4.992m0 0h4.992m-4.993 0 3.181 3.183a8.25 8.25 0 0 0 13.803-3.7M4.031 9.865a8.25 8.25 0 0 1 13.803-3.7l3.181 3.182m0-4.991v4.99"
        />
      </svg>
    </button>

    <span class="badge variant-filled p-4">
      <h1 class="h1">
        {COM2_STBY_FREQ_L}.{COM2_STBY_FREQ_R}
      </h1>
    </span>
  </div>

  <svelte:fragment slot="trail">
    <button type="button" class="btn btn-lg variant-filled-primary">
      <svg
        xmlns="http://www.w3.org/2000/svg"
        fill="none"
        viewBox="0 0 24 24"
        stroke-width="1.5"
        stroke="currentColor"
        class="w-6 h-6"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          d="M3.75 6A2.25 2.25 0 0 1 6 3.75h2.25A2.25 2.25 0 0 1 10.5 6v2.25a2.25 2.25 0 0 1-2.25 2.25H6a2.25 2.25 0 0 1-2.25-2.25V6ZM3.75 15.75A2.25 2.25 0 0 1 6 13.5h2.25a2.25 2.25 0 0 1 2.25 2.25V18a2.25 2.25 0 0 1-2.25 2.25H6A2.25 2.25 0 0 1 3.75 18v-2.25ZM13.5 6a2.25 2.25 0 0 1 2.25-2.25H18A2.25 2.25 0 0 1 20.25 6v2.25A2.25 2.25 0 0 1 18 10.5h-2.25a2.25 2.25 0 0 1-2.25-2.25V6ZM13.5 15.75a2.25 2.25 0 0 1 2.25-2.25H18a2.25 2.25 0 0 1 2.25 2.25V18A2.25 2.25 0 0 1 18 20.25h-2.25A2.25 2.25 0 0 1 13.5 18v-2.25Z"
        />
      </svg>
    </button>
  </svelte:fragment>
</AppBar>