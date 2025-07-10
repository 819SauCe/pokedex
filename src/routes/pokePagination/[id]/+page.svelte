<script>
  import { onMount } from 'svelte';
  import { page } from '$app/stores';

    let id;
    let pokemons = [];
    let pagination = 0;
    $: id = parseInt($page.params.id) || 0;
    $: pagination = id * 39;
    let ant_setas = "<--"
    let next_setas = "-->"

    function next_page() {
        window.location.href = `/pokePagination/${id + 1}`;
    }

  onMount(async () => {
    const res = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=40&offset=${pagination}`);
    const data = await res.json();
    const detailed = await Promise.all(data.results.map(p => fetch(p.url).then(r => r.json())));
    const most_detailed = await Promise.all(detailed.map(p => fetch(p.forms[0].url).then(r => r.json())));
    const speciesDetails = await Promise.all(detailed.map(p => fetch(`https://pokeapi.co/api/v2/pokemon-species/${p.id}`).then(r => r.json())));
    pokemons = detailed.map((poke, i) => ({ ...most_detailed[i], types: poke.types, sprites: poke.sprites, name: poke.name, des: speciesDetails[i].flavor_text_entries.find(e => e.language.name === 'pt' || 'en')?.flavor_text }));
    });

</script>

<div class="vitrine">
    {#each pokemons as pokemon}
        <div class="poke-card">
            <div class="img-card">
                <img src={pokemon.sprites.front_default} alt={pokemon.name}>
            </div>
            <div class="poke-info">
                <p class="nome">{pokemon.name}</p>
                <p>{pokemon.des}</p>
                <div class="type-container">
                    {#each pokemon.types as t}
                    {#if t.type.name === "water"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #6338ff, #3a5eff);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "grass"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #4ecf71, #2da85c);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "fire"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #ff5938, #ff9262);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "electric"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #cfd13b, #ffeb3b);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "poison"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #a10091, #d940c7);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "bug"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #53a100, #7be04a);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "flying"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #6254b8, #88e0ff);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "rock"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #e08f2b, #f9b14a);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "ground"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #ffc06e, #d69b56);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "normal"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #adadad, #d4d4d4);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "fighting"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #ff0000, #ff6767);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "psychic"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #910077, #d92bd9);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "fairy"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #ff52df, #ffa9f7);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "dragon"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #690055, #a743ff);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "ghost"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #753e6b, #af6abf);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "ice"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #66ddee, #a0faff);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "steel"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #7a8d8c, #b5c7c6);">{t.type.name}</p>
                    {/if}
                    {#if t.type.name === "dark"}
                        <p id="poke-type" style="background: linear-gradient(135deg, #2b2b2b, #575757);">{t.type.name}</p>
                    {/if}
                    {/each}
                </div>
            </div>
        </div>
    {/each}

    
</div>
<div class="pagination">
    {#if id > 0}
        <button class="ant-page">{ant_setas}</button>
    {/if}
        <button class="next-page" on:click={next_page()}>{next_setas}</button>
</div>

<style>
    .vitrine {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        margin-top: 3rem;
        padding: 0 5rem 0 5rem;
        gap: 2rem;
    }
    .poke-card {
        position: relative;
        width: 15rem;
        height: 17rem;
        border-radius: 15px;
        border: 1px solid black;
        padding: 1rem;
        background-color: #eb4034;
    }
    .img-card {
        display: flex;
        width: 100%;
        align-items: center;
        justify-content: center;
        border: 1px solid black;
        background-color: #ad0c006c;
    }
    .poke-info .nome {
        padding: 0%;
        margin: 0%;
    }
    .type-container {
        display: flex;
        gap: 0.2rem;
        position: absolute;
        bottom: 0px;
    }
    #poke-type {
        display: flex;
        color: white;
        width: 4rem;
        height: 1.5rem;
        border-radius: 15px;
        align-items: center;
        justify-content: center;
        border: 1px solid black;
        margin-top: 0.1rem;
    }
    .pagination {
  display: flex;
  gap: 12px;
  justify-content: center;
  align-items: center;
  margin: 20px 0;
}

.pagination button {
  background: #e5e7eb;
  color: #111827;
  border: none;
  padding: 10px 14px;
  border-radius: 9999px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 2px 5px rgba(0,0,0,0.05);
}

.pagination button:hover {
  background: #2563eb;
  color: white;
  transform: translateY(-1px);
}

</style>