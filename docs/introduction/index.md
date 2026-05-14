<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LA NIEBLA 3D</title>

<style>
html, body{
    margin:0;
    overflow:hidden;
    background:black;
    font-family:Arial, sans-serif;
}

canvas{
    display:block;
}

#startScreen{
    position:fixed;
    inset:0;
    display:flex;
    align-items:center;
    justify-content:center;
    background:linear-gradient(to bottom, #000000, #17001f);
    z-index:100;
}

#panel{
    text-align:center;
    padding:40px;
    border-radius:20px;
    background:rgba(0,0,0,0.8);
    border:2px solid #00ffff;
    box-shadow:0 0 30px #00ffff;
}

#panel h1{
    color:#00ffff;
    font-size:52px;
    margin-bottom:10px;
}

#panel p{
    color:white;
    margin-bottom:25px;
}

#enterBtn{
    padding:16px 35px;
    border:none;
    border-radius:12px;
    cursor:pointer;
    font-size:20px;
    font-weight:bold;
    background:#00ffff;
    transition:0.3s;
}

#enterBtn:hover{
    transform:scale(1.05);
    box-shadow:0 0 20px #00ffff;
}

#info{
    position:absolute;
    top:10px;
    left:10px;
    color:white;
    z-index:5;
    background:rgba(0,0,0,0.5);
    padding:10px;
    border-radius:10px;
}
</style>
</head>

<body>

<div id="startScreen">
    <div id="panel">
        <h1>LA NIEBLA</h1>
        <p>Night Club Experience 3D</p>
        <button id="enterBtn">ENTRAR AL CLUB</button>
    </div>
</div>

<div id="info">
WASD = Moverse<br>
Mouse = Cámara
</div>

<script type="module">

import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js';
import { PointerLockControls } from 'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/controls/PointerLockControls.js';

const scene = new THREE.Scene();
scene.background = new THREE.Color(0x050505);
scene.fog = new THREE.FogExp2(0x111111, 0.025);

const camera = new THREE.PerspectiveCamera(
75,
window.innerWidth/window.innerHeight,
0.1,
1000
);

const renderer = new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.shadowMap.enabled = true;
document.body.appendChild(renderer.domElement);

const controls = new PointerLockControls(camera, document.body);
scene.add(controls.getObject());

const startScreen = document.getElementById("startScreen");
const enterBtn = document.getElementById("enterBtn");

enterBtn.addEventListener("click", ()=>{

    startScreen.style.display = "none";
    controls.lock();

});

// luces
const ambient = new THREE.AmbientLight(0xffffff,0.4);
scene.add(ambient);

const neon1 = new THREE.PointLight(0xff00ff,3,50);
neon1.position.set(0,10,0);
scene.add(neon1);

const neon2 = new THREE.PointLight(0x00ffff,3,50);
neon2.position.set(20,10,-10);
scene.add(neon2);

const neon3 = new THREE.PointLight(0xff0000,2,50);
neon3.position.set(-20,10,-10);
scene.add(neon3);

// piso
const floorGeo = new THREE.PlaneGeometry(120,120);
const floorMat = new THREE.MeshStandardMaterial({
    color:0x111111,
    metalness:0.8,
    roughness:0.2
});

const floor = new THREE.Mesh(floorGeo,floorMat);
floor.rotation.x = -Math.PI/2;
scene.add(floor);

// paredes
function wall(x,z,w,h,d,color=0x1a1a1a){

    const geo = new THREE.BoxGeometry(w,h,d);
    const mat = new THREE.MeshStandardMaterial({color});

    const mesh = new THREE.Mesh(geo,mat);

    mesh.position.set(x,h/2,z);

    scene.add(mesh);

}

wall(0,-60,120,15,2);
wall(0,60,120,15,2);
wall(-60,0,2,15,120);
wall(60,0,2,15,120);

