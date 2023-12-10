<script>
	import Icon from './Icon.svelte'

	export let text = false

	let bottom
	let side
	let arrowSide
	let position
	let tooltipEl
	let tooltipCont
	let show = false
	
	// helper variables
	let helperCalc
	let iw
	let touchmoveAttached = false

	function mouseenter(e) {
		if (show) return

		let { left: eleft, right: eright, top: etop, width: ewidth } = tooltipCont.getBoundingClientRect()
		iw = document.documentElement.clientWidth
		bottom = window.innerHeight - etop
		side = 0
		position = 'left'
		
		window.requestAnimationFrame(() => {
			let tooltipWidth = tooltipEl.clientWidth / 2
			let ewidth2 = ewidth / 2
			
			if (iw > (eleft + ewidth2 + tooltipWidth + 16)) {
				position = 'left'
				helperCalc = eleft + ewidth2
				side = Math.max(helperCalc - tooltipWidth, 16)
				arrowSide = helperCalc - side
			} else {
				position = 'right'
				helperCalc = eleft + ewidth2
				side = Math.max(16, iw - eright + ewidth2 - tooltipWidth)
				arrowSide = iw - eright + ewidth2 - side
			}

			show = true

			if (!touchmoveAttached) {
				window.addEventListener('touchmove', mouseleave)
				touchmoveAttached = true
			}
		})
	}

	function mouseleave() {
		if (!show) return

		show = false

		if (touchmoveAttached) {
			window.removeEventListener('touchmove', mouseleave)
			touchmoveAttached = false
		}
	}

	function change(text) {
		if (!tooltipCont || !show) return

		mouseleave()
		mouseenter()
	}

	$: change(text);
</script>

{#if text || $$slots.tooltip}
	<div
		class="tooltipCont"
		on:mousemove="{mouseenter}"
		on:mouseleave="{mouseleave}"
		bind:this="{tooltipCont}"
		>
		<slot>
			<Icon icon="info"></Icon>
		</slot>
		<div bind:this="{tooltipEl}" class="tooltipInfo" class:show style="bottom: {bottom}px; {position}: {side}px">
			<span class="tooltipArrow {position}" style="{position}: {arrowSide}px"></span>
			<slot name="tooltip">
				{text}
			</slot>
		</div>
	</div>
{/if}