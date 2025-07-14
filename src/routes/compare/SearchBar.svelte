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
	width: 90%;
	padding: 0.6rem 1rem;
	border-radius: 8px;
	border: 1px solid #cbd5e1;
	font-size: 1rem;
	box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
	transition: border 0.2s ease;
}

.search-input:focus {
	outline: none;
	border-color: #3b82f6;
	box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.2);
}

.search-dropdown {
	position: absolute;
	top: 110%;
	width: 100%;
	background: white;
	border-radius: 0.75rem;
	box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
	overflow: hidden;
	z-index: 1000;
	padding: 0.3rem 0;
}

.search-dropdown li {
	padding: 0.75rem 1rem;
	cursor: pointer;
	transition: background 0.2s, color 0.2s;
	font-weight: 500;
	color: #1e293b;
}

.search-dropdown li:hover {
	background: #e0f2fe;
	color: #1d4ed8;
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
