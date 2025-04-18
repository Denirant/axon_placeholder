<!-- WaveEffect.svelte -->
<script>
  import { onMount, onDestroy } from 'svelte';
  import * as THREE from 'three';
  
  // Component props with default values
  export let resolution = 100; // Number of points in the grid
  export let waveSpeed = 0.5; // Speed of the wave animation
  export let waveHeight = 15; // Maximum height of the waves
  export let waveFrequency = 0.1; // Frequency of the waves
  export let pointSizeMin = 0.5; // Minimum point size
  export let pointSizeMax = 3.0; // Maximum point size
  export let interactionRadius = 20; // Radius of cursor interaction
  export let interactionForce = 5; // Force of cursor interaction
  export let basePointSize = 2.0; // Base size of the points
  export let planeSize = 100; // Size of the plane (width and depth)
  export let rotationX = Math.PI * 0.2; // Rotation around X axis (in radians)
  export let rotationY = 0; // Rotation around Y axis (in radians)
  export let rotationZ = 0; // Rotation around Z axis (in radians)
  export let fadeThreshold = 0.5; // Size threshold for fade effect (0.0 - 1.0)
  export let fadeStart = 0.0; // When to start fading (0.0 - 1.0, must be less than fadeThreshold)
  export let wavePattern = 1; // Wave pattern type (1-5)

  let container;
  let scene, camera, renderer;
  let positions = [];
  let originalPositions = [];
  let mouse = new THREE.Vector2(0, 0);
  let mousePosition = new THREE.Vector3(0, 0, 0);
  let raycaster = new THREE.Raycaster();
  let clock;
  let frame;
  let pointsObject;
  
  // Track previous values to prevent infinite loops
  let prevRotationX = rotationX;
  let prevRotationY = rotationY;
  let prevRotationZ = rotationZ;
  let prevPlaneSize = planeSize;
  let prevWavePattern = wavePattern;
  
  // Initialize the scene
  function init() {
    if (!container) return;
    
    // Create scene
    scene = new THREE.Scene();
    scene.background = new THREE.Color(0x000000);
    
    // Create camera
    const aspect = window.innerWidth / window.innerHeight;
    camera = new THREE.PerspectiveCamera(75, aspect, 0.1, 1000);
    camera.position.set(0, 30, 50);
    camera.lookAt(0, 0, 0);
    
    // Create renderer
    renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    container.appendChild(renderer.domElement);
    
    // Initialize clock
    clock = new THREE.Clock();
    
    // Create points
    pointsObject = createPoints();
    
    // Apply initial rotation
    updateRotation();
    
    // Add event listeners
    window.addEventListener('resize', onWindowResize);
    window.addEventListener('mousemove', onMouseMove);

    // Start animation
    animate();
  }
  
  // Update rotation of the points object
  function updateRotation() {
    if (!pointsObject) return;
    
    pointsObject.rotation.set(rotationX, rotationY, rotationZ);
  }
  
  // Create the points
  function createPoints() {
    // Create geometry
    const geometry = new THREE.BufferGeometry();
    positions = [];
    originalPositions = [];
    
    // Create a grid of points
    const gridSize = resolution;
    const spacing = planeSize / gridSize;
    const halfSize = planeSize / 2;
    
    for (let i = 0; i < gridSize; i++) {
      for (let j = 0; j < gridSize; j++) {
        // Calculate position
        const x = (i * spacing) - halfSize;
        const y = 0;
        const z = (j * spacing) - halfSize;
        
        // Store positions
        positions.push(x, y, z);
        originalPositions.push(x, y, z);
      }
    }
    
    // Add position attribute to geometry
    geometry.setAttribute('position', new THREE.Float32BufferAttribute(positions, 3));
    
    // Create a custom shader material to handle dynamic point sizes and opacity
    const shaderMaterial = new THREE.ShaderMaterial({
      uniforms: {
        basePointSize: { value: basePointSize },
        pointSizeMin: { value: pointSizeMin },
        pointSizeMax: { value: pointSizeMax },
        waveHeight: { value: waveHeight },
        color: { value: new THREE.Color(0xffffff) },
        fadeThreshold: { value: fadeThreshold },
        fadeStart: { value: fadeStart }
      },
      vertexShader: `
        uniform float basePointSize;
        uniform float pointSizeMin;
        uniform float pointSizeMax;
        uniform float waveHeight;
        uniform float fadeThreshold;
        uniform float fadeStart;
        
        varying float vOpacity;
        
        void main() {
          vec4 mvPosition = modelViewMatrix * vec4(position, 1.0);
          gl_Position = projectionMatrix * mvPosition;
          
          // Calculate normalized height for size (0.0 to 1.0 range)
          float normalizedHeight = (position.y + waveHeight) / (waveHeight * 2.0);
          
          // Calculate point size based on wave height
          float pointSize = pointSizeMin + normalizedHeight * (pointSizeMax - pointSizeMin);
          
          // Normalize size for opacity calculation (0.0 to 1.0 range)
          float sizeRatio = normalizedHeight;
          
          // Calculate smooth opacity fade based on extended thresholds
          vOpacity = smoothstep(fadeStart, fadeThreshold, sizeRatio);
          
          // Set minimum size to avoid GPU rendering issues
          float minSize = 0.1;
          
          // Apply size based on calculated values and minimum threshold
          gl_PointSize = max(minSize, basePointSize * pointSize * (300.0 / -mvPosition.z));
        }
      `,
      fragmentShader: `
        uniform vec3 color;
        varying float vOpacity;
        
        void main() {
          // Create a circular point with soft edges
          vec2 center = vec2(0.5, 0.5);
          float dist = distance(gl_PointCoord, center);
          
          // Soften the edge of the circle slightly
          float circle = smoothstep(0.5, 0.48, dist);
          
          // Apply opacity based on size and distance from center
          float finalOpacity = vOpacity * circle;
          
          // Skip rendering completely transparent pixels
          if (finalOpacity < 0.001) {
            discard;
          } else {
            gl_FragColor = vec4(color, finalOpacity);
          }
        }
      `,
      transparent: true,
      depthTest: false, // Disable depth test to avoid z-fighting with transparent points
      depthWrite: false, // Disable depth write to improve blending
      blending: THREE.AdditiveBlending // Use additive blending for smoother transitions
    });
    
    // Create the points with the shader material
    const pointsObj = new THREE.Points(geometry, shaderMaterial);
    
    scene.add(pointsObj);
    
    return pointsObj;
  }
  
  // Handle window resize
  function onWindowResize() {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  }
  
  // Handle mouse movement
  function onMouseMove(event) {
    // Calculate mouse position in normalized device coordinates
    mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
    mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
    
    // Update raycaster
    raycaster.setFromCamera(mouse, camera);
    
    // Calculate intersection with the plane
    // Create a plane that matches current rotation
    const normal = new THREE.Vector3(0, 1, 0);
    normal.applyEuler(new THREE.Euler(rotationX, rotationY, rotationZ));
    
    const plane = new THREE.Plane(normal.normalize(), 0);
    const intersection = new THREE.Vector3();
    const didIntersect = raycaster.ray.intersectPlane(plane, intersection);
    
    if (didIntersect) {
      // Convert intersection point to local coordinates of the points object
      const localIntersection = intersection.clone();
      if (pointsObject) {
        pointsObject.worldToLocal(localIntersection);
      }
      
      // Store mouse position for interaction
      mousePosition.copy(localIntersection);
    }
  }
  
  // Different wave patterns
  function wavePattern1(x, z, time) {
    // Classic sine/cosine waves with ripple
    const wave1 = Math.sin(x * waveFrequency + time * waveSpeed) * 
                 Math.cos(z * waveFrequency + time * waveSpeed) * waveHeight;
    
    const wave2 = Math.sin(x * waveFrequency * 0.5 + time * waveSpeed * 1.3) * 
                 Math.sin(z * waveFrequency * 0.7 + time * waveSpeed * 0.8) * waveHeight * 0.5;
    
    const wave3 = Math.cos(x * waveFrequency * 0.2 - time * waveSpeed * 0.5) * 
                 Math.sin(z * waveFrequency * 0.3 + time * waveSpeed * 1.2) * waveHeight * 0.3;
    
    // Add a circular ripple effect
    const distanceFromCenter = Math.sqrt(x * x + z * z);
    const ripple = Math.sin(distanceFromCenter * 0.3 - time * waveSpeed * 2) * waveHeight * 0.2;
    
    return wave1 + wave2 + wave3 + ripple;
  }
  
  function wavePattern2(x, z, time) {
    // Radial waves pattern
    const freq = waveFrequency * 0.7;
    const speed = waveSpeed * 1.2;
    
    const distanceFromCenter = Math.sqrt(x * x + z * z);
    const angle = Math.atan2(z, x);
    
    // Multiple radial waves with different frequencies
    const radial1 = Math.sin(distanceFromCenter * freq - time * speed) * waveHeight * 0.7;
    const radial2 = Math.sin(distanceFromCenter * freq * 0.5 - time * speed * 0.7) * waveHeight * 0.4;
    const radial3 = Math.sin(distanceFromCenter * freq * 0.3 + time * speed * 0.5) * waveHeight * 0.3;
    
    // Add angular waves
    const angular = Math.sin(angle * 5 + time * speed * 0.3) * waveHeight * 0.15;
    
    // Add spiral pattern
    const spiral = Math.sin(distanceFromCenter * freq * 0.8 + angle * 3 - time * speed) * waveHeight * 0.3;
    
    return radial1 + radial2 + radial3 + angular + spiral;
  }
  
  function wavePattern3(x, z, time) {
    // Interference patterns like water drops
    const speed = waveSpeed * 0.8;
    const freq = waveFrequency * 1.2;
    
    // Create multiple "drop" centers
    const dropPoints = [
      { x: 20, z: 20, phase: 0 },
      { x: -30, z: 10, phase: 1 },
      { x: 0, z: -25, phase: 2 },
      { x: -15, z: -15, phase: 3 },
      { x: 30, z: -20, phase: 4 }
    ];
    
    let result = 0;
    
    // Sum waves from all drop points
    for (const drop of dropPoints) {
      const dx = x - drop.x;
      const dz = z - drop.z;
      const distance = Math.sqrt(dx * dx + dz * dz);
      
      // Waves get smaller as they spread out
      const amplitude = waveHeight * 0.5 * (1 / (1 + distance * 0.03));
      
      // Each drop has its own phase
      result += Math.sin(distance * freq - time * speed + drop.phase) * amplitude;
    }
    
    // Add a slow-moving base wave
    const baseWave = Math.sin(x * freq * 0.1 + z * freq * 0.1 + time * speed * 0.2) * waveHeight * 0.3;
    
    return result + baseWave;
  }
  
  function wavePattern4(x, z, time) {
    // Geometric wave pattern with squares and diamonds
    const freq = waveFrequency;
    const speed = waveSpeed;
    
    // Manhattan distance for square patterns
    const manhattanDist = Math.abs(x) + Math.abs(z);
    const squareWave = Math.sin(manhattanDist * freq * 0.5 - time * speed) * waveHeight * 0.5;
    
    // Chebyshev distance for diamond patterns
    const chebyshevDist = Math.max(Math.abs(x), Math.abs(z));
    const diamondWave = Math.sin(chebyshevDist * freq * 0.4 - time * speed * 1.2) * waveHeight * 0.6;
    
    // Euclidean distance for circular patterns
    const euclideanDist = Math.sqrt(x * x + z * z);
    const circleWave = Math.sin(euclideanDist * freq * 0.3 - time * speed * 0.8) * waveHeight * 0.4;
    
    // Mix the patterns with a time-based weight
    const mixFactor = Math.sin(time * 0.3) * 0.5 + 0.5;
    
    return squareWave * (1 - mixFactor) + diamondWave * mixFactor + circleWave;
  }
  
  function wavePattern5(x, z, time) {
    // Fractal-like pattern
    const frequencyMultiplier = waveFrequency * 0.8;
    
    let result = 0;
    let amplitude = waveHeight;
    let frequency = frequencyMultiplier;
    
    // Sum multiple octaves of noise
    for (let i = 0; i < 5; i++) {
      const wx = x * frequency;
      const wz = z * frequency;
      const t = time * waveSpeed * (i * 0.2 + 0.8);
      
      // Use sine approximation for noise
      const noise = Math.sin(wx + t) * Math.cos(wz + t * 1.1) * 
                   Math.sin(wx * 0.7 + wz * 0.7 + t * 0.9);
      
      result += noise * amplitude;
      
      // Adjust for next octave
      amplitude *= 0.5;
      frequency *= 2.0;
    }
    
    // Add some angular variation
    const angle = Math.atan2(z, x);
    const angularWave = Math.sin(angle * 6 + time * waveSpeed * 0.2) * waveHeight * 0.1;
    
    return result + angularWave;
  }
  
  // Wave pattern selector
  function getWaveHeight(x, z, time) {
    switch(wavePattern) {
      case 1: return wavePattern1(x, z, time);
      case 2: return wavePattern2(x, z, time);
      case 3: return wavePattern3(x, z, time);
      case 4: return wavePattern4(x, z, time);
      case 5: return wavePattern5(x, z, time);
      default: return wavePattern1(x, z, time);
    }
  }
  
  // Check for prop changes that require updates
  function checkForChanges() {
    // Check if rotation changed
    if (rotationX !== prevRotationX || rotationY !== prevRotationY || rotationZ !== prevRotationZ) {
      prevRotationX = rotationX;
      prevRotationY = rotationY;
      prevRotationZ = rotationZ;
      updateRotation();
    }
    
    // Check if plane size or wave pattern changed
    if ((planeSize !== prevPlaneSize || wavePattern !== prevWavePattern) && pointsObject) {
      prevPlaneSize = planeSize;
      prevWavePattern = wavePattern;
      scene.remove(pointsObject);
      pointsObject = createPoints();
      updateRotation();
    }
  }
  
  // Animation loop
  function animate() {
    frame = requestAnimationFrame(animate);
    
    // Check for prop changes
    checkForChanges();
    
    if (!pointsObject || !pointsObject.geometry) return;
    
    const time = clock.getElapsedTime();
    const positionArray = pointsObject.geometry.attributes.position.array;
    
    // Update shader uniforms
    if (pointsObject.material.uniforms) {
      pointsObject.material.uniforms.basePointSize.value = basePointSize;
      pointsObject.material.uniforms.pointSizeMin.value = pointSizeMin;
      pointsObject.material.uniforms.pointSizeMax.value = pointSizeMax;
      pointsObject.material.uniforms.waveHeight.value = waveHeight;
      pointsObject.material.uniforms.fadeThreshold.value = fadeThreshold;
      pointsObject.material.uniforms.fadeStart.value = fadeStart;
    }
    
    // Update each point's position
    for (let i = 0; i < positionArray.length; i += 3) {
      const x = originalPositions[i];
      const z = originalPositions[i + 2];
      
      // Calculate wave height using the selected pattern
      const y = getWaveHeight(x, z, time);
      positionArray[i + 1] = y;
      
      // Apply cursor interaction
      if (mousePosition.x !== 0 || mousePosition.y !== 0 || mousePosition.z !== 0) {
        const dx = x - mousePosition.x;
        const dz = z - mousePosition.z;
        const distance = Math.sqrt(dx * dx + dz * dz);
        
        if (distance < interactionRadius) {
          const force = (1 - distance / interactionRadius) * interactionForce;
          const angle = Math.atan2(dz, dx);
          
          positionArray[i] = x + Math.cos(angle) * force;
          positionArray[i + 2] = z + Math.sin(angle) * force;
        } else {
          // Gradually return to original position
          positionArray[i] = x + (positionArray[i] - x) * 0.9;
          positionArray[i + 2] = z + (positionArray[i + 2] - z) * 0.9;
        }
      }
    }
    
    // Update geometry
    pointsObject.geometry.attributes.position.needsUpdate = true;
    
    // Render scene
    renderer.render(scene, camera);
  }
  
  onMount(() => {
    init();
  });
  
  onDestroy(() => {
    if (frame) {
      cancelAnimationFrame(frame);
    }
    
    if (renderer) {
      renderer.dispose();
    }
    
    if (scene) {
      // Clean up scene
      scene.children.forEach(child => {
        if (child.geometry) child.geometry.dispose();
        if (child.material) child.material.dispose();
        scene.remove(child);
      });
    }
    
    window.removeEventListener('resize', onWindowResize);
    window.removeEventListener('mousemove', onMouseMove);
  });
</script>

<div bind:this={container} class="container"></div>

<style>
  .container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
    background-color: #000;
  }
</style>