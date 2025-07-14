<script>
    import { onMount } from "svelte";
    let query = "";
    let allNames = [];
    let matches = [];
    let show_search = false;

    function search() {
        show_search = !show_search;
    }

    onMount(async () => {
        const res = await fetch(
            "https://pokeapi.co/api/v2/pokemon?limit=10000",
        );
        const data = await res.json();
        allNames = data.results.map((p) => p.name);
        const close = () => (show_search = false);
        window.addEventListener("click", close);
        return () => window.removeEventListener("click", close);
    });

    function updateMatches() {
        matches = allNames
            .filter((name) => name.includes(query.toLowerCase()))
            .slice(0, 5);
    }
</script>

<header>
    <div class="logo">
        <a href="/">
            <img class="logo-img" src="/Pokédex_logo.png" alt="logo" />
        </a>
    </div>

    <div style="position:relative;display: flex; flex-direction:column;">
        <div class="searchBar">
            <input
                type="text"
                on:click|stopPropagation={() => (show_search = true)}
                bind:value={query}
                on:input={updateMatches}
                placeholder="Search..."
            />
            <button aria-label="true">
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="24"
                    height="24"
                    fill="none"
                    stroke="black"
                    stroke-width="2"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    class="feather feather-search"
                >
                    <circle cx="11" cy="11" r="8" />
                    <line x1="21" y1="21" x2="16.65" y2="16.65" />
                </svg>
            </button>
        </div>
        {#if show_search}
            <ul class="searchs">
                {#each matches as name}
                    <a href="/pokemon/{name}">
                        <li>{name}</li>
                    </a>
                {/each}
            </ul>
        {/if}
    </div>

    <div class="nav">
        <a href="https://github.com/819SauCe" class="github">
            <img src="/github-icon-1-logo-svgrepo-com.svg" alt="github" />
        </a>
        <a
            href="https://www.instagram.com/viitojooj?igsh=bTAyOWtlc2ZvMHBn&utm_source=qr"
            class="instagram"
        >
            <img src="/instagram-logo-svgrepo-com.svg" alt="github" />
        </a>
        <a href="https://google.com" class="ladding-page">
            <img src="/page-svgrepo-com.svg" alt="ladding-page" />
        </a>
    </div>
</header>

<style>
	header {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 0 2rem;
		height: 5rem;
		background: linear-gradient(90deg, #feffbe 0%, #ffb0b0 100%);
		box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
	}

	.logo-img {
		width: 9rem;
		height: auto;
		filter: drop-shadow(0 2px 3px rgba(0, 0, 0, 0.3));
	}

	.nav {
		display: flex;
		gap: 1rem;
	}

	.nav img {
		width: 2.4rem;
		height: 2.4rem;
		transition: transform 0.2s ease;
	}

	.nav img:hover {
		transform: scale(1.15);
	}

	.searchBar {
		display: flex;
		align-items: center;
		gap: 10px;
		background: #fff;
		border-radius: 999px;
		padding: 10px 20px;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
		width: 25rem;
		position: relative;
	}

	.searchBar input {
		border: none;
		background: transparent;
		outline: none;
		flex: 1;
		font-size: 16px;
		color: #111827;
	}

	.searchBar button {
		background: #3b4cca;
		border: none;
		padding: 6px;
		border-radius: 50%;
		cursor: pointer;
		display: flex;
		align-items: center;
		justify-content: center;
		transition: background 0.2s;
	}

	.searchBar button:hover {
		background: #2a36a6;
	}

	.searchBar svg {
		stroke: white;
	}

	.searchs {
		position: absolute;
		top: 4.5rem;
		left: 1rem;
		background-color: white;
		width: 25rem;
		z-index: 1000;
		box-shadow: 0 6px 16px rgba(0, 0, 0, 0.15);
		border-radius: 0.5rem;
		overflow: hidden;
	}

	.searchs a {
		text-decoration: none;
		color: #333;
		display: block;
		transition: background 0.2s;
	}

	.searchs li {
		padding: 1rem;
		border-bottom: 1px solid #eee;
	}

	.searchs a:hover {
		background: #f0f4ff;
		color: #1d4ed8;
	}

	@media (max-width: 600px) {
		header {
			flex-direction: column;
			padding: 1rem;
			height: auto;
			gap: 0.5rem;
		}

		.logo-img {
			width: 6rem;
		}

		.searchBar {
			width: 100%;
			max-width: 100%;
			padding: 8px 14px;
		}

		.searchs {
			width: 100%;
			left: 0;
			margin-left: 0;
		}

		.nav {
			gap: 0.5rem;
		}

		.nav img {
			width: 2rem;
			height: 2rem;
		}
	}
</style>
