<svelte:options immutable={true} />

<script>
  import { onMount, afterUpdate, tick } from "svelte";
  // import { flip } from "svelte/animate";
  // import { fade } from "svelte/transition";
  // import VirtualList from '@sveltejs/svelte-virtual-list';
  import VirtualList from "./VirtualList.svelte";
  import {
    playerStore,
    playPause,
    play,
    queueSong,
    resetSong,
    jumpTo,
    PLAYED,
    CURRENT,
    QUEUE,
    PREV_QUEUE,
    REMAINING,
  } from "../store/playerStore.js";
  import {
    // send,
    // receive,
    filterListByName,
    addType,
  } from "../utils.js";

  // Constants / Variables
  let autoscroll = false;
  let scrollToIndex;
  let liStart = undefined;
  let liEnd = undefined;

  $: isPaused = $playerStore.paused;
  $: filterText = $playerStore.filterText;
  $: previous = $playerStore.previous;
  $: current = $playerStore.current;
  $: next = $playerStore.next;
  $: nextPrev = $playerStore.nextPrev;
  $: remaining = $playerStore.remaining;

  // Derived
  $: completeList = filterListByName(
    [
      ...previous.map((i) => addType(i, PLAYED)),
      addType(current, CURRENT),
      ...next.map((i) => addType(i, QUEUE)),
      ...nextPrev.map((i) => addType(i, PREV_QUEUE)),
      ...remaining.map((i) => addType(i, REMAINING)),
    ],
    filterText
  );

  $: {
    [current].map(() => {
      autoscroll = true;
    });
  }

  function scrollToCurrent() {
    if (autoscroll && !filterText) {
      let index = completeList.indexOf(current);
      if (index > 0) index -= 1; //move current a bit to center
      scrollToIndex(index);
      autoscroll = false;
    }
  }

  // Event handler
  function handlePlay(event, song) {
    play(song);
    autoscroll = true;
    window.getSelection().removeAllRanges();
    //audio.play()
    // TODO audio.autoplay = true
  }

  function handleDblClick(e, song) {
    jumpTo(song);
  }

  function handlePlayPause(event) {
    playPause();
  }

  // Life cycle
  afterUpdate(function (x) {
    scrollToCurrent();
  });
</script>

<ul
  class="playlist _overflow"
  class:_top={liStart !== 0}
  class:_bottom={liEnd !== completeList.length}
>
  <VirtualList
    items={completeList}
    let:item={song}
    bind:start={liStart}
    bind:end={liEnd}
    bind:scrollToIndex
  >
    <li class="song {song.type}" on:dblclick={(e) => handleDblClick(e, song)}>
      <span class="status-icon" />
      <span class="spacer" />
      <span class="name">{song.name}</span>
      <span class="spacer" />
      {#if song.type === PLAYED}
        <button on:click={(e) => queueSong(song, previous)}> play next </button>
      {:else if song.type === CURRENT}
        <button on:click={handlePlayPause}>
          {#if isPaused}play{:else}pause{/if}
        </button>
      {:else if song.type === QUEUE}
        <button on:click={(e) => resetSong(song, next)}>
          reset to playlist
        </button>
      {:else if song.type === PREV_QUEUE}
        <button on:click={(e) => resetSong(song, nextPrev)}>
          reset to history
        </button>
      {:else if song.type === REMAINING}
        <button on:click={(e) => queueSong(song, remaining)}>
          play next
        </button>
      {/if}
    </li>
  </VirtualList>
</ul>

<style>
  .spacer {
    min-width: 0.6em;
  }

  .playlist {
    display: flex;
    flex-flow: column;
    flex: 1;
    overflow: auto;
    display: block;
    position: relative;
    z-index: 0;
  }
  .playlist._overflow::before,
  .playlist._overflow::after {
    content: "";
    position: absolute;
    z-index: 1;
    width: 100%;
  }
  .playlist._overflow._top::before {
    height: 2rem;
    top: 0;
    box-shadow: 0 2rem 1rem -1rem white inset;
  }
  .playlist._overflow._bottom::after {
    height: 2rem;
    bottom: 0;
    box-shadow: 0 -2rem 1rem -1rem white inset;
  }

  li {
    padding: 0.2em;
    border-bottom: 1.1px solid currentColor;
    display: flex;
    align-items: center;
    position: relative;
    height: 45px; /*important for exact virtual list*/
    line-height: 1;
  }
  li:active {
    background: #eee;
  }

  li .name {
    flex: 1;
  }

  .PLAYED,
  .PREV_QUEUE {
    background: #e3e3e3;
  }

  .CURRENT {
    border-top: 3px solid;
    font-weight: bold;
  }

  .status-icon {
    min-width: 1.2em;
    text-align: center;
  }
  .CURRENT .status-icon:before {
    content: "playing:";
  }
  .PLAYED .status-icon:before,
  .QUEUE .status-icon:before,
  .PREV_QUEUE .status-icon:before {
    content: "next";
  }
  .PLAYED .status-icon:before {
    content: "";
  }
  .PREV_QUEUE .status-icon:before {
    content: "#next";
  }
</style>
