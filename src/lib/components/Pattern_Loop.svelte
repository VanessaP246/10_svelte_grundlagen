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

  // P1: verlängerte Lerps für Loop
  // t=0   → Solid-Original
  // t=0.5 → bisheriger t=1 (Hollow)
  // t=1   → Fläche 0: alle Punkte kollinear auf y = 4th − (th/tw)·x
  let p1 = $derived([
    [lerp(tw,  2*tw,  t), lerp(3*th, 2*th, t)],  // Punkt 1: bewegt sich weiter bis [3tw, th]
    [0,                   4*th               ],  // Punkt 2: statisch
    [0,                   lerp(0,   4*th, t) ],  // Punkt 3: jetzt bis 4·th
    [tw,                  lerp(-th, 3*th, t) ],  // Punkt 4: jetzt bis 3·th
  ]);

  // P2: statisch
  const p2 = [
    [2*tw, -2*th],
    [2*tw,  2*th],
    [tw,    3*th],
    [tw,   -th  ],
  ];

  // P3: Punkte direkt aus P1 referenziert → immer exakt bündig
  // t=0 → Nullfläche (unsichtbar)
  // t=1 → identische Punkte wie P1 bei t=0 → Loop geschlossen
  let p3 = $derived([
    [0,  0   ],  // Drehpunkt (fest)
    [tw, -th ],  // P2 links oben (fest)
    p1[3],       // P1 rechts oben — läuft mit
    p1[2],       // P1 links oben  — läuft mit
  ]);

  // Farb-Interpolation: P3 startet bei P2-Farbe, endet bei P1-Farbe
  function hexToRgb(hex) {
    return [
      parseInt(hex.slice(1, 3), 16),
      parseInt(hex.slice(3, 5), 16),
      parseInt(hex.slice(5, 7), 16),
    ];
  }
  function lerpColor(hex1, hex2, t) {
    const [r1, g1, b1] = hexToRgb(hex1);
    const [r2, g2, b2] = hexToRgb(hex2);
    return `rgb(${Math.round(lerp(r1,r2,t))},${Math.round(lerp(g1,g2,t))},${Math.round(lerp(b1,b2,t))})`;
  }

  let p3ColorA = $derived(lerpColor('#cc6230', '#e67d3c', t));
let p3ColorB = $derived(lerpColor('#144849', '#1b6566', t));
let p3ColorC = $derived(lerpColor('#2cb0b2', '#35c9cc', t));

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
            <polygon points={pts(p3)} fill={p3ColorA} stroke-width="0"/>
          </g>
          <g transform="rotate(120, 0, 0)">
            <polygon points={pts(p2)} fill="#0d3233" stroke-width="0"/>
            <polygon points={pts(p1)} fill="#1b6566" stroke-width="0"/>
            <polygon points={pts(p3)} fill={p3ColorB} stroke-width="0"/>
          </g>
          <g transform="rotate(240, 0, 0)">
            <polygon points={pts(p2)} fill="#289799" stroke-width="0"/>
            <polygon points={pts(p1)} fill="#35c9cc" stroke-width="0"/>
            <polygon points={pts(p3)} fill={p3ColorC} stroke-width="0"/>
          </g>
        </g>
      {/each}
    {/each}
  </svg>
</div>
<div class="sidebar-right">
  <Slider bind:value={t} label="Loop" min={0} max={1} step={0.01}/>
</div>