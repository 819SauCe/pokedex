<script>
	import { page } from "$app/stores";
	import { onDestroy } from "svelte";

	let pokemons = [];
	let loading = true;

	const typeColors = {
		grass: "#3ba55d",
		fire: "#ff6f61",
		water: "#3b9ee3",
		electric: "#f4d23c",
		poison: "#aa5da1",
		flying: "#89aae3",
		bug: "#94bc4a",
		normal: "#a8a77a",
		ground: "#e2bf65",
		fairy: "#ee99ac",
		fighting: "#c22e28",
		psychic: "#f95587",
		rock: "#b6a136",
		ghost: "#735797",
		ice: "#96d9d6",
		dragon: "#6f35fc",
		dark: "#705746",
		steel: "#b7b7ce",
	};

	function next_page(id) {
		window.location.href = `/pokePagination/${id + 1}`;
	}

	function prev_page(id) {
		window.location.href = `/pokePagination/${id - 1}`;
	}

	const unsubscribe = page.subscribe(async ($page) => {
		loading = true;
		pokemons = [];

		const id = parseInt($page.params.id) || 0;
		const pagination = id * 39;

		const res = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=40&offset=${pagination}`);
		if (!res.ok) {
			console.error(`Erro ao buscar lista: ${res.statusText}`);
			loading = false;
			return;
		}

		const data = await res.json();

		const detailed = await Promise.all(
			data.results.map(async (p) => {
				const r = await fetch(p.url);
				if (!r.ok) return null;
				return r.json();
			})
		);

		const filtered = detailed.filter(p => p !== null);

		const speciesDetails = await Promise.all(
			filtered.map(async (p) => {
				const r = await fetch(`https://pokeapi.co/api/v2/pokemon-species/${p.id}`);
				if (!r.ok) return { flavor_text_entries: [] };
				return r.json();
			})
		);

		pokemons = filtered.map((poke, i) => ({
			...poke,
			id,
			types: poke.types,
			sprites: poke.sprites,
			name: poke.name,
			des:
				speciesDetails[i].flavor_text_entries.find(
					(e) => e.language.name === "en",
				)?.flavor_text || "",
		}));

		loading = false;
	});

	onDestroy(() => unsubscribe());
</script>

{#if !loading}
	<div class="vitrine">
		{#each pokemons as pokemon}
			<div class="poke-card">
				<div class="img-card">
					<a href="/pokemon/{pokemon.name}">
						<img src={pokemon.sprites.front_default} alt={pokemon.name + " sprite"} loading="lazy"/>
					</a>
				</div>
				<div class="poke-info">
					<a href="/pokemon/{pokemon.name}">
						<p class="nome">{pokemon.name}</p>
					</a>
					<p>{pokemon.des}</p>
					<div class="type-container">
						{#each pokemon.types as t}
							<p id="poke-type" style="background: {typeColors[t.type.name] || '#999'}">
								{t.type.name}
							</p>
						{/each}
					</div>
				</div>
			</div>
		{/each}
	</div>

	{#if pokemons.length > 0}
		<div class="pagination">
			{#if pokemons[0].id > 0}
				<button on:click={() => prev_page(pokemons[0].id)}>&larr;</button>
			{/if}
			<button on:click={() => next_page(pokemons[0].id)}>&rarr;</button>
		</div>
	{/if}
{:else}
	<div class="loader"></div>
{/if}

<style>
	:global(body) {
		background: linear-gradient(#f2f9ff, #d6eaff);
		font-family: "Poppins", sans-serif;
		margin: 0;
	}

	.vitrine {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
		gap: 2rem;
		padding: 3rem;
	}

	.poke-card {
		background-color: #fff;
		border-radius: 16px;
		overflow: hidden;
		box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
		transition: all 0.3s ease;
		display: flex;
		flex-direction: column;
	}

	.poke-card:hover {
		transform: translateY(-6px);
		box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
	}

	.img-card {
		background: linear-gradient(to bottom, #fff3f3, #f9e0e0);
		padding: 1.5rem;
		display: flex;
		justify-content: center;
		border-bottom: 1px solid #eee;
	}

	.img-card img {
		width: 96px;
		height: 96px;
	}

	.poke-info {
		padding: 1rem;
		text-align: center;
	}

	.poke-info .nome {
		font-weight: 700;
		font-size: 1.2rem;
		text-transform: capitalize;
		margin: 0.5rem 0;
		color: #333;
	}

	.poke-info p {
		font-size: 0.875rem;
		color: #444;
	}

	.type-container {
		display: flex;
		justify-content: center;
		flex-wrap: wrap;
		gap: 0.5rem;
		margin-top: 0.8rem;
	}

	#poke-type {
		padding: 0.3rem 0.8rem;
		border-radius: 999px;
		color: #fff;
		font-size: 0.75rem;
		font-weight: 600;
		text-transform: capitalize;
		box-shadow: inset 0 0 3px rgba(0, 0, 0, 0.2);
	}

	.pagination {
		display: flex;
		justify-content: center;
		align-items: center;
		gap: 1rem;
		margin: 3rem 0 2rem;
	}

	.pagination button {
		background: #e5e7eb;
		color: #111827;
		border: none;
		padding: 10px 18px;
		border-radius: 9999px;
		font-weight: 600;
		font-size: 1rem;
		cursor: pointer;
		transition: all 0.3s ease;
		box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
	}

	.pagination button:hover {
		background: #3b4cca;
		color: white;
		transform: translateY(-2px);
	}

	.loader {
		width: 50px;
		padding: 8px;
		aspect-ratio: 1;
		border-radius: 50%;
		background: #25b09b;
		--_m:
			conic-gradient(#0000 10%,#000),
			linear-gradient(#000 0 0) content-box;
		-webkit-mask: var(--_m);
				mask: var(--_m);
		-webkit-mask-composite: source-out;
				mask-composite: subtract;
		animation: l3 1s infinite linear;
		margin: 100px auto;
	}
	@keyframes l3 {
		to { transform: rotate(1turn) }
	}
</style>
