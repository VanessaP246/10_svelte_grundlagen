<script>
    const elementWidth = 100;
    const elementHeight = 100;

    const elementLenngthX = Math.round(1000 / elementWidth);
    const elementLengthY = Math.round(1000 / elementHeight);

    function getColor (xi, yi) {
        // if ((xi + yi) % 2 === 0) return 'cyan';
        // else return 'green';

        if(xi %2 == 0 && yi %2 == 0) return 'cyan';
        else if(xi %2 == 0 && yi %2 == 1) return 'green';
        else if(xi %2 == 1 && yi %2 == 1) return 'cyan';
        else if(xi %2 == 1 && yi %2 == 0) return 'green';
    }
</script>

<div class="svg-container">
	<svg class="svg-canvas" viewBox="0 0 1000 1000">
		{#each Array(elementLengthY) as _, yi}
			{#each Array(elementLenngthX) as _, xi}
				<g transform="translate({xi * elementWidth},{yi * elementHeight})">
					<polygon points="0,{elementHeight / 2} {elementWidth / 2},0 {elementWidth},{elementHeight / 2} {elementWidth / 2},{elementHeight}" fill={getColor(xi, yi)}></polygon>
				</g>
			{/each}
		{/each}
	</svg>
</div>
