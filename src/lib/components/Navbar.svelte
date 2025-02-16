<script>
  export let isDarkMode = false;
  export let toggleDarkMode;
  export let isHomePage = false;
</script>

<nav>
  <div class="nav-content">
    <div class="nav-links left">
      <a href="/forum" class="nav-button">
        <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <path d="M21 11.5a8.38 8.38 0 0 1-.9 3.8 8.5 8.5 0 0 1-7.6 4.7 8.38 8.38 0 0 1-3.8-.9L3 21l1.9-5.7a8.38 8.38 0 0 1-.9-3.8 8.5 8.5 0 0 1 4.7-7.6 8.38 8.38 0 0 1 3.8-.9h.5a8.48 8.48 0 0 1 8 8v.5z"></path>
        </svg>
        <span>Forum</span>
      </a>
      <a href="/reader" class="nav-button">
        <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <path d="M4 19.5A2.5 2.5 0 0 1 6.5 17H20"></path>
          <path d="M6.5 2H20v20H6.5A2.5 2.5 0 0 1 4 19.5v-15A2.5 2.5 0 0 1 6.5 2z"></path>
        </svg>
        <span>Reader</span>
      </a>
      <a href="/writer?dark={isDarkMode}" class="nav-button">
        <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <path d="M12 19l7-7 3 3-7 7-3-3z"></path>
          <path d="M18 13l-1.5-7.5L2 2l3.5 14.5L13 18l5-5z"></path>
          <path d="M2 2l7.586 7.586"></path>
          <circle cx="11" cy="11" r="2"></circle>
        </svg>
        <span>Writer</span>
      </a>
    </div>
    <div class="nav-links right">
      {#if isHomePage}
        <button on:click={() => window.scrollTo({ top: 0, behavior: 'smooth' })}>
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
            <polyline points="9 22 9 12 15 12 15 22"></polyline>
          </svg>
          <span>Home</span>
        </button>
        <button on:click={() => document.getElementById('cards')?.scrollIntoView({ behavior: 'smooth' })}>
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <rect x="3" y="3" width="7" height="7"></rect>
            <rect x="14" y="3" width="7" height="7"></rect>
            <rect x="14" y="14" width="7" height="7"></rect>
            <rect x="3" y="14" width="7" height="7"></rect>
          </svg>
          <span>Lits</span>
        </button>
      {:else}
        <a href="/" class="nav-button">
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
            <polyline points="9 22 9 12 15 12 15 22"></polyline>
          </svg>
          <span>Home</span>
        </a>
      {/if}
      <button 
        class="dark-mode-toggle"
        class:dark-mode={isDarkMode}
        on:click={toggleDarkMode}
        title="Toggle Dark Mode"
      >
        {#if isDarkMode}
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="12" cy="12" r="5"></circle>
            <line x1="12" y1="1" x2="12" y2="3"></line>
            <line x1="12" y1="21" x2="12" y2="23"></line>
            <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
            <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
            <line x1="1" y1="12" x2="3" y2="12"></line>
            <line x1="21" y1="12" x2="23" y2="12"></line>
            <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
            <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
          </svg>
        {:else}
          <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path>
          </svg>
        {/if}
      </button>
    </div>
  </div>
</nav>

<style>
  nav {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 1000;
    background-color: rgba(25, 29, 50, 0.95);
    border-bottom: 1px solid rgba(255, 255, 255, 0.2);
    backdrop-filter: blur(20px) saturate(180%);
    -webkit-backdrop-filter: blur(20px) saturate(180%);
    box-shadow: 0 2px 12px rgba(0, 0, 0, 0.2);
    height: auto;
    padding: 0.25rem 0;
  }

  .nav-content {
    max-width: 100%;
    margin: 0;
    padding: 0.25rem 1.7rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: relative;
  }

  .nav-links {
    display: flex;
    gap: 0.5rem;
    padding: 0.25rem;
    background: rgba(255, 255, 255, 0.1);
    border-radius: 0.5rem;
    border: 1px solid rgba(255, 255, 255, 0.2);
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
  }

  .nav-links.left {
    margin-right: auto;
  }

  .nav-links.right {
    margin-left: auto;
  }

  .nav-links button,
  .nav-links a.nav-button {
    background: none;
    border: none;
    color: rgba(255, 255, 255, 0.9);
    font-family: 'Quicksand', sans-serif;
    font-weight: 500;
    font-size: 0.85rem;
    cursor: pointer;
    padding: 0.35rem 0.5rem;
    transition: all 0.2s ease;
    display: flex;
    align-items: center;
    gap: 0.5rem;
    border-radius: 0.4rem;
    text-decoration: none;
    white-space: nowrap;
  }

  .nav-links button:hover,
  .nav-links a.nav-button:hover {
    color: #ffffff;
    background: rgba(255, 255, 255, 0.15);
  }

  @media (max-width: 768px) {
    .nav-links span {
      display: none;
    }

    .nav-links button,
    .nav-links a.nav-button {
      padding: 0.35rem;
    }
  }

  @media (max-width: 480px) {
    .nav-links span {
      display: none;
    }

    .nav-links button,
    .nav-links a.nav-button {
      padding: 0.35rem;
    }
  }
</style>
