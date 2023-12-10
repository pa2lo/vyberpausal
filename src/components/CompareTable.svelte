<script>
	import { slide } from 'svelte/transition'
	import { createEventDispatcher } from 'svelte'
	import Tooltip from './Tooltip.svelte'
	import Icon from './Icon.svelte'

	export let comapreServices

	const dispatch = createEventDispatcher();

	let providerImg = {
		"Telekom": './img/telekom.png',
		"Orange": './img/orange.png',
		"O2": './img/o2.png',
		"4ka": './img/4ka.png'
	}

	function removeFromCompare(service) {
		dispatch('rfc', service)
	}
	function emitEnd() {
		dispatch('aniend')
	}

	function valueText(num) {
		if (num == 0) return '-'
		else if (num == 9999) return 'Nekonečno'
		else return num
	}

	function dataText(num) {
		if (num == 0) return '-'
		else if (num == 9999) return 'Nekonečno'
		else if (num < 1) return `${num*1000}MB`
		else return `${num}GB`
	}
</script>

<section transition:slide on:introend={emitEnd} class="compareSection container">
	<h2 class="compareTitle">{comapreServices.length} paušálov na porovnanie</h2>
	<div class="compoareTableCont">
		<table class="compareTable">
			<tr>
				<td class="rowTitle">Operátor</td>
				{#each comapreServices as service}
					<td class="compareItemImg">
						<img class="serviceLogo" src="{providerImg[service.provider]}" alt="{service.provider}">
					</td>
				{/each}
			</tr>
			<tr>
				<td class="rowTitle">Názov</td>
				{#each comapreServices as service}
					<td class="compareItemText">
						<a href="{service.url}" target="_blank" class="compareServiceTitle">
							{service.name}
						</a>
					</td>
				{/each}
			</tr>
			<tr>
				<td class="rowTitle">Minúty</td>
				{#each comapreServices as service}
					<td class="compareItemText">
						<div class="compareItemTextCont">
							{ valueText(service.mins) }
							{#if service.minsNote}
								<Tooltip text="{service.minsNote}"></Tooltip>
							{/if}
						</div>
					</td>
				{/each}
			</tr>
			<tr>
				<td class="rowTitle">Správy</td>
				{#each comapreServices as service}
					<td class="compareItemText">
						<div class="compareItemTextCont">
							{ valueText(service.messages) }
							{#if service.messagesNote}
								<Tooltip text="{service.messagesNote}"></Tooltip>
							{/if}
						</div>
					</td>
				{/each}
			</tr>
			<tr>
				<td class="rowTitle">Dáta</td>
				{#each comapreServices as service}
					<td class="compareItemText">
						<div class="compareItemTextCont">
							{ dataText(service.data) }
							{#if service.dataNote}
								<Tooltip text="{service.dataNote}"></Tooltip>
							{/if}
						</div>
					</td>
				{/each}
			</tr>
			<tr>
				<td class="rowTitle">Cena</td>
				{#each comapreServices as service}
					<td class="compareItemText price">
						<div class="compareItemTextCont">
							{service.price}€
							{#if service.priceNote}
								<Tooltip text="{service.priceNote}"></Tooltip>
							{/if}
						</div>
					</td>
				{/each}
			</tr>
			<tr>
				<td class="rowTitle"></td>
				{#each comapreServices as service}
					<td class="compareItemLink">
						<a href="#compare" on:click|preventDefault="{removeFromCompare(service)}"><Icon icon="x"></Icon>Odobrať</a>
					</td>
				{/each}
			</tr>
		</table>
	</div>
</section>