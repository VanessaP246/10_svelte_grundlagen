<script>
	import { slide } from 'svelte/transition';
	import Header from '$lib/components/Header.svelte'
	import PatternNavigation from '$lib/components/PatternNavigation.svelte'
	

	// Pattern-Navigation
	import Pattern1 from '$lib/components/Pattern2.svelte'
	import Pattern2 from '$lib/components/Pattern3.svelte'
	import Pattern3 from '$lib/components/Pattern4.svelte'

	import Footer from '$lib/components/Footer.svelte'
	import chroma from 'chroma-js';

	// Array mit den Patterns als Objekte, mit 3 Properties: name, component, description
	let patterns = [
		{
			name: 'Pattern 1',
			component: Pattern1,
			description: 'Verschieben & Rotieren der Mittelachse'
		},
		{
			name: 'Pattern 2',
			component: Pattern2,
			description: 'Verschieben der Mittelpunkte'
		},
		{
			name: 'Pattern 3',
			component: Pattern3,
			description: 'Beides kombiniert'
		}
	];

  // Reative State Variable mit dem Index fürs Pattern im Array patterns
	let selectedPattern = $state(2);

  // Property component vom selektierten Pattern, in die reaktivere Variable SelectedPattern schreiben.
  // SelectedPattern Komponente wird unten mit <SelectedPattern /> geladen. Und gewechselt, wenn geklickt wird.
	let SelectedPattern = $derived(patterns[selectedPattern].component);
</script>

<div class="app-container">
	<Header />

	<main class="app-main">
		<div class="sidebar-left">

      <!-- Schleife durch die Patterns und Buttons mit Events erstellen, um die Patterns umzuschalten. -->
			{#each patterns as pattern, index}
				<button
					class="sidebar-left-item"
					class:selected={selectedPattern === index}
					onclick={() => (selectedPattern = index)}
					>{pattern.name}
					{#if selectedPattern === index}
						<div transition:slide class="sidebar-left-description">{pattern.description}</div>
					{/if}
				</button>
			{/each}
		</div>

    <!-- SelectedPattern rendern, wird automatisch mit state / derived geändert. -->
		<SelectedPattern />
	</main>

	<Footer />
</div>

<!-- Style für die Sidebar mit items. -->
<style>
	.sidebar-left {
		display: flex;
		width: 350px;
		flex-direction: column;
		background: #1c1c1c;
		border-right: 1px solid #353535;
		/* padding: 20px; */
		padding-top: 30px;
		color: #fff;
	}
	.sidebar-left-item {
		background: none;
		color: #888;
		border: none;
		/* margin-bottom: 1rem; */
		cursor: pointer;
		text-align: left;
		font-size: 0.9rem;
		width: 100%;
		padding: 10px 20px 10px 20px;
		border-top: 1px solid #333;
		letter-spacing: 0.02rem;
	}
	.sidebar-left-item:last-child {
		border-bottom: 1px solid #333;
	}
	.sidebar-left-item:hover {
		color: #aaa;
	}
	.sidebar-left-item.selected {
		color: #ddd;
	}
	.sidebar-left-description {
		font-size: 0.7rem;
		color: #aaa;
		margin-top: 0.5rem;
		line-height: 1.4;
	}
</style>