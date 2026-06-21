<script>
  import Slider from '$lib/components/Slider.svelte'
  const triangleWidth = 60;
  const triangleHeight = Math.tan((30 * Math.PI) / 180) * triangleWidth;
  const moduleCountX = Math.ceil(1000 / (triangleWidth * 4));
  const moduleCountY = Math.ceil(1000 / (triangleHeight * 6)) + 1;
  let t = $state(0);
  function lerp(a, b, t) { return a + (b - a) * t; }
  const tw = triangleWidth;
  const th = triangleHeight;

  let p1 = $derived([
    [lerp(tw,  2*tw,  t),  lerp(3*th, 2*th,   t)],
    [0,                    4*th                 ],
    [0,                    lerp(0,    2*th,   t)],
    [tw,                   lerp(-th,  th,     t)],
  ]);
  const p2 = [
    [2*tw,  -2*th ],
    [2*tw,   2*th ],
    [tw,     3*th ],
    [tw,    -th   ],
  ];
  // Neues Polygon p3: wächst von unsichtbar (t=0) zu Parallelogramm (t=1)
  // Punkte bleiben stets an den beschriebenen Positionen angeheftet:
  //   1. Drehpunkt der Gruppe
  //   2. P2 links oben  (statisch)
  //   3. P1 rechts oben (animiert mit P1[3])
  //   4. P1 links oben  (animiert mit P1[2])
  let p3 = $derived([
    [0,   0                    ],  // Drehpunkt
    [tw,  -th                  ],  // P2 links oben (P2[3], statisch)
    [tw,  lerp(-th,  th,  t)  ],  // P1 rechts oben (= P1[3])
    [0,   lerp(0,  2*th,  t)  ],  // P1 links oben  (= P1[2])
  ]);

  function pts(points) {
    return points.map(([x, y]) => `${x} ${y}`).join(', ');
  }
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
            <polygon points={pts(p2)} fill="#995328" stroke-width="0"/>
            <polygon points={pts(p1)} fill="#e67d3c" stroke-width="0"/>
            <polygon points={pts(p3)} fill="#cc6230" stroke-width="0"/>
          </g>
          <g transform="rotate(120, 0, 0)">
            <polygon points={pts(p2)} fill="#0d3233" stroke-width="0"/>
            <polygon points={pts(p1)} fill="#1b6566" stroke-width="0"/>
            <polygon points={pts(p3)} fill="#144849" stroke-width="0"/>
          </g>
          <g transform="rotate(240, 0, 0)">
            <polygon points={pts(p2)} fill="#289799" stroke-width="0"/>
            <polygon points={pts(p1)} fill="#35c9cc" stroke-width="0"/>
            <polygon points={pts(p3)} fill="#2cb0b2" stroke-width="0"/>
          </g>
        </g>
      {/each}
    {/each}
  </svg>
</div>
<div class="sidebar-right">
  <Slider bind:value={t} label="Solid → Hollow" min={0} max={1} step={0.01}/>
</div>