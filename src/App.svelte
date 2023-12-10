<script>
	import RangeSlider from './components/RangeSlider.svelte'
	import SwitchInput from './components/SwitchInput.svelte'
	import ServiceItem from './components/ServiceItem.svelte'
	import Wave from './components/Wave.svelte'
	import Icon from './components/Icon.svelte'
	import Tooltip from './components/Tooltip.svelte'
	import CompareTable from './components/CompareTable.svelte'

	import { fly } from 'svelte/transition'

	let useMins = true
	let	mins = 100
	let useMessages = false
	let messages = 50
	let useData = true
	let	data = 2

	let providers = ['Telekom', 'Orange', 'O2', '4ka']

	let compareServicesList = []
	let comapreServices = []
	let compareInit = false

	let processing = false

	let services = false
	let resultServices = []
	let remainingServices = []
	let remainingShow = 0

	let initSearch = false
	let resultEl

	async function getServices() {
		if (!services) {
			const response = await fetch('./services.json')
			services = response.json()
		}
		return services
	}

	function resetState() {
		resultServices = []
		remainingServices = []
		remainingShow = 0
	}

	async function processServices() {
		processing = true

		// get services
		let servicesList = await getServices()

		// conditions
		let filteredServices = servicesList.services.filter(item => (!(useMins && item.mins < mins) && !(useMessages && item.messages < messages) && !(useData && item.data < data)))

		// filter providers
		filteredServices = filteredServices.filter(item => (providers.includes(item.provider)))

		// sort
		filteredServices = filteredServices.sort((a, b) => a.price - b.price)

		// reset state
		resetState()

		// unique
		resultServices = filteredServices.filter((v, i, a) => a.findIndex(t=>(t.provider === v.provider))==i)

		// diff
		remainingServices = filteredServices.filter(item => !resultServices.includes(item))

		if (providers.length < 4) {
			let available = remainingServices.length
			let needFill = 4 - providers.length

			if (needFill > available) remainingShow = available
			else remainingShow = needFill
		}

		processing = false
		initSearch = true
		if (resultServices.length > 0) {
			window.requestAnimationFrame(() => {
				window.scrollTo({
					top: resultEl.offsetTop - 64,
					behavior: 'smooth'
				})
			})
		}
	}

	function addRemaining(e) {
		let scrollby = e.target.offsetTop + document.querySelector('.searchResultsCont').offsetTop - 64

		let available = remainingServices.length
		let diff = available - remainingShow

		if (diff > 8) remainingShow += 8
		else remainingShow += diff

		window.requestAnimationFrame(() => {
			window.scrollTo({
				top: scrollby,
				behavior: 'smooth'
			})
		})
	}

	function addToCompare(event) {
		compareServicesList = [...compareServicesList, event.detail.name]
		comapreServices = [...comapreServices, event.detail]
	}
	function removeFromCompare(event) {
		compareServicesList = compareServicesList.filter(item => item !== event.detail.name)
		comapreServices = comapreServices.filter(item => item.name !== event.detail.name)
		if (compareServicesList.length == 0) compareInit = false
	}

	function showComparison() {
		if (compareInit) scrollToCompare()
		compareInit = true
	}
	function scrollToCompare(e) {
		document.querySelector('.compareSection').scrollIntoView({behavior: "smooth"})
	}

	// $: compareServicesListLength = compareServicesList.length
</script>

