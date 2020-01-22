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
    padding: 10px;
    width: 40px;
    height: 40px;
    cursor: pointer;
  }

  .inner-button {
    border: 1px solid #fff;
    margin: 0 auto;
    padding: 4px;
    height: 50%;
    width: 50%;
    border-radius: 100%;
  }

  .button-center {
    background-color: #fff;
    height: 100%;
    width: 100%;
    border-radius: 100%;
  }

  .active .inner-button {
    border: 1px solid blue !important;
  }

  .active .button-center {
    background-color: blue !important;
  }
</style>

<div class="carousel-indicators">
  {#each slides as slide, i (slide.id)}
    <button
      type="button"
      animate:flip={{ delay: 250, duration: 250, easing: quintOut }}
      on:click={jumpToIndex(i)}
      class:active={selectedIndex === i}>
      <div class="inner-button">

        <div class="button-center" />
      </div>
    </button>
  {/each}
</div>
