<script>
	import { fly } from 'svelte/transition'
	import { createEventDispatcher } from 'svelte';
	import Icon from './Icon.svelte'
	import Tooltip from './Tooltip.svelte'

	export let service
	export let isCompared = false

	const dispatch = createEventDispatcher();

	let providerImg = {
		"Telekom": './img/telekom.png',
		"Orange": './img/orange.png',
		"O2": './img/o2.png',
		"4ka": './img/4ka.png'
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

	function toggleComparison(service) {
		if (isCompared) {
			dispatch('rfc', service)
		} else {
			dispatch('atc', service)
		}
	}
</script>

<div in:fly={{ y: 100, duration: 500 }} class="service">
	<div class="serviceheader">
		<img class="serviceLogo" src="{providerImg[service.provider]}" alt="{service.provider}">
		<div class="serviceName">{service.name}</div>
	</div>
	<div class="serviceSection">
		<div class="serviceLineTitle">Minúty</div>
		<div class="serviceLineValue">
			{ valueText(service.mins) }
			{#if service.minsNote}
				<Tooltip text="{service.minsNote}"></Tooltip>
			{/if}
		</div>
	</div>
	<div class="serviceSection">
		<div class="serviceLineTitle">SMS / MMS</div>
		<div class="serviceLineValue">
			{ valueText(service.messages) }
			{#if service.messagesNote}
				<Tooltip text="{service.messagesNote}"></Tooltip>
			{/if}
		</div>
	</div>
	<div class="serviceSection">
		<div class="serviceLineTitle">Dáta</div>
		<div class="serviceLineValue">
			{ dataText(service.data) }
			{#if service.dataNote}
				<Tooltip text="{service.dataNote}"></Tooltip>
			{/if}
		</div>
	</div>
	<div class="serviceSection">
		<div class="serviceLineTitle">Cena</div>
		<div class="serviceLineValue">
			{service.price}€
			{#if service.priceNote}
				<Tooltip text="{service.priceNote}"></Tooltip>
			{/if}
		</div>
	</div>
	<div class="serviceFooter">
		<a class="serviceLink" href="{service.url}" target="_blank"><Icon icon="link"></Icon><span class="serviceLinkTitle">Prejsť na stránku</span></a>
		<a class="serviceLink" class:red={isCompared} href="#compare" on:click|preventDefault="{toggleComparison(service)}"><Icon icon="plus"></Icon><span class="serviceLinkTitle">{#if isCompared}Odobrať z porovnania{:else}Pridať do porovnania{/if}</span></a>
	</div>
</div>