<script>
    // You can customize these props as needed
    export let size = 50; // Size in pixels
    export let strokeWidth = 8; // Width of the circular track
    export let primaryColor = "#fff"; // Light blue background circle color
    export let accentColor = "#3d3d3d"; // Blue progress indicator color

    import logo from './assets/favicon.ico'
  </script>
  
  <div class="loader-container relative pulse-animation" style="width: {size}px; height: {size}px;">
    <svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
      <!-- Background circle -->
      <circle
        cx="50"
        cy="50"
        r="46"
        fill="none"
        stroke={primaryColor}
        stroke-width={strokeWidth}
      />
      
      <!-- Loading indicator segment -->
      <circle
        cx="50"
        cy="50"
        r="46"
        fill="none"
        stroke={accentColor}
        stroke-width={strokeWidth}
        stroke-dasharray="290"
        stroke-dashoffset="240"
        stroke-linecap="round"
        class="progress-circle"
      />
    </svg>


    <img src={logo} class="w-[30px] h-[30px] absolute top-[10px] left-[10px] pulse-animation">
  </div>

  
  <style>

  .pulse-animation {
    animation: pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite;
  }

  @keyframes pulse {
    0%, 100% {
      opacity: 0.4;
    }
    80% {
      opacity: 0.8;
    }
  }

    .loader-container {
      position: relative;
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
        stroke-dashoffset: 240;
      }
      50% {
        stroke-dashoffset: 80;
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
  </style>