<script>
  import Slider from '$lib/components/Slider.svelte'

  const triangleWidth = 60;
  const triangleHeight = Math.tan((30 * Math.PI) / 180) * triangleWidth;

  // Use ceil so the grid fully covers the right and bottom edges
  const moduleCountX = Math.ceil(1000 / (triangleWidth * 4));
  // add one extra row to ensure the bottom edge is completely filled
  const moduleCountY = Math.ceil(1000 / (triangleHeight * 6)) + 1;

  let t = $state(0);

  function lerp(a, b, t) { return a + (b - a) * t; }

  const tw = triangleWidth;
  const th = triangleHeight;

  // Polygon1 (hell): animiert von Solid → Hollow
let p1 = $derived([
    [lerp(tw,  2*tw,  t),  lerp(3*th, 2*th,   t)],   // Punkt 1
    [0,                    4*th                 ],   // Punkt 2
    [0,                    lerp(0,    2*th,   t)],   // Punkt 3 ← end: 2*th (statt 5*th/3)
    [tw,                   lerp(-th,  th,     t)],   // Punkt 4 ← end: th  (statt 2*th/3)
  ]);

    // Polygon2 (dunkel): statisch, immer in Solid-Form (Parallelogramm)
  const p2 = [
    [2*tw,  -2*th ],  // Punkt 1
    [2*tw,   2*th ],  // Punkt 2
    [tw,     3*th ],  // Punkt 3
    [tw,    -th   ],  // Punkt 4
  ];


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
          </g>

          <g transform="rotate(120, 0, 0)">
            <polygon points={pts(p2)} fill="#0d3233" stroke-width="0"/>
            <polygon points={pts(p1)} fill="#1b6566" stroke-width="0"/>
          </g>

          <g transform="rotate(240, 0, 0)">
            <polygon points={pts(p2)} fill="#289799" stroke-width="0"/>
            <polygon points={pts(p1)} fill="#35c9cc" stroke-width="0"/>
          </g>

        </g>
      {/each}
    {/each}
  </svg>
</div>

<div class="sidebar-right">
  <Slider bind:value={t} label="Solid → Hollow" min={0} max={1} step={0.01}/>
</div>
