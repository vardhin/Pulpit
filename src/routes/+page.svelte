<script>
  import HandwritingAnimation from '$lib/components/HandwritingAnimation.svelte';
  import AnimatedQuotes from '$lib/components/AnimatedQuotes.svelte';
  import { onMount } from 'svelte';
  
  // Initialize dark mode from localStorage
  let isDarkMode = false;
  let backgroundImage = '';
  let isLoading = true;

  // Collection of tundra like images with crystal clear lakes and riversfor hero section
  const natureImages = [
    'https://images.pexels.com/photos/147411/italy-mountains-dawn-daybreak-147411.jpeg',  // Misty mountain lake
    'https://images.pexels.com/photos/1586298/pexels-photo-1586298.jpeg',  // Autumn forest
    'https://images.pexels.com/photos/235621/pexels-photo-235621.jpeg',    // Foggy pine forest
    'https://images.pexels.com/photos/2662116/pexels-photo-2662116.jpeg', // Misty forest
    'https://images.pexels.com/photos/1559117/pexels-photo-1559117.jpeg', // Forest river
    'https://images.pexels.com/photos/1266810/pexels-photo-1266810.jpeg',  // Serene lake sunset
  ];

  function loadBackgroundImage() {
    const randomIndex = Math.floor(Math.random() * natureImages.length);
    backgroundImage = `url('${natureImages[randomIndex]}')`;
  }

  let currentSection = 'hero';
  const sections = ['hero', 'cards'];

  function navigateSection(direction) {
    const currentIndex = sections.indexOf(currentSection);
    let nextIndex;
    
    if (direction === 'up') {
      nextIndex = Math.max(0, currentIndex - 1);
    } else {
      nextIndex = Math.min(sections.length - 1, currentIndex + 1);
    }
    
    currentSection = sections[nextIndex];
    scrollToSection(currentSection);
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

    // Add Google Fonts link dynamically
    const link = document.createElement('link');
    link.href = 'https://fonts.googleapis.com/css2?family=Quicksand:wght@300;400;500;600;700&display=swap';
    link.rel = 'stylesheet';
    document.head.appendChild(link);

    let scrollAccumulator = 0;
    let scrollTimeout;

    const handleWheel = (e) => {
      e.preventDefault();
      
      // Check if it's a fast scroll/flick (higher velocity)
      const flickThreshold = 20; // Adjust this threshold as needed
      
      if (Math.abs(e.deltaY) > flickThreshold) {
        if (e.deltaY > 0) {
          navigateSection('down');
        } else {
          navigateSection('up');
        }
      }
    };

    const handleKeydown = (e) => {
      if (e.key === 'ArrowUp') {
        e.preventDefault();
        navigateSection('up');
      } else if (e.key === 'ArrowDown') {
        e.preventDefault();
        navigateSection('down');
      }
    };

    // Add event listeners
    document.addEventListener('wheel', handleWheel, { passive: false });
    document.addEventListener('keydown', handleKeydown);

    return () => {
      document.removeEventListener('wheel', handleWheel);
      document.removeEventListener('keydown', handleKeydown);
    };
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
        <span>Lits</span>
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

    <div class="navigation-arrows">
      <button 
        class="nav-arrow up"
        on:click={() => navigateSection('up')}
        style="opacity: {currentSection === 'hero' ? '0.5' : '1'}"
        disabled={currentSection === 'hero'}
      >
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <polyline points="18 15 12 9 6 15"></polyline>
        </svg>
      </button>
      <button 
        class="nav-arrow down"
        on:click={() => navigateSection('down')}
        style="opacity: {currentSection === 'cards' ? '0.5' : '1'}"
        disabled={currentSection === 'cards'}
      >
        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <polyline points="6 9 12 15 18 9"></polyline>
        </svg>
      </button>
    </div>
</main>

<style>
    :global(body), :global(html) {
        margin: 0;
        padding: 0;
        width: 100%;
        height: 100%;
        overflow: visible;
        scrollbar-width: none;  /* Firefox */
        -ms-overflow-style: none;  /* Internet Explorer 10+ */
    }

    :global(body::-webkit-scrollbar), :global(html::-webkit-scrollbar) {
        display: none;  /* WebKit */
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
        overflow-y: hidden;
        overflow-x: hidden;
        scroll-behavior: smooth;
        scroll-snap-type: y mandatory;
        scrollbar-width: none;  /* Firefox */
        -ms-overflow-style: none;  /* Internet Explorer 10+ */
    }

    main::-webkit-scrollbar {
        display: none;  /* WebKit */
    }

    .hero-section, .cards-section {
        scroll-snap-align: start;
        scroll-snap-stop: always;
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
        user-select: none;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
    }

    .pulpit-segment {
        background-color: rgba(2, 83, 153, 0.95);
        backdrop-filter: blur(15px) saturate(150%);
        -webkit-backdrop-filter: blur(15px) saturate(150%);
        box-shadow: 
            4px 0 8px rgba(25, 29, 50, 0.15),
            inset 0 0 0 1px rgba(255, 255, 255, 0.25);
        border-right: 1px solid rgba(255, 255, 255, 0.2);
        height: 81%;
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
                rgba(0, 20, 40, 0.2) 24px, /* Changed to dark blue with opacity */
                rgba(0, 20, 40, 0.2) 25px
            ),
            /* Noise texture */
            url("data:image/svg+xml,%3Csvg viewBox='0 0 400 400' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)'/%3E%3C/svg%3E");
        background-size: 100% 25px, 150px 150px;
        opacity: 0.5;
        z-index: 0;
        mix-blend-mode: multiply; /* Changed blend mode to work better with dark lines */
    }

    /* Dark mode update for the paper texture */
    :global(body.dark-mode) .pulpit-segment {
        background-color: rgba(1, 48, 91, 0.95);
    }

    :global(body.dark-mode) .pulpit-segment::before {
        background-image: 
            repeating-linear-gradient(
                0deg,
                transparent,
                transparent 24px,
                rgba(255, 255, 255, 0.5) 24px,
                rgba(255, 255, 255, 0.5) 25px
            ),
            url("data:image/svg+xml,%3Csvg viewBox='0 0 400 400' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)'/%3E%3C/svg%3E");
        opacity: 0.4;
        mix-blend-mode: soft-light; /* Keeping original blend mode for dark mode */
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
        pointer-events: none;
        -webkit-user-drag: none;
        user-select: none;
    }

    .logo img {
        width: 100%;
        height: 100%;
        object-fit: cover;
        filter: brightness(0.9) contrast(1.1);
        pointer-events: none;
        -webkit-user-drag: none;
        user-select: none;
    }

    .cards-section {
        min-height: 100vh;
        padding: 4rem 2%;
        background-color: rgba(2, 83, 153, 0.95);
        backdrop-filter: blur(15px) saturate(150%);
        -webkit-backdrop-filter: blur(15px) saturate(150%);
        box-shadow: 
            4px 0 8px rgba(25, 29, 50, 0.15),
            inset 0 0 0 1px rgba(255, 255, 255, 0.25);
        position: relative;
        overflow: hidden;
    }

    /* Paper texture and ruling for cards-section */
    .cards-section::before {
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
                rgba(0, 20, 40, 0.2) 24px,
                rgba(0, 20, 40, 0.2) 25px
            ),
            /* Noise texture */
            url("data:image/svg+xml,%3Csvg viewBox='0 0 400 400' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)'/%3E%3C/svg%3E");
        background-size: 100% 25px, 150px 150px;
        opacity: 0.5;
        z-index: 0;
        mix-blend-mode: multiply;
    }

    /* Dark mode update for the cards section */
    :global(body.dark-mode) .cards-section {
        background-color: rgba(1, 48, 91, 0.95);
    }

    :global(body.dark-mode) .cards-section::before {
        background-image: 
            repeating-linear-gradient(
                0deg,
                transparent,
                transparent 24px,
                rgba(255, 255, 255, 0.5) 24px,
                rgba(255, 255, 255, 0.5) 25px
            ),
            url("data:image/svg+xml,%3Csvg viewBox='0 0 400 400' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)'/%3E%3C/svg%3E");
        opacity: 0.4;
        mix-blend-mode: soft-light;
    }

    .cards-container {
        position: relative;
        z-index: 1;
        max-width: 90%;
        margin: 0 auto;
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(18rem, 1fr));
        gap: 2rem;
        padding: 1rem;
    }

    .card {
        background: rgba(255, 255, 255, 0.9);
        backdrop-filter: blur(10px);
        -webkit-backdrop-filter: blur(10px);
        border: 1px solid rgba(255, 255, 255, 0.3);
        box-shadow: 
            0 8px 32px rgba(0, 0, 0, 0.1),
            inset 0 0 0 1px rgba(255, 255, 255, 0.5);
        border-radius: 0.5rem;
        padding: 1.5rem;
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
        background-color: rgba(25, 29, 50, 0.95); /* Darker, more neutral color */
        border-bottom: 1px solid rgba(255, 255, 255, 0.2);
        backdrop-filter: blur(20px) saturate(180%);
        -webkit-backdrop-filter: blur(20px) saturate(180%);
        box-shadow: 0 2px 12px rgba(0, 0, 0, 0.2);
        height: 3.12rem;
    }

    .nav-content {
        max-width: 100%;
        margin: 0;
        padding: 0.5rem 1.7rem;  /* Reduced padding */
        display: flex;
        justify-content: flex-start;
        align-items: center;
    }

    .nav-links {
        display: flex;
        gap: 0.25rem;  /* Reduced gap */
        padding: 0.15rem;  /* Reduced padding */
        background: rgba(255, 255, 255, 0.1);
        border-radius: 0.5rem;  /* Slightly reduced border radius */
        backdrop-filter: blur(20px) saturate(180%);
        -webkit-backdrop-filter: blur(20px) saturate(180%);
        border: 1px solid rgba(255, 255, 255, 0.2);
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
    }

    .nav-links button,
    .nav-links a.nav-button {
        background: none;
        border: none;
        color: rgba(255, 255, 255, 0.9);
        font-family: 'Quicksand', sans-serif;
        font-weight: 500;
        font-size: 0.85rem;  /* Reduced font size */
        cursor: pointer;
        padding: 0.25rem 0.5rem;  /* Reduced padding */
        transition: all 0.2s ease;
        display: flex;
        align-items: center;
        gap: 0.35rem;  /* Reduced gap */
        border-radius: 0.4rem;  /* Slightly reduced border radius */
        text-decoration: none;
    }

    .nav-links button:hover,
    .nav-links a.nav-button:hover {
        color: #ffffff;
        background: rgba(255, 255, 255, 0.15);
    }

    .dark-mode-toggle {
        color: rgba(255, 255, 255, 0.9) !important;
    }

    .dark-mode-toggle:hover {
        color: #ffffff !important;
        background: rgba(255, 255, 255, 0.15) !important;
    }

    /* Update dark mode styles for the navigation */
    :global(body.dark-mode) nav {
        background-color: rgba(20, 24, 42, 0.95); /* Darker, more neutral color for dark mode */
        border-bottom-color: rgba(255, 255, 255, 0.1);
    }

    :global(body.dark-mode) .nav-links {
        background: rgba(255, 255, 255, 0.05);
        border-color: rgba(255, 255, 255, 0.1);
    }

    :global(body.dark-mode) .nav-links button,
    :global(body.dark-mode) .nav-links a.nav-button {
        color: rgba(255, 255, 255, 0.9);
    }

    :global(body.dark-mode) .nav-links button:hover,
    :global(body.dark-mode) .nav-links a.nav-button:hover {
        color: #ffffff;
        background: rgba(255, 255, 255, 0.1);
    }

    /* Add CSS variables for consistent theming */
    :global(:root) {
        --bg-primary: rgba(255, 255, 255, 0.5);
        --text-primary: #191D32;
        --panel-shadow: rgba(0, 0, 0, 0.15);
        --border-color: rgba(25, 29, 50, 0.35);
        --hero-left-bg: #025399;
    }

    :global(body.dark-mode) {
        --bg-primary: rgba(26, 32, 44, 0.7);
        --text-primary: #EEEEEE;
        --panel-shadow: rgba(0, 0, 0, 0.3);
        --border-color: rgba(255, 255, 255, 0.1);
        --hero-left-bg: #01305B;
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
        font-family: 'Quicksand', sans-serif;
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
        font-family: 'Quicksand', sans-serif;
        font-size: 3rem;
        color: rgba(255, 255, 255, 0.95);
        font-weight: 500;
        font-style: italic;
        text-shadow: 
            0 0 20px rgba(255,255,255,0.4),
            0 0 40px rgba(255,255,255,0.2),
            0 0 60px rgba(255,255,255,0.1);
        transform: rotate(-5deg);
        user-select: none;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
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

    .navigation-arrows {
      position: fixed;
      bottom: 2rem;
      left: 2rem;
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
      z-index: 1000;
    }

    .nav-arrow {
      background: rgba(25, 29, 50, 0.9);
      border: 1px solid rgba(255, 255, 255, 0.2);
      border-radius: 50%;
      width: 40px;
      height: 40px;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      color: white;
      transition: all 0.3s ease;
      backdrop-filter: blur(10px);
      -webkit-backdrop-filter: blur(10px);
    }

    .nav-arrow:hover:not(:disabled) {
      background: rgba(25, 29, 50, 1);
      transform: scale(1.1);
    }

    .nav-arrow:disabled {
      cursor: not-allowed;
    }

    :global(body.dark-mode) .nav-arrow {
      background: rgba(255, 255, 255, 0.2);
    }

    :global(body.dark-mode) .nav-arrow:hover:not(:disabled) {
      background: rgba(255, 255, 255, 0.3);
    }
</style>
