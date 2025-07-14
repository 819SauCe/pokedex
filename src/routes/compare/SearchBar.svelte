<script>
	import { createEventDispatcher, onMount } from 'svelte';

	export let placeholder = "Search...";
	let query = '';
	let matches = [];
	let show_search = false;
	let allNames = [];
	const dispatch = createEventDispatcher();

	onMount(async () => {
		const res = await fetch("https://pokeapi.co/api/v2/pokemon?limit=10000");
		const data = await res.json();
		allNames = data.results.map((p) => p.name);
	});

	function updateMatches() {
		matches = allNames
			.filter(name => name.includes(query.toLowerCase()))
			.slice(0, 5);
	}

	function select(name) {
		query = name;
		show_search = false;
		dispatch('select', name);
	}
</script>

<style>
	.search-bar {
		position: relative;
		width: 100%;
	}

	.search-input {
		width: 100%;
		padding: 0.5rem;
		border-radius: 6px;
		border: 1px solid #ccc;
	}

	.search-dropdown {
		position: absolute;
		top: 110%;
		width: 100%;
		background: white;
		box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
		z-index: 1000;
	}

	.search-dropdown li {
		padding: 0.5rem 1rem;
		cursor: pointer;
	}

	.search-dropdown li:hover {
		background: #eee;
	}
</style>

<div class="search-bar">
	<input
		class="search-input"
		type="text"
		bind:value={query}
		on:input={() => { updateMatches(); show_search = true }}
		placeholder={placeholder}
	/>

	{#if show_search && matches.length}
		<ul class="search-dropdown">
			{#each matches as name}
				<li on:click={() => select(name)}>{name}</li>
			{/each}
		</ul>
	{/if}
</div>
