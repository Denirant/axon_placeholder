<script>
    export let size = 100;
    export let strokeWidth = 4;
    export let primaryColor = "#151515";
    export let accentColor = "#f2f2f2";
    
    import LogoIcon from './assets/favicon.svg';
    
    let isVisible = true;
    let containerSize = size;
    let backgroundColor = "transparent";

    // Check if localStorage has any items and set delay accordingly
    const hasLocalStorage = () => {
        return localStorage.length > 0;
    };
    
    const delay = hasLocalStorage() ? 0 : 4000;
    const subDelay = hasLocalStorage() ? 0 : 100;

    setTimeout(() => {
        isVisible = false;
        backgroundColor = "#212121";
        setTimeout(() => {
            containerSize = 100; 
        }, subDelay);
    }, delay);
</script>

<div class="loader-wrapper">
    <div class="loader-container relative transition-all duration-400" 
         style="width: {containerSize}px; height: {containerSize}px; overflow: visible;">
        {#if isVisible}
            <svg viewBox="0 0 100 100" class="pulse-animation" xmlns="http://www.w3.org/2000/svg">
                <circle
                    cx="50"
                    cy="50"
                    r="24"
                    fill="none"
                    stroke={primaryColor}
                    stroke-width={strokeWidth}
                />
                
                <circle
                    cx="50"
                    cy="50"
                    r="24"
                    fill="none"
                    stroke={accentColor}
                    stroke-width={strokeWidth}
                    stroke-dasharray="160"
                    stroke-dashoffset="90"
                    stroke-linecap="round"
                    class="progress-circle"
                />
            </svg>
        {/if}
        
        <img 
            src={LogoIcon} 
            class="logo-image" 
            class:enlarged={!isVisible} 
            class:centered={!isVisible} 
            alt="Logo"
        >
    </div>
</div>

<style>
    .loader-wrapper {
        position: relative;
        display: inline-block;
    }
    
    .pulse-animation {
        animation: pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite;
        opacity: 1;
        transition: opacity 0.5s ease-out;
    }
    
    @keyframes pulse {
        0%, 100% {
            opacity: 0.8;
        }
        80% {
            opacity: 0.95;
        }
    }
    
    .loader-container {
        position: relative;
        transition: all 0.5s ease-in-out;
        border-radius: 50%;
    }
    
    svg {
        width: 100%;
        height: 100%;
        transform: rotate(-90deg);
    }
    
    .progress-circle {
        transform-origin: center;
        animation: dash 2s cubic-bezier(0.35, 0, 0.65, 1) infinite,
                    spin 1s linear infinite;
    }
    
    @keyframes dash {
        0%, 100% {
            stroke-dashoffset: 160;
        }
        50% {
            stroke-dashoffset: 90;
        }
    }
    
    @keyframes spin {
        0% {
            transform: rotate(0deg);
        }
        100% {
            transform: rotate(360deg);
        }
    }
    
    .logo-image {
        position: absolute;
        top: 31px;
        left: 31px;
        width: 38px;
        height: 38px;
        transition: all .2s ease-in-out;
        z-index: 10;
    }
    
    .logo-image.enlarged {
        width: 90px;
        height: 90px;
    }
    
    .logo-image.centered {
        top: 5px;
        left: 5px;
    }
</style>