<header>
    <div class="logo">
        <a href="/">
            <img class="logo-img" src="/Pokédex_logo.png" alt="logo" />
        </a>
    </div>

    <div class="searchBar">
        <input type="text" placeholder="Search..." />
        <button aria-label="true">
            <svg xmlns="http://www.w3.org/2000/svg"
                    width="24"
                    height="24"
                    fill="none"
                    stroke="black"
                    stroke-width="2"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    class="feather feather-search">
                <circle cx="11" cy="11" r="8" />
                <line x1="21" y1="21" x2="16.65" y2="16.65" />
            </svg>
        </button>
    </div>

    <div class="nav">
        <a href="https://github.com/819SauCe" class="github">
            <img src="/github-icon-1-logo-svgrepo-com.svg" alt="github" />
        </a>
        <a href="https://www.instagram.com/viitojooj?igsh=bTAyOWtlc2ZvMHBn&utm_source=qr" class="instagram">
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
        justify-content: space-around;
        width: 100%;
        height: 5rem;
        align-items: center;
        background-color: #bf3c37;
    }
    .logo-img {
        width: 10rem;
        height: auto;
    }
    .nav {
        display: flex;
        gap: 1rem;
    }
    .nav img {
        width: 2.5rem;
        height: 2.5rem;
    }
    .searchBar {
        display: flex;
        align-items: center;
        gap: 8px;
        background: #f3f4f6;
        border-radius: 9999px;
        padding: 8px 16px;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
        width: 25rem;
        transition: box-shadow 0.3s ease;
    }
    .searchBar:hover {
        box-shadow: 0 6px 14px rgba(0, 0, 0, 0.1);
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
        background: #2563eb;
        border: none;
        padding: 6px;
        border-radius: 50%;
        cursor: pointer;
        transition: background 0.3s ease;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .searchBar button:hover {
        background: #1e40af;
    }
    .searchBar svg {
        stroke: white;
    }
</style>
