<!-- <script lang="ts">
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Counter from './lib/Counter.svelte'
</script>

<main>
  <div>
    <a href="https://vite.dev" target="_blank" rel="noreferrer">
      <img src={viteLogo} class="logo" alt="Vite Logo" />
    </a>
    <a href="https://svelte.dev" target="_blank" rel="noreferrer">
      <img src={svelteLogo} class="logo svelte" alt="Svelte Logo" />
    </a>
  </div>
  <h1>Vite + Svelte</h1>

  <div class="card">
    <Counter />
  </div>

  <p>
    Check out <a href="https://github.com/sveltejs/kit#readme" target="_blank" rel="noreferrer">SvelteKit</a>, the official Svelte app framework powered by Vite!
  </p>

  <p class="read-the-docs">
    Click on the Vite and Svelte logos to learn more
  </p>
</main>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
  .read-the-docs {
    color: #888;
  }
</style> -->



<div class="absolute w-full h-full flex z-50">
    <div
        class="absolute rounded-xl w-full h-full backdrop-blur-sm flex justify-center"
    >
        <div class="m-auto pb-44 flex flex-col justify-center">
            <div class="max-w-md">
                <div class="text-center text-2xl font-medium z-50">
                    Unable to Connect
                </div>

                <div class=" mt-4 text-center text-sm w-full">
                    We're having trouble connecting to the server right now. This could be due to maintenance or technical issues.
                    <br class=" " />
                    <br class=" " />
                    <a
                        class="font-semibold underline"
                        href=""
                        target="_blank"
                        >View server logs</a
                    >
                    or
                    <a
                        class="font-semibold underline"
                        href=""
                        target="_blank"
                        >Contact support team.</a
                    >
                </div>

                <div class=" mt-6 mx-auto relative group w-fit">
                    <button
                        class="relative z-20 flex px-5 py-2 rounded-full bg-gray-100 hover:bg-gray-200 transition font-medium text-sm"
                        on:click={() => {
                            location.href = "/";
                        }}
                    >
                        Try Again
                    </button>
                </div>
            </div>
        </div>
    </div>
</div>
