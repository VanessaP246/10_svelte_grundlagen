<script>
	import Slider from '$lib/components/Slider.svelte'
	import Toggle from '$lib/components/Toggle.svelte'
	import chroma from 'chroma-js';

	let midpointDistance = $state(0);
	let offset1x = $state(0);
	let offset2x = $state(0);
	let linkValues = $state(true);

	$effect(() => {
		if (linkValues) {
			offset2x = offset1x >= 0 ? 60 - offset1x : -60 - offset1x;
		}
	});

	const triangleWidth = 60;
	const triangleHeight = Math.tan((30 * Math.PI) / 180) * triangleWidth;

	let offset1y = $derived(Math.tan((30 * Math.PI) / 180) * offset1x);
	let offset2y = $derived(-Math.tan((30 * Math.PI) / 180) * offset2x);

	const moduleCountX = Math.round(1000 / triangleWidth);
	const moduleCountY = Math.round(1000 / triangleHeight);

	function getOffset(xi, yi) {
		return yi % 2 === 1 ? triangleWidth * 2 : 0;
	}
</script>

<div class="svg-container">
	<svg class="svg-canvas" viewBox="0 0 1000 1000">
		<defs>
			<!-- Feste Außenkontur des Parallelogramms (unabhängig von Offsets) -->
			<clipPath id="diamond-clip">
				<polygon points="0 0, {2 * triangleWidth} {-2 * triangleHeight}, {2 * triangleWidth} {2 * triangleHeight}, 0 {4 * triangleHeight}" />
			</clipPath>
		</defs>

		{#each Array(moduleCountY) as _, yi}
			{#each Array(moduleCountX) as _, xi}
				<g transform="translate({xi * triangleWidth * 4 + getOffset(xi, yi)},{yi * triangleHeight * 6})">

					<!-- 0° – clip-path direkt auf die Gruppe -->
					<g clip-path="url(#diamond-clip)">
						<polygon
							points="0 0, {triangleWidth + offset1x + offset2x} {-triangleHeight - offset1y + offset2y}, {triangleWidth - offset1x + offset2x} {3 * triangleHeight + offset1y + offset2y} 0 {4 * triangleHeight}"
							stroke="black" stroke-width="0" fill="#e67d3c"
						/>
						<polygon
							points="{0 + offset1x + offset2x} {0 - offset1y + offset2y}, {triangleWidth} {-triangleHeight}, {triangleWidth} {3 * triangleHeight} {0 - offset1x + offset2x} {4 * triangleHeight + offset1y + offset2y}"
							stroke="black" stroke-width="0" fill="#995328"
							transform="translate({triangleWidth}, {-triangleHeight})"
						/>
					</g>

					<!-- 120° – innere Wrapper-Gruppe mit clip-path -->
					<g transform="rotate(120, 0, 0)">
						<g clip-path="url(#diamond-clip)">
							<polygon
								points="0 0, {triangleWidth + offset1x + offset2x} {-triangleHeight - offset1y + offset2y}, {triangleWidth - offset1x + offset2x} {3 * triangleHeight + offset1y + offset2y} 0 {4 * triangleHeight}"
								stroke="black" stroke-width="0" fill="#1b6566"
							/>
							<polygon
								points="{0 + offset1x + offset2x} {0 - offset1y + offset2y}, {triangleWidth} {-triangleHeight}, {triangleWidth} {3 * triangleHeight} {0 - offset1x + offset2x} {4 * triangleHeight + offset1y + offset2y}"
								stroke="black" stroke-width="0" fill="#0d3233"
								transform="translate({triangleWidth}, {-triangleHeight})"
							/>
						</g>
					</g>

					<!-- 240° – innere Wrapper-Gruppe mit clip-path -->
					<g transform="rotate(240, 0, 0)">
						<g clip-path="url(#diamond-clip)">
							<polygon
								points="0 0, {triangleWidth + offset1x + offset2x} {-triangleHeight - offset1y + offset2y}, {triangleWidth - offset1x + offset2x} {3 * triangleHeight + offset1y + offset2y} 0 {4 * triangleHeight}"
								stroke="black" stroke-width="0" fill="#35c9cc"
							/>
							<polygon
								points="{0 + offset1x + offset2x} {0 - offset1y + offset2y}, {triangleWidth} {-triangleHeight}, {triangleWidth} {3 * triangleHeight} {0 - offset1x + offset2x} {4 * triangleHeight + offset1y + offset2y}"
								stroke="black" stroke-width="0" fill="#289799"
								transform="translate({triangleWidth}, {-triangleHeight})"
							/>
						</g>
					</g>

				</g>
			{/each}
		{/each}
	</svg>
</div>

<div class="sidebar-right">
	<Slider bind:value={offset1x} label="Rotation der Mittelachse" min={-60} max={60} snapValues={[0]} />
<Slider bind:value={offset2x} label="Verschiebung der Mittelachse" min={-60} max={60} snapValues={[0]} />
<Toggle bind:value={linkValues} label="Beide Werte koppeln" />
</div>