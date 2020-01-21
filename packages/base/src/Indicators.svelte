<script>
  import { quintOut } from "svelte/easing";
  import { fly } from "svelte/transition";
  import { flip } from "svelte/animate";
  import { createEventDispatcher } from "svelte";

  const dispatch = createEventDispatcher();
  export let slides;
  export let selectedIndex;

  const jumpToIndex = index => () => dispatch("navigate", index);
</script>

<style>
  .carousel-indicators {
    color: green;
    position: absolute;
    display: flex;
    flex-flow: nowrap;
    bottom: 35px;
    align-self: center;
    background-color: rgba(50, 50, 50, 0.5);
    border-radius: 15px;
  }

  .carousel-indicators button {
    background-color: transparent;
    border: none;
    padding: 15px;
    cursor: pointer;
  }

  .carousel-indicators .active {
    color: blue !important;
  }
</style>

<div class="carousel-indicators">
  {#each slides as slide, i (slide.id)}
    <button
      type="button"
      animate:flip={{ delay: 250, duration: 250, easing: quintOut }}
      on:click={jumpToIndex(i)}
      class:active={selectedIndex === i}>
      O
    </button>
  {/each}
</div>
