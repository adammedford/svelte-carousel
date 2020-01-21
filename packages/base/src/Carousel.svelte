<script>
  import { onDestroy } from "svelte";
  import { onMount } from "svelte";
  import InnerCarousel from "./InnerCarousel.svelte";
  export let slides;
  export let delay;
  export let autoplay;
  export let displayCount = 5;
  export let centered = true;
  export let infinite = true;

  $: innerSlides = slides;

  let mySlides;
  let mySlideChildren;

  onMount(() => {
    mySlideChildren = Array.from(mySlides.children).map((node, id) => ({
      id,
      content: node.outerHTML
    }));
    mySlides.remove();
  });
</script>

<style>

</style>

<div bind:this={mySlides}>
  <slot />
</div>
<InnerCarousel
  {delay}
  {autoplay}
  {displayCount}
  {centered}
  {infinite}
  slides={mySlideChildren} />
