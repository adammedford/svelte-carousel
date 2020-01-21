<script>
  import { quintOut } from "svelte/easing";
  import { fly } from "svelte/transition";
  export let slide;
</script>

<style>
  div {
    width: 100%;
    height: 100%;
  }
</style>

{@html slide.content}
