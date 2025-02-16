<script>
  export let isLoading = true;
</script>

{#if isLoading}
  <div class="loading-overlay" class:fade-out={!isLoading}>
    <div class="loading-content">
      <div class="loading-text">Pulpit!</div>
    </div>
  </div>
{/if}

<style>
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
</style>
