<main>
    <h1>Wellcome to my pokedex</h1>
    <hr>

    <div class="action-container">
        <div class="action">
            <div class="action-choice">
            <a href="/pokePagination/0">
                <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/25.png" alt="Ver todos os pokemons" />
                <p class="desc">Explore todos os Pokémons e descubra suas características únicas.</p>
                <p class="tittle-choice">See all Pokémons</p>
            </a>
            </div>

            <div class="action-choice">
                <img src="https://png.pngtree.com/png-vector/20221125/ourmid/pngtree-no-image-available-icon-flatvector-illustration-picture-coming-creative-vector-png-image_40968940.jpg" alt="Ver todos os pokemons">
                <p>Comparar pokemons</p>
            </div>

            <div class="action-choice">
                <img src="https://png.pngtree.com/png-vector/20221125/ourmid/pngtree-no-image-available-icon-flatvector-illustration-picture-coming-creative-vector-png-image_40968940.jpg" alt="Ver todos os pokemons">
                <p>See all pokemons</p>
            </div>

            <div class="action-choice">
                <img src="https://png.pngtree.com/png-vector/20221125/ourmid/pngtree-no-image-available-icon-flatvector-illustration-picture-coming-creative-vector-png-image_40968940.jpg" alt="Ver todos os pokemons">
                <p>See all pokemons</p>
            </div>
        </div>
    </div>
</main>

<style>
  main {
    padding: 2rem 4rem;
    font-family: 'Segoe UI', sans-serif;
    text-align: center;
  }
  h1 {
    font-size: 2.5rem;
    margin-bottom: 0.5rem;
  }
  .action {
    display: flex;
    flex-wrap: wrap;
    gap: 2rem;
    justify-content: center;
  }
  .action-choice {
    width: 16rem;
    background: #f9f9f9;
    border-radius: 1rem;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    padding: 1rem;
    transition: transform 0.2s ease-in-out;
  }
  .action-choice:hover {
    transform: scale(1.05);
  }
  .action-choice img {
    width: 8rem;
    height: 8rem;
  }
  .desc {
    font-size: 0.9rem;
    margin: 1rem 0;
    color: #333;
  }
  .tittle-choice {
    font-weight: bold;
    color: #0070f3;
  }
</style>