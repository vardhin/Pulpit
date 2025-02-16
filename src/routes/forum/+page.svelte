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

  // Add these image collections at the top of your script
  const imageCollections = {
    scenery: [
      'https://images.pexels.com/photos/2014422/pexels-photo-2014422.jpeg',
      'https://images.pexels.com/photos/1287145/pexels-photo-1287145.jpeg',
      'https://images.pexels.com/photos/417074/pexels-photo-417074.jpeg',
      'https://images.pexels.com/photos/552789/pexels-photo-552789.jpeg',
      'https://images.pexels.com/photos/1287145/pexels-photo-1287145.jpeg'
    ]
  };

  let backgroundImage = '';

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

    // Load random background image
    const collection = imageCollections.scenery;
    const randomIndex = Math.floor(Math.random() * collection.length);
    backgroundImage = `url('${collection[randomIndex]}')`;
  });
</script>

<Loadingscreen {isLoading} />
<Navbar {isDarkMode} {toggleDarkMode} />

<div class="page-background" style="background-image: {backgroundImage}">
  <main>
    <div class="forum-container">
      <div class="forum-content">
        <header class="forum-header">
          <h1>Pulpit Forum</h1>
          <p>Join the conversation with fellow writers</p>
        </header>

        <div class="forum-messages">
          <!-- Add your forum messages here -->
          <div class="message">
            <div class="message-avatar">JD</div>
            <div class="message-content">
              <div class="message-header">
                <span class="message-author">John Doe</span>
                <span class="message-date">2 hours ago</span>
              </div>
              <p>Sample forum message...</p>
            </div>
          </div>
        </div>
      </div>

      <div class="forum-footer">
        {#each forumCategories as category}
          <button class="category-button">
            <div class="category-icon">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                {@html category.icon}
              </svg>
            </div>
            <span>{category.title}</span>
          </button>
        {/each}
      </div>
    </div>
  </main>
</div>

<style>
  .page-background {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    z-index: 0;
  }

  main {
    position: relative;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    padding: 0;
    z-index: 2;
  }

  .forum-container {
    width: 80%;
    height: 100vh;
    background: rgba(255, 255, 255, 0.15);
    backdrop-filter: blur(15px);
    -webkit-backdrop-filter: blur(15px);
    border-radius: 0;
    display: flex;
    flex-direction: column;
    box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
    border: 1px solid rgba(255, 255, 255, 0.18);
    margin: 0 auto;
    overflow-y: auto;
    padding-top: 64px;
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

  .forum-panel {
    background: rgba(255, 255, 255, 0.8);
    backdrop-filter: blur(10px) saturate(180%);
    -webkit-backdrop-filter: blur(10px) saturate(180%);
    border-radius: 1rem;
    margin-bottom: 1.5rem;
    box-shadow: 
      0 4px 6px -1px rgba(0, 0, 0, 0.1),
      0 2px 4px -1px rgba(0, 0, 0, 0.06),
      0 0 0 1px rgba(255, 255, 255, 0.1);
    border: 1px solid rgba(255, 255, 255, 0.2);
    transition: transform 0.2s, box-shadow 0.2s;
  }

  .forum-panel:hover {
    transform: translateY(-2px);
    box-shadow: 
      0 8px 12px -1px rgba(0, 0, 0, 0.15),
      0 4px 6px -1px rgba(0, 0, 0, 0.1),
      0 0 0 1px rgba(255, 255, 255, 0.2);
  }

  .forum-panel-header {
    display: flex;
    align-items: center;
    gap: 1rem;
    padding: 1.5rem;
    border-bottom: 1px solid rgba(0, 0, 0, 0.1);
  }

  .forum-messages {
    padding: 1rem;
  }

  .forum-message {
    display: flex;
    gap: 1rem;
    padding: 1rem;
    border-bottom: 1px solid rgba(0, 0, 0, 0.05);
  }

  .message-avatar {
    width: 40px;
    height: 40px;
    background: #4a5568;
    color: white;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 500;
  }

  .message-content {
    flex: 1;
  }

  .message-header {
    display: flex;
    justify-content: space-between;
    margin-bottom: 0.5rem;
  }

  .message-author {
    font-weight: 500;
    color: #2d3748;
  }

  .message-date {
    color: #718096;
    font-size: 0.9em;
  }

  .forum-panel-footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem 1.5rem;
    border-top: 1px solid rgba(0, 0, 0, 0.1);
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

  /* Dark mode adjustments */
  :global(body.dark-mode) .page-background::before {
    background: rgba(0, 0, 0, 0.7);
  }

  :global(body.dark-mode) main {
    background: rgba(0, 0, 0, 0.2);
  }

  :global(body.dark-mode) .forum-panel {
    background: rgba(26, 32, 44, 0.8);
    box-shadow: 
      0 4px 6px -1px rgba(0, 0, 0, 0.2),
      0 2px 4px -1px rgba(0, 0, 0, 0.15),
      0 0 0 1px rgba(255, 255, 255, 0.05);
  }

  :global(body.dark-mode) .forum-panel:hover {
    box-shadow: 
      0 8px 12px -1px rgba(0, 0, 0, 0.25),
      0 4px 6px -1px rgba(0, 0, 0, 0.2),
      0 0 0 1px rgba(255, 255, 255, 0.1);
  }

  :global(body.dark-mode) .message-author {
    color: #e2e8f0;
  }

  :global(body.dark-mode) .message-date {
    color: #a0aec0;
  }

  /* Responsive styles */
  @media (max-width: 768px) {
    .forum-header h1 {
      font-size: 2rem;
    }

    .forum-header p {
      font-size: 1rem;
    }

    .forum-panel {
      flex-direction: column;
    }

    .forum-panel-header {
      flex-direction: column;
      align-items: flex-start;
    }

    .forum-panel-header h2 {
      margin-top: 0.5rem;
    }

    .forum-messages {
      padding: 0.5rem;
    }

    .forum-message {
      flex-direction: column;
      align-items: flex-start;
    }

    .message-avatar {
      margin-right: 0;
    }

    .message-content {
      margin-top: 0.5rem;
    }

    .forum-panel-footer {
      flex-direction: column;
      align-items: flex-start;
    }

    .category-stats {
      margin-top: 0.5rem;
    }
  }

  .forum-content {
    flex: 1;
    padding: 2rem;
    overflow-y: auto;
  }

  .forum-footer {
    padding: 1rem;
    display: flex;
    gap: 1rem;
    justify-content: center;
    background: rgba(255, 255, 255, 0.1);
    border-top: 1px solid rgba(255, 255, 255, 0.2);
  }

  .category-button {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.75rem 1rem;
    background: rgba(255, 255, 255, 0.15);
    backdrop-filter: blur(5px);
    -webkit-backdrop-filter: blur(5px);
    border: 1px solid rgba(255, 255, 255, 0.18);
    border-radius: 0.5rem;
    color: white;
    cursor: pointer;
    transition: all 0.2s;
    white-space: nowrap;
  }

  .category-button:hover {
    background: rgba(255, 255, 255, 0.25);
    transform: translateY(-2px);
  }

  /* Dark mode adjustments */
  :global(body.dark-mode) .forum-container {
    background: rgba(0, 0, 0, 0.25);
  }

  :global(body.dark-mode) .category-button {
    background: rgba(0, 0, 0, 0.2);
  }

  :global(body.dark-mode) .category-button:hover {
    background: rgba(0, 0, 0, 0.3);
  }
</style>
