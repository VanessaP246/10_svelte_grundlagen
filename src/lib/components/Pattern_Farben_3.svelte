<script>
    import Slider from '$lib/components/Slider.svelte';
    import chroma from 'chroma-js';

    let offset1x = $state(0);
    let offset2x = $state(0);

    let hue0 = $state(0);
    let hue1 = $state(135);
    let hue2 = $state(270);

    let lightness0 = $state(0.6);
    let lightness1 = $state(0.3);
    let lightness2 = $state(0.9);

    let saturation    = $state(0.15);
    let lightnessDiff = $state(0.05);

    const colorModes = [1, 3, 6];
    let colorModeIndex = $state(2);
    let colorMode = $derived(colorModes[colorModeIndex]);

    function makeColor(h, l) {
        return {
            light: chroma.oklch(l + lightnessDiff, saturation, h).hex(),
            dark:  chroma.oklch(l - lightnessDiff, saturation, h).hex(),
        };
    }

    let c0 = $derived(makeColor(hue0, lightness0));
    let c1 = $derived(makeColor(hue1, lightness1));
    let c2 = $derived(makeColor(hue2, lightness2));

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

    <!-- Gruppe 1 -->
    <div class="group">
        <Slider bind:value={hue1}       label="Farbton 1"    min={0}   max={360}  step={1}    snapValues={[0, 60, 120, 180, 240, 300, 360]} />
        <Slider bind:value={lightness1} label="Helligkeit 1" min={0.1} max={0.95} step={0.01} snapValues={[0.3, 0.6, 0.9]} />
    </div>

    <!-- Gruppe 2 -->
    <div class="group">
        <Slider bind:value={hue0}       label="Farbton 2"    min={0}   max={360}  step={1}    snapValues={[0, 60, 120, 180, 240, 300, 360]} />
        <Slider bind:value={lightness0} label="Helligkeit 2" min={0.1} max={0.95} step={0.01} snapValues={[0.3, 0.6, 0.9]} />
    </div>

    <!-- Gruppe 3 -->
    <div class="group">
        <Slider bind:value={hue2}       label="Farbton 3"    min={0}   max={360}  step={1}    snapValues={[0, 60, 120, 180, 240, 300, 360]} />
        <Slider bind:value={lightness2} label="Helligkeit 3" min={0.1} max={0.95} step={0.01} snapValues={[0.3, 0.6, 0.9]} />
    </div>

    <hr class="divider" />

    <!-- Global -->
    <Slider bind:value={saturation}    label="Sättigung"              min={0}    max={0.24} step={0.01} snapValues={[0.15]} />
    <Slider bind:value={lightnessDiff} label="Helligkeitsunterschied" min={0.02} max={0.08} step={0.01} snapValues={[0.05]} />

</div>

<style>
.group {
    margin-bottom: 1.4rem;
}

.divider {
    border: none;
    border-top: 1px solid currentColor;
    opacity: 0.15;
    margin: 1.2rem 0;
}
</style>
