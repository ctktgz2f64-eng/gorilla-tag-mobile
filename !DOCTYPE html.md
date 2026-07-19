<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1.0">  
    <title>Mobile Gorilla Tag Web Prototype</title>  
    <style>  
        body { margin: 0; overflow: hidden; background-color: #87CEEB; }  
        #joystick-container {  
            position: absolute; bottom: 50px; left: 50px;  
            width: 100px; height: 100px; background: rgba(255,255,255,0.3);  
            border-radius: 50%; touch-action: none; display: flex;  
            align-items: center; justify-content: center;  
        }  
        #joystick-knob {  
            width: 40px; height: 40px; background: #fff; border-radius: 50%;  
        }  
        #ui-info {  
            position: absolute; top: 10px; left: 10px;  
            font-family: sans-serif; color: white; text-shadow: 1px 1px 2px black;  
        }  
    </style>  
    <!-- Include Three.js for 3D Graphics -->  
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>  
</head>  
<body>  
  
```
<div id="ui-info">🦍 Mobile Monke Prototype (Use Screen to Look, Joystick to Move)</div>
<div id="joystick-container">
    <div id="joystick-knob"></div>
</div>

<script>
    // 1. SETUP THE 3D WORLD
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x87CEEB); // Sky blue
    
    const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    document.body.appendChild(renderer.domElement);

    // 2. LIGHTING
    const light = new THREE.DirectionalLight(0xffffff, 1);
    light.position.set(5, 10, 7).normalize();
    scene.add(light);
    scene.add(new THREE.AmbientLight(0x404040));

    // 3. THE GROUND (Forest Floor)
    const floorGeo = new THREE.PlaneGeometry(100, 100);
    const floorMat = new THREE.MeshLambertMaterial({ color: 0x228B22 }); // Forest Green
    const floor = new THREE.Mesh(floorGeo, floorMat);
    floor.rotation.x = -Math.PI / 2;
    scene.add(floor);

    // 4. THE GORILLA (Placeholder: A Brown Box/Sphere)
    const monkeGeo = new THREE.BoxGeometry(2, 2, 2);
    const monkeMat = new THREE.MeshLambertMaterial({ color: 0x4A2E1B }); // Gorilla Brown
    const player = new THREE.Mesh(monkeGeo, monkeMat);
    player.position.y = 1; // Sit on ground
    scene.add(player);

    // Camera follow setup
    camera.position.set(0, 5, 10);

    // 5. MOBILE TOUCH CONTROLS (Joystick)
    const knob = document.getElementById('joystick-knob');
    let moveX = 0, moveZ = 0;

    document.getElementById('joystick-container').addEventListener('touchmove', (e) => {
        const touch = e.touches[0];
        const rect = e.currentTarget.getBoundingClientRect();
        const centerX = rect.left + rect.width / 2;
        const centerY = rect.top + rect.height / 2;
        
        // Calculate distance from center
        let deltaX = touch.clientX - centerX;
        let deltaY = touch.clientY - centerY;
        const dist = Math.min(Math.sqrt(deltaX*deltaX + deltaY*deltaY), 40);
        
        const angle = Math.atan2(deltaY, deltaX);
        const knobX = Math.cos(angle) * dist;
        const knobY = Math.sin(angle) * dist;
        
        knob.style.transform = `translate(${knobX}px, ${knobY}px)`;
        
        // Set movement vectors
        moveX = knobX / 40;
        moveZ = knobY / 40;
    });

    document.getElementById('joystick-container').addEventListener('touchend', () => {
        knob.style.transform = 'translate(0px, 0px)';
        moveX = 0;
        moveZ = 0;
    });

    // 6. GAME LOOP (Updates every frame)
    function animate() {
        requestAnimationFrame(animate);

        // Move the gorilla based on joystick
        player.position.x += moveX * 0.2;
        player.position.z += moveZ * 0.2;

        // Make camera follow player
        camera.position.x = player.position.x;
        camera.position.z = player.position.z + 10;
        camera.lookAt(player.position);

        renderer.render(scene, camera);
    }
    animate();

    // Handle Screen Resize
    window.addEventListener('resize', () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
    });
</script>

```
</body>  
</html>  
