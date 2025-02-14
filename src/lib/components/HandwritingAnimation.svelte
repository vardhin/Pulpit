<script>
  import { onMount } from 'svelte';
  import Vara from 'vara';

  export let text;
  export let fontSize = 22;
  export let strokeWidth = 2;
  export let color = '#000';
  export let duration = 2000;

  let container;
  const containerId = `vara-container-${Math.random().toString(36).substr(2, 9)}`;

  // Change the font URL to a different handwriting font
  const fontUrl = 'https://raw.githubusercontent.com/akzhy/Vara/master/fonts/Pacifico/PacificoSLO.json';
  // Alternative fonts you can try:
  // 'https://raw.githubusercontent.com/akzhy/Vara/master/fonts/Pacifico/PacificoSLO.json'
  // 'https://raw.githubusercontent.com/akzhy/Vara/master/fonts/Satisfy/SatisfySL.json'

  onMount(() => {
    container.id = containerId;
    new Vara(
      `#${containerId}`,
      fontUrl,
      [{
        text,
        fontSize,
        strokeWidth,
        color,
        duration,
        textAlign: 'center'
      }],
      {
        ready: () => console.log('Font loaded')
      }
    );
  });
</script>

<div bind:this={container}></div>

<style>
  div {
    width: 100%;
    height: 100%;
  }
</style>
