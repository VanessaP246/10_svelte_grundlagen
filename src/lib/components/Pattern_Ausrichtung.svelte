<script>
  import Slider from '$lib/components/Slider.svelte';
  import chroma from 'chroma-js';

  const triangleWidth = 60;
  const triangleHeight = Math.tan((30 * Math.PI) / 180) * triangleWidth;
  const moduleCountX = Math.ceil(1000 / (triangleWidth * 4));
  const moduleCountY = Math.ceil(1000 / (triangleHeight * 6)) + 1;

  let t = $state(0);
  function lerp(a, b, t) { return a + (b - a) * t; }
  const tw = triangleWidth;
  const th = triangleHeight;

  // t1: 0→1 während t: 0→0.5  (erste Phase)
  // t2: 0→1 während t: 0.5→1  (zweite Phase)
  let t1 = $derived(Math.min(t * 2, 1));
  let t2 = $derived(Math.max((t - 0.5) * 2, 0));

  // Phase 1: vertikale Parallelogramme → Raute
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

    // Phase 2: Raute → neue asymmetrische Formen
  // p1_b: Punkt rechts unten (Index 0) bleibt starr auf [2*tw, 2*th] stehen.
  // Punkt rechts oben (Index 3) wandert linear zum Mittelpunkt von P2[1] und P2[2] (also [tw, 2*th])
  let p1_b = $derived([
    [2*tw,               2*th             ],
    [0,                  4*th             ],
    [0,                  2*th             ],
    [lerp(tw, 2*tw, t2), lerp(th, 0, t2)  ], // Bewegt sich schräg nach rechts oben zu [2*tw, 0]
]);
  // p2_b wächst zur oberen Hälfte (der Punkt rechts unten [2*tw, 2*th] bleibt fest)
  let p2_b = $derived([
      [2*tw,               -2*th                    ],
      [2*tw,               2*th                     ], 
      [lerp(tw, 0, t2),    lerp(3*th, 2*th,   t2)  ],
      [lerp(tw, 0, t2),    lerp(-th,   0,     t2)  ],
  ]);
  // p3_b schrumpft von der Raute zur Linie
  let p3_b = $derived([
      [0,                  0                        ],
      [lerp(tw, 0, t2),    lerp(-th,   0,     t2)  ],
      [lerp(tw, 0, t2),    lerp(th,   2*th,   t2)  ],
      [0,                  2*th                     ],
  ]);

  // Aktive Polygone je nach Phase
  let p1 = $derived(t <= 0.5 ? p1_a : p1_b);
  let p2 = $derived(t <= 0.5 ? p2_static : p2_b);
  let p3 = $derived(t <= 0.5 ? p3_a : p3_b);

  function pts(points) {
      return points.map(([x, y]) => `${x} ${y}`).join(', ');
  }
  function getOffset(xi, yi) {
      return yi % 2 === 1 ? triangleWidth * 2 : 0;
  }

  // --- STATISCHE FARBEN ---

  // 1. Gruppe rechts unten (Ausrichtung nach rechts)
  const color_ru_p1 = '#DC6563'; // Hell (Mitte)
  const color_ru_p2 = '#B84546'; // Dunkel (Außen)
  const color_ru_p3 = chroma.mix('#B84546', '#DC6563', 0.5, 'oklch').hex(); // Mittelwert

  // 2. Gruppe links unten (120 Grad rotiert)
  const color_lu_p1 = '#701101'; // Hell (Mitte)
  const color_lu_p2 = '#500000'; // Dunkel (Außen)
  const color_lu_p3 = chroma.mix('#500000', '#701101', 0.5, 'oklch').hex(); // Mittelwert

  // 3. Gruppe oben (240 Grad rotiert)
  const color_o_p1 = '#FFE56D';  // Hell (Mitte)
  const color_o_p2 = '#FCC447';  // Dunkel (Außen)
  const color_o_p3 = chroma.mix('#FCC447', '#FFE56D', 0.5, 'oklch').hex();  // Mittelwert
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
</div>
