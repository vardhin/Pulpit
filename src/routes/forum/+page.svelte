<script>
  import { onMount } from 'svelte';
  import Navbar from '$lib/components/Navbar.svelte';
  import Loadingscreen from '$lib/components/Loadingscreen.svelte';
  
  // Initialize dark mode from localStorage
  let isDarkMode = false;
  let isLoading = true;

  // Sample forum data (replace with actual data from your backend)
  let forumCategories = [
    {
      id: 1,
      title: "Writing Tips & Techniques",
      description: "Share and discuss writing strategies, tips, and best practices",
      topics: 156,
      posts: 1243,
      icon: `<path d="M12 19l7-7 3 3-7 7-3-3z"></path><path d="M18 13l-1.5-7.5L2 2l3.5 14.5L13 18l5-5z"></path>`
    },
    {
      id: 2,
      title: "Creative Writing",
      description: "Share your stories, poems, and creative pieces",
      topics: 234,
      posts: 2156,
      icon: `<path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path><polyline points="14 2 14 8 20 8"></polyline><line x1="16" y1="13" x2="8" y2="13"></line><line x1="16" y1="17" x2="8" y2="17"></line><polyline points="10 9 9 9 8 9"></polyline>`
    },
    {
      id: 3,
      title: "Writing Resources",
      description: "Find and share useful writing resources and tools",
      topics: 89,
      posts: 567,
      icon: `<path d="M4 19.5A2.5 2.5 0 0 1 6.5 17H20"></path><path d="M6.5 2H20v20H6.5A2.5 2.5 0 0 1 4 19.5v-15A2.5 2.5 0 0 1 6.5 2z"></path>`
    }
  ];

  function toggleDarkMode() {
    isDarkMode = !isDarkMode;
    localStorage.setItem('darkMode', isDarkMode);
    if (isDarkMode) {
      document.body.classList.add('dark-mode');
    } else {
      document.body.classList.remove('dark-mode');
    }
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

<Loadingscreen {isLoading} />
<Navbar {isDarkMode} {toggleDarkMode} />

<main>
  <div class="forum-container">
    <header class="forum-header">
      <h1>Pulpit Forum</h1>
      <p>Join the conversation with fellow writers</p>
    </header>

    <section class="categories">
      {#each forumCategories as category}
        <div class="category-card">
          <div class="category-icon">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              {@html category.icon}
            </svg>
          </div>
          <div class="category-content">
            <h2>{category.title}</h2>
            <p>{category.description}</p>
            <div class="category-stats">
              <span>{category.topics} Topics</span>
              <span>{category.posts} Posts</span>
            </div>
          </div>
          <div class="category-arrow">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <polyline points="9 18 15 12 9 6"></polyline>
            </svg>
          </div>
        </div>
      {/each}
    </section>
  </div>
</main>

<style>
  /* Reuse font declarations from landing page */
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

  /* Paper texture and ruling for main */
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

  .forum-container {
    position: relative;
    z-index: 1;
    max-width: 1200px;
    margin: 0 auto;
  }

  .forum-header {
    text-align: center;
    color: white;
    margin-bottom: 3rem;
  }

  .forum-header h1 {
    font-family: 'Quicksand', sans-serif;
    font-size: 2.5rem;
    font-weight: 600;
    margin: 0;
    margin-bottom: 0.5rem;
  }

  .forum-header p {
    font-size: 1.1rem;
    opacity: 0.9;
    margin: 0;
  }

  .categories {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 1.5rem;
    padding: 1rem;
  }

  .category-card {
    background: rgba(255, 255, 255, 0.95);
    border-radius: 1rem;
    padding: 1.5rem;
    display: flex;
    align-items: center;
    gap: 1.5rem;
    transition: all 0.3s ease;
    cursor: pointer;
    border: 1px solid rgba(255, 255, 255, 0.3);
    box-shadow: 
      0 8px 32px rgba(0, 0, 0, 0.1),
      inset 0 0 0 1px rgba(255, 255, 255, 0.5);
  }

  .category-card:hover {
    transform: translateY(-5px);
    box-shadow: 
      0 12px 48px rgba(0, 0, 0, 0.15),
      inset 0 0 0 1px rgba(255, 255, 255, 0.6);
  }

  .category-icon {
    width: 48px;
    height: 48px;
    background: rgba(2, 83, 153, 0.1);
    border-radius: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: #025399;
    transition: all 0.3s ease;
    flex-shrink: 0;
  }

  .category-card:hover .category-icon {
    background: #025399;
    color: white;
    transform: scale(1.1);
  }

  .category-content {
    flex: 1;
  }

  .category-content h2 {
    color: #191D32;
    margin: 0;
    font-family: 'Quicksand', sans-serif;
    font-size: 1.25rem;
    font-weight: 600;
  }

  .category-content p {
    color: #4A5568;
    margin: 0.5rem 0;
    font-size: 0.9rem;
    line-height: 1.4;
  }

  .category-stats {
    display: flex;
    gap: 1rem;
    font-size: 0.8rem;
    color: #718096;
  }

  .category-arrow {
    color: #025399;
    opacity: 0;
    transform: translateX(-10px);
    transition: all 0.3s ease;
  }

  .category-card:hover .category-arrow {
    opacity: 1;
    transform: translateX(0);
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

  :global(body.dark-mode) .category-card {
    background: rgba(26, 32, 44, 0.95);
    border-color: rgba(255, 255, 255, 0.1);
  }

  :global(body.dark-mode) .category-content h2 {
    color: #E2E8F0;
  }

  :global(body.dark-mode) .category-content p {
    color: #A0AEC0;
  }

  :global(body.dark-mode) .category-stats {
    color: #718096;
  }

  :global(body.dark-mode) .category-icon {
    background: rgba(255, 255, 255, 0.1);
    color: #E2E8F0;
  }

  :global(body.dark-mode) .category-card:hover .category-icon {
    background: #E2E8F0;
    color: #1A202C;
  }

  :global(body.dark-mode) .category-arrow {
    color: #90CDF4;
  }

  /* Responsive styles */
  @media (max-width: 768px) {
    .forum-header h1 {
      font-size: 2rem;
    }

    .forum-header p {
      font-size: 1rem;
    }

    .categories {
      grid-template-columns: 1fr;
    }
  }
</style>
