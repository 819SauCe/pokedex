<script>
	import { page } from '$app/stores';
	import { onDestroy, afterUpdate } from 'svelte';
	import Chart from 'chart.js/auto';

	let pokemon1, pokemon2;
	let p1 = null, p2 = null;
	let error = null;
	let chart;

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
</script>


<style>
.container {
	background: linear-gradient(to bottom right, #d0ebff, #ffffff);
	font-family: 'Segoe UI', sans-serif;
	padding: 2rem;
	text-align: center;
}

.versus-box {
	display: flex;
	flex-wrap: wrap;
	justify-content: center;
	align-items: flex-start;
	gap: 3rem;
	margin-top: 2rem;
}

.card {
	background: #f8fbff;
	border-radius: 1.25rem;
	padding: 2rem;
	box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
	width: 300px;
	transition: all 0.3s ease;
	border: 2px solid #3b82f6;
}

.card:hover {
	transform: translateY(-6px);
	box-shadow: 0 12px 30px rgba(0, 0, 0, 0.15);
}

.card img {
	width: 140px;
	height: 140px;
	margin-bottom: 1rem;
	filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.2));
}

.card h3 {
	color: #1e3a8a;
	font-size: 1.5rem;
	margin-bottom: 1rem;
}

.card p {
	margin: 0.3rem 0;
	font-weight: 500;
	color: #111827;
}

.vs {
	font-size: 3rem;
	font-weight: 800;
	color: #2563eb;
	margin-top: 3rem;
}

canvas {
	max-width: 650px;
	margin: 3rem auto 1rem;
	border: 1px solid #dbeafe;
	border-radius: 1rem;
	background: #ffffff;
	padding: 1rem;
}

h2 {
	margin-top: 2rem;
	font-size: 1.4rem;
	color: #1e40af;
}

</style>

<div class="container">
	{#if p1 && p2}
		<div class="versus-box">
			<div class="card">
				<img src={p1.sprites.other["official-artwork"].front_default} alt={p1.name} />
				<h3>{p1.name.toUpperCase()}</h3>
				{#each p1.stats as stat}
					<p>{stat.stat.name}: {stat.base_stat}</p>
				{/each}
			</div>
			<div class="vs">VS</div>
			<div class="card">
				<img src={p2.sprites.other["official-artwork"].front_default} alt={p2.name} />
				<h3>{p2.name.toUpperCase()}</h3>
				{#each p2.stats as stat}
					<p>{stat.stat.name}: {stat.base_stat}</p>
				{/each}
			</div>
		</div>

		<canvas id="statsChart" width="400" height="400"></canvas>
		<h2>{getWinChance()}</h2>
	{:else}
		<p>Carregando Pokémon...</p>
	{/if}
</div>
