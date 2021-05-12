<script>
	// import { onMount, afterUpdate, onDestroy } from 'svelte';
	import {
		playerStore,
		playPause,
		play,
		playPrev,
		playNext,
		toggleRandom,
		setFilterText,
	} from "./store/playerStore.js";
	import Playlist from "./components/Playlist.svelte";
	import { debounce, sortByIndexId } from "./utils.js";

	// constants / variables / state
	let audio;
	$: paused = $playerStore.paused;
	$: isRandom = $playerStore.isRandom;
	$: previous = $playerStore.previous;
	$: current = $playerStore.current;
	$: next = $playerStore.next;
	$: nextPrev = $playerStore.nextPrev;
	$: remaining = $playerStore.remaining;

	// derived
	$: currentCounter = previous.length + (current ? 1 : 0);
	$: totalCount =
		currentCounter + next.length + nextPrev.length + remaining.length;

	// event handler
	const handleFilter = debounce(function (event) {
		setFilterText(event.target.value);
	}, 700);

	const handlePlay = () => {
		playPause(true);
	};
	const handlePause = (event) => {
		const { currentTime, duration } = event.target;
		if (currentTime === duration) playNext();
		else playPause(false);
	};
</script>

<div class="controls" style="display: normal">
	<button on:click={playPrev}><i>&lt;&lt;</i> prev</button>
	<button on:click={playNext}>next <i>{isRandom ? ">?" : ">>"}</i> </button>
	<label class="button">
		<input type="checkbox" checked={isRandom} on:click={toggleRandom} />
		random
	</label>
	<input
		class="search"
		type="text"
		placeholder="filter:"
		on:input={handleFilter}
	/>
	<div class="info">
		{currentCounter}/{totalCount}&emsp;
		{#if current}<strong>{current.name}</strong>{/if}
	</div>
	<audio
		autoplay
		controls
		src={current && current.src}
		bind:paused
		on:pause={handlePause}
		on:play={handlePlay}
	>
		<track kind="captions" />
	</audio>
</div>

<Playlist />

<style>
	:global(html body) {
		display: flex;
		flex-flow: column-reverse nowrap;
		height: 100vh;
	}
	:global(html ul, html li, html button) {
		margin: 0;
	}
	:global(html button, .button) {
		padding: 0 1.5rem;
	}
	:global(html i) {
		font-style: normal;
		font-size: 1.8rem;
		font-weight: normal;
	}
	label input {
		margin: 0;
		margin-inline-end: 1ch;
	}
	button,
	label {
		display: inline-flex;
		align-items: center;
	}

	.controls {
		display: flex;
		flex-flow: row wrap;
		z-index: 1;
		background: white;
	}
	.controls .search {
		flex: 1 0 5rem;
	}
	.controls .search:focus {
		flex: 1 1 40rem;
	}
	.controls .info {
		flex: 1 0 100%;
		font-size: 11px;
	}

	audio {
		width: 100%;
		height: 2rem;
	}
</style>
