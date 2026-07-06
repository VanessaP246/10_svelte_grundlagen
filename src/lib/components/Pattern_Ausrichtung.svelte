<script>
  import Slider from '$lib/components/Slider.svelte';
  import RangeSlider from '$lib/components/RangeSlider.svelte';
  import Toggle from '$lib/components/Toggle.svelte';
  import chroma from 'chroma-js';

  const triangleWidth = 60;
  const triangleHeight = Math.tan((30 * Math.PI) / 180) * triangleWidth;
  const moduleCountX = Math.ceil(1000 / (triangleWidth * 4));
  const moduleCountY = Math.ceil(1000 / (triangleHeight * 6)) + 1;

  let t = $state(0);
  function lerp(a, b, t) { return a + (b - a) * t; }
  const tw = triangleWidth;
  const th = triangleHeight;

  let t1 = $derived(Math.min(t * 2, 1));
  let t2 = $derived(Math.max((t - 0.5) * 2, 0));

  let p1_a = $derived([
      [lerp(tw,  2*tw,  t1), lerp(3*th, 2*th, t1)],
      [0,                    4*th                 ],
      [0,                    lerp(0, 2*th,   t1)  ],
      [tw,                   lerp(-th, th,   t1)  ],
  ]);
  const p2_static = [[2*tw, -2*th], [2*tw, 2*th], [tw, 3*th], [tw, -th]];
  let p3_a = $derived([
      [0,  0                    ],
      [tw, -th                  ],
      [tw, lerp(-th,  th,  t1) ],
      [0,  lerp(0,  2*th,  t1) ],
  ]);

  let p1_b = $derived([
    [2*tw,               2*th             ],
    [0,                  4*th             ],
    [0,                  2*th             ],
    [lerp(tw, 2*tw, t2), lerp(th, 0, t2)  ], 
  ]);
  let p2_b = $derived([
      [2*tw,               -2*th                    ],
      [2*tw,               2*th                     ], 
      [lerp(tw, 0, t2),    lerp(3*th, 2*th,   t2)  ],
      [lerp(tw, 0, t2),    lerp(-th,   0,     t2)  ],
  ]);
  let p3_b = $derived([
      [0,                  0                        ],
      [lerp(tw, 0, t2),    lerp(-th,   0,     t2)  ],
      [lerp(tw, 0, t2),    lerp(th,   2*th,   t2)  ],
      [0,                  2*th                     ],
  ]);

  let p1 = $derived(t <= 0.5 ? p1_a : p1_b);
  let p2 = $derived(t <= 0.5 ? p2_static : p2_b);
  let p3 = $derived(t <= 0.5 ? p3_a : p3_b);

  function pts(points) {
      return points.map(([x, y]) => `${x} ${y}`).join(', ');
  }
  function getOffset(xi, yi) {
      return yi % 2 === 1 ? triangleWidth * 2 : 0;
  }

  // --- HILFSFUNKTION FÜR KOMPLEMENTÄRFARBEN ---
  function getComplement(hex) {
    const [L, C, H] = chroma(hex).oklch();
    return chroma.oklch(L, C, (H + 180) % 360).hex();
  }

  // --- ERWEITERTE OPTIONEN (Toggle) ---
  let advanced = $state(false);

  // --- FARBBEREICH (Hue), nur relevant wenn advanced === true ---
  let hueMin = $state(0);
  let hueMax = $state(270);

  function hueAt(pos) {
    return hueMin + pos * (hueMax - hueMin);
  }

  // --- URSPRÜNGLICHE FESTE FARBEN (advanced === false) ---
  const orig_ru_p1 = '#DC6563'; // Hell (Mitte)
  const orig_ru_p2 = '#B84546'; // Dunkel (Außen)
  const orig_ru_p3 = getComplement(chroma.mix(orig_ru_p2, orig_ru_p1, 0.5, 'oklch').hex());

  const orig_lu_p1 = '#701101'; // Hell (Mitte)
  const orig_lu_p2 = '#500000'; // Dunkel (Außen)
  const orig_lu_p3 = getComplement(orig_lu_p1);

  const orig_o_p1 = '#FFE56D';  // Hell (Mitte)
  const orig_o_p2 = '#FCC447';  // Dunkel (Außen)
  const orig_o_p3 = getComplement(orig_o_p2);

  // --- DYNAMISCHE FARBEN (advanced === true), aus Farbbereich-Slider ---
  let dyn_ru_p1 = $derived(chroma.oklch(0.65, 0.15, hueAt(0)).hex());
  let dyn_ru_p2 = $derived(chroma.oklch(0.5,  0.15, hueAt(0)).hex());
  let dyn_ru_p3 = $derived(getComplement(chroma.mix(dyn_ru_p2, dyn_ru_p1, 0.5, 'oklch').hex()));

  let dyn_lu_p1 = $derived(chroma.oklch(0.35, 0.15, hueAt(0.5)).hex());
  let dyn_lu_p2 = $derived(chroma.oklch(0.2,  0.15, hueAt(0.5)).hex());
  let dyn_lu_p3 = $derived(getComplement(chroma.mix(dyn_lu_p2, dyn_lu_p1, 0.5, 'oklch').hex()));

  let dyn_o_p1 = $derived(chroma.oklch(0.9,  0.15, hueAt(1)).hex());
  let dyn_o_p2 = $derived(chroma.oklch(0.78, 0.15, hueAt(1)).hex());
  let dyn_o_p3 = $derived(getComplement(chroma.mix(dyn_o_p2, dyn_o_p1, 0.5, 'oklch').hex()));

  // --- AKTIVE FARBEN je nach Toggle-Status ---
  let color_ru_p1 = $derived(advanced ? dyn_ru_p1 : orig_ru_p1);
  let color_ru_p2 = $derived(advanced ? dyn_ru_p2 : orig_ru_p2);
  let color_ru_p3 = $derived(advanced ? dyn_ru_p3 : orig_ru_p3);

  let color_lu_p1 = $derived(advanced ? dyn_lu_p1 : orig_lu_p1);
  let color_lu_p2 = $derived(advanced ? dyn_lu_p2 : orig_lu_p2);
  let color_lu_p3 = $derived(advanced ? dyn_lu_p3 : orig_lu_p3);

  let color_o_p1 = $derived(advanced ? dyn_o_p1 : orig_o_p1);
  let color_o_p2 = $derived(advanced ? dyn_o_p2 : orig_o_p2);
  let color_o_p3 = $derived(advanced ? dyn_o_p3 : orig_o_p3);
