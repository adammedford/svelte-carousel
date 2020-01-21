<script>
  import Carousel from "amedford-svelte-carousel-base";
  let uid = 0;
  let autoplay = true;
  let delay = 2000;

  let raw = `[
    {
      "class": "red",
      "content": "<div class='centered'><h1>Slide 1</h1></div>"
    },
    {
      "class": "green",
      "content": "<div class='centered'><h1>Slide 2</h1></div>"
    },
    {
      "class": "yellow",
      "content": "<div class='centered'><h1>Slide 3</h1></div>"
    },
    {
      "class": "blue",
      "content": "<div class='centered'><h1>Slide 4</h1></div>"
    }
  ]`;
  let parsed;

  $: {
    try {
      if (raw) {
        parsed = JSON.parse(raw);
      }
    } catch (error) {
      console.log("parsing failed", error);
      parsed = [];
    }
  }

  $: slides = parsed && parsed.length ? parsed : [];

  let displayCount = 3;
  let car;
</script>

<style>
  :global(.centered) {
    display: flex;
    justify-content: center;
    align-content: center;
    align-items: center;
    height: 100%;
    width: 100%;
  }

  :global(.red) {
    background-color: red !important;
  }
  :global(.green) {
    background-color: green !important;
  }
  :global(.yellow) {
    background-color: yellow !important;
  }
  :global(.blue) {
    background-color: blue !important;
  }

  .slides-editor {
    width: 100%;
    height: 500px;
    overflow: auto;
  }

  table {
    width: 100%;
  }

  .red,
  .green,
  .blue,
  .yellow {
    width: 100%;
  }
</style>

<main>
  <Carousel {slides} {delay} {displayCount} {autoplay} bind:this={car}>
    <div class="blue">test 1</div>
    <div class="red">test 2</div>
    <div class="yellow">test 3</div>
    <div class="green">test 4</div>
  </Carousel>
  <table>
    <tr>
      <td># of Items to Display</td>
      <td>
        <input type="number" bind:value={displayCount} />
      </td>
    </tr>
    <tr>
      <td>Autoplay</td>
      <td>
        <input type="checkbox" bind:checked={autoplay} />
      </td>
    </tr>
    <tr>
      <td>Autoplay Delay</td>
      <td>
        <input placeholder="Delay" bind:value={delay} />
      </td>
    </tr>
    <tr>
      <td colspan="2">
        <label>Slides</label>
        <pre
          class="slides-editor"
          contenteditable="true"
          bind:innerHTML={raw} />
      </td>
    </tr>
  </table>

</main>
