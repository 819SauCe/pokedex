<main>
  <h1>Bem-vindo à Pokédex</h1>
  <p class="subtitle">Explore, compare e descubra o mundo Pokémon</p>
  <hr />

  <div class="action-container">
    <div class="action">
      <a href="/pokePagination/0" class="action-choice">
        <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/25.png" alt="Ver todos os pokemons" />
        <p class="desc">Explore todos os Pokémons e descubra suas características únicas.</p>
        <p class="title-choice">Ver todos os Pokémons</p>
      </a>

      <a href="/compare" class="action-choice">
        <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/6.png" alt="Comparar Pokémons" />
        <p class="desc">Compare dois Pokémons e veja quem se destaca em cada atributo.</p>
        <p class="title-choice">Comparar Pokémons</p>
      </a>
    </div>
  </div>
</main>

<style>
  main {
    padding: 4rem 2rem;
    font-family: 'Segoe UI', sans-serif;
    text-align: center;
    background: linear-gradient(to bottom, #e0f0ff, #ffffff);
    min-height: 100vh;
  }

  h1 {
    font-size: 3rem;
    margin-bottom: 0.2rem;
    color: #3b4cca;
  }

  .subtitle {
    font-size: 1.2rem;
    color: #555;
    margin-bottom: 2rem;
  }

  hr {
    width: 60px;
    border: 2px solid #3b4cca;
    margin: 1rem auto 2rem auto;
    border-radius: 2px;
  }

  .action {
    display: flex;
    flex-wrap: wrap;
    gap: 2rem;
    justify-content: center;
  }

  .action-choice {
    width: 18rem;
    background: white;
    border-radius: 1.25rem;
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.1);
    padding: 1.5rem;
    text-decoration: none;
    transition: transform 0.25s ease;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .action-choice:hover {
    transform: translateY(-6px);
  }

  .action-choice img {
    width: 100px;
    height: 100px;
    margin-bottom: 1rem;
  }

  .desc {
    font-size: 0.95rem;
    color: #444;
    margin-bottom: 0.75rem;
  }

  .title-choice {
    font-weight: bold;
    font-size: 1.1rem;
    color: #3b4cca;
  }
</style>
