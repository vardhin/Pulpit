<script>
  import HandwritingAnimation from '$lib/components/HandwritingAnimation.svelte';
  import AnimatedQuotes from '$lib/components/AnimatedQuotes.svelte';
  import Navbar from '$lib/components/Navbar.svelte';
  import Loadingscreen from '$lib/components/Loadingscreen.svelte';
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

<Navbar {isDarkMode} {toggleDarkMode} />
<Loadingscreen {isLoading} />

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
                <div class="card-icon">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <path d="M12 19l7-7 3 3-7 7-3-3z"/>
                        <path d="M18 13l-1.5-7.5L2 2l3.5 14.5L13 18l5-5z"/>
                    </svg>
                </div>
                <h3>Write</h3>
                <p>Create beautiful, distraction-free content with our minimalist writing environment.</p>
                <a href="/writer" class="card-link">Start Writing →</a>
            </div>
            <div class="card">
                <div class="card-icon">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <path d="M4 19.5A2.5 2.5 0 0 1 6.5 17H20"></path>
                        <path d="M6.5 2H20v20H6.5A2.5 2.5 0 0 1 4 19.5v-15A2.5 2.5 0 0 1 6.5 2z"></path>
                    </svg>
                </div>
                <h3>Read</h3>
                <p>Discover thought-provoking articles and stories from our community of writers.</p>
                <a href="/reader" class="card-link">Browse Library →</a>
            </div>
            <div class="card">
                <div class="card-icon">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <path d="M21 11.5a8.38 8.38 0 0 1-.9 3.8 8.5 8.5 0 0 1-7.6 4.7 8.38 8.38 0 0 1-3.8-.9L3 21l1.9-5.7a8.38 8.38 0 0 1-.9-3.8 8.5 8.5 0 0 1 4.7-7.6 8.38 8.38 0 0 1 3.8-.9h.5a8.48 8.48 0 0 1 8 8v.5z"></path>
                    </svg>
                </div>
                <h3>Forum</h3>
                <p>Join discussions, share ideas, and connect with other writers in our community forum.</p>
                <a href="/forum" class="card-link">Visit Forum →</a>
            </div>
        </div>
    </section>

    <div class="navigation-controls">
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
    </div>
</main>

<style>
    /* Add local font declarations at the top of your styles */
    @font-face {
        font-family: 'Quicksand';
        src: url('/fonts/Quicksand-Light.ttf') format('truetype');
        font-weight: 300;
        font-style: normal;
        font-display: swap;
    }

    @font-face {
        font-family: 'Quicksand';
        src: url('/fonts/Quicksand-Regular.ttf') format('truetype');
        font-weight: 400;
        font-style: normal;
        font-display: swap;
    }

    @font-face {
        font-family: 'Quicksand';
        src: url('/fonts/Quicksand-Medium.ttf') format('truetype');
        font-weight: 500;
        font-style: normal;
        font-display: swap;
    }

    @font-face {
        font-family: 'Quicksand';
        src: url('/fonts/Quicksand-SemiBold.ttf') format('truetype');
        font-weight: 600;
        font-style: normal;
        font-display: swap;
    }

    @font-face {
        font-family: 'Quicksand';
        src: url('/fonts/Quicksand-Bold.ttf') format('truetype');
        font-weight: 700;
        font-style: normal;
        font-display: swap;
    }

    /* Add global font application after the font-face declarations */
    :global(body) {
        font-family: 'Quicksand', sans-serif;
    }

    /* Update the loading-text-unanimated class with smaller font sizes */
    .loading-text-unanimated {
        font-family: 'Quicksand', sans-serif;
        font-size: 3.5rem;  /* Reduced from 5rem */
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
        .loading-text-unanimated {
            font-size: 2rem;  /* Reduced from 3rem */
        }
    }

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
        background: rgba(255, 255, 255, 0.95);
        backdrop-filter: blur(10px);
        -webkit-backdrop-filter: blur(10px);
        border: 1px solid rgba(255, 255, 255, 0.3);
        box-shadow: 
            0 8px 32px rgba(0, 0, 0, 0.1),
            inset 0 0 0 1px rgba(255, 255, 255, 0.5);
        border-radius: 1rem;
        padding: 2rem;
        transition: all 0.3s ease;
        display: flex;
        flex-direction: column;
        gap: 1rem;
        position: relative;
        overflow: hidden;
    }

    .card::before {
        content: '';
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        height: 4px;
        background: linear-gradient(90deg, #025399, #0077CC);
        opacity: 0;
        transition: opacity 0.3s ease;
    }

    .card:hover {
        transform: translateY(-5px);
        box-shadow: 
            0 12px 48px rgba(0, 0, 0, 0.15),
            inset 0 0 0 1px rgba(255, 255, 255, 0.6);
    }

    .card:hover::before {
        opacity: 1;
    }

    .card-icon {
        width: 48px;
        height: 48px;
        background: rgba(2, 83, 153, 0.1);
        border-radius: 12px;
        display: flex;
        align-items: center;
        justify-content: center;
        color: #025399;
        transition: all 0.3s ease;
    }

    .card:hover .card-icon {
        background: #025399;
        color: white;
        transform: scale(1.1);
    }

    .card h3 {
        color: #191D32;
        margin: 0;
        font-family: 'Quicksand', sans-serif;
        font-size: 1.5rem;
        font-weight: 600;
    }

    .card p {
        color: #4A5568;
        line-height: 1.6;
        margin: 0;
        font-size: 1rem;
    }

    .card-link {
        margin-top: auto;
        color: #025399;
        text-decoration: none;
        font-weight: 500;
        display: inline-flex;
        align-items: center;
        gap: 0.5rem;
        transition: gap 0.3s ease;
    }

    .card-link:hover {
        gap: 0.75rem;
    }

    /* Dark mode styles */
    :global(body.dark-mode) .card {
        background: rgba(26, 32, 44, 0.95);
        border-color: rgba(255, 255, 255, 0.1);
    }

    :global(body.dark-mode) .card h3 {
        color: #E2E8F0;
    }

    :global(body.dark-mode) .card p {
        color: #A0AEC0;
    }

    :global(body.dark-mode) .card-icon {
        background: rgba(255, 255, 255, 0.1);
        color: #E2E8F0;
    }

    :global(body.dark-mode) .card:hover .card-icon {
        background: #E2E8F0;
        color: #1A202C;
    }

    :global(body.dark-mode) .card-link {
        color: #90CDF4;
    }

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
        height: 3.12rem;
    }

    .nav-content {
        max-width: 100%;
        margin: 0;
        padding: 0.5rem 1.7rem;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .nav-links {
        display: flex;
        gap: 0.5rem;
        padding: 0.5rem;
        background: rgba(255, 255, 255, 0.1);
        border-radius: 0.5rem;
        backdrop-filter: blur(20px) saturate(180%);
        -webkit-backdrop-filter: blur(20px) saturate(180%);
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
        padding: 0.5rem 0.75rem;
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

    .navigation-controls {
      position: fixed;
      bottom: 2rem;
      left: 2rem;
      z-index: 1000;
    }

    .navigation-arrows {
      display: flex;
      flex-direction: column;
      gap: 0.5rem;
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
