<script>
  import HandwritingAnimation from '$lib/components/HandwritingAnimation.svelte';
  import AnimatedQuotes from '$lib/components/AnimatedQuotes.svelte';

  function scrollToSection(sectionId) {
    const element = document.getElementById(sectionId);
    element?.scrollIntoView({ behavior: 'smooth' });
  }
</script>

<nav>
  <div class="nav-content">
    <div class="nav-logo" on:click={() => scrollToSection('hero')}>Logo</div>
    <div class="nav-links">
      <button on:click={() => scrollToSection('hero')}>Home</button>
      <button on:click={() => scrollToSection('cards')}>Cards</button>
    </div>
  </div>
</nav>

<main>
    <section id="hero" class="hero-section">
        <div class="hero-left">
            <div class="pulpit-segment">
                <div class="logo">
                    <img src="https://placehold.co/300x300/733a0f/e6d5c3.png" alt="Logo" />
                </div>
                <h1 class="brand-name">pulpit!</h1>
            </div>
        </div>
        <div class="hero-right">
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
        background-color: #d4c0a7;
        background-image: 
            linear-gradient(rgba(89, 44, 12, 0.05) 1px, transparent 1px);
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
            radial-gradient(circle at 50% 50%, rgba(89, 44, 12, 0.03) 0%, transparent 100%);
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
        box-shadow: inset 0 0 80px rgba(81, 47, 13, 0.15);
        pointer-events: none;
    }

    .hero-section {
        min-height: 100vh;
        display: flex;
        flex-direction: row;
        position: relative;
        padding-top: 60px; /* Account for fixed nav */
        gap: 0; /* Ensure no gap between sections */
    }

    .hero-left {
        width: 25%;
        position: relative;
        z-index: 1;
        display: flex; /* Add this */
    }

    .pulpit-segment {
        background-color: #aa763f;
        height: 85%;
        width: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: flex-start;
        gap: 1.5rem;
        padding: 2rem;
        padding-top: 6rem;
        box-shadow: 4px 0 8px rgba(81, 47, 13, 0.15);
        border-right: 1px solid rgba(89, 44, 12, 0.25);
    }

    .hero-right {
        width: 75%;
        position: relative;
    }

    .brand-name {
        font-family: 'Times New Roman', serif;
        color: #592c0c;
        font-size: 2.5rem;
        margin: 0;
    }

    .logo {
        width: 300px;
        height: 300px;
        border-radius: 50%;
        overflow: hidden;
    }

    .logo img {
        width: 100%;
        height: 100%;
        object-fit: cover;
    }

    .cards-section {
        min-height: 100vh;
        padding: 4rem 2rem;
        background-color: rgba(89, 44, 12, 0.12);
        position: relative;
    }

    .cards-container {
        max-width: 1200px;
        margin: 0 auto;
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
        gap: 2rem;
        padding: 1rem;
    }

    .card {
        background-color: #e6d5c3;
        border: 1px solid rgba(89, 44, 12, 0.35);
        border-radius: 8px;
        padding: 1.5rem;
        box-shadow: 
            0 4px 6px rgba(81, 47, 13, 0.2),
            inset 0 0 20px rgba(89, 44, 12, 0.1);
        transition: transform 0.2s ease;
    }

    .card:hover {
        transform: translateY(-5px);
    }

    .card h3 {
        color: #592c0c;
        margin-bottom: 1rem;
        font-family: 'Times New Roman', serif;
    }

    .card p {
        color: #3d2813;
        line-height: 1.6;
    }

    nav {
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        z-index: 1000;
        background-color: rgba(212, 192, 167, 0.95);
        border-bottom: 1px solid rgba(89, 44, 12, 0.35);
        box-shadow: 0 2px 4px rgba(81, 47, 13, 0.2);
        backdrop-filter: blur(5px);
    }

    .nav-content {
        max-width: 1200px;
        margin: 0 auto;
        padding: 1rem 2rem;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .nav-logo {
        font-family: 'Times New Roman', serif;
        font-size: 1.5rem;
        color: #592c0c;
        cursor: pointer;
    }

    .nav-links {
        display: flex;
        gap: 1.5rem;
    }

    .nav-links button {
        background: none;
        border: none;
        color: #592c0c;
        font-family: 'Times New Roman', serif;
        font-size: 1rem;
        cursor: pointer;
        padding: 0.5rem 1rem;
        transition: color 0.2s ease;
    }

    .nav-links button:hover {
        color: #3d2813;
    }

    .quotes-wrapper {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        overflow: hidden;
    }
</style>
