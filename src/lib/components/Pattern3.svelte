<script>
	import Slider from '$lib/components/Slider.svelte'

	let midpointDistance = $state(0);
	let offset1x = $state(0);
	let offset2x = $state(0);

	// Dreiecke aus denen das einzelne Parallelogramm besteht
	const triangleWidth = 60;
	const triangleHeight = Math.tan(30 * Math.PI / 180) * triangleWidth;

    // Rotation der Mittelachse: Offset für die Verschiebung der Endpunkte der Mittellinien der Rauten
    let offset1y = $derived(Math.tan(30 * Math.PI / 180) * offset1x);

    // Verschiebung der Mittelachse: Offset für die Verschiebung der Mittellinie
    let offset2y = $derived(-Math.tan(30 * Math.PI / 180) * offset2x);

	// Verschiebung der Mittelpunkte der langen Seiten (30 Grad nach rechts oben)
	let midpointOffsetX = $derived(midpointDistance * Math.cos(30 * Math.PI / 180));
	let midpointOffsetY = $derived(-midpointDistance * Math.sin(30 * Math.PI / 180));

	// Breite & Höhe des gesamten Grundmoduls
    const moduleCountX = Math.round(1000 / triangleWidth);
    const moduleCountY = Math.round(1000 / triangleHeight);

    function getOffset(xi, yi) {
    return yi % 2 === 1 ? triangleWidth * 2 : 0;
}

</script>

<div class="svg-container">
	<svg class="svg-canvas" viewBox="0 0 1000 1000">
	{#each Array(moduleCountY) as _, yi}
			{#each Array(moduleCountX) as _, xi}

			<g transform="translate({xi * triangleWidth * 4 + getOffset(xi, yi)},{yi * triangleHeight * 6})">
				<g>
					<polygon points="0 0, {triangleWidth+offset1x+offset2x-midpointOffsetX} {-triangleHeight-offset1y+offset2y+midpointOffsetY}, {triangleWidth+midpointOffsetX} {triangleHeight+midpointOffsetY}, {triangleWidth-offset1x+offset2x-midpointOffsetX} {3 * triangleHeight+offset1y+offset2y+midpointOffsetY}, 0 {4 * triangleHeight}, {0+midpointOffsetX} {2 * triangleHeight+midpointOffsetY}"  stroke="black" stroke-width="0" fill="#e67d3c" />
					<polygon points="{0+offset1x+offset2x-midpointOffsetX} {0-offset1y+offset2y+midpointOffsetY}, {triangleWidth} {-triangleHeight}, {triangleWidth+midpointOffsetX} {triangleHeight+midpointOffsetY}, {triangleWidth} {3 * triangleHeight} {0-offset1x+offset2x-midpointOffsetX} {(4 * triangleHeight)+offset1y+offset2y+midpointOffsetY}, {0-offset1x+offset2x+midpointOffsetX} {2*triangleHeight+offset1y+offset2y+midpointOffsetY}" stroke="black" stroke-width="0" fill="#995328" transform="translate({triangleWidth}, {-triangleHeight})"/>
				</g>

				<g transform="rotate(120, 0, 0)">
					<polygon points="0 0, {triangleWidth+offset1x+offset2x-midpointOffsetX} {-triangleHeight-offset1y+offset2y+midpointOffsetY}, {triangleWidth+midpointOffsetX} {triangleHeight+midpointOffsetY}, {triangleWidth-offset1x+offset2x-midpointOffsetX} {3 * triangleHeight+offset1y+offset2y+midpointOffsetY}, 0 {4 * triangleHeight}, {0+midpointOffsetX} {2 * triangleHeight+midpointOffsetY}" stroke="black" stroke-width="0" fill="#1b6566" />
					<polygon points="{0+offset1x+offset2x-midpointOffsetX} {0-offset1y+offset2y+midpointOffsetY}, {triangleWidth} {-triangleHeight}, {triangleWidth+midpointOffsetX} {triangleHeight+midpointOffsetY}, {triangleWidth} {3 * triangleHeight} {0-offset1x+offset2x-midpointOffsetX} {(4 * triangleHeight)+offset1y+offset2y+midpointOffsetY}, {0-offset1x+offset2x+midpointOffsetX} {2*triangleHeight+offset1y+offset2y+midpointOffsetY}" stroke="black" stroke-width="0" fill="#0d3233" transform="translate({triangleWidth}, {-triangleHeight})"/>
				</g>

				<g transform="rotate(240, 0, 0)">
					<polygon points="0 0, {triangleWidth+offset1x+offset2x-midpointOffsetX} {-triangleHeight-offset1y+offset2y+midpointOffsetY}, {triangleWidth+midpointOffsetX} {triangleHeight+midpointOffsetY}, {triangleWidth-offset1x+offset2x-midpointOffsetX} {3 * triangleHeight+offset1y+offset2y+midpointOffsetY}, 0 {4 * triangleHeight}, {0+midpointOffsetX} {2 * triangleHeight+midpointOffsetY}" stroke="black" stroke-width="0" fill="#35c9cc" />
					<polygon points="{0+offset1x+offset2x-midpointOffsetX} {0-offset1y+offset2y+midpointOffsetY}, {triangleWidth} {-triangleHeight}, {triangleWidth+midpointOffsetX} {triangleHeight+midpointOffsetY}, {triangleWidth} {3 * triangleHeight} {0-offset1x+offset2x-midpointOffsetX} {(4 * triangleHeight)+offset1y+offset2y+midpointOffsetY}, {0-offset1x+offset2x+midpointOffsetX} {2*triangleHeight+offset1y+offset2y+midpointOffsetY}" stroke="black" stroke-width="0" fill="#289799" transform="translate({triangleWidth}, {-triangleHeight})"/>
				</g>
			</g>
			{/each}
		{/each}
		 
	</svg>
</div>

<div class="sidebar-right">
	<Slider bind:value={offset1x} label="Rotation der Mittelachse" min={-60} max={60} />
	<Slider bind:value={offset2x} label="Verschiebung der Mittelachse" min={-60} max={60} />
	<Slider bind:value={midpointDistance} label="Verschiebung der Mittelpunkte" min={-28} max={28} />
</div>
