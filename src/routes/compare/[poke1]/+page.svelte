<script>
	import { page } from '$app/stores';
	import { onDestroy, afterUpdate } from 'svelte';
	import { goto } from '$app/navigation';
	import Chart from 'chart.js/auto';
	import SearchBar from '../SearchBar.svelte';

	let pokemon1, pokemon2;
	let p1 = null, p2 = null;
	let error = null;
	let chart;
	let search = '';

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
</script>

<style>
	.container {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		min-height: 100vh;
		background: linear-gradient(to bottom, #e0f0ff, #ffffff);
		font-family: 'Segoe UI', sans-serif;
	}

	.versus-box {
		display: flex;
		justify-content: center;
		align-items: center;
		gap: 5rem;
		margin-top: 2rem;
	}

	.card {
		background: white;
		border-radius: 1rem;
		padding: 2rem;
		box-shadow: 0 0 20px rgba(0,0,0,0.1);
		width: 280px;
		text-align: center;
		transition: transform 0.3s ease;
	}

	.card:hover {
		transform: scale(1.03);
	}

	.card img {
		width: 120px;
		height: 120px;
		margin-bottom: 1rem;
	}

	.vs {
		font-size: 3rem;
		font-weight: bold;
		color: #333;
	}

	canvas {
		max-width: 600px;
		margin: 2rem 0;
	}

	h2 {
		margin-top: 2rem;
		font-size: 1.5rem;
		color: #333;
	}

	input {
		padding: 0.5rem;
		width: 100%;
		border-radius: 6px;
		border: 1px solid #ccc;
		margin-bottom: 1rem;
	}

	button {
		padding: 0.5rem 1rem;
		border-radius: 6px;
		border: none;
		background: #007bff;
		color: white;
		cursor: pointer;
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
					<SearchBar placeholder="Segundo Pokémon..." on:select={(e) => input2 = e.detail} />
					<button on:click={handleSearch}>Buscar</button>
				</div>
			{/if}
		</div>

		{#if p2}
			<canvas id="statsChart" width="400" height="400"></canvas>
			<h2>{getWinChance()}</h2>
		{/if}
	{:else}
		<p>Carregando Pokémon...</p>
	{/if}
</div>
