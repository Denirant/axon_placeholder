<script>
  import Loader from "./Loader.svelte";
  import { onMount } from 'svelte';
  
  let backgroundColor = "white";
  localStorage.clear();


  function generateRandomNumber() {
  const length = Math.random() < 0.5 ? 12 : 13;
  let result = Math.floor(Math.random() * 9) + 1;

  for (let i = 1; i < length; i++) {
    result = result * 10 + Math.floor(Math.random() * 10);
  }

  return result;
}
  
  onMount(() => {
      setTimeout(() => {
          backgroundColor = "#151515";
          localStorage.setItem('controlsSize', 'sm');
          localStorage.setItem('local', 'ru-RU');
          localStorage.setItem('showWidgets', false);
          localStorage.setItem('showPinned', false);
          localStorage.setItem('showOverlay', false);
          localStorage.setItem('theme', 'system');
          localStorage.setItem('version', 'v1.0.2');
          localStorage.setItem('accessToken', null);
          localStorage.setItem('toastToken', generateRandomNumber());
          localStorage.setItem('dismissedUpdateSocket', generateRandomNumber());
          localStorage.setItem('role', null);
          localStorage.setItem('sidebar', false);
          localStorage.setItem('engineDefault', duckduckgo);
      }, 2000);
  });
</script>

<div class="flex justify-center items-center w-screen h-screen border app-container"
   style="background-color: {backgroundColor}">
  <Loader />
</div>

<style>
  :global(body) {
      margin: 0;
      padding: 0;
      overflow: hidden;
  }
  
  .app-container {
      transition: background-color 0.5s ease-in-out;
  }
</style>