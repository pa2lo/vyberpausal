<script>
	import { slide } from 'svelte/transition';

	export let value = 0
	export let min = 0
	export let max = 100
	export let step = 1
</script>

<div transition:slide class="optionRangeCont" style="--complete: {value / max * 100}%">
	<input class="optionRange" type="range" min={min} max={max} step={step} bind:value="{value}">
	<span class="optionRangeTooltip" style="--transform: {value / max}">
		{#if value >= max}
			Nekonečno
		{:else}
			{value}
		{/if}
	</span>
</div>