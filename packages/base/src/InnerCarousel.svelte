<script>
  import { onDestroy } from "svelte";
  import Indicators from "./Indicators.svelte";
  import { quintOut } from "svelte/easing";
  import { fly } from "svelte/transition";
  import { flip } from "svelte/animate";
  export let slides;
  export let delay;
  export let autoplay;
  export let displayCount = 5;
  export let centered = true;
  export let infinite = true;
  let uid = 0;
  let visibleItems;
  const indexSlides = (slide, index, arr) => ({ ...slide, id: uid++, index });
  $: slidesIndexed = (slides || []).map(indexSlides);

  $: {
    displayCount;
    setupState(slidesIndexed);
  }

  function getVisibleItemsWithOffset(slides$) {
    const diff = displayCount - slides$.length;
    // positive diff means we need more slides
    if (diff === 0) {
      return slides$;
    } else if (diff < 0) {
      return slides$.slice(0, displayCount);
    }
    let newItems = [...slides$];
    for (let i = 0; i < diff; i++) {
      newItems.push(getSlide(i));
    }
    return newItems;
  }

  function setupState(slides$) {
    visibleItems = getVisibleItemsWithOffset(slides$);
  }

  const getSlide = index => {
    let length = slidesIndexed.length;
    let wrappedIndex =
      index > length - 1 ? index % length : index < 0 ? length + index : index;
    return { ...slidesIndexed[wrappedIndex], id: uid++ };
  };

  const nextSlideSingle = () => {
    visibleItems = [getSlide(selectedIndex + 1)];
  };
  const previousSlideSingle = () => {
    visibleItems = [getSlide(selectedIndex - 1)];
  };

  const nextSlideMulti = () => {
    let newItems = visibleItems.slice(1);
    const lastItemIndex = newItems[newItems.length - 1].index;
    newItems.push(getSlide(lastItemIndex + 1));
    visibleItems = getVisibleItemsWithOffset(newItems);
  };

  const previousSlideMulti = () => {
    let newItems = visibleItems.slice(0, -1);
    const firstItemIndex = newItems[0].index;
    newItems.unshift(getSlide(firstItemIndex - 1));
    visibleItems = getVisibleItemsWithOffset(newItems);
  };

  const nextSlide = () =>
    displayCount > 1 ? nextSlideMulti() : nextSlideSingle();
  const previousSlide = () =>
    displayCount > 1 ? previousSlideMulti() : previousSlideSingle();

  let isReverse = false;
  let width;
  let timer;

  function Timer(callback, delayMs) {
    let callbackStartTime;
    let remaining = 0;
    this.timerId = null;
    this.paused = false;
    this.pause = () => {
      this.clear();
      remaining -= Date.now() - callbackStartTime;
      this.paused = true;
    };
    this.resume = () => {
      setTimeout(this.setTimeout.bind(this), remaining);
      this.paused = false;
    };
    this.setTimeout = () => {
      this.clear();
      this.timerId = setInterval(() => {
        callbackStartTime = Date.now();
        callback();
      }, delayMs);
    };
    this.clear = () => {
      clearInterval(this.timerId);
    };
    this.setTimeout();

    onDestroy(() => {
      this.clear();
    });
  }

  $: if (autoplay) {
    timer && timer.clear();
    timer = new Timer(nextSlide, delay);
  } else {
    timer && timer.clear();
    timer = undefined;
  }

  function next(event) {
    nextSlide();
  }

  function previous(event) {
    isReverse = true;
    previousSlide();
    setTimeout(() => (isReverse = false), 0);
  }

  function handleNavigate(event) {
    visibleItems = getVisibleItemsWithOffset(slidesIndexed.slice(event.detail));
  }

  const animationProps = { duration: 1000, opacity: 0.5, easing: quintOut };
  $: forwardAnimation = { ...animationProps, x: width };
  $: reverseAnimation = { ...animationProps, x: width * -1 };
  $: selectedIndex = visibleItems[0].index;
</script>

<style>
  .carousel {
    background-color: #000;
    display: flex;
    justify-content: center;
    align-content: center;
    align-items: stretch;
    position: relative;
    overflow: hidden;
    width: 100%;
    height: 400px;
  }

  button {
    height: 100%;
    width: 40px;
    border: none;
    color: #fff;
    background-color: rgba(50, 50, 50, 0.5);
    z-index: 100;
    position: absolute;
  }

  .slide-wrapper {
    padding: 10px 5px;
  }

  .next {
    right: 0;
  }

  .previous {
    left: 0;
  }

  .slide-container {
    flex: 1;
    display: flex;
  }
</style>

{#if slides && slides.length}
  <div
    class="carousel"
    bind:clientWidth={width}
    on:mouseover={() => timer && timer.pause()}
    on:mouseleave={() => timer && timer.resume()}>
    <button class="previous" on:click={previous} type="button">{'<'}</button>
    {#each visibleItems as slide, i (slide.id)}
      <div class="slide-container" animate:flip={{ duration: 1000 }}>
        {#if i < displayCount}
          <div
            class="slide-container slide-wrapper"
            out:fly={isReverse ? forwardAnimation : reverseAnimation}
            in:fly={isReverse ? reverseAnimation : forwardAnimation}>
            {@html slide.content}
          </div>
        {/if}
      </div>
    {/each}
    <button class="next" on:click={next} type="button">{'>'}</button>
    <Indicators on:navigate={handleNavigate} {slides} {selectedIndex} />

  </div>
{/if}
