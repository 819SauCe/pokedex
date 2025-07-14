<script>
	import { page } from '$app/stores';
	import { onDestroy, afterUpdate } from 'svelte';
	import { goto } from '$app/navigation';
	import Chart from 'chart.js/auto';
	import SearchBar from './SearchBar.svelte';

	let pokemon1, pokemon2;
	let p1 = null, p2 = null;
	let error = null;
	let chart;
	let search = '';
	let input1 = '', input2 = '';

	const unsubscribe = page.subscribe(async ($page) => {
		pokemon1 = $page.params.poke1;
		pokemon2 = $page.params.poke2;

		try {
			if (pokemon1) {
				const res1 = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemon1}`);
				if (!res1.ok) throw new Error('P1 Not Found');
				p1 = await res1.json();
			}

			if (pokemon2) {
				const res2 = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemon2}`);
				if (!res2.ok) throw new Error('P2 Not Found');
				p2 = await res2.json();
			}
		} catch (err) {
			error = err.message;
		}
	});

	onDestroy(() => unsubscribe());

	afterUpdate(() => {
		if (p1 && p2) drawChart();
	});

	function drawChart() {
		const ctx = document.getElementById('statsChart')?.getContext('2d');
		if (!ctx) return;
		if (chart) chart.destroy();
		chart = new Chart(ctx, {
			type: 'radar',
			data: {
				labels: p1.stats.map(s => s.stat.name),
				datasets: [
					{
						label: p1.name,
						data: p1.stats.map(s => s.base_stat),
						fill: true
					},
					{
						label: p2.name,
						data: p2.stats.map(s => s.base_stat),
						fill: true
					}
				]
			},
			options: {
				scales: {
					r: {
						beginAtZero: true
					}
				}
			}
		});
	}

	function getWinChance() {
		if (!p1 || !p2) return '';
		const total1 = p1.stats.reduce((a, s) => a + s.base_stat, 0);
		const total2 = p2.stats.reduce((a, s) => a + s.base_stat, 0);
		const perc = Math.round((total1 / (total1 + total2)) * 100);
		return `${p1.name.toUpperCase()} tem ${perc}% de chance de ganhar com base nos status`;
	}

	function handleSearch() {
		if (search.trim()) {
			goto(`/compare/${pokemon1}/${search.toLowerCase()}`);
		}
	}

	function handleDualSearch() {
	if (input1.trim() && input2.trim()) {
		goto(`/compare/${input1.toLowerCase()}/${input2.toLowerCase()}`);
	} else if (input1.trim()) {
		goto(`/compare/${input1.toLowerCase()}`);
	} else if (input2.trim()) {
		goto(`/compare/${input2.toLowerCase()}`);
	}
}

</script>

<style>
	.container {
	display: flex;
	flex-direction: column;
	align-items: center;
	padding: 2rem;
	min-height: 100vh;
	background: linear-gradient(to bottom, #e0f0ff, #ffffff);
	font-family: 'Segoe UI', sans-serif;
	text-align: center;
	box-sizing: border-box;
}

.versus-box {
	display: flex;
	flex-wrap: wrap;
	justify-content: center;
	gap: 2rem;
	margin-top: 2rem;
	width: 100%;
}

.card {
	background: #fff;
	border-radius: 1rem;
	padding: 1.5rem;
	box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
	width: 100%;
	max-width: 300px;
	transition: transform 0.3s ease;
}

.card:hover {
	transform: translateY(-5px);
}

.card img {
	width: 120px;
	height: 120px;
	object-fit: contain;
	margin-bottom: 1rem;
	filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.1));
}

.card h3 {
	font-size: 1.25rem;
	color: #1e3a8a;
	margin-bottom: 0.5rem;
}

.card p {
	margin: 0.25rem 0;
	font-weight: 500;
	color: #1f2937;
}

.vs {
	font-size: 2rem;
	font-weight: bold;
	color: #2563eb;
	align-self: center;
}

canvas {
	max-width: 100%;
	margin: 2rem 0;
	background: #fff;
	border-radius: 1rem;
	padding: 1rem;
	box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
}

h2 {
	font-size: 1.2rem;
	color: #1e40af;
	margin-top: 1rem;
}

input {
	padding: 0.6rem;
	width: 100%;
	border-radius: 0.5rem;
	border: 1px solid #cbd5e1;
	margin-bottom: 1rem;
	font-size: 1rem;
	box-sizing: border-box;
}

button {
	padding: 0.6rem 1.2rem;
	border-radius: 0.5rem;
	border: none;
	background: #2563eb;
	color: white;
	font-weight: 600;
	cursor: pointer;
	transition: background 0.2s;
}

button:hover {
	background: #1e3a8a;
}

@media (max-width: 640px) {
	.vs {
		display: none;
	}
}

</style>

<div class="container">
	{#if p1}
		<div class="versus-box">
			<div class="card">
				<img src={p1.sprites.other["official-artwork"].front_default} alt={p1.name} />
				<h3>{p1.name.toUpperCase()}</h3>
				{#each p1.stats as stat}
					<p>{stat.stat.name}: {stat.base_stat}</p>
				{/each}
			</div>

			<div class="vs">VS</div>

			{#if p2}
				<div class="card">
					<img src={p2.sprites.other["official-artwork"].front_default} alt={p2.name} />
					<h3>{p2.name.toUpperCase()}</h3>
					{#each p2.stats as stat}
						<p>{stat.stat.name}: {stat.base_stat}</p>
					{/each}
				</div>
			{:else}
				<div class="card">
					<h3>Buscar Pokémon</h3>
					<SearchBar placeholder="Primeiro Pokémon..." on:select={(e) => input1 = e.detail} />
					<button on:click={handleSearch}>Buscar</button>
				</div>
			{/if}
		</div>

		{#if p2}
			<canvas id="statsChart" width="400" height="400"></canvas>
			<h2>{getWinChance()}</h2>
		{/if}
	{:else}
		<div class="versus-box">
			<div class="card">
				<h3>Primeiro Pokémon</h3>
				<SearchBar placeholder="Primeiro Pokémon..." on:select={(e) => input1 = e.detail} />
			</div>
			<div class="vs">VS</div>
			<div class="card">
				<h3>Segundo Pokémon</h3>
				<SearchBar placeholder="Segundo Pokémon..." on:select={(e) => input2 = e.detail} />
			</div>
		</div>
		<button on:click={handleDualSearch} style="margin-top: 1rem;">Comparar</button>
	{/if}
</div>