// barra
function createBar(){

    const geo = new THREE.BoxGeometry(25,3,5);

    const mat = new THREE.MeshStandardMaterial({
        color:0x222222,
        emissive:0x00ffff,
        emissiveIntensity:0.4
    });

    const bar = new THREE.Mesh(geo,mat);

    bar.position.set(28,1.5,10);

    scene.add(bar);

}

createBar();

// mesas de billar
function createPoolTable(x,z){

    const geo = new THREE.BoxGeometry(10,1.5,5);

    const mat = new THREE.MeshStandardMaterial({
        color:0x006600,
        emissive:0x003300,
        emissiveIntensity:0.3
    });

    const table = new THREE.Mesh(geo,mat);

    table.position.set(x,1,z);

    scene.add(table);

}

createPoolTable(-35,-20);
createPoolTable(-35,5);

// zona vip
function createVIP(){

    const geo = new THREE.BoxGeometry(18,1,12);

    const mat = new THREE.MeshStandardMaterial({
        color:0x220022,
        emissive:0xff00ff,
        emissiveIntensity:0.4
    });

    const vip = new THREE.Mesh(geo,mat);

    vip.position.set(35,0.5,-25);

    scene.add(vip);

}

createVIP();

// personas
function createPerson(x,z,color){

    const geo = new THREE.CapsuleGeometry(0.7,2.2,4,8);

    const mat = new THREE.MeshStandardMaterial({
        color,
        emissive:color,
        emissiveIntensity:0.2
    });

    const person = new THREE.Mesh(geo,mat);

    person.position.set(x,2,z);

    scene.add(person);

}

for(let i=0;i<20;i++){

    createPerson(
        Math.random()*70-35,
        Math.random()*70-35,
        Math.random()*0xffffff
    );

}

// tubos neon
for(let i=-50;i<=50;i+=10){

    const geo = new THREE.BoxGeometry(8,0.3,0.3);

    const mat = new THREE.MeshStandardMaterial({
        color:0x00ffff,
        emissive:0x00ffff,
        emissiveIntensity:3
    });

    const tube = new THREE.Mesh(geo,mat);

    tube.position.set(i,13,0);

    scene.add(tube);

}

// movimiento
camera.position.set(0,3,20);

const keys = {
    w:false,
    a:false,
    s:false,
    d:false
};

const velocity = new THREE.Vector3();
const direction = new THREE.Vector3();

window.addEventListener("keydown",(e)=>{

    if(e.key.toLowerCase()==="w") keys.w=true;
    if(e.key.toLowerCase()==="a") keys.a=true;
    if(e.key.toLowerCase()==="s") keys.s=true;
    if(e.key.toLowerCase()==="d") keys.d=true;

});

window.addEventListener("keyup",(e)=>{

    if(e.key.toLowerCase()==="w") keys.w=false;
    if(e.key.toLowerCase()==="a") keys.a=false;
    if(e.key.toLowerCase()==="s") keys.s=false;
    if(e.key.toLowerCase()==="d") keys.d=false;

});

function animate(){

    requestAnimationFrame(animate);

    velocity.x *= 0.9;
    velocity.z *= 0.9;

    direction.z = Number(keys.w) - Number(keys.s);
    direction.x = Number(keys.d) - Number(keys.a);

    direction.normalize();

    if(keys.w || keys.s){
        velocity.z -= direction.z * 0.15;
    }

    if(keys.a || keys.d){
        velocity.x -= direction.x * 0.15;
    }

    controls.moveRight(-velocity.x);
    controls.moveForward(-velocity.z);

    neon1.intensity = 2 + Math.sin(Date.now()*0.003);
    neon2.intensity = 2 + Math.cos(Date.now()*0.002);

    renderer.render(scene,camera);

}

animate();

window.addEventListener("resize", ()=>{

    camera.aspect = window.innerWidth/window.innerHeight;
    camera.updateProjectionMatrix();

    renderer.setSize(window.innerWidth, window.innerHeight);

});

</script>

</body>
</html>
