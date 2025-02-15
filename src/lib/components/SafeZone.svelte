<script>
    import { onMount } from 'svelte';
    
    export let position = { x: 0, y: 0 };
    
    let safeZone;
    let parentRect;
    let safeZoneRect;
    let adjustedPosition = { x: 0, y: 0 };
    
    function adjustPosition() {
        if (!safeZone || !parentRect) return;
        
        // Update safe zone rect as it might have changed
        safeZoneRect = safeZone.getBoundingClientRect();
        
        // Calculate boundaries
        const minX = 0;
        const maxX = parentRect.width - safeZoneRect.width;
        const minY = 0;
        const maxY = parentRect.height - safeZoneRect.height;
        
        // Adjust position to stay within parent boundaries
        adjustedPosition = {
            x: Math.max(minX, Math.min(maxX, position.x)),
            y: Math.max(minY, Math.min(maxY, position.y))
        };
        
        // Apply position
        safeZone.style.transform = `translate(${adjustedPosition.x}px, ${adjustedPosition.y}px)`;
    }
    
    onMount(() => {
        const parent = safeZone.parentElement;
        parentRect = parent.getBoundingClientRect();
        
        // Initial position adjustment
        adjustPosition();
        
        // Update both rects on resize
        const resizeObserver = new ResizeObserver(() => {
            parentRect = parent.getBoundingClientRect();
            adjustPosition();
        });
        
        // Observe both parent and safe zone for size changes
        resizeObserver.observe(parent);
        resizeObserver.observe(safeZone);
        
        return () => {
            resizeObserver.disconnect();
        };
    });
    
    $: if (position) {
        adjustPosition();
    }
</script>

<div 
    bind:this={safeZone}
    class="safe-zone"
>
    <slot />
</div>

<style>
    .safe-zone {
        position: absolute;
        pointer-events: auto;
        /* Ensure the safe zone doesn't overflow its container */
        max-width: 100%;
        max-height: 100%;
    }
</style> 