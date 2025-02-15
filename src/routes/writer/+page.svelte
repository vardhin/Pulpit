<script>
    import { browser } from '$app/environment';
    import { onMount } from 'svelte';
    import { page } from '$app/stores';

    let content = '';
    let wordCount = 0;
    let backgroundImage = '';
    let imageType = 'scenery'; // default value
    let customSearch = '';
    let isFullscreen = false;
    let textColor = '#1a202c'; // default dark color
    let isControlsExpanded = false;
    let isGenerating = false;
    let apiKey = 'AIzaSyDmUL1q-p4HcDrp3UUih6PANUbMK7xCXok'; // You'll need to set this to your Gemini API key
    let isPromptBarOpen = false;
    let promptInstructions = '';
    let fontSize = 1.2; // default size in rem

    // Add new dark mode state
    let isDarkMode = false;

    // Add new file management state variables
    let isFileManagerOpen = false;
    let files = [];
    let currentFileName = 'untitled.txt';

    // Curated collections of high-quality images
    const imageCollections = {
        scenery: [
            'https://images.pexels.com/photos/2014422/pexels-photo-2014422.jpeg',
            'https://images.pexels.com/photos/1287145/pexels-photo-1287145.jpeg',
            'https://images.pexels.com/photos/417074/pexels-photo-417074.jpeg',
            'https://images.pexels.com/photos/552789/pexels-photo-552789.jpeg',
            'https://images.pexels.com/photos/1287145/pexels-photo-1287145.jpeg'
        ],
        nature: [
            'https://images.pexels.com/photos/3225517/pexels-photo-3225517.jpeg',
            'https://images.pexels.com/photos/1770809/pexels-photo-1770809.jpeg',
            'https://images.pexels.com/photos/1761279/pexels-photo-1761279.jpeg',
            'https://images.pexels.com/photos/1766838/pexels-photo-1766838.jpeg',
            'https://images.pexels.com/photos/3225529/pexels-photo-3225529.jpeg'
        ],
        architecture: [
            'https://images.pexels.com/photos/256150/pexels-photo-256150.jpeg',
            'https://images.pexels.com/photos/1732414/pexels-photo-1732414.jpeg',
            'https://images.pexels.com/photos/1769384/pexels-photo-1769384.jpeg',
            'https://images.pexels.com/photos/1732414/pexels-photo-1732414.jpeg',
            'https://images.pexels.com/photos/1732414/pexels-photo-1732414.jpeg'
        ]
    };

    const imageTypes = [
        { value: 'scenery', label: 'Scenery' },
        { value: 'nature', label: 'Nature' },
        { value: 'architecture', label: 'Architecture' }
    ];

    const fonts = [
        // Regular fonts
        { family: 'Roboto', label: 'Roboto' },
        { family: 'Open Sans', label: 'Open Sans' },
        { family: 'Lato', label: 'Lato' },
        { family: 'Montserrat', label: 'Montserrat' },
        { family: 'Poppins', label: 'Poppins' },
        { family: 'Playfair Display', label: 'Playfair Display' },
        { family: 'Merriweather', label: 'Merriweather' },
        { family: 'Ubuntu', label: 'Ubuntu' },
        { family: 'Raleway', label: 'Raleway' },
        { family: 'Source Sans Pro', label: 'Source Sans Pro' },
        { family: 'Quicksand', label: 'Quicksand' },
        { family: 'Josefin Sans', label: 'Josefin Sans' },
        { family: 'Comfortaa', label: 'Comfortaa' },
        { family: 'Righteous', label: 'Righteous' },
        { family: 'Abril Fatface', label: 'Abril Fatface' },
        { family: 'Lobster', label: 'Lobster' },
        { family: 'Great Vibes', label: 'Great Vibes' },
        { family: 'Sacramento', label: 'Sacramento' },
        { family: 'Parisienne', label: 'Parisienne' },
        { family: 'Cinzel', label: 'Cinzel' },
        // Handwriting fonts
        { family: 'Caveat', label: 'Caveat (Handwriting)' },
        { family: 'Dancing Script', label: 'Dancing Script (Handwriting)' },
        { family: 'Pacifico', label: 'Pacifico (Handwriting)' },
        { family: 'Indie Flower', label: 'Indie Flower (Handwriting)' },
        { family: 'Shadows Into Light', label: 'Shadows Into Light (Handwriting)' },
        { family: 'Permanent Marker', label: 'Permanent Marker (Handwriting)' },
        { family: 'Satisfy', label: 'Satisfy (Handwriting)' },
        { family: 'Kalam', label: 'Kalam (Handwriting)' },
        { family: 'Homemade Apple', label: 'Homemade Apple (Handwriting)' },
        { family: 'Patrick Hand', label: 'Patrick Hand (Handwriting)' }
    ];
    
    let selectedFont = fonts[0].family;
    
    // Add undo/redo history
    let history = [''];
    let currentIndex = 0;

    // Add new chat-related state variables
    let chatPanelOpen = false;
    let chatMessage = '';
    let chatHistory = [];
    let isChatGenerating = false;

    // Add marked.js for markdown parsing
    import { marked } from 'marked';

    // Add new state variable for preview mode
    let isPreviewMode = false;

    // Add new state variables for the circular menu
    let isCircularMenuOpen = false;
    let circularMenuPosition = { x: 0, y: 0 };

    // Add new keyboard shortcut handler
    function handleKeyboardShortcut(event) {
        // Alt + C to toggle menu (changed from Space)
        if (event.altKey && event.code === 'KeyC') {
            event.preventDefault();
            isCircularMenuOpen = !isCircularMenuOpen;
            if (isCircularMenuOpen) {
                // Center the menu in the viewport
                circularMenuPosition = {
                    x: window.innerWidth / 2,
                    y: window.innerHeight / 2
                };
            }
        }
    }

    // Add double-right-click handler
    let lastClickTime = 0;
    function handleContextMenu(event) {
        const currentTime = new Date().getTime();
        const timeDiff = currentTime - lastClickTime;
        
        if (timeDiff < 300) { // Double click threshold
            // Handle double right-click for circular menu
            event.preventDefault();
            isCircularMenuOpen = !isCircularMenuOpen;
            if (isCircularMenuOpen) {
                circularMenuPosition = { x: event.clientX, y: event.clientY };
            }
            isContextMenuOpen = false; // Ensure context menu is closed
        } else {
            // Handle single right-click for context menu
            event.preventDefault();
            selectedText = window.getSelection().toString();
            contextMenuPosition = {
                x: event.clientX,
                y: event.clientY
            };
            isContextMenuOpen = true;
            isCircularMenuOpen = false; // Ensure circular menu is closed
        }
        
        lastClickTime = currentTime;
    }

    // Add long press handler for mobile/touch devices
    let pressTimer;
    function handleTouchStart(event) {
        pressTimer = setTimeout(() => {
            isCircularMenuOpen = !isCircularMenuOpen;
            if (isCircularMenuOpen) {
                const touch = event.touches[0];
                circularMenuPosition = { x: touch.clientX, y: touch.clientY };
            }
        }, 500); // 500ms long press
    }

    function handleTouchEnd() {
        clearTimeout(pressTimer);
    }

    // Update the existing handleMiddleClick function
    function handleMiddleClick(event) {
        if (event.button === 1) { // Middle mouse button
            event.preventDefault();
            isCircularMenuOpen = !isCircularMenuOpen;
            if (isCircularMenuOpen) {
                circularMenuPosition = { x: event.clientX, y: event.clientY };
            }
        }
    }

    function loadBackgroundImage() {
        try {
            if (!imageCollections[imageType]) {
                return;
            }
            const collection = imageCollections[imageType];
            const randomIndex = Math.floor(Math.random() * collection.length);
            const imageUrl = collection[randomIndex];
            backgroundImage = `url('${imageUrl}')`;
            console.log('Background image URL:', imageUrl);
        } catch (error) {
            console.error('Failed to load background image:', error);
        }
    }

    function loadFont(fontFamily) {
        if (!browser) return;
        
        // Update the existing link or create a new one
        let link = document.querySelector(`link[href*="${fontFamily}"]`);
        if (!link) {
            link = document.createElement('link');
            link.rel = 'stylesheet';
            document.head.appendChild(link);
        }
        link.href = `https://fonts.googleapis.com/css2?family=${fontFamily.replace(' ', '+')}:wght@300;400;500;600;700&display=swap`;
    }

    // Modify the onMount function
    onMount(async () => {
        if (browser) {
            try {
                // Add Google Fonts link dynamically first
                const link = document.createElement('link');
                link.href = 'https://fonts.googleapis.com/css2?family=Quicksand:wght@300;400;500;600;700&display=swap';
                link.rel = 'stylesheet';
                document.head.appendChild(link);

                // Then wait for fonts to load
                await document.fonts.ready;
                
                // Now load other fonts and initialize
                loadFont(selectedFont);
                loadBackgroundImage();
                
                // Check dark mode preference
                const storedDarkMode = localStorage.getItem('darkMode') === 'true';
                isDarkMode = storedDarkMode;
                if (storedDarkMode) {
                    document.body.classList.add('dark-mode');
                }
                
                loadFiles();
                
                // Remove loading screen after everything is ready
                setTimeout(() => {
                    isLoading = false;
                }, 500);
            } catch (error) {
                console.error('Failed to initialize:', error);
                // Fallback - remove loading screen even if initialization fails
                isLoading = false;
            }
        }
    });

    function updateWordCount() {
        wordCount = content.trim() ? content.trim().split(/\s+/).length : 0;
    }

    // Auto-resize textarea height based on content
    function adjustTextareaHeight(event) {
        const textarea = event.target;
        const maxHeight = window.innerHeight * 0.7; // 70% of viewport height
        textarea.style.height = 'auto';
        const newHeight = Math.min(textarea.scrollHeight, maxHeight);
        textarea.style.height = newHeight + 'px';
    }

    function toggleFullscreen() {
        if (!document.fullscreenElement) {
            document.documentElement.requestFullscreen();
            isFullscreen = true;
        } else {
            document.exitFullscreen();
            isFullscreen = false;
        }
    }

    function updateTextColor(color) {
        textColor = color;
    }

    function toggleControls() {
        isControlsExpanded = !isControlsExpanded;
    }

    function handleClickOutside(event) {
        if (isContextMenuOpen && !event.target.closest('.context-menu')) {
            isContextMenuOpen = false;
        }
        if (isCircularMenuOpen && !event.target.closest('.circular-menu')) {
            isCircularMenuOpen = false;
        }
    }

    async function generateCompletion() {
        if (!content.trim() || isGenerating) return;
        
        isGenerating = true;
        const prompt = content.trim();
        
        try {
            const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key=${apiKey}`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    contents: [{
                        parts: [{
                            text: prompt
                        }]
                    }]
                })
            });

            const data = await response.json();
            if (data.candidates && data.candidates[0]?.content?.parts?.[0]?.text) {
                const completion = data.candidates[0].content.parts[0].text;
                content += `\n${completion}`;
                updateWordCount();
            }
        } catch (error) {
            console.error('Error generating completion:', error);
        } finally {
            isGenerating = false;
        }
    }

    function togglePromptBar() {
        isPromptBarOpen = !isPromptBarOpen;
    }

    async function quickComplete() {
        if (!content.trim() || isGenerating) return;
        await generateCompletion();
    }

    async function generateWithInstructions() {
        if (!content.trim() || isGenerating) return;
        
        isGenerating = true;
        const prompt = `${promptInstructions}\n\nText to complete:\n${content.trim()}`;
        
        try {
            const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key=${apiKey}`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    contents: [{
                        parts: [{
                            text: prompt
                        }]
                    }]
                })
            });

            const data = await response.json();
            if (data.candidates && data.candidates[0]?.content?.parts?.[0]?.text) {
                const completion = data.candidates[0].content.parts[0].text;
                content += `\n${completion}`;
                updateWordCount();
            }
        } catch (error) {
            console.error('Error generating completion:', error);
        } finally {
            isGenerating = false;
        }
    }

    function saveToHistory(newContent) {
        // Only save if content actually changed
        if (newContent !== history[currentIndex]) {
            // Remove any future redos
            history = history.slice(0, currentIndex + 1);
            // Add new state
            history = [...history, newContent];
            currentIndex = history.length - 1;
        }
    }

    function undo() {
        if (currentIndex > 0) {
            currentIndex--;
            content = history[currentIndex];
            updateWordCount();
        }
    }

    function redo() {
        if (currentIndex < history.length - 1) {
            currentIndex++;
            content = history[currentIndex];
            updateWordCount();
        }
    }

    // Modify the existing textarea input handler
    function handleTextareaInput(e) {
        updateWordCount();
        adjustTextareaHeight(e);
        saveToHistory(content);
        saveCurrentFile();
    }

    function updateFontSize(size) {
        fontSize = size;
    }

    // Add new chat functions
    async function sendChatMessage() {
        if (!chatMessage.trim() || isChatGenerating) return;
        
        isChatGenerating = true;
        const message = chatMessage.trim();
        
        // Add user message to chat history
        chatHistory = [...chatHistory, { role: 'user', content: message }];
        chatMessage = ''; // Clear input

        try {
            const response = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent?key=${apiKey}`, {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    contents: [{
                        parts: [{
                            text: message
                        }]
                    }]
                })
            });

            const data = await response.json();
            if (data.candidates && data.candidates[0]?.content?.parts?.[0]?.text) {
                const reply = data.candidates[0].content.parts[0].text;
                // Add AI response to chat history with formatted content
                chatHistory = [...chatHistory, { 
                    role: 'assistant', 
                    content: reply,
                    formatted: marked(reply) // Parse markdown
                }];
            }
        } catch (error) {
            console.error('Error in chat:', error);
            chatHistory = [...chatHistory, { role: 'error', content: 'Failed to get response' }];
        } finally {
            isChatGenerating = false;
        }
    }

    function toggleChatPanel() {
        chatPanelOpen = !chatPanelOpen;
    }

    function toggleDarkMode() {
        isDarkMode = !isDarkMode;
        if (browser) {
            document.body.classList.toggle('dark-mode');
            localStorage.setItem('darkMode', isDarkMode);
        }
    }

    // File management functions
    function loadFiles() {
        const fileKeys = Object.keys(localStorage).filter(key => key.startsWith('file_'));
        files = fileKeys.map(key => ({
            name: key.replace('file_', ''),
            content: localStorage.getItem(key)
        }));
    }

    function createNewFile() {
        const fileName = prompt('Enter file name:', 'untitled.txt');
        if (fileName) {
            if (localStorage.getItem(`file_${fileName}`)) {
                alert('File already exists!');
                return;
            }
            localStorage.setItem(`file_${fileName}`, '');
            loadFiles();
            openFile(fileName);
        }
    }

    function deleteFile(fileName) {
        if (confirm(`Are you sure you want to delete ${fileName}?`)) {
            localStorage.removeItem(`file_${fileName}`);
            if (currentFileName === fileName) {
                content = '';
                currentFileName = 'untitled.txt';
            }
            loadFiles();
        }
    }

    function openFile(fileName) {
        content = localStorage.getItem(`file_${fileName}`) || '';
        currentFileName = fileName;
        updateWordCount();
    }

    function saveCurrentFile() {
        if (currentFileName) {
            localStorage.setItem(`file_${currentFileName}`, content);
        }
    }

    function toggleFileManager() {
        isFileManagerOpen = !isFileManagerOpen;
    }

    // Replace the exportToPDF function with this new version
    function exportToPDF() {
        // Create a new window/document for printing
        const printWindow = window.open('', '_blank');
        if (!printWindow) {
            alert('Please allow popups to export PDF');
            return;
        }

        // Add content and styling to the new window
        printWindow.document.write(`
            <!DOCTYPE html>
            <html>
            <head>
                <title>${currentFileName}</title>
                <style>
                    body {
                        font-family: "${selectedFont}", sans-serif;
                        font-size: ${fontSize}rem;
                        color: ${textColor};
                        line-height: 1.6;
                        padding: 40px;
                        max-width: 800px;
                        margin: 0 auto;
                    }
                    /* Add markdown styles */
                    h1 { font-size: 2em; margin: 0.67em 0; }
                    h2 { font-size: 1.5em; margin: 0.83em 0; }
                    h3 { font-size: 1.17em; margin: 1em 0; }
                    blockquote {
                        margin: 1em 0;
                        padding-left: 1em;
                        border-left: 4px solid #e2e8f0;
                        color: #718096;
                    }
                    code {
                        background-color: rgba(0, 0, 0, 0.05);
                        padding: 0.2em 0.4em;
                        border-radius: 3px;
                        font-family: monospace;
                    }
                    pre code {
                        display: block;
                        padding: 1em;
                        overflow-x: auto;
                    }
                    table {
                        border-collapse: collapse;
                        width: 100%;
                        margin: 1em 0;
                    }
                    th, td {
                        border: 1px solid #e2e8f0;
                        padding: 0.5em;
                        text-align: left;
                    }
                    @media print {
                        body {
                            padding: 0;
                        }
                        @page {
                            margin: 2cm;
                        }
                    }
                </style>
                <!-- Add the current font -->
                <link href="https://fonts.googleapis.com/css2?family=${selectedFont.replace(' ', '+')}:wght@400;700&display=swap" rel="stylesheet">
            </head>
            <body>
                ${marked(content)}
            </body>
            </html>
        `);

        // Wait for font to load and then print
        printWindow.document.addEventListener('DOMContentLoaded', () => {
            setTimeout(() => {
                printWindow.print();
                // Close the window after printing (or if printing is cancelled)
                printWindow.addEventListener('afterprint', () => {
                    printWindow.close();
                });
            }, 1000); // Give time for fonts to load
        });
    }

    // Close circular menu when clicking outside
    function handleClickOutsideCircular(event) {
        if (isCircularMenuOpen && !event.target.closest('.circular-menu')) {
            isCircularMenuOpen = false;
        }
        handleClickOutside(event);
    }

    // Add new state for context menu
    let isContextMenuOpen = false;
    let contextMenuPosition = { x: 0, y: 0 };
    let selectedText = '';

    // Handle context menu actions
    async function handleContextMenuAction(action) {
        switch (action) {
            case 'copy':
                try {
                    await navigator.clipboard.writeText(selectedText);
                } catch (err) {
                    console.error('Failed to copy:', err);
                }
                break;
            case 'cut':
                try {
                    await navigator.clipboard.writeText(selectedText);
                    // Replace selected text with empty string
                    const start = content.indexOf(selectedText);
                    if (start !== -1) {
                        content = content.slice(0, start) + content.slice(start + selectedText.length);
                        updateWordCount();
                    }
                } catch (err) {
                    console.error('Failed to cut:', err);
                }
                break;
            case 'paste':
                try {
                    const clipboardText = await navigator.clipboard.readText();
                    // Insert at cursor position or replace selected text
                    const textarea = document.querySelector('textarea');
                    const start = textarea.selectionStart;
                    const end = textarea.selectionEnd;
                    content = content.slice(0, start) + clipboardText + content.slice(end);
                    updateWordCount();
                } catch (err) {
                    console.error('Failed to paste:', err);
                }
                break;
            case 'selectAll':
                document.querySelector('textarea').select();
                break;
        }
        isContextMenuOpen = false;
    }

    // Close context menu when clicking outside
    function handleClickOutsideContext(event) {
        if (isContextMenuOpen && !event.target.closest('.context-menu')) {
            isContextMenuOpen = false;
        }
    }

    // Initialize dark mode from URL parameter
    $: {
        if (browser && $page.url.searchParams) {
            const darkParam = $page.url.searchParams.get('dark');
            isDarkMode = darkParam === 'true';
            if (isDarkMode) {
                document.body.classList.add('dark-mode');
            } else {
                document.body.classList.remove('dark-mode');
            }
        }
    }

    // Update URL when dark mode changes
    $: if (browser && isDarkMode !== undefined) {
        const url = new URL(window.location.href);
        url.searchParams.set('dark', isDarkMode);
        window.history.replaceState({}, '', url);
    }

    // Add loading state
    let isLoading = true;

    // Add onMount handler
    onMount(() => {
        if (browser) {
            // Simulate loading time and fade out
            setTimeout(() => {
                isLoading = false;
            }, 2500); // Adjust timing as needed
        }
    });
</script>

<!-- Add the loading overlay at the very top of your template -->
<svelte:head>
    <link 
        rel="preload" 
        href="https://fonts.gstatic.com/s/quicksand/v30/6xK-dSZaM9iE8KbpRA_LJ3z8mH9BOJvgkP8o58a-wjwxUD2GF9Zc.woff2" 
        as="font" 
        type="font/woff2" 
        crossorigin
    />
</svelte:head>

{#if isLoading}
    <div class="loading-overlay" class:fade-out={!isLoading}>
        <div class="loading-content">
            <div class="loading-text" style="font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;">
                Pulpit!
            </div>
        </div>
    </div>
{/if}

<svelte:window 
    on:click={handleClickOutside}
    on:mousedown={handleMiddleClick}
    on:keydown={handleKeyboardShortcut}
    on:contextmenu={handleContextMenu}
    on:touchstart={handleTouchStart}
    on:touchend={handleTouchEnd}
/>

<!-- Add a small floating button for touch devices -->
<button 
    class="floating-menu-btn"
    on:click={(event) => {
        isCircularMenuOpen = !isCircularMenuOpen;
        if (isCircularMenuOpen) {
            circularMenuPosition = { x: event.clientX, y: event.clientY };
        }
    }}
    title="Open Menu (Alt + C)"
>
    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
        <circle cx="12" cy="12" r="1"></circle>
        <circle cx="19" cy="12" r="1"></circle>
        <circle cx="5" cy="12" r="1"></circle>
    </svg>
</button>

<div class="app" class:dark-mode={isDarkMode} style="background-image: {backgroundImage}; font-size: {fontSize}rem">
    <div class="writing-container" class:dark-mode={isDarkMode} style:font-size="{fontSize}rem">
        {#if isPreviewMode}
            <div 
                class="markdown-preview"
                class:dark-mode={isDarkMode}
                style:color={textColor}
                style:font-size="inherit"
                on:click={() => isPreviewMode = false}
                role="button"
                tabindex="0"
                title="Click to edit"
            >
                {@html marked(content)}
            </div>
        {:else}
            <textarea
                class:dark-mode={isDarkMode}
                style:color={textColor}
                style:font-size="inherit"
                bind:value={content}
                on:input={handleTextareaInput}
                placeholder="Start writing... (Supports Markdown formatting)"
                spellcheck="true"
                aria-label="Writing area"
                autocomplete="off"
                autocorrect="on"
            ></textarea>
        {/if}
    </div>
</div>

<!-- Replace floating buttons with vertical control bar -->
<div class="control-bar" class:dark-mode={isDarkMode}>
    <!-- Add home button at the top -->
    <button 
        class="control-btn"
        on:click={() => window.location.href = '/'}
        title="Back to Home"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path>
            <polyline points="9 22 9 12 15 12 15 22"></polyline>
        </svg>
    </button>

    <button 
        class="control-btn"
        on:click={toggleFileManager}
        class:active={isFileManagerOpen}
        title="File Manager"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"></path>
        </svg>
    </button>

    <button 
        class="control-btn"
        class:dark-mode={isDarkMode}
        on:click={toggleDarkMode}
        title="Toggle Dark Mode"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            {#if isDarkMode}
                <!-- Sun icon -->
                <circle cx="12" cy="12" r="5"></circle>
                <line x1="12" y1="1" x2="12" y2="3"></line>
                <line x1="12" y1="21" x2="12" y2="23"></line>
                <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
                <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
                <line x1="1" y1="12" x2="3" y2="12"></line>
                <line x1="21" y1="12" x2="23" y2="12"></line>
                <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
                <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
            {:else}
                <!-- Moon icon -->
                <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path>
            {/if}
        </svg>
    </button>

    <button 
        class="control-btn"
        on:click={undo}
        disabled={currentIndex === 0}
        title="Undo"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M3 7v6h6"></path>
            <path d="M21 17a9 9 0 00-9-9 9 9 0 00-6 2.3L3 13"></path>
        </svg>
    </button>

    <button 
        class="control-btn"
        on:click={redo}
        disabled={currentIndex === history.length - 1}
        title="Redo"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 7v6h-6"></path>
            <path d="M3 17a9 9 0 019-9 9 9 0 016 2.3l3 2.7"></path>
        </svg>
    </button>

    <button 
        class="control-btn settings-btn"
        class:active={isControlsExpanded}
        on:click={() => {
            console.log('Before:', isControlsExpanded);
            isControlsExpanded = !isControlsExpanded;
            console.log('After:', isControlsExpanded);
            isCircularMenuOpen = false;
        }}
        title="Settings"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="12" cy="12" r="3"></circle>
            <path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-2 2 2 2 0 0 1-2-2v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83 0 2 2 0 0 1 0-2.83l.06-.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1-2-2 2 2 0 0 1 2-2h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 0-2.83 2 2 0 0 1 2.83 0l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 2-2 2 2 0 0 1 2 2v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 0 2 2 0 0 1 0 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 2 2 2 2 0 0 1-2 2h-.09a1.65 1.65 0 0 0-1.51 1z"></path>
        </svg>
    </button>

    <button 
        class="control-btn"
        on:click={quickComplete}
        disabled={isGenerating}
        title="Quick Complete"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <polygon points="5 3 19 12 5 21 5 3"></polygon>
        </svg>
    </button>

    <button 
        class="control-btn"
        on:click={togglePromptBar}
        class:active={isPromptBarOpen}
        title="Custom Instructions"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <line x1="21" y1="10" x2="3" y2="10"></line>
            <line x1="21" y1="6" x2="3" y2="6"></line>
            <line x1="21" y1="14" x2="3" y2="14"></line>
            <line x1="21" y1="18" x2="3" y2="18"></line>
        </svg>
    </button>

    <!-- Add new export button -->
    <button 
        class="control-btn"
        on:click={exportToPDF}
        title="Export as PDF"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"></path>
            <polyline points="14 2 14 8 20 8"></polyline>
            <line x1="12" y1="18" x2="12" y2="12"></line>
            <line x1="9" y1="15" x2="15" y2="15"></line>
        </svg>
    </button>

    <!-- Add new fullscreen button -->
    <button 
        class="control-btn"
        on:click={toggleFullscreen}
        title="Toggle Fullscreen"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            {#if isFullscreen}
                <!-- Minimize icon -->
                <path d="M8 3v3a2 2 0 0 1-2 2H3m18 0h-3a2 2 0 0 1-2-2V3m0 18v-3a2 2 0 0 1 2-2h3M3 16h3a2 2 0 0 1 2 2v3"></path>
            {:else}
                <!-- Maximize icon -->
                <path d="M15 3h6v6M9 21H3v-6M21 3l-7 7M3 21l7-7"></path>
            {/if}
        </svg>
    </button>

    <!-- Add new chat button to control bar -->
    <button 
        class="control-btn"
        on:click={toggleChatPanel}
        class:active={chatPanelOpen}
        title="Chat"
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"></path>
        </svg>
    </button>

    <!-- Add preview toggle button to control bar (after the chat button) -->
    <button 
        class="control-btn"
        on:click={() => isPreviewMode = !isPreviewMode}
        class:active={isPreviewMode}
        title={isPreviewMode ? "Edit Mode" : "Preview Mode"}
    >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            {#if isPreviewMode}
                <path d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5L17 3z"></path>
            {:else}
                <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path>
                <circle cx="12" cy="12" r="3"></circle>
            {/if}
        </svg>
    </button>
</div>

<!-- Replace the settings-panel div with this new sidebar version -->
<div class="settings-panel" class:open={isControlsExpanded} class:dark-mode={isDarkMode}>
    <div class="settings-header">
        <h3>Settings</h3>
        <button class="close-btn" on:click={() => isControlsExpanded = false}>
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <line x1="18" y1="6" x2="6" y2="18"></line>
                <line x1="6" y1="6" x2="18" y2="18"></line>
            </svg>
        </button>
    </div>
    <div class="settings-content">
        <div class="settings-section">
            <h4>Text Appearance</h4>
            <div class="setting-item">
                <label for="color-picker">Text Color</label>
                <input 
                    type="color" 
                    id="color-picker" 
                    bind:value={textColor}
                    on:input={(e) => updateTextColor(e.target.value)}
                />
            </div>
            <div class="setting-item">
                <label for="font-size">Font Size</label>
                <div class="font-size-controls">
                    <button 
                        class="size-btn"
                        on:click={() => updateFontSize(Math.max(0.8, fontSize - 0.1))}
                        disabled={fontSize <= 0.8}
                        title="Decrease font size"
                    >
                        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <line x1="5" y1="12" x2="19" y2="12"></line>
                        </svg>
                    </button>
                    <span class="size-display">{fontSize.toFixed(1)}rem</span>
                    <button 
                        class="size-btn"
                        on:click={() => updateFontSize(Math.min(2.5, fontSize + 0.1))}
                        disabled={fontSize >= 2.5}
                        title="Increase font size"
                    >
                        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <line x1="12" y1="5" x2="12" y2="19"></line>
                            <line x1="5" y1="12" x2="19" y2="12"></line>
                        </svg>
                    </button>
                </div>
            </div>
            <div class="setting-item">
                <label for="font-select">Font Family</label>
                <select 
                    id="font-select" 
                    bind:value={selectedFont} 
                    on:change={() => loadFont(selectedFont)}
                >
                    {#each fonts as font}
                        <option value={font.family}>{font.label}</option>
                    {/each}
                </select>
            </div>
        </div>

        <div class="settings-section">
            <h4>Background</h4>
            <div class="setting-item">
                <label for="background-type">Image Type</label>
                <select id="background-type" bind:value={imageType} on:change={loadBackgroundImage}>
                    {#each imageTypes as type}
                        <option value={type.value}>{type.label}</option>
                    {/each}
                </select>
            </div>
            <button class="refresh-btn" on:click={loadBackgroundImage}>
                <span class="icon">↻</span>
                <span>New Background</span>
            </button>
        </div>
    </div>
</div>

<!-- Add prompt instructions sidebar -->
<div class="prompt-bar" class:open={isPromptBarOpen} class:dark-mode={isDarkMode}>
    <div class="prompt-bar-header">
        <h3>Custom Instructions</h3>
        <button class="close-btn" on:click={togglePromptBar}>×</button>
    </div>
    <textarea
        bind:value={promptInstructions}
        placeholder="Add instructions for the AI (e.g., 'Write in a professional tone' or 'Continue the story with a dramatic twist')"
    ></textarea>
    <button 
        class="generate-btn"
        on:click={generateWithInstructions}
        disabled={isGenerating}
    >
        {isGenerating ? 'Generating...' : 'Generate with Instructions'}
    </button>
</div>

<!-- Add chat panel -->
<div class="chat-panel" class:open={chatPanelOpen} class:dark-mode={isDarkMode}>
    <div class="chat-header">
        <h3>Chat with AI</h3>
        <button class="close-btn" on:click={toggleChatPanel}>×</button>
    </div>
    
    <div class="chat-messages">
        {#each chatHistory as message}
            <div class="message {message.role}">
                <div class="message-content">
                    {#if message.formatted}
                        {@html message.formatted}
                    {:else}
                        {message.content}
                    {/if}
                </div>
            </div>
        {/each}
    </div>
    
    <div class="chat-input">
        <textarea
            bind:value={chatMessage}
            placeholder="Type your message..."
            on:keydown={(e) => {
                if (e.key === 'Enter' && !e.shiftKey) {
                    e.preventDefault();
                    sendChatMessage();
                }
            }}
        ></textarea>
        <button 
            class="send-btn"
            on:click={sendChatMessage}
            disabled={isChatGenerating}
        >
            {#if isChatGenerating}
                <span class="loading">...</span>
            {:else}
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <line x1="22" y1="2" x2="11" y2="13"></line>
                    <polygon points="22 2 15 22 11 13 2 9 22 2"></polygon>
                </svg>
            {/if}
        </button>
    </div>
</div>

<!-- Update file manager sidebar -->
<div class="file-manager" class:open={isFileManagerOpen} class:dark-mode={isDarkMode}>
    <div class="file-manager-header">
        <h3>Files</h3>
        <div class="file-actions">
            <button class="file-btn" on:click={createNewFile} title="New File">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <line x1="12" y1="5" x2="12" y2="19"></line>
                    <line x1="5" y1="12" x2="19" y2="12"></line>
                </svg>
            </button>
            <button class="file-btn" on:click={toggleFileManager} title="Close">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <line x1="18" y1="6" x2="6" y2="18"></line>
                    <line x1="6" y1="6" x2="18" y2="18"></line>
                </svg>
            </button>
        </div>
    </div>
    <div class="file-list">
        {#each files as file}
            <div class="file-item" class:active={currentFileName === file.name}>
                <button 
                    class="file-name"
                    on:click={() => openFile(file.name)}
                >
                    {file.name}
                </button>
                <button 
                    class="delete-btn"
                    on:click={() => deleteFile(file.name)}
                    title="Delete file"
                >
                    ×
                </button>
            </div>
        {/each}
    </div>
</div>

<!-- Add circular menu -->
{#if isCircularMenuOpen}
    <div 
        class="circular-menu"
        style="left: {circularMenuPosition.x}px; top: {circularMenuPosition.y}px;"
    >
        <div class="menu-items">
            <button class="menu-item" on:click={() => toggleFileManager()} title="File Manager">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"></path>
                </svg>
            </button>
            <button class="menu-item" on:click={() => toggleDarkMode()} title="Dark Mode">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    {#if isDarkMode}
                        <circle cx="12" cy="12" r="5"></circle>
                        <line x1="12" y1="1" x2="12" y2="3"></line>
                        <line x1="12" y1="21" x2="12" y2="23"></line>
                        <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
                        <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
                        <line x1="1" y1="12" x2="3" y2="12"></line>
                        <line x1="21" y1="12" x2="23" y2="12"></line>
                        <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
                        <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
                    {:else}
                        <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path>
                    {/if}
                </svg>
            </button>
            <button class="menu-item" on:click={() => undo()} title="Undo">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M3 7v6h6"></path>
                    <path d="M21 17a9 9 0 00-9-9 9 9 0 00-6 2.3L3 13"></path>
                </svg>
            </button>
            <button class="menu-item" on:click={() => redo()} title="Redo">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M21 7v6h-6"></path>
                    <path d="M3 17a9 9 0 019-9 9 9 0 016 2.3l3 2.7"></path>
                </svg>
            </button>
            <button 
                class="menu-item" 
                on:click|stopPropagation={() => {
                    isControlsExpanded = !isControlsExpanded;
                    isCircularMenuOpen = false;
                }} 
                title="Settings"
            >
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <circle cx="12" cy="12" r="3"></circle>
                    <path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1 0 2.83 2 2 0 0 1-2.83 0l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-2 2 2 2 0 0 1-2-2v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83 0 2 2 0 0 1 0-2.83l.06-.06a1.65 1.65 0 0 0 .33-1.82 1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1-2-2 2 2 0 0 1 2-2h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 0-2.83 2 2 0 0 1 2.83 0l.06.06a1.65 1.65 0 0 0 1.82.33H9a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 2-2 2 2 0 0 1 2 2v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 0 2 2 0 0 1 0 2.83l-.06.06a1.65 1.65 0 0 0-.33 1.82V9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 2 2 2 2 0 0 1-2 2h-.09a1.65 1.65 0 0 0-1.51 1z"></path>
                </svg>
            </button>
            <button class="menu-item" on:click={() => toggleChatPanel()} title="Chat">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"></path>
                </svg>
            </button>
            <button class="menu-item" on:click={() => toggleFullscreen()} title="Fullscreen">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    {#if isFullscreen}
                        <path d="M8 3v3a2 2 0 0 1-2 2H3m18 0h-3a2 2 0 0 1-2-2V3m0 18v-3a2 2 0 0 1 2-2h3M3 16h3a2 2 0 0 1 2 2v3"></path>
                    {:else}
                        <path d="M15 3h6v6M9 21H3v-6M21 3l-7 7M3 21l7-7"></path>
                    {/if}
                </svg>
            </button>
            <button class="menu-item" on:click={() => isPreviewMode = !isPreviewMode} title="Preview Mode">
                <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    {#if isPreviewMode}
                        <path d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5L17 3z"></path>
                    {:else}
                        <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"></path>
                        <circle cx="12" cy="12" r="3"></circle>
                    {/if}
                </svg>
            </button>
            <!-- Generate text button in the middle -->
            <button 
                class="menu-item center-item" 
                on:click={() => quickComplete()} 
                disabled={isGenerating}
                title="Generate Text"
            >
                <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <polygon points="5 3 19 12 5 21 5 3"></polygon>
                </svg>
            </button>
        </div>
    </div>
{/if}

<!-- Add custom context menu -->
{#if isContextMenuOpen}
    <div 
        class="context-menu"
        style="left: {contextMenuPosition.x}px; top: {contextMenuPosition.y}px;"
    >
        <button 
            class="context-menu-item"
            on:click={() => handleContextMenuAction('copy')}
            disabled={!selectedText}
        >
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <rect x="9" y="9" width="13" height="13" rx="2" ry="2"></rect>
                <path d="M5 15H4a2 2 0 0 1-2-2V4a2 2 0 0 1 2-2h9a2 2 0 0 1 2 2v1"></path>
            </svg>
            Copy
            <span class="shortcut">⌘C</span>
        </button>
        <button 
            class="context-menu-item"
            on:click={() => handleContextMenuAction('cut')}
            disabled={!selectedText}
        >
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <circle cx="6" cy="6" r="3"></circle>
                <circle cx="6" cy="18" r="3"></circle>
                <line x1="20" y1="4" x2="8.12" y2="15.88"></line>
                <line x1="14.47" y1="14.48" x2="20" y2="20"></line>
                <line x1="8.12" y1="8.12" x2="12" y2="12"></line>
            </svg>
            Cut
            <span class="shortcut">⌘X</span>
        </button>
        <button 
            class="context-menu-item"
            on:click={() => handleContextMenuAction('paste')}
        >
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M16 4h2a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h2"></path>
                <rect x="8" y="2" width="8" height="4" rx="1" ry="1"></rect>
            </svg>
            Paste
            <span class="shortcut">⌘V</span>
        </button>
        <div class="context-menu-divider"></div>
        <button 
            class="context-menu-item"
            on:click={() => handleContextMenuAction('selectAll')}
        >
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="M3 3h18v18H3z"></path>
            </svg>
            Select All
            <span class="shortcut">⌘A</span>
        </button>
    </div>
{/if}

<style>
    /* Global dark mode styles */
    :global(body) {
        transition: background-color 0.3s ease, color 0.3s ease;
    }

    :global(body.dark-mode) {
        background-color: #1a202c;
        color: #f7fafc;
    }

    /* Component-specific dark mode styles */
    .app.dark-mode {
        background-color: #1a202c;
    }

    .writing-container.dark-mode {
        background-color: rgba(26, 32, 44, 0.8);
    }

    .writing-container.dark-mode textarea {
        color: #f7fafc;
    }

    .control-bar.dark-mode .control-btn {
        background-color: rgba(26, 32, 44, 0.95);
        color: #e2e8f0;
    }

    .settings-panel.dark-mode,
    .prompt-bar.dark-mode,
    .chat-panel.dark-mode {
        background-color: rgba(26, 32, 44, 0.95);
        color: #f7fafc;
    }

    .settings-panel.dark-mode h3,
    .prompt-bar.dark-mode h3,
    .chat-panel.dark-mode h3 {
        color: #f7fafc;
    }

    .settings-panel.dark-mode .settings-section h4 {
        color: #e2e8f0;
    }

    .settings-panel.dark-mode .setting-item label {
        color: #e2e8f0;
    }

    .settings-panel.dark-mode select,
    .settings-panel.dark-mode input[type="color"] {
        background-color: #2d3748;
        color: #f7fafc;
        border-color: #4a5568;
    }

    .chat-panel.dark-mode .message.user {
        background-color: #2d3748;
        color: #f7fafc;
    }

    .chat-panel.dark-mode .message.assistant {
        background-color: #4a5568;
        color: #f7fafc;
    }

    .chat-panel.dark-mode .chat-input textarea {
        background-color: #2d3748;
        color: #f7fafc;
        border-color: #4a5568;
    }

    /* Keep existing styles but update with dark mode variants */
    .writing-container {
        transition: background-color 0.3s ease;
    }

    textarea {
        transition: color 0.3s ease;
    }

    .control-btn {
        transition: background-color 0.3s ease, color 0.3s ease;
    }

    .settings-panel,
    .prompt-bar,
    .chat-panel {
        transition: background-color 0.3s ease, color 0.3s ease;
    }

    /* Add CSS variables for theming at the top of your styles */
    :global(:root) {
        --bg-primary: rgba(255, 255, 255, 0.7);
        --bg-secondary: rgba(255, 255, 255, 0.95);
        --text-primary: #1a202c;
        --text-secondary: #4a5568;
        --border-color: #e2e8f0;
        --panel-shadow: rgba(0, 0, 0, 0.1);
        --message-bg-user: #4a5568;
        --message-text-user: white;
        --message-bg-assistant: #e2e8f0;
        --message-text-assistant: #2d3748;
    }

    :global(body.dark-mode) {
        --bg-primary: rgba(26, 32, 44, 0.8);
        --bg-secondary: rgba(26, 32, 44, 0.95);
        --text-primary: #f7fafc;
        --text-secondary: #e2e8f0;
        --border-color: #4a5568;
        --panel-shadow: rgba(0, 0, 0, 0.3);
        --message-bg-user: #2d3748;
        --message-text-user: #f7fafc;
        --message-bg-assistant: #4a5568;
        --message-text-assistant: #f7fafc;
    }

    /* Update existing styles to use CSS variables */
    .writing-container {
        background-color: var(--bg-primary);
        box-shadow: 0 2px 12px var(--panel-shadow);
    }

    textarea {
        color: var(--text-primary);
    }

    textarea::placeholder {
        color: var(--text-secondary);
    }

    .control-btn {
        background-color: var(--bg-secondary);
        color: var(--text-secondary);
    }

    .settings-panel, .prompt-bar, .chat-panel {
        background-color: var(--bg-secondary);
        box-shadow: -2px 0 8px var(--panel-shadow);
    }

    .settings-header h3, .prompt-bar-header h3, .chat-header h3 {
        color: var(--text-primary);
    }

    .close-btn {
        color: var(--text-secondary);
    }

    .settings-section h4 {
        color: var(--text-primary);
    }

    .setting-item label {
        color: var(--text-secondary);
    }

    .setting-item select,
    .setting-item input[type="color"] {
        background-color: var(--bg-primary);
        border-color: var(--border-color);
        color: var(--text-primary);
    }

    .message.user {
        background-color: var(--message-bg-user);
        color: var(--message-text-user);
    }

    .message.assistant {
        background-color: var(--message-bg-assistant);
        color: var(--message-text-assistant);
    }

    .chat-input textarea {
        background-color: var(--bg-primary);
        border-color: var(--border-color);
        color: var(--text-primary);
    }

    .settings-header, .chat-header {
        border-bottom-color: var(--border-color);
    }

    .chat-input {
        border-top-color: var(--border-color);
    }

    /* Update any borders or dividers */
    .settings-section {
        border-bottom-color: var(--border-color);
    }

    /* Ensure buttons maintain visibility in dark mode */
    .refresh-btn, .generate-btn, .send-btn {
        background-color: var(--message-bg-user);
        color: var(--message-text-user);
    }

    .refresh-btn:hover, .generate-btn:hover, .send-btn:hover {
        background-color: var(--message-bg-assistant);
    }

    .app {
        width: 100%;
        height: 100vh;
        background-color: #f8f9fa;
        background-size: cover;
        background-position: center;
        background-repeat: no-repeat;
        position: fixed;
        top: 0;
        left: 0;
        transition: background-image 0.3s ease; /* Smooth transition for image load */
    }

    .writing-container {
        display: flex;
        flex-direction: column;
        background-color: rgba(255, 255, 255, 0.7);
        box-shadow: 0 2px 12px rgba(0, 0, 0, 0.15);
        backdrop-filter: blur(14px);
        width: 70%;
        height: 100vh;
        box-sizing: border-box;
        margin: 0 auto;
        padding: 2rem;
    }

    textarea {
        flex: 1;
        width: 100%;
        min-height: 0;
        padding: 0;
        border: none;
        resize: none;
        line-height: 1.8;
        font-family: inherit;
        background-color: transparent;
        color: #1a202c;
        border-radius: 0;
        overflow-y: auto;
        box-sizing: border-box;
    }

    textarea:focus {
        outline: none;
        border-color: rgba(0, 0, 0, 0);
    }

    textarea::placeholder {
        color: #718096;
        opacity: 0.8;
    }

    /* Custom scrollbar styles */
    textarea::-webkit-scrollbar {
        width: 8px;
    }

    textarea::-webkit-scrollbar-track {
        background: rgba(0, 0, 0, 0.05);
        border-radius: 4px;
    }

    textarea::-webkit-scrollbar-thumb {
        background: rgba(0, 0, 0, 0.2);
        border-radius: 4px;
    }

    textarea::-webkit-scrollbar-thumb:hover {
        background: rgba(0, 0, 0, 0.3);
    }

    .word-count {
        text-align: center;
        padding: 0.5rem 0;
        color: #718096;
        font-size: 0.9rem;
        font-weight: 500;
    }

    .control-bar {
        position: fixed;
        top: 50%;
        left: 1rem;
        transform: translateY(-50%);
        display: flex;
        flex-direction: column;
        gap: 0.5rem;
        z-index: 1000;
    }

    .control-btn {
        width: 44px;
        height: 44px;
        border-radius: 50%;
        border: none;
        background-color: rgba(255, 255, 255, 0.95);
        color: #4a5568;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;
        transition: all 0.2s;
        backdrop-filter: blur(10px);
        box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    }

    .control-btn:hover:not(:disabled) {
        transform: none;
        background-color: var(--message-bg-user);
        color: var(--message-text-user);
        box-shadow: 0 2px 12px var(--panel-shadow);
    }

    .control-btn:disabled {
        opacity: 0.5;
        cursor: not-allowed;
        transform: none;
    }

    .control-btn.active {
        background-color: #4a5568;
        color: white;
    }

    @media (max-width: 768px) {
        .control-bar {
            left: 0.5rem;
        }
    }

    .floating-buttons {
        position: fixed;
        bottom: 2rem;
        right: 2rem;
        display: flex;
        gap: 1rem;
        z-index: 1000;
    }

    .prompt-bar {
        position: fixed;
        top: 0;
        right: -400px;
        width: 400px;
        height: 100vh;
        background-color: rgba(255, 255, 255, 0.95);
        box-shadow: -2px 0 8px rgba(0, 0, 0, 0.1);
        transition: right 0.3s ease;
        z-index: 1001;
        padding: 1.5rem;
        box-sizing: border-box;
        display: flex;
        flex-direction: column;
        gap: 1rem;
    }

    .prompt-bar.open {
        right: 0;
    }

    .prompt-bar-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .prompt-bar-header h3 {
        margin: 0;
        color: #2d3748;
    }

    .prompt-bar textarea {
        flex: 1;
        min-height: 200px;
        padding: 1rem;
        border: 1px solid #e2e8f0;
        border-radius: 4px;
        resize: none;
        font-size: 1em;
        line-height: 1.5;
    }

    .generate-btn {
        background-color: #4a5568;
        color: white;
        padding: 0.75rem;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        font-size: 1em;
        transition: background-color 0.2s;
    }

    .generate-btn:hover {
        background-color: #2d3748;
    }

    .generate-btn:disabled {
        background-color: #a0aec0;
        cursor: not-allowed;
    }

    @media (max-width: 768px) {
        .prompt-bar {
            width: 100%;
            right: -100%;
        }

        .floating-buttons {
            bottom: 1rem;
            right: 1rem;
        }
    }

    .font-size-container {
        display: flex;
        flex-direction: column;
        gap: 0.5rem;
        margin-bottom: 1rem;
    }

    .font-size-container label {
        color: #4a5568;
        font-size: 0.9em;
    }

    .font-size-controls {
        display: flex;
        align-items: center;
        gap: 0.5rem;
    }

    .size-btn {
        width: 28px;
        height: 28px;
        padding: 0;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 1.2em;
        font-weight: bold;
    }

    .size-btn:disabled {
        background-color: #a0aec0;
        cursor: not-allowed;
    }

    .size-display {
        flex: 1;
        text-align: center;
        color: #4a5568;
        font-size: 0.9em;
    }

    .settings-panel {
        position: fixed;
        top: 0;
        right: -400px;
        width: 400px;
        height: 100vh;
        background-color: var(--bg-secondary);
        box-shadow: -2px 0 8px var(--panel-shadow);
        transition: right 0.3s ease;
        z-index: 1200;
        display: flex;
        flex-direction: column;
    }

    .settings-panel.open {
        right: 0;
        pointer-events: auto;
    }

    .settings-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 1.5rem;
        border-bottom: 1px solid #e2e8f0;
    }

    .settings-header h3 {
        margin: 0;
        color: #2d3748;
    }

    .settings-content {
        flex: 1;
        padding: 1.5rem;
        overflow-y: auto;
    }

    .settings-section {
        margin-bottom: 2rem;
    }

    .settings-section h4 {
        margin: 0 0 1rem 0;
        color: #4a5568;
    }

    .setting-item {
        margin-bottom: 1rem;
    }

    .setting-item label {
        display: block;
        margin-bottom: 0.5rem;
        color: #4a5568;
        font-size: 0.9em;
    }

    .setting-item select,
    .setting-item input[type="color"] {
        width: 100%;
        padding: 0.5rem;
        border: 1px solid #e2e8f0;
        border-radius: 4px;
        background-color: white;
    }

    .refresh-btn {
        width: 100%;
        padding: 0.75rem;
        background-color: #4a5568;
        color: white;
        border: none;
        border-radius: 4px;
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 0.5rem;
        transition: background-color 0.2s;
    }

    .refresh-btn:hover {
        background-color: #2d3748;
    }

    @media (max-width: 768px) {
        .settings-panel {
            width: 100%;
            right: -100%;
        }
    }

    .chat-panel {
        position: fixed;
        top: 0;
        right: -400px;
        width: 400px;
        height: 100vh;
        background-color: rgba(255, 255, 255, 0.95);
        box-shadow: -2px 0 8px rgba(0, 0, 0, 0.1);
        transition: right 0.3s ease;
        z-index: 1001;
        display: flex;
        flex-direction: column;
    }

    .chat-panel.open {
        right: 0;
    }

    .chat-header {
        padding: 1rem;
        border-bottom: 1px solid #e2e8f0;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .chat-messages {
        flex: 1;
        overflow-y: auto;
        padding: 1rem;
        display: flex;
        flex-direction: column;
        gap: 1rem;
    }

    .message {
        max-width: 80%;
        padding: 0.75rem;
        border-radius: 1rem;
        word-wrap: break-word;
    }

    .message.user {
        align-self: flex-end;
        background-color: #4a5568;
        color: white;
        border-bottom-right-radius: 0.25rem;
    }

    .message.assistant {
        align-self: flex-start;
        background-color: #e2e8f0;
        color: #2d3748;
        border-bottom-left-radius: 0.25rem;
    }

    .message.error {
        align-self: center;
        background-color: #fed7d7;
        color: #c53030;
    }

    .chat-input {
        padding: 1rem;
        border-top: 1px solid #e2e8f0;
        display: flex;
        gap: 0.5rem;
    }

    .chat-input textarea {
        flex: 1;
        min-height: 40px;
        max-height: 120px;
        padding: 0.5rem;
        border: 1px solid #e2e8f0;
        border-radius: 0.5rem;
        resize: none;
        font-size: 0.9em;
    }

    .send-btn {
        width: 40px;
        height: 40px;
        border-radius: 50%;
        border: none;
        background-color: #4a5568;
        color: white;
        display: flex;
        align-items: center;
        justify-content: center;
        cursor: pointer;
        transition: background-color 0.2s;
    }

    .send-btn:hover:not(:disabled) {
        background-color: #2d3748;
    }

    .send-btn:disabled {
        background-color: #a0aec0;
        cursor: not-allowed;
    }

    .loading {
        animation: bounce 1s infinite;
    }

    @keyframes bounce {
        0%, 20%, 50%, 80%, 100% { transform: translateY(0); }
        40% { transform: translateY(-3px); }
        60% { transform: translateY(-1.5px); }
    }

    @media (max-width: 768px) {
        .chat-panel {
            width: 100%;
            right: -100%;
        }
    }

    .message-content :global(h1),
    .message-content :global(h2),
    .message-content :global(h3) {
        margin: 0.5em 0;
        font-weight: bold;
    }

    .message-content :global(ul),
    .message-content :global(ol) {
        margin: 0.5em 0;
        padding-left: 1.5em;
    }

    .message-content :global(li) {
        margin: 0.25em 0;
    }

    .message-content :global(p) {
        margin: 0.5em 0;
    }

    .message-content :global(strong) {
        font-weight: bold;
    }

    .message-content :global(em) {
        font-style: italic;
    }

    /* Optional: Add styles for dark mode if needed */
    .control-btn.dark-mode {
        background-color: #2d3748;
        color: white;
    }

    .file-manager {
        position: fixed;
        top: 0;
        right: -300px;
        width: 300px;
        height: 100vh;
        background-color: rgba(255, 255, 255, 0.95);
        box-shadow: -2px 0 8px rgba(0, 0, 0, 0.1);
        transition: right 0.3s ease;
        z-index: 1001;
        display: flex;
        flex-direction: column;
    }

    .file-manager.open {
        right: 0;
    }

    .file-manager.dark-mode {
        background-color: rgba(26, 32, 44, 0.95);
        color: #f7fafc;
    }

    .file-manager-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 1rem;
        border-bottom: 1px solid var(--border-color);
    }

    .file-manager-header h3 {
        margin: 0;
        color: var(--text-primary);
    }

    .file-actions {
        display: flex;
        gap: 0.5rem;
    }

    .file-btn {
        background: none;
        border: none;
        color: var(--text-secondary);
        cursor: pointer;
        padding: 0.5rem;
        display: flex;
        align-items: center;
        justify-content: center;
        transition: color 0.2s;
    }

    .file-btn:hover {
        color: var(--text-primary);
    }

    .file-list {
        flex: 1;
        overflow-y: auto;
        padding: 1rem;
    }

    .file-item {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0.5rem;
        border-radius: 4px;
        margin-bottom: 0.5rem;
        background-color: var(--bg-primary);
        transition: background-color 0.2s;
    }

    .file-item.active {
        background-color: var(--message-bg-user);
        color: var(--message-text-user);
    }

    .file-name {
        flex: 1;
        text-align: left;
        padding: 0.25rem 0.5rem;
        background: none;
        border: none;
        color: inherit;
        cursor: pointer;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
    }

    .delete-btn {
        background: none;
        border: none;
        color: inherit;
        cursor: pointer;
        padding: 0.25rem 0.5rem;
        opacity: 0.7;
        transition: opacity 0.2s;
    }

    .delete-btn:hover {
        opacity: 1;
    }

    @media (max-width: 768px) {
        .file-manager {
            width: 100%;
            right: -100%;
        }
    }

    .markdown-preview {
        flex: 1;
        width: 100%;
        padding: 0;
        line-height: 1.8;
        font-family: inherit;
        background-color: transparent;
        border-radius: 0;
        overflow-y: auto;
        box-sizing: border-box;
        cursor: pointer;
    }

    /* Style markdown elements */
    .markdown-preview :global(h1) {
        font-size: 2em;
        margin: 0.67em 0;
    }

    .markdown-preview :global(h2) {
        font-size: 1.5em;
        margin: 0.83em 0;
    }

    .markdown-preview :global(h3) {
        font-size: 1.17em;
        margin: 1em 0;
    }

    .markdown-preview :global(strong) {
        font-weight: bold;
    }

    .markdown-preview :global(em) {
        font-style: italic;
    }

    .markdown-preview :global(blockquote) {
        margin: 1em 0;
        padding-left: 1em;
        border-left: 4px solid #e2e8f0;
        color: #718096;
    }

    .markdown-preview :global(code) {
        background-color: rgba(0, 0, 0, 0.05);
        padding: 0.2em 0.4em;
        border-radius: 3px;
        font-family: monospace;
    }

    .markdown-preview :global(pre code) {
        display: block;
        padding: 1em;
        overflow-x: auto;
    }

    .markdown-preview :global(ul), 
    .markdown-preview :global(ol) {
        margin: 1em 0;
        padding-left: 2em;
    }

    .markdown-preview :global(li) {
        margin: 0.5em 0;
    }

    .markdown-preview :global(table) {
        border-collapse: collapse;
        margin: 1em 0;
        width: 100%;
    }

    .markdown-preview :global(th),
    .markdown-preview :global(td) {
        border: 1px solid #e2e8f0;
        padding: 0.5em;
        text-align: left;
    }

    .markdown-preview :global(th) {
        background-color: rgba(0, 0, 0, 0.05);
    }

    /* Dark mode styles for markdown preview */
    .markdown-preview.dark-mode :global(blockquote) {
        border-left-color: #4a5568;
        color: #a0aec0;
    }

    .markdown-preview.dark-mode :global(code) {
        background-color: rgba(255, 255, 255, 0.1);
    }

    .markdown-preview.dark-mode :global(th),
    .markdown-preview.dark-mode :global(td) {
        border-color: #4a5568;
    }

    .markdown-preview.dark-mode :global(th) {
        background-color: rgba(255, 255, 255, 0.1);
    }

    /* Add hover effect */
    .markdown-preview:hover {
        background-color: rgba(0, 0, 0, 0.03);
    }

    .markdown-preview.dark-mode:hover {
        background-color: rgba(255, 255, 255, 0.03);
    }

    .circular-menu {
        position: fixed;
        z-index: 1100;
        transform: translate(-80px, -80px); /* Adjusted to move menu down-right from cursor */
    }

    .menu-items {
        position: relative;
        width: 200px;
        height: 200px;
    }

    .menu-item {
        position: absolute;
        width: 40px;
        height: 40px;
        border-radius: 50%;
        background-color: var(--bg-secondary);
        color: var(--text-secondary);
        border: none;
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
        transition: all 0.3s;
        box-shadow: 0 2px 8px var(--panel-shadow);
    }

    /* Base positions - these should remain unchanged on hover */
    .menu-item:nth-child(1) { transform: translate(80px, -40px); }    /* Top */
    .menu-item:nth-child(2) { transform: translate(120px, 0px); }     /* Top-right */
    .menu-item:nth-child(3) { transform: translate(120px, 80px); }    /* Right */
    .menu-item:nth-child(4) { transform: translate(80px, 120px); }    /* Bottom-right */
    .menu-item:nth-child(5) { transform: translate(0px, 120px); }     /* Bottom */
    .menu-item:nth-child(6) { transform: translate(-40px, 80px); }    /* Bottom-left */
    .menu-item:nth-child(7) { transform: translate(-40px, 0px); }     /* Left */
    .menu-item:nth-child(8) { transform: translate(0px, -40px); }     /* Top-left */
    .menu-item.center-item { transform: translate(40px, 40px); }      /* Center */

    /* Hover effects - only change colors and shadow */
    .menu-item:hover:not(:disabled) {
        background-color: var(--message-bg-user);
        color: var(--message-text-user);
        box-shadow: 0 2px 12px var(--panel-shadow);
    }

    /* Remove the debug styles */
    .settings-panel.open::before {
        content: none;
    }

    /* Update close button styles */
    .close-btn {
        background: none;
        border: none;
        padding: 8px;
        cursor: pointer;
        color: var(--text-secondary);
        display: flex;
        align-items: center;
        justify-content: center;
        transition: color 0.3s ease;
    }

    .close-btn:hover {
        color: var(--text-primary);
    }

    /* Update size buttons styles */
    .size-btn {
        background: none;
        border: none;
        padding: 4px;
        cursor: pointer;
        color: var(--text-secondary);
        display: flex;
        align-items: center;
        justify-content: center;
        transition: color 0.3s ease;
    }

    .size-btn:hover:not(:disabled) {
        color: var(--text-primary);
    }

    .size-btn:disabled {
        opacity: 0.5;
        cursor: not-allowed;
    }

    .size-display {
        min-width: 70px;
        text-align: center;
    }

    .floating-menu-btn {
        position: fixed;
        bottom: 2rem;
        right: 2rem;
        width: 48px;
        height: 48px;
        border-radius: 50%;
        background-color: var(--bg-secondary);
        color: var(--text-secondary);
        border: none;
        cursor: pointer;
        display: flex;
        align-items: center;
        justify-content: center;
        transition: all 0.3s;
        box-shadow: 0 2px 8px var(--panel-shadow);
        z-index: 1000;
    }

    .floating-menu-btn:hover {
        background-color: var(--message-bg-user);
        color: var(--message-text-user);
        box-shadow: 0 2px 12px var(--panel-shadow);
    }

    /* Hide floating button on larger screens */
    @media (min-width: 769px) {
        .floating-menu-btn {
            display: none;
        }
    }

    .context-menu {
        position: fixed;
        min-width: 200px;
        background-color: var(--bg-secondary);
        border-radius: 8px;
        box-shadow: 0 2px 12px var(--panel-shadow);
        padding: 8px 0;
        z-index: 1500;
        animation: fadeIn 0.1s ease;
    }

    .context-menu-item {
        display: flex;
        align-items: center;
        gap: 8px;
        width: 100%;
        padding: 8px 16px;
        border: none;
        background: none;
        color: var(--text-primary);
        font-size: 14px;
        text-align: left;
        cursor: pointer;
        transition: background-color 0.2s;
    }

    .context-menu-item:hover:not(:disabled) {
        background-color: var(--message-bg-user);
        color: var(--message-text-user);
    }

    .context-menu-item:disabled {
        opacity: 0.5;
        cursor: not-allowed;
    }

    .context-menu-divider {
        height: 1px;
        background-color: var(--border-color);
        margin: 4px 0;
    }

    .shortcut {
        margin-left: auto;
        color: var(--text-secondary);
        font-size: 12px;
    }

    @keyframes fadeIn {
        from {
            opacity: 0;
            transform: scale(0.95);
        }
        to {
            opacity: 1;
            transform: scale(1);
        }
    }

    /* Ensure context menu doesn't go off screen */
    @media (max-width: 768px) {
        .context-menu {
            min-width: 160px;
        }
    }

    /* Add these new styles */
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

    @media (max-width: 768px) {
        .loading-text {
            font-size: 3rem;
        }
    }

    /* Add font loading transition styles */
    .writing-container {
        animation: fontLoadTransition 0.5s ease-out forwards;
    }

    @keyframes fontLoadTransition {
        0% {
            filter: blur(3px);
            text-shadow: 
                0 0 10px rgba(255, 255, 255, 0.5),
                0 0 20px rgba(255, 255, 255, 0.3),
                0 0 30px rgba(255, 255, 255, 0.2);
            opacity: 0.7;
        }
        100% {
            filter: blur(0);
            text-shadow: none;
            opacity: 1;
        }
    }

    /* Update textarea styles to work with the animation */
    textarea {
        /* ... existing textarea styles ... */
        will-change: filter, opacity;
        backface-visibility: hidden;
    }

    /* Add dark mode specific glow */
    :global(body.dark-mode) .writing-container {
        animation: fontLoadTransitionDark 0.5s ease-out forwards;
    }

    @keyframes fontLoadTransitionDark {
        0% {
            filter: blur(3px);
            text-shadow: 
                0 0 10px rgba(0, 0, 0, 0.5),
                0 0 20px rgba(0, 0, 0, 0.3),
                0 0 30px rgba(0, 0, 0, 0.2);
            opacity: 0.7;
        }
        100% {
            filter: blur(0);
            text-shadow: none;
            opacity: 1;
        }
    }

    /* Add stronger initial blur/glow effect */
    .writing-container {
        animation: fontLoadTransition 1.2s cubic-bezier(0.4, 0, 0.2, 1) forwards;
    }

    @keyframes fontLoadTransition {
        0% {
            filter: blur(8px) brightness(1.2);
            text-shadow: 
                0 0 20px rgba(255, 255, 255, 0.8),
                0 0 40px rgba(255, 255, 255, 0.6),
                0 0 60px rgba(255, 255, 255, 0.4);
            opacity: 0.3;
        }
        50% {
            filter: blur(4px) brightness(1.1);
            opacity: 0.6;
        }
        100% {
            filter: blur(0) brightness(1);
            text-shadow: none;
            opacity: 1;
        }
    }

    /* Dark mode version */
    :global(body.dark-mode) .writing-container {
        animation: fontLoadTransitionDark 1.2s cubic-bezier(0.4, 0, 0.2, 1) forwards;
    }

    @keyframes fontLoadTransitionDark {
        0% {
            filter: blur(8px) brightness(0.8);
            text-shadow: 
                0 0 20px rgba(0, 0, 0, 0.8),
                0 0 40px rgba(0, 0, 0, 0.6),
                0 0 60px rgba(0, 0, 0, 0.4);
            opacity: 0.3;
        }
        50% {
            filter: blur(4px) brightness(0.9);
            opacity: 0.6;
        }
        100% {
            filter: blur(0) brightness(1);
            text-shadow: none;
            opacity: 1;
        }
    }

    /* Add styles for home button */
    .home-btn {
        width: 52px !important;  /* Make it slightly bigger */
        height: 52px !important;
        margin-bottom: 1rem !important;  /* Add some space before other controls */
    }

    .home-btn svg {
        width: 28px;  /* Larger icon */
        height: 28px;
    }
</style>