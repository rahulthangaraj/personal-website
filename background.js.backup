window.addEventListener('load', function() {
    if (typeof THREE === 'undefined') {
        console.error('Three.js not loaded');
        return;
    }

    let scene, camera, renderer, particles;
    let mouseX = 0, mouseY = 0;
    let targetRotation = { x: 0, y: 0 };
    const windowHalfX = window.innerWidth / 2;
    const windowHalfY = window.innerHeight / 2;

    init();
    animate();

    function init() {
        scene = new THREE.Scene();
        camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 1, 3000);
        camera.position.z = 1000;

        const geometry = new THREE.BufferGeometry();
        const vertices = [];
        const velocities = [];
        const numParticles = 1500;

        // More even distribution using cube coordinates
        for (let i = 0; i < numParticles; i++) {
            const x = (Math.random() - 0.5) * 2000;
            const y = (Math.random() - 0.5) * 2000;
            const z = (Math.random() - 0.5) * 2000;
            
            const magnitude = Math.sqrt(x * x + y * y + z * z);
            const radius = Math.random() * 800 + 200;
            const scale = radius / magnitude;
            
            vertices.push(
                x * scale,
                y * scale,
                z * scale
            );

            // Extremely slow initial velocities
            velocities.push(
                (Math.random() - 0.5) * 0.02,
                (Math.random() - 0.5) * 0.02,
                (Math.random() - 0.5) * 0.02
            );
        }

        geometry.setAttribute('position', new THREE.Float32BufferAttribute(vertices, 3));
        geometry.setAttribute('velocity', new THREE.Float32BufferAttribute(velocities, 3));

        const material = new THREE.PointsMaterial({
            size: 3,
            color: 0xffffff,
            transparent: true,
            opacity: 1,
            sizeAttenuation: true,
            blending: THREE.AdditiveBlending,
            map: createParticleTexture()
        });

        particles = new THREE.Points(geometry, material);
        scene.add(particles);

        renderer = new THREE.WebGLRenderer({
            antialias: true,
            alpha: true
        });
        renderer.setPixelRatio(window.devicePixelRatio);
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setClearColor(0x000000, 1);

        document.getElementById('particles-bg').appendChild(renderer.domElement);
        document.addEventListener('mousemove', onDocumentMouseMove);
        window.addEventListener('resize', onWindowResize);
    }

    function createParticleTexture() {
        const canvas = document.createElement('canvas');
        canvas.width = 32;
        canvas.height = 32;
        const context = canvas.getContext('2d');

        const gradient = context.createRadialGradient(16, 16, 0, 16, 16, 16);
        gradient.addColorStop(0, 'rgba(255,255,255,1)');
        gradient.addColorStop(0.2, 'rgba(255,255,255,0.8)');
        gradient.addColorStop(0.5, 'rgba(255,255,255,0.3)');
        gradient.addColorStop(1, 'rgba(255,255,255,0)');

        context.fillStyle = gradient;
        context.fillRect(0, 0, 32, 32);

        const texture = new THREE.Texture(canvas);
        texture.needsUpdate = true;
        return texture;
    }

    function onDocumentMouseMove(event) {
        mouseX = (event.clientX - windowHalfX) * 0.005;
        mouseY = (event.clientY - windowHalfY) * 0.005;

        targetRotation.x = (event.clientY / window.innerHeight - 0.5) * Math.PI * 0.1;
        targetRotation.y = (event.clientX / window.innerWidth - 0.5) * Math.PI * 0.1;
    }

    function onWindowResize() {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
    }

    function animate() {
        requestAnimationFrame(animate);

        const positions = particles.geometry.attributes.position.array;
        const velocities = particles.geometry.attributes.velocity.array;
        const time = Date.now() * 0.00001;

        for (let i = 0; i < positions.length; i += 3) {
            positions[i] += velocities[i] * 0.2;
            positions[i + 1] += velocities[i + 1] * 0.2;
            positions[i + 2] += velocities[i + 2] * 0.2;

            positions[i + 1] += Math.sin(time * 2 + positions[i] * 0.001) * 0.05;
            positions[i] += Math.cos(time * 2 + positions[i + 1] * 0.001) * 0.05;

            const x = positions[i];
            const y = positions[i + 1];
            const z = positions[i + 2];
            const distance = Math.sqrt(x * x + y * y + z * z);

            if (distance > 1000) {
                const scale = 980 / distance;
                positions[i] *= scale;
                positions[i + 1] *= scale;
                positions[i + 2] *= scale;
                
                velocities[i] *= 0.95;
                velocities[i + 1] *= 0.95;
                velocities[i + 2] *= 0.95;
            }
        }

        particles.geometry.attributes.position.needsUpdate = true;

        camera.position.x += (mouseX * 5 - camera.position.x) * 0.005;
        camera.position.y += (-mouseY * 5 - camera.position.y) * 0.005;
        camera.lookAt(scene.position);

        particles.rotation.x += (targetRotation.x - particles.rotation.x) * 0.01;
        particles.rotation.y += (targetRotation.y - particles.rotation.y) * 0.01;

        renderer.render(scene, camera);
    }
}); 