<script>
  import HandwritingAnimation from '$lib/components/HandwritingAnimation.svelte';
  import AnimatedQuotes from '$lib/components/AnimatedQuotes.svelte';
  import { onMount } from 'svelte';
  
  // Initialize dark mode from localStorage
  let isDarkMode = false;
  let backgroundImage = '';
  let isLoading = true;

  // Collection of nature images for hero section
  const natureImages = [
    'https://images.pexels.com/photos/2662116/pexels-photo-2662116.jpeg', // Misty forest
    'https://images.pexels.com/photos/1363876/pexels-photo-1363876.jpeg', // Mountain lake
    'https://images.pexels.com/photos/1671325/pexels-photo-1671325.jpeg', // Sunset forest
    'https://images.pexels.com/photos/1292115/pexels-photo-1292115.jpeg', // Waterfall
    'https://images.pexels.com/photos/2088210/pexels-photo-2088210.jpeg', // Mountain peaks
    'https://images.pexels.com/photos/1586298/pexels-photo-1586298.jpeg', // Autumn forest
    'https://images.pexels.com/photos/1761279/pexels-photo-1761279.jpeg', // Green valley
    'https://images.pexels.com/photos/1559117/pexels-photo-1559117.jpeg'  // Forest river
  ];

  function loadBackgroundImage() {
    const randomIndex = Math.floor(Math.random() * natureImages.length);
    backgroundImage = `url('${natureImages[randomIndex]}')`;
  }

  onMount(() => {
    // Load dark mode preference from localStorage
    const storedDarkMode = localStorage.getItem('darkMode');
    isDarkMode = storedDarkMode === 'true';
    if (isDarkMode) {
      document.body.classList.add('dark-mode');
    }
    loadBackgroundImage();
    
    // Remove loading state after a short delay
    setTimeout(() => {
      isLoading = false;
    }, 500);
  });

  function scrollToSection(sectionId) {
    const element = document.getElementById(sectionId);
    element?.scrollIntoView({ behavior: 'smooth' });
  }

  function toggleDarkMode() {
    isDarkMode = !isDarkMode;
    // Store the preference in localStorage
    localStorage.setItem('darkMode', isDarkMode);
    if (document.body.classList.contains('dark-mode')) {
      document.body.classList.remove('dark-mode');
    } else {
      document.body.classList.add('dark-mode');
    }
  }
</script>

