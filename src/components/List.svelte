<script>
  import { onMount, onDestroy } from "svelte";
  import inView from "in-view";
  import { geoData } from "./const.js";
  import { activeListItem, activeMapItem } from "./states.js";
  import Pie from "./Pie.svelte";
  import Sankey from "./Sankey/Sankey.svelte";
  import BarChart from "./BarChart.svelte";

  let list;
  onMount(async () => {
    inView.offset(200);
    list.addEventListener("scroll", () => {
      const visibleListItems = Array.from(
        document.getElementsByClassName("list-item")
      ).map(inView.is);
      const topMostVisible = visibleListItems.indexOf(true);
      if (topMostVisible !== $activeMapItem) {
        activeMapItem.set(topMostVisible);
      }
    });
  });

  const unsubscribeActiveListItem = activeListItem.subscribe(
    (newActiveListItem) => {
      if (list) {
        list.scrollTop = document.getElementById(
          `list-item-${newActiveListItem}`
        ).offsetTop;
      }
    }
  );

  onDestroy(unsubscribeActiveListItem);
</script>

<div id="list-items" bind:this={list}>
  <div class="head">
    <h1>The Migration Journey</h1>
    <p>
      Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod
      tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim
      veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
      commodo consequat.
    </p>
  </div>
  <div class="separator" />
  {#each geoData as item, index}
    <div class="list-item" id="list-item-{index}">
      <h2>{item.name}</h2>
      <p>{item.description}</p>
      <!-- Add Svelte components for each section here; add additional ifs accordingly -->
      {#if item.name === "Honduras"}
        <Pie />
      {:else if item.name === "Guatemala"}
        <Pie />
      {:else if item.name === "California"}
        <Sankey />
      {/if}
    </div>
  {/each}
</div>