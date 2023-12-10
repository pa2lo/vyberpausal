<script>
	export let value = false
	export let inputid = ''
</script>

<div class="switchInputCont">
	<input id="{inputid}" class="switchInput" type="checkbox" bind:checked={value} />
	<span class="switchInputText" data-text-on="Áno" data-text-off="Nie"></span>
</div>