<script>
    import { onMount, onDestroy } from 'svelte';
    import Vara from 'vara';

    let quotes = [];
    let quoteQueue = [];
    let activeQuotes = new Set();
    let quotePositions = []; // Array to track all quote positions
    let quoteInterval;
    let mounted = false;
    let MIN_SIZE = 0.8;  // Default values
    let MAX_SIZE = 1.4;

    // Maximum number of concurrent quotes
    const MAX_QUOTES = 2;

    // Define safe zones for quote placement
    const MARGIN = 1; // Reduced from 10 to 5 to allow quotes to spread more

    // Add this constant at the top of the script
    const LITERARY_QUOTES = [
        { text: "It was the best of times, it was the worst of times", author: "A Tale of Two Cities" },
        { text: "All that we see or seem is but a dream within a dream", author: "A Dream Within a Dream" },
        { text: "Not all those who wander are lost", author: "The Fellowship of the Ring" },
        { text: "To be, or not to be, that is the question", author: "Hamlet" },
        { text: "It is a truth universally acknowledged", author: "Pride and Prejudice" },
        { text: "Call me Ishmael", author: "Moby Dick" },
        { text: "So we beat on, boats against the current", author: "The Great Gatsby" },
        { text: "Whatever our souls are made of, his and mine are the same", author: "Wuthering Heights" },
        { text: "All happy families are alike; each unhappy family is unhappy in its own way", author: "Anna Karenina" },
        { text: "It does not do to dwell on dreams and forget to live", author: "Harry Potter and the Sorcerer's Stone" },
        { text: "I am no bird; and no net ensnares me", author: "Jane Eyre" },
        { text: "The world breaks everyone, and afterward, many are strong at the broken places", author: "A Farewell to Arms" },
        { text: "Time is not a line but a dimension", author: "Cat's Eye" },
        { text: "Everything was beautiful, and nothing hurt", author: "Slaughterhouse-Five" },
        { text: "We accept the love we think we deserve", author: "The Perks of Being a Wallflower" },
        { text: "It's the possibility of having a dream come true that makes life interesting", author: "The Alchemist" },
        { text: "There are years that ask questions and years that answer", author: "Their Eyes Were Watching God" },
        { text: "The past is not dead. It's not even past", author: "Requiem for a Nun" },
        { text: "Tomorrow is always fresh, with no mistakes in it yet", author: "Anne of Green Gables" },
        { text: "And, now that you don't have to be perfect, you can be good", author: "East of Eden" },
        { text: "All we have to decide is what to do with the time that is given us", author: "The Fellowship of the Ring" },
        { text: "I took a deep breath and listened to the old brag of my heart: I am, I am, I am", author: "The Bell Jar" },
        { text: "We dream in our waking moments, and walk in our sleep", author: "The Scarlet Letter" },
        { text: "Life appears to me too short to be spent in nursing animosity or registering wrongs", author: "Jane Eyre" },
        { text: "There are darknesses in life and there are lights", author: "Dracula" },
        { text: "It matters not what someone is born, but what they grow to be", author: "Harry Potter and the Goblet of Fire" },
        { text: "The only way out of the labyrinth of suffering is to forgive", author: "Looking for Alaska" },
        { text: "We were the people who were not in the papers", author: "The Handmaid's Tale" },
        { text: "Time moves slowly, but passes quickly", author: "The Color Purple" },
        { text: "Nothing is more human than the will to survive", author: "The Road" }
    ];

    async function fetchQuotes() {
        // Replace API call with local quotes
        const shuffled = [...LITERARY_QUOTES]
            .sort(() => Math.random() - 0.5)
            .slice(0, 10)
            .map(quote => ({
                text: `${quote.text}`,
                author: quote.author
            }));
        quoteQueue = [...quoteQueue, ...shuffled];
    }

    function calculateQuoteDimensions(text, fontSize) {
        // Base size calculation with bounds
        const baseSize = 16; // Base font size in pixels
        const actualFontSize = Math.min(
            Math.max(baseSize * 0.8, fontSize * window.innerWidth / 100),
            baseSize * 2
        );
        const charsPerLine = 40;
        const lines = Math.ceil(text.length / charsPerLine);
        const width = Math.min(400, window.innerWidth * 0.25);
        const height = (lines * actualFontSize * 1.5) + 40;
        return { width, height };
    }

    function isOverlapping(newPosition, newDimensions, safeZoneBounds) {
        // Calculate buffer as 10% of the smaller safe zone dimension
        const bufferSize = Math.min(safeZoneBounds.width, safeZoneBounds.height) * 0.1;
        
        // Check safe zone boundaries with buffer
        if (newPosition.left < bufferSize ||
            newPosition.left + newDimensions.width > safeZoneBounds.width - bufferSize ||
            newPosition.top < bufferSize ||
            newPosition.top + newDimensions.height > safeZoneBounds.height - bufferSize) {
            return true;
        }

        // Check overlap with existing quotes
        return quotePositions.some(existingQuote => {
            // Calculate the minimum required spacing between quotes
            const requiredSpacing = bufferSize;

            // Check if quotes are too close horizontally
            const horizontalOverlap = Math.abs(
                (newPosition.left + newDimensions.width / 2) - 
                (existingQuote.left + existingQuote.width / 2)
            ) < (newDimensions.width / 2 + existingQuote.width / 2 + requiredSpacing);

            // Check if quotes are too close vertically
            const verticalOverlap = Math.abs(
                (newPosition.top + newDimensions.height / 2) - 
                (existingQuote.top + existingQuote.height / 2)
            ) < (newDimensions.height / 2 + existingQuote.height / 2 + requiredSpacing);

            return horizontalOverlap && verticalOverlap;
        });
    }

    function findSafePosition(dimensions) {
        const safeZone = document.querySelector('.quote-container');
        if (!safeZone) return null;

        const safeZoneBounds = safeZone.getBoundingClientRect();
        const maxAttempts = 150; // Increased attempts for better distribution
        const bufferSize = Math.min(safeZoneBounds.width, safeZoneBounds.height) * 0.1;

        // Create a grid system for more organized placement
        const gridSize = Math.max(dimensions.width, dimensions.height) + bufferSize * 2;
        const cols = Math.floor(safeZoneBounds.width / gridSize);
        const rows = Math.floor(safeZoneBounds.height / gridSize);

        // Try grid-aligned positions first
        for (let attempt = 0; attempt < maxAttempts; attempt++) {
            let position;
            
            if (attempt < (cols * rows)) {
                // Try grid-aligned positions first
                const col = attempt % cols;
                const row = Math.floor(attempt / cols);
                position = {
                    left: col * gridSize + bufferSize,
                    top: row * gridSize + bufferSize
                };
            } else {
                // Fall back to random positions if grid positions are full
                position = {
                    left: bufferSize + Math.random() * (safeZoneBounds.width - dimensions.width - 2 * bufferSize),
                    top: bufferSize + Math.random() * (safeZoneBounds.height - dimensions.height - 2 * bufferSize)
                };
            }

            if (!isOverlapping(position, dimensions, safeZoneBounds)) {
                return position;
            }
        }
        
        return null;
    }

    async function createQuote() {
        if (!mounted || typeof window === 'undefined') return;
        
        const fontUrl = "https://raw.githubusercontent.com/akzhy/Vara/master/fonts/Pacifico/PacificoSLO.json";
        
        if (typeof Vara !== 'function') {
            console.error('Vara is not loaded properly');
            return;
        }
        
        if (quoteQueue.length < 10) {
            await fetchQuotes();
        }
        
        if (quoteQueue.length === 0 || activeQuotes.size >= MAX_QUOTES) return;
        
        const quote = quoteQueue[0];
        quoteQueue = quoteQueue.slice(1);
        
        const fontSize = MIN_SIZE + Math.random() * (MAX_SIZE - MIN_SIZE);
        const dimensions = calculateQuoteDimensions(quote.text, fontSize);
        
        const position = findSafePosition(dimensions);
        if (!position) {
            console.log('No safe position found for quote');
            return;
        }

        // Store quote position with exact dimensions
        const quoteData = {
            left: position.left,
            top: position.top,
            width: dimensions.width,
            height: dimensions.height
        };
        quotePositions = [...quotePositions, quoteData];
        
        const containerId = `quote-${Math.random().toString(36).substr(2, 9)}`;
        
        try {
            if (typeof document !== 'undefined') {
                const container = document.createElement('div');
                container.className = 'floating-quote';
                container.id = containerId;
                
                // Position the quote using the calculated safe position
                container.style.left = `${position.left}px`;
                container.style.top = `${position.top}px`;
                container.style.width = `${dimensions.width}px`;
                container.style.minHeight = `${dimensions.height}px`;
                
                const quoteContainer = document.querySelector('.quote-container');
                if (!quoteContainer) {
                    console.error('Quote container not found');
                    return;
                }
                
                quoteContainer.appendChild(container);

                try {
                    const vara = new Vara(
                        `#${containerId}`,
                        fontUrl,
                        [
                            {
                                text: `${quote.text}
                                -${quote.author}`,
                                // Convert vw to pixels for Vara
                                fontSize: (MIN_SIZE + Math.random() * (MAX_SIZE - MIN_SIZE)) * window.innerWidth / 100,
                                strokeWidth: 1.5,
                                color: 'var(--text-primary)',
                                duration: 3000,
                                textAlign: 'left',
                                letterSpacing: 1
                            }
                        ],
                        {
                            ready: () => console.log('Vara ready for', containerId)
                        }
                    );
                } catch (varaError) {
                    console.error('Vara initialization error:', varaError);
                    return;
                }

                activeQuotes.add(containerId);

                const timeout1 = setTimeout(() => {
                    if (!mounted) return;
                    container.classList.add('fade-out');
                    const timeout2 = setTimeout(() => {
                        if (!mounted) return;
                        // Remove position from tracked positions when quote is removed
                        quotePositions = quotePositions.filter(pos => 
                            pos.left !== position.left || pos.top !== position.top
                        );
                        container.remove();
                        activeQuotes.delete(containerId);
                    }, 2000);
                    container.dataset.timeout2 = timeout2;
                }, 8000 + (quote.text.length * 50));
                container.dataset.timeout1 = timeout1;
            }
        } catch (error) {
            console.error('Error creating quote:', error);
            // Remove position if quote creation fails
            quotePositions = quotePositions.filter(pos => 
                pos.left !== position.left || pos.top !== position.top
            );
        }
    }

    onMount(() => {
        // Initialize window-dependent values after mounting
        MIN_SIZE = Math.min(0.8, window.innerWidth * 0.008);
        MAX_SIZE = Math.min(1.4, window.innerWidth * 0.012);
        mounted = true;
        
        // Rest of onMount logic
        fetchQuotes().then(() => {
            for(let i = 0; i < MAX_QUOTES; i++) {
                setTimeout(createQuote, i * 2500);
            }
            
            setTimeout(() => {
                quoteInterval = setInterval(createQuote, 4000);
            }, MAX_QUOTES * 2500);
        });
    });

    onDestroy(() => {
        mounted = false;
        if (quoteInterval) clearInterval(quoteInterval);
        quotePositions = []; // Clear positions on destroy
        
        if (typeof document !== 'undefined') {
            const container = document.querySelector('.quote-container');
            if (container) {
                const quotes = container.querySelectorAll('.floating-quote');
                quotes.forEach(quote => {
                    if (quote.dataset.timeout1) clearTimeout(Number(quote.dataset.timeout1));
                    if (quote.dataset.timeout2) clearTimeout(Number(quote.dataset.timeout2));
                    quote.remove();
                });
            }
        }
    });

    $: if (activeQuotes.size >= MAX_QUOTES) {
        // Temporarily pause quote creation if max quotes reached
        if (quoteInterval) clearInterval(quoteInterval);
    } else if (mounted && !quoteInterval) {
        // Resume quote creation
        quoteInterval = setInterval(createQuote, 800);
    }
</script>

<div class="quote-container">
    <!-- Debug info div removed -->
</div>

<style>
    .quote-container {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        pointer-events: none;
        z-index: 1;
        overflow: hidden;
        box-sizing: border-box;
        background: transparent;
    }

    :global(.floating-quote) {
        position: absolute;
        pointer-events: none;
        transition: opacity 2s ease-in-out;
        opacity: 1;
        background: transparent;
        overflow: visible;
        padding: 10px;
    }

    :global(.floating-quote.fade-out) {
        opacity: 0;
    }

    :global(.floating-quote svg) {
        filter: drop-shadow(0 0 3px var(--quote-shadow));
        overflow: visible;
        font-family: 'Times New Roman', serif;
        margin: 5px;
    }

    :global(.floating-quote:hover svg) {
        transform: none;
    }

    /* Add CSS variables for quote styling */
    :root {
        --text-primary: #191D32;
        --text-secondary: rgba(25, 29, 50, 0.85);
        --quote-shadow: rgba(2, 103, 193, 0.2);
    }

    :global(body.dark-mode) {
        --text-primary: #EEEEEE;
        --text-secondary: rgba(238, 238, 238, 0.85);
        --quote-shadow: rgba(2, 103, 193, 0.3);
    }
</style>