{#if isLoading}
  <div class="loading-overlay" class:fade-out={!isLoading}>
    <div class="loading-content">
      <div class="loading-text">Pulpit!</div>
    </div>
  </div>
{/if}

<nav>
  <div class="nav-content">
    <div class="nav-links">
      <button on:click={() => scrollToSection('hero')}>
        <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
          <polyline points="9 22 9 12 15 12 15 22"></polyline>
        </svg>
        <span>Home</span>
      </button>
      <button on:click={() => scrollToSection('cards')}>
        <svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <rect x="3" y="3" width="7" height="7"></rect>
          <rect x="14" y="3" width="7" height="7"></rect>
          <rect x="14" y="14" width="7" height="7"></rect>
          <rect x="3" y="14" width="7" height="7"></rect>
        </svg>
        <span>Cards</span>
      </button>
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
  </div>
</nav>

<main>
    <section id="hero" class="hero-section">
        <div class="hero-left">
            <div class="pulpit-segment">
                <div class="logo">
                    <img src="/pulpit.jpeg" alt="Logo" />
                </div>
                <div class="brand-name-container">
                    <div class="loading-text-unanimated">Pulpit!</div>
                </div>
            </div>
        </div>
        <div 
            class="hero-right" 
            class:dark-mode={isDarkMode}
            style="background-image: {backgroundImage}"
        >
            <div class="quotes-wrapper">
                <AnimatedQuotes />
            </div>
        </div>
    </section>
    
    <section id="cards" class="cards-section">
        <!-- Add your cards here -->
        <div class="cards-container">
            <div class="card">
                <h3>Card Title</h3>
                <p>Card content goes here...</p>
            </div>
            <div class="card">
                <h3>Card Title</h3>
                <p>Card content goes here...</p>
            </div>
            <div class="card">
                <h3>Card Title</h3>
                <p>Card content goes here...</p>
            </div>
        </div>
    </section>
</main>

<style>
    :global(body), :global(html) {
        margin: 0;
        padding: 0;
        width: 100%;
        height: 100%;
        overflow: hidden;
    }

    main {
        display: flex;
        flex-direction: column;
        min-height: 100vh;
        width: 100vw;
        margin: 0;
        padding: 0;
        background-color: #ffffff;
        background-image: 
            linear-gradient(rgba(25, 29, 50, 0.05) 1px, transparent 1px);
        background-size: 100% 25px;
        overflow-y: auto;
        overflow-x: hidden;
        scroll-behavior: smooth;
    }

    /* Subtle paper texture */
    main::before {
        content: '';
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-image: 
            radial-gradient(circle at 50% 50%, rgba(25, 29, 50, 0.03) 0%, transparent 100%);
        pointer-events: none;
    }

    /* Gentle edge shadow */
    main::after {
        content: '';
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        box-shadow: inset 0 0 80px rgba(25, 29, 50, 0.15);
        pointer-events: none;
    }

    .hero-section {
        min-height: 100vh;
        display: flex;
        flex-direction: row;
        position: relative;
        padding-top: 50px;
        gap: 0;
    }

    .hero-left {
        width: 25%;
        position: relative;
        z-index: 1;
        display: flex;
    }

    .pulpit-segment {
        background-color: rgba(2, 103, 193, 0.85);
        backdrop-filter: blur(30px) saturate(180%);
        -webkit-backdrop-filter: blur(30px) saturate(180%);
        box-shadow: 
            4px 0 8px rgba(25, 29, 50, 0.15),
            inset 0 0 0 1px rgba(255, 255, 255, 0.2);
        border-right: 1px solid rgba(255, 255, 255, 0.3);
        height: 85%;
        width: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: flex-start;
        gap: 1.5rem;
        padding: 2rem;
        padding-top: 6rem;
        position: relative;
        overflow: hidden;
    }

    /* Paper texture and ruling for pulpit-segment */
    .pulpit-segment::before {
        content: '';
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-image: 
            /* Notebook ruling */
            repeating-linear-gradient(
                0deg,
                transparent,
                transparent 24px,
                #CCC5B9 24px, /* Cement color */
                #CCC5B9 25px
            ),
            /* Noise texture */
            url("data:image/svg+xml,%3Csvg viewBox='0 0 400 400' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)'/%3E%3C/svg%3E");
        background-size: 100% 25px, 150px 150px;
        opacity: 0.1;
        z-index: 0;
        mix-blend-mode: overlay;
    }

    /* Dark mode update for the paper texture */
    :global(body.dark-mode) .pulpit-segment {
        background-color: rgba(2, 76, 145, 0.85);
    }

    :global(body.dark-mode) .pulpit-segment::before {
        background-image: 
            /* Notebook ruling */
            repeating-linear-gradient(
                0deg,
                transparent,
                transparent 24px,
                #808080 24px, /* Gray color */
                #808080 25px
            ),
            /* Noise texture */
            url("data:image/svg+xml,%3Csvg viewBox='0 0 400 400' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)'/%3E%3C/svg%3E");
        opacity: 0.15;
    }

    /* Ensure pulpit-segment content stays above the pattern */
    .pulpit-segment > * {
        position: relative;
        z-index: 1;
    }

    .hero-right {
        width: 75%;
        position: relative;
        background-color: var(--bg-primary);
        backdrop-filter: blur(12px);
        box-shadow: 0 2px 12px var(--panel-shadow);
        transition: all 0.3s ease;
        background-size: cover;
        background-position: center;
        background-repeat: no-repeat;
        overflow: hidden;
    }

    .hero-right.dark-mode {
        background-color: var(--bg-primary);
        color: var(--text-primary);
    }

    /* Remove the old background-image and add subtle paper texture */
    .hero-right::before {
        content: '';
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: rgba(255, 255, 255, 0.4);
        backdrop-filter: blur(20px);
        -webkit-backdrop-filter: blur(20px);
        opacity: 0.85;
        transition: background-color 0.3s ease;
    }

    .hero-right.dark-mode::before {
        background-color: var(--bg-primary);
    }

    .logo {
        width: 60%;
        max-width: 300px;
        aspect-ratio: 1;
        border-radius: 50%;
        overflow: hidden;
    }

    .logo img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        filter: brightness(0.9) contrast(1.1);  /* Add subtle filter */
    }

    .cards-section {
        min-height: 100vh;
        padding: 4rem 2%;
        background: rgba(25, 29, 50, 0.05);
        backdrop-filter: blur(10px);
        -webkit-backdrop-filter: blur(10px);
        position: relative;
    }

    .cards-container {
        max-width: 90%;
        margin: 0 auto;
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(18rem, 1fr));
        gap: 2rem;
        padding: 1rem;
    }

    .card {
        background: rgba(255, 255, 255, 0.7);
        backdrop-filter: blur(10px);
        -webkit-backdrop-filter: blur(10px);
        border: 1px solid rgba(255, 255, 255, 0.3);
        box-shadow: 
            0 8px 32px rgba(0, 0, 0, 0.1),
            inset 0 0 0 1px rgba(255, 255, 255, 0.5);
        border-radius: 0.5rem;
        padding: 1.5rem;
        box-shadow: 
            0 4px 6px rgba(25, 29, 50, 0.2),
            inset 0 0 20px rgba(25, 29, 50, 0.1);
        transition: transform 0.2s ease;
    }

    .card:hover {
        transform: translateY(-5px);
    }

    .card h3 {
        color: #191D32;
        margin-bottom: 1rem;
        font-family: 'Times New Roman', serif;
    }

    .card p {
        color: #191D32;
        line-height: 1.6;
    }

    nav {
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        z-index: 1000;
        background-color: rgba(255, 255, 255, 0.85);
        border-bottom: 1px solid rgba(255, 255, 255, 0.4);
        backdrop-filter: blur(20px) saturate(180%);
        -webkit-backdrop-filter: blur(20px) saturate(180%);
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    }

    .nav-content {
        max-width: 90%;
        margin: 0 auto;
        padding: 0.5rem 2%;
        display: flex;
        justify-content: flex-end;
        align-items: center;
    }

    .nav-links {
        display: flex;
        gap: 0.5rem;
        padding: 0.25rem;
        background: rgba(255, 255, 255, 0.3);
        border-radius: 0.625rem;
        backdrop-filter: blur(20px) saturate(180%);
        -webkit-backdrop-filter: blur(20px) saturate(180%);
        border: 1px solid rgba(255, 255, 255, 0.4);
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }

    .nav-links button,
    .nav-links a.nav-button {
        background: none;
        border: none;
        color: #191D32;
        font-family: 'Times New Roman', serif;
        font-size: 0.95rem;
        cursor: pointer;
        padding: 0.35rem 0.75rem;
        transition: all 0.2s ease;
        display: flex;
        align-items: center;
        gap: 0.5rem;
        border-radius: 0.5rem;
        text-decoration: none;
    }

    .nav-links button:hover,
    .nav-links a.nav-button:hover {
        color: #0267C1;
        background: rgba(2, 103, 193, 0.1);
    }

    .dark-mode-toggle {
        background: none;
        border: none;
        color: #191D32;
        cursor: pointer;
        padding: 0.5rem 1rem !important;
        display: flex;
        align-items: center;
        justify-content: center;
        transition: all 0.2s ease;
        border-radius: 0.5rem;
    }

    .dark-mode-toggle:hover {
        color: #0267C1;
        background: rgba(2, 103, 193, 0.1);
    }

    .dark-mode-toggle.dark-mode {
        color: #EEEEEE;
    }

    /* Update dark mode styles for the navigation */
    :global(body.dark-mode) nav {
        background-color: rgba(26, 32, 44, 0.85);
        border-bottom-color: rgba(255, 255, 255, 0.1);
    }

    :global(body.dark-mode) .nav-links {
        background: rgba(0, 0, 0, 0.3);
        border-color: rgba(255, 255, 255, 0.1);
    }

    :global(body.dark-mode) .nav-links button,
    :global(body.dark-mode) .nav-links a.nav-button {
        color: #EEEEEE;
    }

    :global(body.dark-mode) .nav-links button:hover,
    :global(body.dark-mode) .nav-links a.nav-button:hover {
        color: #0267C1;
        background: rgba(2, 103, 193, 0.2);
    }

    /* Add CSS variables for consistent theming */
    :global(:root) {
        --bg-primary: rgba(255, 255, 255, 0.5);
        --text-primary: #191D32;
        --panel-shadow: rgba(0, 0, 0, 0.15);
        --border-color: rgba(25, 29, 50, 0.35);
        --hero-left-bg: #0267C1;
    }

    :global(body.dark-mode) {
        --bg-primary: rgba(26, 32, 44, 0.7);
        --text-primary: #EEEEEE;
        --panel-shadow: rgba(0, 0, 0, 0.3);
        --border-color: rgba(255, 255, 255, 0.1);
        --hero-left-bg: #024C91;
    }

    /* Fix dark mode toggle button */
    .dark-mode-toggle svg {
        width: 1.125rem;
        height: 1.125rem;
        stroke: currentColor;
    }

    .loading-overlay {
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: #000000;
        display: flex;
        align-items: center;
        justify-content: center;
        z-index: 9999;
        opacity: 1;
        transition: all 1.5s cubic-bezier(0.645, 0.045, 0.355, 1);
    }

    .loading-overlay.fade-out {
        opacity: 0;
        pointer-events: none;
    }

    .loading-content {
        text-align: center;
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .loading-text {
        font-family: 'Playfair Display', 'Cormorant Garamond', serif;
        font-size: 5rem;
        color: white;
        font-weight: 400;
        font-style: italic;
        opacity: 0;
        transform: translateY(1.25rem) rotate(-5deg);
        animation: textAnimation 2s cubic-bezier(0.215, 0.610, 0.355, 1) forwards;
        text-shadow: 
          0 0 20px rgba(255,255,255,0.4),
          0 0 40px rgba(255,255,255,0.2),
          0 0 60px rgba(255,255,255,0.1);
    }

    @keyframes textAnimation {
        0% {
            opacity: 0;
            transform: translateY(1.875rem) rotate(-8deg) scale(0.95);
            filter: blur(0.5rem);
        }
        25% {
            opacity: 1;
            transform: translateY(0) rotate(-5deg) scale(1);
            filter: blur(0);
        }
        85% {
            opacity: 1;
            transform: translateY(0) rotate(-5deg) scale(1);
            filter: blur(0);
        }
        100% {
            opacity: 0;
            transform: translateY(-1.25rem) rotate(-3deg) scale(1.05);
            filter: blur(0.75rem);
        }
    }

    .loading-text-unanimated {
        font-family: 'Playfair Display', 'Cormorant Garamond', serif;
        font-size: 3rem;
        color: white;
        font-weight: 400;
        font-style: italic;
        text-shadow: 
            0 0 20px rgba(255,255,255,0.4),
            0 0 40px rgba(255,255,255,0.2),
            0 0 60px rgba(255,255,255,0.1);
        transform: rotate(-5deg);
    }

    @media (max-width: 768px) {
        .hero-section {
            flex-direction: column;
        }

        .hero-left, .hero-right {
            width: 100%;
        }

        .hero-left {
            height: 40vh;
        }

        .hero-right {
            height: 60vh;
        }

        .logo {
            width: 40%;
        }

        .loading-text {
            font-size: 3rem;
        }

        .loading-text-unanimated {
            font-size: 2rem;
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

        .logo {
            width: 50%;
        }
    }
</style>
