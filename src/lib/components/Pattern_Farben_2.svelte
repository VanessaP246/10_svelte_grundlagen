<script>
    import RangeSlider from '$lib/components/RangeSlider.svelte';
    import Slider from '$lib/components/Slider.svelte';
    import chroma from 'chroma-js';

    let offset1x = $state(0);
    let offset2x = $state(0);

    let hueMin = $state(0);
    let hueMax = $state(270);
    const colorModes = [1, 3, 6];
    let colorModeIndex = $state(2);
    let colorMode = $derived(colorModes[colorModeIndex]);

    function hsl(t, l) {
        const h = Math.round(hueMin + t * (hueMax - hueMin));
        return {
            light: chroma.oklch(l + 0.05, 0.15, h).hex(),
            dark:  chroma.oklch(l - 0.05, 0.15, h).hex(),
        };
    }

    let c0 = $derived(hsl(0,   0.6));
    let c1 = $derived(hsl(0.5, 0.3));
    let c2 = $derived(hsl(1,   0.9));

    function getColors(xi, yi) {
        if (colorMode === 6) {
            return [
                [c0.light, c0.dark],
                [c1.light, c1.dark],
                [c2.light, c2.dark],
            ];
        }
        if (colorMode === 3) {
            return [
                [c0.light, c0.light],
                [c1.light, c1.light],
                [c2.light, c2.light],
            ];
        }
        const palette = [c0.light, c1.light, c2.light];
        const col = palette[(xi * 2 + yi * 3) % 3];
        return [
            [col, col],
            [col, col],
            [col, col],
        ];
    }

    const triangleWidth  = 60;
    const triangleHeight = Math.tan((30 * Math.PI) / 180) * triangleWidth;
    let offset1y = $derived( Math.tan((30 * Math.PI) / 180) *  offset1x);
    let offset2y = $derived(-Math.tan((30 * Math.PI) / 180) *  offset2x);
    const moduleCountX = Math.round(1000 / triangleWidth) + 1;
    const moduleCountY = Math.round(1000 / triangleHeight);

    function getOffset(xi, yi) {
        if (yi % 2 !== 1) return 0;
        return colorMode === 1 ? triangleWidth * 6 : triangleWidth * 2;
    }
</script>

<div class="svg-container">
    <svg class="svg-canvas" viewBox="0 0 1000 1000">
        <defs>
            <clipPath id="diamond-clip">
                <polygon points="0 0, {2 * triangleWidth} {-2 * triangleHeight}, {2 * triangleWidth} {2 * triangleHeight}, 0 {4 * triangleHeight}" />
            </clipPath>
        </defs>

        {#each Array(moduleCountY) as _, yi}
            {#each Array(moduleCountX) as _, xi}
                {@const cols = getColors(xi, yi)}
                <g transform="translate({(xi - 1) * triangleWidth * 4 + getOffset(xi, yi)},{yi * triangleHeight * 6})">

                    <!-- 0° -->
                    <g clip-path="url(#diamond-clip)">
                        <polygon
                            points="0 0, {triangleWidth + offset1x + offset2x} {-triangleHeight - offset1y + offset2y}, {triangleWidth - offset1x + offset2x} {3 * triangleHeight + offset1y + offset2y} 0 {4 * triangleHeight}"
                            fill={cols[0][0]}
                        />
                        <polygon
                            points="{0 + offset1x + offset2x} {0 - offset1y + offset2y}, {triangleWidth} {-triangleHeight}, {triangleWidth} {3 * triangleHeight} {0 - offset1x + offset2x} {4 * triangleHeight + offset1y + offset2y}"
                            fill={cols[0][1]}
                            transform="translate({triangleWidth}, {-triangleHeight})"
                        />
                    </g>

                    <!-- 120° -->
                    <g transform="rotate(120, 0, 0)">
                        <g clip-path="url(#diamond-clip)">
                            <polygon
                                points="0 0, {triangleWidth + offset1x + offset2x} {-triangleHeight - offset1y + offset2y}, {triangleWidth - offset1x + offset2x} {3 * triangleHeight + offset1y + offset2y} 0 {4 * triangleHeight}"
                                fill={cols[1][0]}
                            />
                            <polygon
                                points="{0 + offset1x + offset2x} {0 - offset1y + offset2y}, {triangleWidth} {-triangleHeight}, {triangleWidth} {3 * triangleHeight} {0 - offset1x + offset2x} {4 * triangleHeight + offset1y + offset2y}"
                                fill={cols[1][1]}
                                transform="translate({triangleWidth}, {-triangleHeight})"
                            />
                        </g>
                    </g>

                    <!-- 240° -->
                    <g transform="rotate(240, 0, 0)">
                        <g clip-path="url(#diamond-clip)">
                            <polygon
                                points="0 0, {triangleWidth + offset1x + offset2x} {-triangleHeight - offset1y + offset2y}, {triangleWidth - offset1x + offset2x} {3 * triangleHeight + offset1y + offset2y} 0 {4 * triangleHeight}"
                                fill={cols[2][0]}
                            />
                            <polygon
                                points="{0 + offset1x + offset2x} {0 - offset1y + offset2y}, {triangleWidth} {-triangleHeight}, {triangleWidth} {3 * triangleHeight} {0 - offset1x + offset2x} {4 * triangleHeight + offset1y + offset2y}"
                                fill={cols[2][1]}
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
    <div class="mode-buttons">
    <div class="label">Modul-Unterteilungen</div>
    <div class="track">
        {#each ['A', 'B', 'C'] as label, i}
            <button
                class:active={colorModeIndex === i}
                onclick={() => colorModeIndex = i}
            >
                {label}
            </button>
        {/each}
    </div>
</div>
    
    <!-- Erste Trennlinie -->
    <hr class="divider" />

    <RangeSlider
        bind:value1={hueMin}
        bind:value2={hueMax}
        label="Farbbereich"
        min={0}
        max={270}
        step={1}
    />
    
    <!-- <hr class="divider" /> -->

    <!-- <Slider bind:value={offset1x} label="Rotation der Mittelachse"     min={-60} max={60} snapValues={[0]} />
    <Slider bind:value={offset2x} label="Verschiebung der Mittelachse" min={-60} max={60} snapValues={[0]} /> -->
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

    /* Styling für die feinen Trennlinien */
    .divider {
        border: none;
        border-top: 1px solid currentColor;
        opacity: 0.15;
        margin: 1.2rem 0;
    }
</style>