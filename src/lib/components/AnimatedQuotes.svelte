<script>
    import { onMount, onDestroy } from 'svelte';
    import Vara from 'vara';

    let quotes = [];
    let quoteQueue = [];
    let activeQuotes = new Set();
    let quotePositions = []; // Array to track all quote positions
    let quoteInterval;
    let mounted = false;

    // Size constraints
    const MIN_SIZE = 18;
    const MAX_SIZE = 26;
    
    // Maximum number of concurrent quotes
    const MAX_QUOTES = 5;

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

    function isOverlapping(newPos, newWidth, newHeight) {
        if (typeof document === 'undefined') return false;
        
        const containerWidth = window.innerWidth;
        const containerHeight = window.innerHeight;
        const newLeft = (newPos.left * containerWidth) / 100;
        const newTop = (newPos.top * containerHeight) / 100;
        
        // Check against all stored positions
        for (const pos of quotePositions) {
            const posLeft = (pos.left * containerWidth) / 100;
            const posTop = (pos.top * containerHeight) / 100;
            const buffer = 100; // Buffer for spacing between quotes
            
            if (!(newLeft + newWidth + buffer < posLeft ||
                  newLeft - buffer > posLeft + pos.width ||
                  newTop + newHeight + buffer < posTop ||
                  newTop - buffer > posTop + pos.height)) {
                return true;
            }
        }
        
        // Increase viewport buffer for safer margins
        const viewportBuffer = 50; // Increased from 30 to 50
        if (newLeft < viewportBuffer ||
            newLeft + newWidth > containerWidth - viewportBuffer ||
            newTop < viewportBuffer ||
            newTop + newHeight > containerHeight - viewportBuffer) {
            return true;
        }
        
        return false;
    }

    function getRandomPosition(estimatedWidth, estimatedHeight) {
        const maxAttempts = 100;
        let attempts = 0;
        
        const containerWidth = window.innerWidth;
        const containerHeight = window.innerHeight;
        
        while (attempts < maxAttempts) {
            // Adjust safe zones to heavily favor top positioning
            const safeZoneWidth = containerWidth * 0.7;    // 70% of width
            const safeZoneHeight = containerHeight * 0.4;  // Only 40% of height from top
            const offsetX = containerWidth * 0.05;         // Start 5% from left
            const offsetY = containerHeight * 0.02;        // Start 2% from top
            
            // Use square root for vertical position to bias towards top
            const verticalBias = Math.sqrt(Math.random());
            
            // Generate position with stronger top bias
            const left = (offsetX + Math.random() * safeZoneWidth) / containerWidth * 100;
            const top = (offsetY + verticalBias * safeZoneHeight) / containerHeight * 100;
            
            if (!isOverlapping({ left, top }, estimatedWidth, estimatedHeight)) {
                return { left, top, rotation: 0 };
            }
            
            attempts++;
        }
        
        // If no position found after max attempts, return null
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
        
        const containerWidth = window.innerWidth;
        const containerHeight = window.innerHeight;
        const estimatedWidth = Math.min(400, containerWidth * 0.25);
        const estimatedHeight = 150;
        
        const position = getRandomPosition(estimatedWidth, estimatedHeight);
        if (!position) {
            console.log('No safe position found for quote');
            return;
        }
        
        // Add position to tracked positions
        quotePositions.push({
            left: position.left,
            top: position.top,
            width: estimatedWidth,
            height: estimatedHeight
        });
        
        const containerId = `quote-${Math.random().toString(36).substr(2, 9)}`;
        
        try {
            if (typeof document !== 'undefined') {
                const container = document.createElement('div');
                container.className = 'floating-quote';
                container.id = containerId;
                
                container.style.left = `${position.left}%`;
                container.style.top = `${position.top}%`;
                container.style.transform = `rotate(${position.rotation}deg)`;
                container.style.padding = '20px';
                
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
                                fontSize: MIN_SIZE + Math.random() * (MAX_SIZE - MIN_SIZE),
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

    onMount(async () => {
        mounted = true;
        await fetchQuotes();
        
        // More time between initial quotes
        for(let i = 0; i < MAX_QUOTES; i++) {
            setTimeout(createQuote, i * 2500); // 2.5 second delay between each initial quote
        }
        
        // Longer interval for regular quotes
        setTimeout(() => {
            quoteInterval = setInterval(createQuote, 4000); // 4 second interval
        }, MAX_QUOTES * 2500);
    });

    onDestroy(() => {
        mounted = false;
        if (quoteInterval) clearInterval(quoteInterval);
        quotePositions = []; // Clear positions on destroy
        
        // Add browser environment check
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
        border: 2px solid rgba(255, 255, 255, 0.1);
        box-sizing: border-box;
        background: transparent;
    }

    :global(.floating-quote) {
        position: absolute;
        pointer-events: none;
        transition: opacity 2s ease-in-out;
        width: 25vw;
        max-width: 500px;
        min-height: 50px;
        margin: 20px;
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