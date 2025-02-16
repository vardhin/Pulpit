<script>
  import { onMount } from 'svelte';
  
  // Initialize dark mode from localStorage
  let isDarkMode = false;
  let isLoading = true;

  // Sample article data (replace with actual data)
  let article = {
    title: "The Art of Storytelling",
    author: "Jane Doe",
    date: "April 15, 2024",
    content: `
      <h2>Introduction</h2>
      <p>The art of storytelling is as old as human civilization itself. From ancient cave paintings to modern digital media, humans have always found ways to share their stories.</p>
      
      <h2>The Elements of a Good Story</h2>
      <p>Every compelling story contains certain key elements. Character development, plot structure, and thematic depth work together to create an engaging narrative that resonates with readers.</p>
      
      <h3>Character Development</h3>
      <p>Characters are the heart of any story. They must be believable, relatable, and complex enough to sustain reader interest throughout the narrative journey.</p>
      
      <h3>Plot Structure</h3>
      <p>A well-crafted plot moves seamlessly from beginning to end, maintaining tension and reader engagement while providing satisfying resolution to central conflicts.</p>
      
      <blockquote>
        "The first draft is just you telling yourself the story."
        <footer>- Terry Pratchett</footer>
      </blockquote>
    `
  };

  // Progress bar calculation
  let progress = 0;
  let articleContainer;

  function updateReadingProgress() {
    if (!articleContainer) return;
    
    const totalHeight = articleContainer.scrollHeight - articleContainer.clientHeight;
    const currentPosition = articleContainer.scrollTop;
    progress = (currentPosition / totalHeight) * 100;
  }

  onMount(() => {
    // Load dark mode preference from localStorage
    const storedDarkMode = localStorage.getItem('darkMode');
    isDarkMode = storedDarkMode === 'true';
    if (isDarkMode) {
      document.body.classList.add('dark-mode');
    }
    
    // Remove loading state after a short delay
    setTimeout(() => {
      isLoading = false;
    }, 500);
  });
</script>

{#if isLoading}
  <div class="loading-overlay" class:fade-out={!isLoading}>
    <div class="loading-content">
      <div class="loading-text">Reader</div>
    </div>
  </div>
{/if}

<main>
  <div class="progress-bar" style="width: {progress}%"></div>
  
  <div class="reader-container">
    <header class="article-header">
      <h1>{article.title}</h1>
      <div class="article-meta">
        <span class="author">By {article.author}</span>
        <span class="date">{article.date}</span>
      </div>
    </header>

    <div 
      class="article-content" 
      bind:this={articleContainer}
      on:scroll={updateReadingProgress}
    >
      {@html article.content}
    </div>
  </div>
</main>

<style>
  main {
    min-height: 100vh;
    background-color: rgba(2, 83, 153, 0.95);
    backdrop-filter: blur(15px) saturate(150%);
    -webkit-backdrop-filter: blur(15px) saturate(150%);
    position: relative;
    overflow: hidden;
    padding: 2rem;
    padding-top: 5rem;
  }

  /* Paper texture and ruling */
  main::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-image: 
      repeating-linear-gradient(
        0deg,
        transparent,
        transparent 24px,
        rgba(0, 20, 40, 0.2) 24px,
        rgba(0, 20, 40, 0.2) 25px
      ),
      url("data:image/svg+xml,%3Csvg viewBox='0 0 400 400' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noiseFilter'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noiseFilter)'/%3E%3C/svg%3E");
    background-size: 100% 25px, 150px 150px;
    opacity: 0.5;
    z-index: 0;
    mix-blend-mode: multiply;
  }

  .progress-bar {
    position: fixed;
    top: 0;
    left: 0;
    height: 3px;
    background: linear-gradient(90deg, #025399, #0077CC);
    transition: width 0.3s ease;
    z-index: 1000;
  }

  .reader-container {
    position: relative;
    z-index: 1;
    max-width: 800px;
    margin: 0 auto;
    background: rgba(255, 255, 255, 0.95);
    border-radius: 1rem;
    padding: 3rem;
    box-shadow: 
      0 8px 32px rgba(0, 0, 0, 0.1),
      inset 0 0 0 1px rgba(255, 255, 255, 0.5);
  }

  .article-header {
    text-align: center;
    margin-bottom: 3rem;
    padding-bottom: 2rem;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  }

  .article-header h1 {
    font-family: 'Quicksand', sans-serif;
    font-size: 2.5rem;
    font-weight: 600;
    margin: 0;
    margin-bottom: 1rem;
    color: #191D32;
  }

  .article-meta {
    display: flex;
    justify-content: center;
    gap: 1rem;
    color: #4A5568;
    font-size: 0.9rem;
  }

  .article-content {
    max-height: 70vh;
    overflow-y: auto;
    padding-right: 1rem;
    line-height: 1.8;
    color: #2D3748;
  }

  /* Custom scrollbar */
  .article-content::-webkit-scrollbar {
    width: 8px;
  }

  .article-content::-webkit-scrollbar-track {
    background: rgba(0, 0, 0, 0.05);
    border-radius: 4px;
  }

  .article-content::-webkit-scrollbar-thumb {
    background: rgba(2, 83, 153, 0.3);
    border-radius: 4px;
  }

  .article-content::-webkit-scrollbar-thumb:hover {
    background: rgba(2, 83, 153, 0.5);
  }

  .article-content h2 {
    font-family: 'Quicksand', sans-serif;
    font-size: 1.8rem;
    font-weight: 600;
    color: #191D32;
    margin: 2rem 0 1rem;
  }

  .article-content h3 {
    font-family: 'Quicksand', sans-serif;
    font-size: 1.4rem;
    font-weight: 500;
    color: #2D3748;
    margin: 1.5rem 0 1rem;
  }

  .article-content p {
    margin: 1rem 0;
    font-size: 1.1rem;
  }

  .article-content blockquote {
    margin: 2rem 0;
    padding: 1rem 2rem;
    border-left: 4px solid #025399;
    background: rgba(2, 83, 153, 0.05);
    font-style: italic;
  }

  .article-content blockquote footer {
    margin-top: 0.5rem;
    font-size: 0.9rem;
    color: #4A5568;
  }

  /* Dark mode styles */
  :global(body.dark-mode) main {
    background-color: rgba(1, 48, 91, 0.95);
  }

  :global(body.dark-mode) main::before {
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

  :global(body.dark-mode) .reader-container {
    background: rgba(26, 32, 44, 0.95);
    border-color: rgba(255, 255, 255, 0.1);
  }

  :global(body.dark-mode) .article-header h1 {
    color: #E2E8F0;
  }

  :global(body.dark-mode) .article-meta {
    color: #A0AEC0;
  }

  :global(body.dark-mode) .article-content {
    color: #E2E8F0;
  }

  :global(body.dark-mode) .article-content h2,
  :global(body.dark-mode) .article-content h3 {
    color: #E2E8F0;
  }

  :global(body.dark-mode) .article-content blockquote {
    background: rgba(255, 255, 255, 0.05);
    border-left-color: #90CDF4;
  }

  :global(body.dark-mode) .article-content blockquote footer {
    color: #A0AEC0;
  }

  /* Loading animation styles */
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

  /* Responsive styles */
  @media (max-width: 768px) {
    .reader-container {
      padding: 2rem;
      margin: 1rem;
    }

    .article-header h1 {
      font-size: 2rem;
    }

    .article-content {
      font-size: 1rem;
    }

    .loading-text {
      font-size: 3rem;
    }
  }
</style>
