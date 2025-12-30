<!DOCTYPE html>
<html>
<head>
<title>Mini Minecraft</title>
<style>
body { margin:0; overflow:hidden; background:black; }
#info {
 position:fixed; top:10px; left:10px; color:white;
 font-family:monospace; background:#000a; padding:10px;
}
</style>
</head>
<body>

<div id="info">
W A S D = Move<br>
Mouse = Look<br>
Left Click = Break Block<br>
Right Click = Place Block
</div>

<script src="https://cdn.jsdelivr.net/npm/three@0.158/build/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.158/examples/js/controls/PointerLockControls.js"></script>

<script>
let scene = new THREE.Scene();
let camera = new THREE.PerspectiveCamera(75, innerWidth/innerHeight, 0.1, 1000);
let renderer = new THREE.WebGLRenderer();
renderer.setSize(innerWidth, innerHeight);
document.body.appendChild(renderer.domElement);

let controls = new THREE.PointerLockControls(camera, document.body);
document.body.addEventListener('click', () => controls.lock());
scene.add(controls.getObject());

let light = new THREE.DirectionalLight(0xffffff, 1);
light.position.set(5,10,5);
scene.add(light);
scene.add(new THREE.AmbientLight(0x404040));

let blocks = [];
let geo = new THREE.BoxGeometry();
let mat = new THREE.MeshLambertMaterial({color:0x55ff55});

for(let x=-5;x<5;x++){
 for(let z=-5;z<5;z++){
  let b = new THREE.Mesh(geo, mat);
  b.position.set(x,0,z);
  scene.add(b);
  blocks.push(b);
 }
}

camera.position.y = 2;

let keys = {};
onkeydown=e=>keys[e.key]=true;
onkeyup=e=>keys[e.key]=false;

let raycaster = new THREE.Raycaster();
let mouse = new THREE.Vector2(0,0);

onclick = e=>{
 raycaster.setFromCamera(mouse,camera);
 let hits = raycaster.intersectObjects(blocks);
 if(hits.length>0){
  let b = hits[0].object;
  scene.remove(b);
  blocks.splice(blocks.indexOf(b),1);
 }
};

oncontextmenu = e=>{
 e.preventDefault();
 raycaster.setFromCamera(mouse,camera);
 let hits = raycaster.intersectObjects(blocks);
 if(hits.length>0){
  let pos = hits[0].object.position.clone();
  pos.y += 1;
  let nb = new THREE.Mesh(geo, mat);
  nb.position.copy(pos);
  scene.add(nb);
  blocks.push(nb);
 }
};

function animate(){
 requestAnimationFrame(animate);
 let speed = 0.1;
 if(keys["w"]) controls.moveForward(speed);
 if(keys["s"]) controls.moveForward(-speed);
 if(keys["a"]) controls.moveRight(-speed);
 if(keys["d"]) controls.moveRight(speed);
 renderer.render(scene,camera);
}
animate();
</script>
</body>
</html>
