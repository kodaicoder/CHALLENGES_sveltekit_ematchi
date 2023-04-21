<script lang="ts">
	import { get_twemoji_url } from '$lib/utils/utils';
	import { receive } from '$lib/utils/transitions';

	export let found: string[];
</script>

<div class="found">
	{#each found as emoji}
		<div class="pair">
			<img src={get_twemoji_url(emoji)} alt={emoji} in:receive={{ key: `${emoji}:first` }} />
			<img src={get_twemoji_url(emoji)} alt={emoji} in:receive={{ key: `${emoji}:second` }} />
		</div>
	{/each}
</div>

<style>
	.found {
		display: flex;
		justify-content: center;
		align-items: center;
		gap: 1em;
		height: 100%;
		filter: drop-shadow(0.2em 0.2em 0.5em rgba(0, 0, 0, 0.2));
	}
	.pair {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 4em;
		height: 4em;
		background-color: white;
		padding: 3em;
		border-radius: 50%;
	}
	img {
		position: absolute;

		width: 3em;
		height: 3em;
	}
</style>
