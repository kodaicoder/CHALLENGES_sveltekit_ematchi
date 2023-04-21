<script lang="ts">
	import { get_twemoji_url } from '$lib/utils/utils';
	import { send } from '$lib/utils/transitions';
	import { fade } from 'svelte/transition';
	export let emoji: string;
	export let selected: boolean;
	export let isFounded: boolean;

	export let group: 'first' | 'second';
</script>

<div class="square" class:flipped={selected && !isFounded}>
	<button type="button" on:click disabled={selected || isFounded} class:founded={isFounded} />
	<div class="bg" class:founded={isFounded} />
	{#if !isFounded}
		<img src={get_twemoji_url(emoji)} alt={emoji} out:send={{ key: `${emoji}:${group}` }} />
	{/if}
</div>

<style>
	.square {
		display: flex;
		justify-content: center;
		align-items: center;
		transform-style: preserve-3d;
		transition: transform 0.5s;
	}

	.flipped {
		transform: rotateY(180deg);
		border-radius: 1em;
		z-index: 2;
	}

	button {
		position: absolute;
		width: 100%;
		height: 100%;
		backface-visibility: hidden;
		background-color: #aaa;
		border: 0;
		border-radius: 1em;
		font-size: inherit;
	}
	button:disabled {
		cursor: default;
	}

	.bg {
		position: absolute;
		width: 100%;
		height: 100%;
		background-color: white;
		transform: rotateY(180deg);
		backface-visibility: hidden;
		border-radius: 1em;
		border: 0.5em solid #eee;
		transition: transform;
		transition-duration: 500ms;
	}

	.founded {
		background-color: var(--main-bg-color);
		border: none;
	}

	img {
		width: 5em;
		height: 5em;
		pointer-events: none;
		transform: rotateY(180deg);
		backface-visibility: hidden;
	}
</style>
