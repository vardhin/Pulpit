<script>
  import { onMount } from 'svelte';
  import Vara from 'vara';

  export let text = "Hello World"; // Text to animate
  export let fontSize = 40;
  export let strokeWidth = 0.7;

  onMount(() => {
    new Vara(
      "#vara-container",
      "https://raw.githubusercontent.com/akzhy/Vara/master/fonts/Satisfy/SatisfySL.json",
      [{
        text: text,
        fontSize: fontSize,
        strokeWidth: strokeWidth,
      }]
    );
  });
</script>

<div id="vara-container"></div>

<style>
  #vara-container {
    width: 100%;
    max-width: 600px;
    margin: 0 auto;
  }
</style>
