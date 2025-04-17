<script>
  import Loader from "./Loader.svelte";
  import { onMount } from 'svelte';
  
  let backgroundColor = "#151515";
  
  // Removed localStorage.clear() to preserve existing localStorage items
  
  function generateRandomNumber() {
    const length = Math.random() < 0.5 ? 12 : 13;
    let result = Math.floor(Math.random() * 9) + 1;

    for (let i = 1; i < length; i++) {
      result = result * 10 + Math.floor(Math.random() * 10);
    }

    return result;
  }
  
  onMount(() => {
    // Only set these localStorage values if they don't exist yet
    const initializeStorage = () => {
      if (!localStorage.getItem('controlsSize')) localStorage.setItem('controlsSize', 'sm');
      if (!localStorage.getItem('local')) localStorage.setItem('local', 'ru-RU');
      if (!localStorage.getItem('showWidgets')) localStorage.setItem('showWidgets', false);
      if (!localStorage.getItem('showPinned')) localStorage.setItem('showPinned', false);
      if (!localStorage.getItem('showOverlay')) localStorage.setItem('showOverlay', false);
      if (!localStorage.getItem('theme')) localStorage.setItem('theme', 'system');
      if (!localStorage.getItem('version')) localStorage.setItem('version', 'v1.0.2');
      if (!localStorage.getItem('accessToken')) localStorage.setItem('accessToken', null);
      if (!localStorage.getItem('toastToken')) localStorage.setItem('toastToken', generateRandomNumber());
      if (!localStorage.getItem('dismissedUpdateSocket')) localStorage.setItem('dismissedUpdateSocket', generateRandomNumber());
      if (!localStorage.getItem('role')) localStorage.setItem('role', null);
      if (!localStorage.getItem('sidebar')) localStorage.setItem('sidebar', false);
      if (!localStorage.getItem('engineDefault')) localStorage.setItem('engineDefault', 'duckduckgo');
    };

    // Set background color and initialize localStorage values with a delay only if localStorage is empty
    const delay = localStorage.length > 0 ? 0 : 2000;
    
    setTimeout(() => {
      backgroundColor = "#151515";
      initializeStorage();
    }, delay);
  });
</script>

<div class="flex justify-center items-center w-screen h-screen border app-container safe-area-inset"
     style="background-color: {backgroundColor}">
  <Loader />
</div>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    overflow: hidden;
    background-color: #151515; /* Устанавливаем тот же цвет фона, что и в приложении */
  }
  
  :global(html) {
    height: 100%;
    background-color: #151515;
  }
  
  .app-container {
    transition: background-color 0.5s ease-in-out;
  }
  
  .safe-area-inset {
    padding-top: env(safe-area-inset-top);
    padding-bottom: env(safe-area-inset-bottom);
    padding-left: env(safe-area-inset-left);
    padding-right: env(safe-area-inset-right);
  }
</style>