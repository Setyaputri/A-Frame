<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1" />
<title>Satellite Orbit Visualization - A-Frame</title>
<script src="https://aframe.io/releases/1.4.1/aframe.min.js"></script>
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet" />
<style>
  /* Reset & base */
  *, *::before, *::after {
    box-sizing: border-box;
  }
  body, html {
    margin: 0; padding: 0; height: 100%;
    font-family: 'Inter', sans-serif;
    color: #e0e0e0;
    background: radial-gradient(ellipse at center, #000000 0%, #0a0a0a 100%);
    overflow: hidden;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
  /* HEADER: sticky glass morphism */
  header {
    height: 64px;
    position: sticky;
    top: 0;
    background: rgba(15, 15, 25, 0.5);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    display: flex;
    align-items: center;
    padding: 0 24px;
    z-index: 1000;
    box-shadow: 0 0 10px rgba(0,0,0,0.3);
    border-bottom: 1px solid rgba(255,255,255,0.1);
  }
  header .logo {
    font-weight: 700;
    font-size: clamp(1.25rem, 2vw, 1.75rem);
    background: linear-gradient(90deg, #6366f1, #06b6d4);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    user-select: none;
  }

  /* A-Frame canvas fills remaining space */
  #scene-container {
    height: calc(100vh - 64px);
    width: 100vw;
  }

  /* Custom orbit animation using A-Frame Animation component */
  .satellite-group {
    cursor: pointer;
    transition: transform 0.3s ease;
  }
  .satellite-group:hover {
    transform: scale(1.3);
  }

  /* Tooltip for accessibility */
  #tooltip {
    position: fixed;
    pointer-events: none;
    background: rgba(0,0,0,0.7);
    color: white;
    padding: 6px 10px;
    font-size: 14px;
    border-radius: 6px;
    font-weight: 500;
    user-select: none;
    opacity: 0;
    transition: opacity 0.3s ease;
    z-index: 2000;
    max-width: 220px;
    word-wrap: break-word;
  }
</style>
</head>
<body>
<header role="banner" aria-label="Application Header">
  <div class="logo" aria-live="polite">Satellite Orbit Visualization</div>
</header>

<div id="scene-container" role="main" aria-label="3D satellite orbit visualization">
  <a-scene 
    vr-mode-ui="enabled: false" 
    embedded 
    background="color: #000014" 
    renderer="antialias: true; precision: high" 
    keyboard-shortcuts="enabled: true"
    inspector="url: https://aframe.io/releases/1.4.1/aframe-inspector.min.js"
  >
    <!-- Assets -->
    <a-assets>
      <img id="starfield" src="https://cdn.aframe.io/360-image-gallery-boilerplate/img/starfield.png" alt="Star field background" crossorigin="anonymous" />
    </a-assets>

    <!-- Camera with cursor -->
    <a-entity id="cameraRig" position="0 1.6 6">
      <a-camera 
        wasd-controls-enabled="true" 
        look-controls="pointerLockEnabled: true" 
        position="0 0 0" 
        fov="75" 
        near="0.1" far="1000"
        aria-label="3D camera view"
      >
        <a-cursor 
          id="cursor" 
          color="#6366f1"
          fuse="true"
          fuse-timeout="1500"
          aria-label="Look cursor" 
          animation__click="property: scale; startEvents: click; from: 1 1 1; to: 1.5 1.5 1.5; dur: 150; dir: alternate"
        ></a-cursor>
      </a-camera>
    </a-entity>

    <!-- Earth with blue material -->
    <a-sphere 
      id="earth" 
      color="#3b82f6"
      radius="1" 
      segments-height="64" 
      segments-width="64" 
      rotation="0 -23.5 0" 
      aria-label="Earth globe"
      position="0 1.6 0"
      shadow="receive:true"
      material="shader: standard; roughness: 0.5; metalness: 0.1; color: #3b82f6; emissive: #1e40af; emissiveIntensity: 0.2;"
    ></a-sphere>

    <!-- Starfield Background Sphere -->
    <a-sphere 
      radius="90" 
      src="#starfield" 
      material="side:back; shader:flat;" 
      position="0 1.6 0" 
      aria-label="Star field background"
    ></a-sphere>

    <!-- Additional star particles scattered in space -->
    <a-entity id="starfield-particles"></a-entity>

    <!-- Satellite Orbit Groups -->

    <a-entity id="orbit1" position="0 1.6 0" rotation="0 0 0" animation="property: rotation; to: 0 360 0; loop: true; dur: 20000; easing: linear">
      <a-entity class="satellite-group" position="3 0 0" aria-label="Satellite 1" role="img">
        <a-box depth="0.25" height="0.15" width="0.4" color="#06b6d4" material="shader: standard; metalness: 0.9; roughness: 0.2; emissive: #047ea3; emissiveIntensity: 0.3" animation="property: rotation; to: 0 360 0; loop: true; dur: 8000; easing: linear" shadow="cast:true"></a-box>
        <a-box depth="0.02" height="0.12" width="0.8" color="#0ea5e9" position="0 0.08 0" material="shader: standard; metalness: 0.8; roughness: 0.3; emissive: #0369a1; emissiveIntensity: 0.15" shadow="cast:true"></a-box>
        <a-box depth="0.02" height="0.12" width="0.8" color="#0ea5e9" position="0 -0.08 0" material="shader: standard; metalness: 0.8; roughness: 0.3; emissive: #0369a1; emissiveIntensity: 0.15" shadow="cast:true"></a-box>
        <a-cylinder radius="0.015" height="0.15" color="#0e7490" position="0 0 0.23" material="shader: standard; metalness: 0.95; roughness: 0.25; emissive: #134e4a; emissiveIntensity: 0.2" shadow="cast:true"></a-cylinder>
      </a-entity>
    </a-entity>

    <a-entity id="orbit2" position="0 1.6 0" rotation="45 0 0" animation="property: rotation; to: 45 360 0; loop: true; dur: 30000; easing: linear">
      <a-entity class="satellite-group" position="0 0 4" aria-label="Satellite 2" role="img">
        <a-sphere radius="0.14" color="#8b5cf6" material="shader: standard; metalness: 0.8; roughness: 0.15; emissive: #7c3aed; emissiveIntensity: 0.25" animation="property: rotation; to: 0 360 0; loop: true; dur: 7000; easing: linear" shadow="cast:true"></a-sphere>
        <a-box depth="0.01" height="0.2" width="0.5" color="#a78bfa" position="0.35 0 0" material="shader: standard; metalness: 0.9; roughness: 0.2; emissive: #6b21a8; emissiveIntensity: 0.15" shadow="cast:true"></a-box>
        <a-box depth="0.01" height="0.2" width="0.5" color="#a78bfa" position="-0.35 0 0" material="shader: standard; metalness: 0.9; roughness: 0.2; emissive: #6b21a8; emissiveIntensity: 0.15" shadow="cast:true"></a-box>
        <a-cylinder radius="0.012" height="0.12" color="#6d28d9" position="0 0 0.16" material="shader: standard; metalness: 0.9; roughness: 0.3; emissive: #4c1d95; emissiveIntensity: 0.2" shadow="cast:true"></a-cylinder>
      </a-entity>
    </a-entity>

    <a-entity id="orbit3" position="0 1.6 0" rotation="90 45 0" animation="property: rotation; to: 90 45 360; loop: true; dur: 40000; easing: linear">
      <a-entity class="satellite-group" position="0 0 3" aria-label="Satellite 3" role="img">
        <a-box depth="0.2" height="0.15" width="0.35" color="#f59e0b" material="shader: standard; metalness: 0.85; roughness: 0.25; emissive: #b45309; emissiveIntensity: 0.3" animation="property: rotation; to: 0 360 0; loop: true; dur: 9000; easing: linear" shadow="cast:true"></a-box>
        <a-box depth="0.02" height="0.14" width="0.75" color="#fde68a" position="0 0.07 0" material="shader: standard; metalness: 0.8; roughness: 0.3; emissive: #a16207; emissiveIntensity: 0.2" shadow="cast:true"></a-box>
        <a-box depth="0.02" height="0.14" width="0.75" color="#fde68a" position="0 -0.07 0" material="shader: standard; metalness: 0.8; roughness: 0.3; emissive: #a16207; emissiveIntensity: 0.2" shadow="cast:true"></a-box>
        <a-cylinder radius="0.013" height="0.15" color="#b45309" position="0 0 0.19" material="shader: standard; metalness: 0.9; roughness: 0.3; emissive: #78350f; emissiveIntensity: 0.3" shadow="cast:true"></a-cylinder>
      </a-entity>
    </a-entity>

    <!-- Meteors with flame effect -->
    <a-entity id="meteors">
      <!-- Meteor 1 -->
      <a-entity position="-40 10 -20" rotation="0 45 0" animation="property: position; from: -40 10 -20; to: 40 -10 20; dur: 6000; easing: linear; loop: true; delay: 0" aria-label="Meteor 1" role="img">
        <a-cylinder radius="0.06" height="0.3" color="#ff4500" material="shader: standard; emissive: #ff6b35; emissiveIntensity: 0.9; metalness: 0.3; roughness: 0.05" rotation="90 0 0" shadow="cast:true"></a-cylinder>
        <!-- Flame cones with animated flicker -->
        <a-cone radius-bottom="0.1" radius-top="0.02" height="0.4" color="#ffae42" position="0 -0.15 0" material="shader: standard; opacity: 0.8; emissive: #ffb347; emissiveIntensity: 0.75; transparent: true" rotation="90 0 0" shadow="cast:false">
          <a-animation attribute="material.opacity" dur="800" direction="alternate" repeat="indefinite" from="0.5" to="0.9"></a-animation>
          <a-animation attribute="scale" dur="800" direction="alternate" repeat="indefinite" from="1 0.8 1" to="1 1.2 1"></a-animation>
        </a-cone>
        <a-cone radius-bottom="0.075" radius-top="0.01" height="0.3" color="#ff4500" position="0 -0.22 0" material="shader: standard; opacity: 0.6; emissive: #ff6347; emissiveIntensity: 0.85; transparent: true" rotation="90 0 0" shadow="cast:false">
          <a-animation attribute="material.opacity" dur="600" direction="alternate" repeat="indefinite" from="0.4" to="0.8"></a-animation>
          <a-animation attribute="scale" dur="600" direction="alternate" repeat="indefinite" from="1 0.75 1" to="1 1.3 1"></a-animation>
        </a-cone>
      </a-entity>

      <!-- Meteor 2 -->
      <a-entity position="45 15 -10" rotation="0 135 0" animation="property: position; from: 45 15 -10; to: -45 -15 10; dur: 7500; easing: linear; loop: true; delay: 1500" aria-label="Meteor 2" role="img">
        <a-cone radius-bottom="0.07" radius-top="0.01" height="0.35" color="#ff6347" material="shader: standard; emissive: #ff6f61; emissiveIntensity: 0.8; metalness: 0.4; roughness: 0.05" rotation="90 0 0" shadow="cast:true"></a-cone>
        <a-cone radius-bottom="0.12" radius-top="0.03" height="0.5" color="#ffae42" position="0 -0.18 0" material="shader: standard; opacity: 0.75; emissive: #ffbd69; emissiveIntensity: 0.7; transparent: true" rotation="90 0 0" shadow="cast:false">
          <a-animation attribute="material.opacity" dur="700" direction="alternate" repeat="indefinite" from="0.5" to="0.9"></a-animation>
          <a-animation attribute="scale" dur="700" direction="alternate" repeat="indefinite" from="1 0.9 1" to="1 1.3 1"></a-animation>
        </a-cone>
      </a-entity>

      <!-- Meteor 3 -->
      <a-entity position="30 8 35" rotation="0 -60 0" animation="property: position; from: 30 8 35; to: -30 -8 -35; dur: 8000; easing: linear; loop: true; delay: 3000" aria-label="Meteor 3" role="img">
        <a-cylinder radius="0.05" height="0.25" color="#ff4500" material="shader: standard; emissive: #ff7f50; emissiveIntensity: 0.85; metalness: 0.25; roughness: 0.05" rotation="90 0 0" shadow="cast:true"></a-cylinder>
        <a-cone radius-bottom="0.09" radius-top="0.02" height="0.4" color="#ff8c00" position="0 -0.15 0" material="shader: standard; opacity: 0.7; emissive: #ffb732; emissiveIntensity: 0.8; transparent: true" rotation="90 0 0" shadow="cast:false">
          <a-animation attribute="material.opacity" dur="750" direction="alternate" repeat="indefinite" from="0.5" to="0.85"></a-animation>
          <a-animation attribute="scale" dur="750" direction="alternate" repeat="indefinite" from="1 0.85 1" to="1 1.25 1"></a-animation>
        </a-cone>
      </a-entity>
    </a-entity>

  </a-scene>
</div>

<!-- Tooltip for satellites (accessibility & interaction) -->
<div id="tooltip" role="tooltip">Satellite info</div>

<script>
  // Basic tooltip logic for satellites on hover - A-Frame interaction
  const tooltip = document.getElementById('tooltip');
  const scene = document.querySelector('a-scene');

  scene.addEventListener('loaded', () => {
    const camera = scene.camera.el;
    scene.renderer.domElement.addEventListener('mousemove', (event) => {
      const mouse = new THREE.Vector2();
      mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
      mouse.y = - (event.clientY / window.innerHeight) * 2 + 1;

      const raycaster = new THREE.Raycaster();
      raycaster.setFromCamera(mouse, scene.camera);

      const satellites = Array.from(scene.querySelectorAll('.satellite-group')).map(el => el.object3D);
      const intersects = raycaster.intersectObjects(satellites, true);

      if(intersects.length > 0) {
        let elIntersected = intersects[0].object.el;
        while(elIntersected && !elIntersected.classList.contains('satellite-group')) {
          elIntersected = elIntersected.parentEl;
        }

        if(elIntersected) {
          tooltip.style.opacity = '1';
          tooltip.style.left = (event.clientX + 15) + 'px';
          tooltip.style.top = (event.clientY + 15) + 'px';
          tooltip.textContent = elIntersected.getAttribute('aria-label') || 'Satellite';
        }
      } else {
        tooltip.style.opacity = '0';
      }
    });

    // Generate star particles to simulate distant stars
    const starfieldParticles = document.getElementById('starfield-particles');
    const starCount = 200;

    for(let i = 0; i < starCount; i++) {
      const radius = 20 + Math.random() * 30;
      const theta = Math.random() * 2 * Math.PI;
      const phi = Math.acos(2 * Math.random() - 1);

      const x = radius * Math.sin(phi) * Math.cos(theta);
      const y = radius * Math.sin(phi) * Math.sin(theta);
      const z = radius * Math.cos(phi);

      const star = document.createElement('a-sphere');
      star.setAttribute('position', `${x} ${y + 1.6} ${z}`);
      star.setAttribute('radius', '0.05');
      star.setAttribute('color', '#ffffff');
      star.setAttribute('material', 'shader: standard; emissive: white; emissiveIntensity: 0.8; roughness: 0.2; metalness: 0.5;');
      star.setAttribute('animation__twinkle', `
        property: emissiveIntensity; 
        from: 0.3; to: 1; 
        dur: ${3000 + Math.random() * 2000}; 
        dir: alternate; 
        loop: true; 
        easing: easeInOutSine;
        delay: ${Math.random() * 3000}
      `);

      starfieldParticles.appendChild(star);
    }
  });
</script>
</body>
</html>
