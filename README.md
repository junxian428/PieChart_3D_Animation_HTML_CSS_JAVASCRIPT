PieChartHTMLv6 = 
"<!DOCTYPE html>
<html lang=""en"">
<head>
<meta charset=""UTF-8"">
<meta name=""viewport"" content=""width=device-width, initial-scale=1.0"">
<style>
    body, iframe{
        height: 100vh;
        width: 100vw;
        margin: 0;
        padding: 0;
        border: none;
        position: fixed; 
        background-color:transparent;
        font-family: Arial, sans-serif;
    }
    html, body {
        background-color:transparent;
        margin: 0;
        padding: 0;
        height: 100%;
    }
</style>
</head>
<body>
<iframe srcdoc=""
<!DOCTYPE html>
<html lang='en'>
<head>
<meta charset='UTF-8'>
<title>3D Pie Chart</title>
<style>
    body { margin:0; background:#001133; overflow:hidden; color:white; display:flex; justify-content:center; align-items:center; font-size:20px; }
    canvas { display:block; }
</style>
</head>
<body>
<script type='module'>
    import * as THREE from 'https://esm.sh/three@0.160.0';
    import { OrbitControls } from 'https://esm.sh/three@0.160.0/examples/jsm/controls/OrbitControls.js';

// ---------- DATA FROM POWER BI (delimiter format) ----------
//const dataStr = 'Category,Incomplete,Value,7|Category,Completed,Value,122|Category,Pending Receive,Value,7';
const dataStr = '"&[BatchStatusString]&"'

let data = [];

// Split string by '|' first, then by ',' to extract category and value
dataStr.split('|').forEach(item => {
    const parts = item.split(',');
    // Expecting format: Category, <name>, Value, <number>
    if(parts.length === 4) {
        data.push({
            category: parts[1].trim(),
            value: parseFloat(parts[3].trim()),
            color: Math.floor(Math.random() * 0xffffff) // random color
        });
    }
});

    // ---------- SCENE ----------
    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x001133);

    const camera = new THREE.PerspectiveCamera(45, window.innerWidth/window.innerHeight, 0.1, 1000);
    camera.position.set(0,8,12);

    const renderer = new THREE.WebGLRenderer({ antialias:true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(window.devicePixelRatio);
    document.body.appendChild(renderer.domElement);

    const controls = new OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true;

    scene.add(new THREE.AmbientLight(0xffffff,0.6));
    const light = new THREE.DirectionalLight(0xffffff,0.8);
    light.position.set(5,10,7);
    scene.add(light);

    const pieGroup = new THREE.Group();
    scene.add(pieGroup);

    // ---------- CREATE PIE ----------
    const total = data.reduce((s,d) => s + d.value, 0);
    let startAngle = 0;
    const radius = 5, height = 1;

  function createLabel(text, x, y, z) {
    const canvas = document.createElement('canvas');
    canvas.width = canvas.height = 1000; // Bigger canvas for higher resolution
    const ctx = canvas.getContext('2d');
    ctx.fillStyle = 'white';
    ctx.font = '30px Arial'; // Bigger font
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    ctx.fillText(text, canvas.width / 2, canvas.height / 2);

    const texture = new THREE.CanvasTexture(canvas);
    texture.minFilter = THREE.LinearFilter; // Prevents blurry text
    const material = new THREE.SpriteMaterial({ map: texture });
    const sprite = new THREE.Sprite(material);

    sprite.scale.set(28, 28, 1); // Increase sprite size
    sprite.position.set(x, y, z);
    return sprite;
}

    data.forEach(slice => {
        slice.color = Math.floor(Math.random() * 0xffffff);

        const angle = (slice.value / total) * Math.PI * 2;

        const shape = new THREE.Shape();
        shape.moveTo(0,0);
        shape.absarc(0,0,radius,startAngle,startAngle+angle);
        shape.lineTo(0,0);

        const geometry = new THREE.ExtrudeGeometry(shape,{depth: height, bevelEnabled:false});
        const material = new THREE.MeshStandardMaterial({color: slice.color});
        const mesh = new THREE.Mesh(geometry, material);
        mesh.rotation.x = -Math.PI/2;
        pieGroup.add(mesh);

        const midAngle = startAngle + angle/2;
        const x = radius*0.7*Math.cos(midAngle);
        const z = radius*0.7*Math.sin(midAngle);
        const y = height + 0.2;
        const percent = ((slice.value/total)*100).toFixed(1)+'%';
        const label = createLabel(slice.category+' | '+slice.value+' ('+percent+')', x, y, z);
        pieGroup.add(label);

        startAngle += angle;
    });

    function animate(){
        requestAnimationFrame(animate);
        pieGroup.rotation.y += 0.005;
        controls.update();
        renderer.render(scene,camera);
    }
    animate();

    window.addEventListener('resize', ()=>{
        camera.aspect = window.innerWidth/window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
    });


</script>
</body>
</html>
""></iframe>
</body>
</html>"
