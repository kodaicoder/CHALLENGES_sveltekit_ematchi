<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import Square from './Square.svelte';

	export let grid: string[];
	export let found: string[];

	const dispatch = createEventDispatcher();

	let first: number = -1;
	let second: number = -1;
	let reset_timeout: number;

	function onCardClick(emoji: string, index: number) {
		clearTimeout(reset_timeout);
		if (first === -1 && second === -1) {
			first = index;
		} else if (second === -1) {
			second = index;
			if (grid[first] === grid[second]) {
				//? Correct
				dispatch('found', {
					emoji
				});
			} else {
				//? Incorrect
				reset_timeout = setTimeout(() => {
					first = second = -1;
				}, 1000);
			}
		} else {
			second = -1;
			first = index;
		}
	}

	export function resetCard() {
		first = -1;
		second = -1;
	}
</script>

<div class="grid">
	{#each grid as emoji, index}
		<Square
			{emoji}
			on:click={() => {
				onCardClick(emoji, index);
			}}
			selected={first === index || second === index}
			isFounded={found.includes(emoji)}
			group={grid.indexOf(emoji) === index ? 'first' : 'second'}
		/>
	{/each}
</div>

<style>
	.grid {
		display: grid;
		grid-template-columns: repeat(var(--size), 1fr);
		grid-template-rows: repeat(var(--size), 1fr);
		gap: 0.5em;
		height: 100%;
		perspective: 100vw;
	}
</style>
