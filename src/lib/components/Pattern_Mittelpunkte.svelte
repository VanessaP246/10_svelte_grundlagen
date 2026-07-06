<script>
	import Slider from '$lib/components/Slider.svelte';

	let midpointDistance = $state(0);

	// Farb-Modi für die Unterteilungen
	const colorModes = [1, 3, 6];
	let colorModeIndex = $state(2);
	let colorMode = $derived(colorModes[colorModeIndex]);

	// Die ursprünglichen Gelb- und Rot-Töne als statische Farbpaletten
	const c0 = { light: '#DC6563', dark: '#B84546' }; // Rot-Töne
	const c1 = { light: '#701101', dark: '#500000' }; // Dunkelrot-Töne
	const c2 = { light: '#FFE56D', dark: '#FCC447' }; // Gelb-Töne

	// Bestimmt die Farben der Segmente reaktiv basierend auf dem gewählten Modus
	function getColors(xi, yi) {
		if (colorMode === 6) {
			return [
				[c0.light, c0.dark],
				[c1.light, c1.dark],
				[c2.light, c2.dark]
			];
		}
		if (colorMode === 3) {
			return [
				[c0.light, c0.light],
				[c1.light, c1.light],
				[c2.light, c2.light]
			];
		}
		// colorMode === 1
		const palette = [c0.light, c1.light, c2.light];
		const col = palette[(xi * 2 + yi * 3) % 3];
		return [
			[col, col],
			[col, col],
			[col, col]
		];
	}

	// Geometrie-Berechnungen
	const triangleWidth = 60;
	const triangleHeight = Math.tan((30 * Math.PI) / 180) * triangleWidth;

	// Verschiebung der Mittelpunkte der langen Seiten (30 Grad nach rechts oben)
	let midpointOffsetX = $derived(midpointDistance * Math.cos((30 * Math.PI) / 180));
	let midpointOffsetY = $derived(-midpointDistance * Math.sin((30 * Math.PI) / 180));

	const moduleCountX = Math.round(1000 / triangleWidth) + 1;
	const moduleCountY = Math.round(1000 / triangleHeight);

	// Versatz der Zeilen (beeinflusst durch den Modul-Modus)
	function getOffset(xi, yi) {
		if (yi % 2 !== 1) return 0;
		return colorMode === 1 ? triangleWidth * 6 : triangleWidth * 2;
	}
</script>

