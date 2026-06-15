<script>
    import RangeSlider from '$lib/components/RangeSlider.svelte';
    import Slider from '$lib/components/Slider.svelte';

    let offset1x = $state(0);
    let offset2x = $state(0);

    let hueMin = $state(0);
    let hueMax = $state(360);

    // Saturation & Lightness sind gruppenübergreifend einheitlich –
    // das war im Hex-Code nicht erkennbar
    const S        = 50;
    const L_light  = 50;
    const L_dark   = 35;

    // t = 0 / 0.5 / 1  →  gleichmäßig über den gewählten Hue-Bereich verteilt
    function hsl(t) {
        const h = Math.round(hueMin + t * (hueMax - hueMin));
        return {
            light: `hsl(${h}, ${S}%, ${L_light}%)`,
            dark:  `hsl(${h}, ${S}%, ${L_dark}%)`,
        };
    }

    let c0 = $derived(hsl(0));    // 0°-Gruppe
    let c1 = $derived(hsl(0.5)); // 120°-Gruppe
    let c2 = $derived(hsl(1));   // 240°-Gruppe

    const triangleWidth  = 60;
    const triangleHeight = Math.tan((30 * Math.PI) / 180) * triangleWidth;
    let offset1y = $derived( Math.tan((30 * Math.PI) / 180) *  offset1x);
    let offset2y = $derived(-Math.tan((30 * Math.PI) / 180) *  offset2x);
    const moduleCountX = Math.round(1000 / triangleWidth);
    const moduleCountY = Math.round(1000 / triangleHeight);

    function getOffset(xi, yi) {
        return yi % 2 === 1 ? triangleWidth * 2 : 0;
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
                <g transform="translate({xi * triangleWidth * 4 + getOffset(xi, yi)},{yi * triangleHeight * 6})">

                    <!-- 0° -->
                    <g clip-path="url(#diamond-clip)">
                        <polygon
                            points="0 0, {triangleWidth + offset1x + offset2x} {-triangleHeight - offset1y + offset2y}, {triangleWidth - offset1x + offset2x} {3 * triangleHeight + offset1y + offset2y} 0 {4 * triangleHeight}"
                            stroke="black" stroke-width="0" fill={c0.light}
                        />
                        <polygon
                            points="{0 + offset1x + offset2x} {0 - offset1y + offset2y}, {triangleWidth} {-triangleHeight}, {triangleWidth} {3 * triangleHeight} {0 - offset1x + offset2x} {4 * triangleHeight + offset1y + offset2y}"
                            stroke="black" stroke-width="0" fill={c0.dark}
                            transform="translate({triangleWidth}, {-triangleHeight})"
                        />
                    </g>

                    <!-- 120° -->
                    <g transform="rotate(120, 0, 0)">
                        <g clip-path="url(#diamond-clip)">
                            <polygon
                                points="0 0, {triangleWidth + offset1x + offset2x} {-triangleHeight - offset1y + offset2y}, {triangleWidth - offset1x + offset2x} {3 * triangleHeight + offset1y + offset2y} 0 {4 * triangleHeight}"
                                stroke="black" stroke-width="0" fill={c1.light}
                            />
                            <polygon
                                points="{0 + offset1x + offset2x} {0 - offset1y + offset2y}, {triangleWidth} {-triangleHeight}, {triangleWidth} {3 * triangleHeight} {0 - offset1x + offset2x} {4 * triangleHeight + offset1y + offset2y}"
                                stroke="black" stroke-width="0" fill={c1.dark}
                                transform="translate({triangleWidth}, {-triangleHeight})"
                            />
                        </g>
                    </g>

                    <!-- 240° -->
                    <g transform="rotate(240, 0, 0)">
                        <g clip-path="url(#diamond-clip)">
                            <polygon
                                points="0 0, {triangleWidth + offset1x + offset2x} {-triangleHeight - offset1y + offset2y}, {triangleWidth - offset1x + offset2x} {3 * triangleHeight + offset1y + offset2y} 0 {4 * triangleHeight}"
                                stroke="black" stroke-width="0" fill={c2.light}
                            />
                            <polygon
                                points="{0 + offset1x + offset2x} {0 - offset1y + offset2y}, {triangleWidth} {-triangleHeight}, {triangleWidth} {3 * triangleHeight} {0 - offset1x + offset2x} {4 * triangleHeight + offset1y + offset2y}"
                                stroke="black" stroke-width="0" fill={c2.dark}
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
    <RangeSlider
    bind:value1={hueMin}
    bind:value2={hueMax}
    label="Farbbereich (Hue 0–270°)"
    min={0}
    max={270}
    step={1}
/>
    <Slider bind:value={offset1x} label="Rotation der Mittelachse"     min={-60} max={60} snapValues={[0]} />
    <Slider bind:value={offset2x} label="Verschiebung der Mittelachse" min={-60} max={60} snapValues={[0]} />
</div>
