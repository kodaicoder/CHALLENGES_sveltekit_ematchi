<script lang="ts">
	import '../app.css';
	import { confetti } from '@neoconfetti/svelte';
	import { levels } from '$lib/interfaces/levels';
	import Game from '$lib/components/Game.svelte';
	import Modal from '$lib/components/Modal.svelte';

	let gameState: 'waiting' | 'playing' | 'paused' | 'win' | 'lose' = 'waiting';

	let game: Game;

	function playHandler() {
		gameState = 'playing';
	}

	function winHandler() {
		gameState = 'win';
	}

	function loseHandler() {
		gameState = 'lose';
	}

	function pausedHandler() {
		gameState = 'paused';
	}
</script>

<Game
	bind:this={game}
	on:play={playHandler}
	on:win={winHandler}
	on:lose={loseHandler}
	on:paused={pausedHandler}
/>

{#if gameState !== 'playing'}
	<Modal>
		<h1><span>🤯</span>e<span>match</span>i<span>🤯</span></h1>

		{#if gameState == 'win' || gameState == 'lose'}
			{#if gameState == 'win'}
				<p>Congratulation🎉</p>
				<div class="confetti" use:confetti={{ stageWidth: innerWidth, stageHeight: innerHeight }} />
			{/if}
			<p>you {gameState} the game!</p>
			{#if gameState == 'lose'}
				<p>Try again?</p>
			{/if}
		{:else if gameState == 'paused'}
			<p>the game is paused...</p>
		{:else if gameState == 'waiting'}
			<p>the emoji matching game</p>
			<p>Choose a level</p>
		{/if}

		<div class="actions">
			{#if gameState === 'paused'}
				<button on:click={() => game.resume()}>resume</button>
				<button on:click={() => (gameState = 'waiting')}>quit</button>
			{:else}
				{#each levels as level}
					<button
						on:click={() => {
							game.start(level);
						}}>{level.label.toUpperCase()}</button
					>
				{/each}
			{/if}
		</div>
	</Modal>
{/if}

<style>
	h1 {
		font-size: 4em;
	}

	h1 span {
		color: blueviolet;
	}

	h1 span:last-child,
	h1 span:first-child {
		vertical-align: text-bottom;
	}

	button {
		padding: 1rem;
		margin: 0 0.5rem;
	}

	.confetti {
		position: fixed;
		top: 30%;
		left: 50%;
		width: 100%;
		height: 100%;
		pointer-events: none;
	}
</style>