<div class="svg-container">
	<svg class="svg-canvas" viewBox="0 0 1000 1000">
		{#each Array(moduleCountY) as _, yi}
			{#each Array(moduleCountX) as _, xi}
				{@const cols = getColors(xi, yi)}
				<g
					transform="translate({(xi - 1) * triangleWidth * 4 + getOffset(xi, yi)},{yi *
						triangleHeight *
						6})"
				>
					<!-- Gruppe 1 (0° - Rot-Töne) -->
					<g>
						<polygon
							points="
                                0 0,
                                {triangleWidth - midpointOffsetX} {-triangleHeight +
								midpointOffsetY},
                                {triangleWidth + midpointOffsetX} {triangleHeight +
								midpointOffsetY},
                                {triangleWidth - midpointOffsetX} {3 * triangleHeight +
								midpointOffsetY},
                                0 {4 * triangleHeight},
                                {midpointOffsetX} {2 * triangleHeight + midpointOffsetY}
                            "
							stroke="black"
							stroke-width="0"
							fill={cols[0][0]}
						/>
						<polygon
							points="
                                {-midpointOffsetX} {midpointOffsetY},
                                {triangleWidth} {-triangleHeight},
                                {triangleWidth + midpointOffsetX} {triangleHeight +
								midpointOffsetY},
                                {triangleWidth} {3 * triangleHeight},
                                {-midpointOffsetX} {4 * triangleHeight + midpointOffsetY},
                                {midpointOffsetX} {2 * triangleHeight + midpointOffsetY}
                            "
							stroke="black"
							stroke-width="0"
							fill={cols[0][1]}
							transform="translate({triangleWidth}, {-triangleHeight})"
						/>
					</g>

					<!-- Gruppe 2 (120° - Dunkelrot-Töne) -->
					<g transform="rotate(120, 0, 0)">
						<polygon
							points="
                                0 0,
                                {triangleWidth - midpointOffsetX} {-triangleHeight +
								midpointOffsetY},
                                {triangleWidth + midpointOffsetX} {triangleHeight +
								midpointOffsetY},
                                {triangleWidth - midpointOffsetX} {3 * triangleHeight +
								midpointOffsetY},
                                0 {4 * triangleHeight},
                                {midpointOffsetX} {2 * triangleHeight + midpointOffsetY}
                            "
							stroke="black"
							stroke-width="0"
							fill={cols[1][0]}
						/>
						<polygon
							points="
                                {-midpointOffsetX} {midpointOffsetY},
                                {triangleWidth} {-triangleHeight},
                                {triangleWidth + midpointOffsetX} {triangleHeight +
								midpointOffsetY},
                                {triangleWidth} {3 * triangleHeight},
                                {-midpointOffsetX} {4 * triangleHeight + midpointOffsetY},
                                {midpointOffsetX} {2 * triangleHeight + midpointOffsetY}
                            "
							stroke="black"
							stroke-width="0"
							fill={cols[1][1]}
							transform="translate({triangleWidth}, {-triangleHeight})"
						/>
					</g>

					<!-- Gruppe 3 (240° - Gelb-Töne) -->
					<g transform="rotate(240, 0, 0)">
						<polygon
							points="
                                0 0,
                                {triangleWidth - midpointOffsetX} {-triangleHeight +
								midpointOffsetY},
                                {triangleWidth + midpointOffsetX} {triangleHeight +
								midpointOffsetY},
                                {triangleWidth - midpointOffsetX} {3 * triangleHeight +
								midpointOffsetY},
                                0 {4 * triangleHeight},
                                {midpointOffsetX} {2 * triangleHeight + midpointOffsetY}
                            "
							stroke="black"
							stroke-width="0"
							fill={cols[2][0]}
						/>
						<polygon
							points="
                                {-midpointOffsetX} {midpointOffsetY},
                                {triangleWidth} {-triangleHeight},
                                {triangleWidth + midpointOffsetX} {triangleHeight +
								midpointOffsetY},
                                {triangleWidth} {3 * triangleHeight},
                                {-midpointOffsetX} {4 * triangleHeight + midpointOffsetY},
                                {midpointOffsetX} {2 * triangleHeight + midpointOffsetY}
                            "
							stroke="black"
							stroke-width="0"
							fill={cols[2][1]}
							transform="translate({triangleWidth}, {-triangleHeight})"
						/>
					</g>
				</g>
			{/each}
		{/each}
	</svg>
</div>

<div class="sidebar-right">
	<Slider
		bind:value={midpointDistance}
		label="Verschiebung der Mittelpunkte"
		min={-43}
		max={43}
		snapValues={[0]}
	/>

	<hr class="divider" />

	<div class="mode-buttons">
		<div class="label">Modul-Unterteilungen</div>
		<div class="track">
			{#each ['A', 'B', 'C'] as label, i}
				<button class:active={colorModeIndex === i} onclick={() => (colorModeIndex = i)}>
					{label}
				</button>
			{/each}
		</div>
	</div>
</div>

<style>
	.mode-buttons {
		display: flex;
		flex-direction: column;
		width: 100%;
		margin-bottom: 1rem;
	}

	.mode-buttons .track {
		display: flex;
		width: 100%;
		height: 20px;
		background: transparent;
		gap: 2px;
	}

	.mode-buttons button {
		flex: 1;
		height: 100%;
		background: #444;
		border: 1px solid transparent;
		border-radius: 4px;
		color: #fff;
		font-size: 0.75rem;
		font-variant-numeric: tabular-nums;
		line-height: 18px;
		cursor: pointer;
		user-select: none;
	}

	.mode-buttons button.active {
		background: #666;
		border-color: #777;
	}

	.label {
		font-size: 0.75rem;
		margin-top: 0;
		margin-bottom: 0.3rem;
		color: #ccc;
	}

	.divider {
		border: none;
		border-top: 1px solid currentColor;
		opacity: 0.15;
		margin: 1.2rem 0;
	}
</style>