</script>

<div class="svg-container">
  <svg class="svg-canvas" viewBox="0 0 1000 1000">
    {#each Array(moduleCountY) as _, yi}
      {#each Array(moduleCountX) as _, xi}
        <g transform="translate({xi * triangleWidth * 4 + getOffset(xi, yi)},{yi * triangleHeight * 6})">
          
          <!-- Gruppe rechts unten -->
          <g>
            <polygon points={pts(p2)} fill={color_ru_p2} stroke-width="0"/>
            <polygon points={pts(p1)} fill={color_ru_p1} stroke-width="0"/>
            <polygon points={pts(p3)} fill={color_ru_p3} stroke-width="0"/>
          </g>
          
          <!-- Gruppe links unten -->
          <g transform="rotate(120, 0, 0)">
            <polygon points={pts(p2)} fill={color_lu_p2} stroke-width="0"/>
            <polygon points={pts(p1)} fill={color_lu_p1} stroke-width="0"/>
            <polygon points={pts(p3)} fill={color_lu_p3} stroke-width="0"/>
          </g>
          
          <!-- Gruppe oben -->
          <g transform="rotate(240, 0, 0)">
            <polygon points={pts(p2)} fill={color_o_p2} stroke-width="0"/>
            <polygon points={pts(p1)} fill={color_o_p1} stroke-width="0"/>
            <polygon points={pts(p3)} fill={color_o_p3} stroke-width="0"/>
          </g>
          
        </g>
      {/each}
    {/each}
  </svg>
</div>

<div class="sidebar-right">
  <Slider bind:value={t} label="Ausrichtung der Parallelogramme" min={0} max={1} step={0.01} snapValues={[0.5]}/>

  <hr class="divider" />

  <Toggle bind:value={advanced} label="Erweitert: Farbvarianten" />

  {#if advanced}
    <RangeSlider
      bind:value1={hueMin}
      bind:value2={hueMax}
      label="Farbbereich"
      min={0}
      max={270}
      step={1}
    />
  {/if}
</div>

<style>
  .divider {
    border: none;
    border-top: 1px solid currentColor;
    opacity: 0.15;
    margin: 1.2rem 0;
  }
</style>