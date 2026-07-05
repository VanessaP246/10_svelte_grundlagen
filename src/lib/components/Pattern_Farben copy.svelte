<script>
    import Slider from '$lib/components/Slider.svelte';
    import ColorPicker from './ColorPicker.svelte'; // Pfad zu deiner Colorpicker-Komponente anpassen
    import chroma from 'chroma-js';

    let offset1x = $state(0);
    let offset2x = $state(0);

    const colorModes = [1, 3, 6];
    let colorModeIndex = $state(2);
    let colorMode = $derived(colorModes[colorModeIndex]);

    // Zustand für die 3 anpassbaren Farben (Standard-Palette)
    let colors = $state(['#f06292', '#4fc3f7', '#aed581']);
    let activeColorTab = $state(0); // Aktuell ausgewählte Farbe im Picker (0, 1 oder 2)

    // Funktion, um basierend auf einer Custom-Farbe eine helle und dunkle Variante zu berechnen.
    // Wir verschieben die Helligkeit (L) im OKLCH-Farbraum um +0.05 / -0.05, genau wie im Original.
    function getLightDark(hexColor) {
        try {
            const c = chroma(hexColor);
            const oklch = c.oklch(); // [L, C, H]
            const l = oklch[0];
            const chromaVal = Number.isNaN(oklch[1]) ? 0 : oklch[1];
            const h = Number.isNaN(oklch[2]) ? 0 : oklch[2];

            return {
                light: chroma.oklch(Math.min(1, l + 0.05), chromaVal, h).hex(),
                dark:  chroma.oklch(Math.max(0, l - 0.05), chromaVal, h).hex(),
            };
        } catch (e) {
            // Fallback für ungültige Zwischenzustände beim Tippen/Verschieben
            return { light: hexColor, dark: hexColor };
        }
    }

    // Reaktive Ableitung der drei Farbpaare aus dem colors-Array
    let c0 = $derived(getLightDark(colors[0]));
    let c1 = $derived(getLightDark(colors[1]));
    let c2 = $derived(getLightDark(colors[2]));

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
    <Slider
        bind:value={colorModeIndex}
        label="Modul-Unterteilungen"
        min={0}
        max={2}
        step={1}
        snapValues={[0, 1, 2]}
        snapWidth={999}
        thumbSize="33.33%"
    />
    
    <hr class="divider" />

    <!-- Neuer Colorpicker-Bereich mit Tabs -->
    <div class="color-picker-section">
        <span class="section-label">Muster-Farben</span>
        <div class="color-tabs">
            {#each colors as col, i}
                <button 
                    class="tab-btn" 
                    class:active={activeColorTab === i} 
                    onclick={() => activeColorTab = i}
                >
                    <span class="color-preview" style="background-color: {col}"></span>
                    Farbe {i + 1}
                </button>
            {/each}
        </div>

        <div class="picker-container">
            <!-- Bindung direkt an das ausgewählte Element im reaktiven Array -->
            <ColorPicker bind:color={colors[activeColorTab]} width={200} height={150} />
        </div>
    </div>
    
    <hr class="divider" />

    <Slider bind:value={offset1x} label="Rotation der Mittelachse"     min={-60} max={60} snapValues={[0]} />
    <Slider bind:value={offset2x} label="Verschiebung der Mittelachse" min={-60} max={60} snapValues={[0]} />
</div>

<style>
    /* Styling für die feinen Trennlinien */
    .divider {
        border: none;
        border-top: 1px solid currentColor;
        opacity: 0.15;
        margin: 1.2rem 0;
    }

    /* Styling für den Colorpicker-Bereich */
    .color-picker-section {
        display: flex;
        flex-direction: column;
        gap: 0.8rem;
    }

    .section-label {
        font-size: 0.8rem;
        font-weight: 600;
        text-transform: uppercase;
        letter-spacing: 0.05em;
        opacity: 0.7;
    }

    .color-tabs {
        display: flex;
        gap: 0.3rem;
        background: rgba(0, 0, 0, 0.05);
        padding: 0.2rem;
        border-radius: 8px;
    }

    :global(body.dark) .color-tabs,
    @media (prefers-color-scheme: dark) {
        .color-tabs {
            background: rgba(255, 255, 255, 0.05);
        }
    }

    .tab-btn {
        flex: 1;
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 0.4rem;
        background: transparent;
        border: none;
        padding: 0.5rem 0.2rem;
        font-size: 0.8rem;
        border-radius: 6px;
        cursor: pointer;
        transition: background 0.2s, color 0.2s;
        color: inherit;
        font-family: inherit;
    }

    .tab-btn:hover {
        background: rgba(0, 0, 0, 0.03);
    }

    .tab-btn.active {
        background: #fff;
        color: #000;
        box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
        font-weight: 500;
    }

    @media (prefers-color-scheme: dark) {
        .tab-btn.active {
            background: #2a2a2a;
            color: #fff;
            box-shadow: 0 1px 3px rgba(0, 0, 0, 0.3);
        }
    }

    .color-preview {
        width: 12px;
        height: 12px;
        border-radius: 50%;
        border: 1px solid rgba(0, 0, 0, 0.15);
        display: inline-block;
    }

    .picker-container {
        display: flex;
        justify-content: center;
        padding: 0.2rem 0;
    }
</style>