<main class="container">
	<div class="optionsCont">
		<div class="option" class:disabled="{!useMins}">
			<label class="optionLabel" for="useMins">
				<Tooltip text="{useMins ? 'Vypnúť' : 'Zapnúť'}">
					<SwitchInput bind:value={useMins} inputid="useMins"></SwitchInput>
				</Tooltip>
				<span class="optionTitle">Voľné minúty</span>
			</label>
			<div class="optionValue">
				{#if useMins && mins >= 500}
					Nekonečno
				{:else if useMins && mins > 0}
					{ mins }
				{:else}
					Žiadne
				{/if}
			</div>
			{#if useMins}
				<RangeSlider bind:value={mins} min=0 max=500 step=10></RangeSlider>
			{/if}
		</div>

		<div class="option" class:disabled="{!useMessages}">
			<label class="optionLabel" for="useMessages">
				<Tooltip text="{useMessages ? 'Vypnúť' : 'Zapnúť'}">
					<SwitchInput bind:value={useMessages} inputid="useMessages"></SwitchInput>
				</Tooltip>
				<span class="optionTitle">Voľné správy</span>
			</label>
			<div class="optionValue">
				{#if useMessages && messages >= 500}
					Nekonečno
				{:else if useMessages && messages > 0}
					{ messages }
				{:else}
					Žiadne
				{/if}
			</div>
			{#if useMessages}
				<RangeSlider bind:value={messages} min=0 max=500 step=10></RangeSlider>
			{/if}
		</div>

		<div class="option" class:disabled="{!useData}">
			<div class="optionLabel">
				<Tooltip text="{useData ? 'Vypnúť' : 'Zapnúť'}">
					<SwitchInput bind:value={useData} inputid="useData"></SwitchInput>
				</Tooltip>
				<label for="useData" class="optionTitle">Voľné dáta</label>
			</div>
			<div class="optionValue">
				{#if useData && data >= 30}
					Nekonečno
				{:else if useData && data >= 1}
					{ data.toFixed(1) }GB
				{:else if useData && data > 0}
					{ data*1000 }MB
				{:else}
					Žiadne
				{/if}
			</div>
			{#if useData}
				<RangeSlider bind:value={data} min=0 max=30 step=0.5></RangeSlider>
			{/if}
		</div>

		<div class="option">
			<div class="optionLabelsGroup">
				<label class="optionLabel">
					<input class="optionCb" bind:group={providers} value="Telekom" type="checkbox" name="providers">
					<span class="optionTitle">Telekom</span>
				</label>
				<label class="optionLabel">
					<input class="optionCb" bind:group={providers} value="Orange" type="checkbox" name="providers">
					<span class="optionTitle">Orange</span>
				</label>
				<label class="optionLabel">
					<input class="optionCb" bind:group={providers} value="O2" type="checkbox" name="providers">
					<span class="optionTitle">O2</span>
				</label>
				<label class="optionLabel">
					<input class="optionCb" bind:group={providers} value="4ka" type="checkbox" name="providers">
					<span class="optionTitle">4ka</span>
				</label>
			</div>
		</div>

		<div class="optionsFooter">
			<button on:click={processServices} class="button" disabled="{processing || !providers.length}"><Icon icon="search"></Icon> Nájsť paušály</button>
		</div>

	</div>
</main>

{#if initSearch}
	<section bind:this="{resultEl}" class="searchResultsSection">
		<div class="searchResultsHeader highPadding">
			Nájdených <span class="searchResultsCount">{resultServices.length + remainingServices.length}</span> paušálov
		</div>
		<div class="searchResultsCont">
			<Wave top={true}></Wave>
			<div class="searchResults container">
				<div class="servicesGrid">
					{#each resultServices as service (service.name)}
						<ServiceItem service={service} isCompared={compareServicesList.includes(service.name)} on:atc={addToCompare} on:rfc={removeFromCompare}></ServiceItem>
					{/each}
					{#if remainingShow > 0}
						{#each remainingServices.slice(0, remainingShow) as service (service.name)}
							<ServiceItem service={service} isCompared={compareServicesList.includes(service.name)} on:atc={addToCompare} on:rfc={removeFromCompare}></ServiceItem>
						{/each}
					{/if}
				</div>
			</div>
			{#if remainingShow < remainingServices.length}
				<button on:click={addRemaining} class="button addRemaining"><Icon icon="down"></Icon> Zobraziť ďalšie</button>
			{/if}
			<Wave></Wave>
		</div>
	</section>

	{#if compareServicesList.length}
		<div transition:fly={{y: 60, duration: 250}} class="compareBtnCont">
			<button class="button compareButton" on:click="{showComparison}" class:shake="{compareServicesList.length > 1 && !compareInit}">Porovnať <span class="compareBtnCount">{compareServicesList.length}</span></button>
		</div>
	{/if}

	{#if compareInit && compareServicesList.length}
		<CompareTable on:aniend={scrollToCompare} comapreServices={comapreServices} on:rfc={removeFromCompare}></CompareTable>
	{/if}

{/if}