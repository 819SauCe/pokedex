<script>
  import { page } from '$app/stores';
  import { onDestroy } from 'svelte';

  let pokemon = null;
  let species = null;
  let error = null;
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

  $: mainType = pokemon?.types?.[0]?.type?.name;
  $: pageColor = typeColors[mainType] || '#e3350d';


  const unsubscribe = page.subscribe(async ($page) => {
    const name = $page.params.pokename;
    if (!name) return;

    try {
      const res = await fetch(`https://pokeapi.co/api/v2/pokemon/${name}`);
      if (!res.ok) throw new Error('Not Found');
      pokemon = await res.json();

      const speciesRes = await fetch(`https://pokeapi.co/api/v2/pokemon-species/${name}`);
      if (!speciesRes.ok) throw new Error('Species Not Found');
      species = await speciesRes.json();
    } catch (err) {
      error = err.message;
    }
  });

  onDestroy(() => unsubscribe());

  function getFlavorText(entries) {
    const entry = entries.find(e => e.language.name === 'en');
    return entry ? entry.flavor_text.replace(/\n|\f/g, ' ') : 'No description available.';
  }
</script>

{#if error}
  <div class="error"><h2>Erro: {error}</h2></div>
{:else if pokemon && species}
  <div class="container" style="--primary: {pageColor}; --accent: {pageColor};">

    <h1>{pokemon.name}</h1>
    <div class="sprites">
      <img src={pokemon.sprites.front_default} alt="front" />
      <img src={pokemon.sprites.back_default} alt="back" />
      <img src={pokemon.sprites.front_shiny} alt="shiny front" />
      <img src={pokemon.sprites.back_shiny} alt="shiny back" />
    </div>

    <div class="info">
      <section class="description">
        <p><strong>Descrição:</strong> {getFlavorText(species.flavor_text_entries)}</p>
        <p><strong>Habitat:</strong> {species.habitat ? species.habitat.name : 'Desconhecido'}</p>
      </section>

      <section>
        <p><strong>Altura:</strong> {pokemon.height / 10} m</p>
        <p><strong>Peso:</strong> {pokemon.weight / 10} kg</p>
        <p><strong>Experiência Base:</strong> {pokemon.base_experience}</p>
      </section>

      <section>
        <strong>Tipos:</strong>
        <div class="tags">{#each pokemon.types as t}<span>{t.type.name}</span>{/each}</div>
      </section>

      <section>
        <strong>Habilidades:</strong>
        <div class="tags">{#each pokemon.abilities as a}<span>{a.ability.name}</span>{/each}</div>
      </section>

      <section>
        <strong>Stats:</strong>
        <ul>{#each pokemon.stats as s}<li>{s.stat.name}: {s.base_stat}</li>{/each}</ul>
      </section>

      <section class="moves">
        <strong>Movimentos:</strong>
        <ul>{#each pokemon.moves as m}<li>{m.move.name}</li>{/each}</ul>
      </section>
    </div>
  </div>
{:else}
  <div class="loading-spinner"></div>
{/if}

<style>
:root {
  --primary: #e3350d;
  --secondary: #f5f5f5;
  --text: #1d1d1d;
  --bg: #ffffff;
  --accent: #ffcb05;
  --border: #adadad;
  --radius: 1rem;
  --font: 'Pokemon Solid', sans-serif;
}

@font-face {
  font-family: 'Pokemon Solid';
  src: url('https://fonts.cdnfonts.com/s/16163/Pokemon%20Solid.ttf') format('truetype');
}

:global(body, html) {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
  background: var(--secondary);
  font-family: var(--font), sans-serif;
  color: var(--text);
  overflow-x: hidden;
}

.container {
  max-width: 1200px;
  margin: auto;
  padding: 2rem;
  box-sizing: border-box;
}

h1 {
  text-transform: capitalize;
  text-align: center;
  color: var(--primary);
  font-size: 4rem;
  letter-spacing: 2px;
}

.sprites {
  display: flex;
  justify-content: center;
  gap: 2rem;
  flex-wrap: wrap;
  margin-bottom: 2rem;
}

.sprites img {
  width: 180px;
  height: auto;
  image-rendering: pixelated;
  background: #fff;
  border: 4px solid var(--accent);
  border-radius: var(--radius);
  padding: 1rem;
}

.info {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 2rem;
}

section {
  background: var(--bg);
  padding: 1rem 1.5rem;
  border-radius: var(--radius);
  box-shadow: 0 4px 10px rgba(0,0,0,0.1);
  border-left: 5px solid var(--border);
  font-size: 1rem;
}

.tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.tags span {
  background: var(--accent);
  color: white;
  padding: 0.4rem 0.8rem;
  border-radius: 999px;
  font-weight: bold;
  text-transform: capitalize;
}

.moves ul {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 0.5rem;
  max-height: 350px;
  overflow-y: auto;
}

.moves li {
  background: var(--bg);
  padding: 0.4rem 0.6rem;
  border: 2px solid var(--border);
  border-radius: var(--radius);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

li {
  list-style: none;
}

.error, .loading {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  font-size: 2rem;
  color: var(--primary);
}

.loading-spinner {
  width: 64px;
  height: 64px;
  border: 6px solid #ccc;
  border-top-color: var(--primary);
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin: 120px auto;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}


</style>
