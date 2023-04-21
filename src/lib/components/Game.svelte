<script lang="ts">
	import type { Level } from '$lib/interfaces/levels';
	import { levels } from '$lib/interfaces/levels';
	import { shuffle } from '$lib/utils/utils';
	import { onMount, createEventDispatcher } from 'svelte';
	import Countdown from './Countdown.svelte';
	import Found from './Found.svelte';
	import Grid from './Grid.svelte';

	const dispatch = createEventDispatcher();

	let size: number;
	let grid: string[] = [];
	let found: string[] = [];
	let remaining: number = 0;
	let duration: number = 0;
	let playing: boolean = false;
	let gameGrid: Grid;

	export function start(level: Level) {
		size = level.size;
		grid = create_grid(level);
		remaining = duration = level.duration;
		resume();
		gameGrid.resetCard();
	}

	export function resume() {
		playing = true;
		countDown();
		dispatch('play');
	}

	function create_grid(level: Level): string[] {
		//? copy emojis array from Level seed
		const copy = level.emojis.slice();

		//? declare a empty array of string for made it as a container of pairs of picked emojis
		const pairs: string[] = [];

		//? loop thru and pick emoji loop lap will depend on size of game board that depend on level of the game
		for (let i = 0; i < (level.size * level.size) / 2; i++) {
			//? random a number but it will not greater than copied emojis length
			const index = Math.floor(Math.random() * copy.length);

			//? pick a emoji from copied emojis array
			const emoji = copy[index];

			//? removed a emoji that already picked from copied emojis array
			copy.splice(index, 1);

			//? push emoji to pairs array at this point you will get 1 emoji at a time of 1 loop lap
			pairs.push(emoji);
		}
		//? rest found array
		found = [];

		//? made a emojis has a pairs of it self by pushing pairs again to pairs array
		return shuffle([...pairs, ...pairs]);
	}

	function foundHandler(e: CustomEvent<any>) {
		//? listen to found event and bring founded emoji in to the list of found
		setTimeout(() => {
			found = [...found, e.detail.emoji];

			if (found.length === (size * size) / 2) {
				//? WIN
				dispatch('win');
			}
		}, 350);
	}

	function countDown() {
		const start = Date.now();
		let remaining_at_start = remaining;

		const loop = () => {
			if (!playing) return;
			requestAnimationFrame(loop);

			remaining = remaining_at_start - (Date.now() - start);
			if (remaining <= 0) {
				//? LOSE
				playing = false;
				dispatch('lose');
			}
		};

		loop();
	}

	function countdownClickHandler() {
		//? PAUSE
		playing = false;
		dispatch('paused');
	}
</script>

<div class="game" style="--size: {size}">
	<!-- content here -->
	<div class="info">
		{#if playing}
			<Countdown {remaining} {duration} on:click={countdownClickHandler} />
		{/if}
	</div>

	<div class="grid-container">
		<Grid {grid} on:found={foundHandler} {found} bind:this={gameGrid} />
	</div>
	<div class="info">
		<Found {found} />
	</div>
</div>

<style>
	.game {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 100%;
		font-size: min(1vmin, 0.35rem);
	}

	.info {
		width: 80em;
		height: 10em;
		/* background-color: purple; */
	}

	.grid-container {
		width: 80em;
		height: 80em;
	}
</style>
