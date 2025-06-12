# Universal-Simulation-Construct
A 3d rendered scaffolding of our known universe 
<!--
Copyright © 2025 Richie Steven Vallance. All rights reserved.

This code and concept are proprietary and confidential.
No part of this code may be used, reproduced, copied, modified, published, transmitted, distributed, performed, displayed, or disclosed in any form or by any means, electronic, mechanical, photocopying, recording, or otherwise, without the prior written permission of the copyright holder (Richie Steven Vallance).

All rights reserved.
-->

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<title>Recursive Folding Universe — Richie Steven Vallance © 2025</title>
<style>
  body { margin: 0; overflow: hidden; background: #111; color: #eee; font-family: Arial, sans-serif; }
  #info { position: absolute; top: 10px; left: 10px; z-index: 100; background: rgba(0,0,0,0.7); padding: 10px 15px; border-radius: 6px; font-size: 14px; }
  #playButton { position: absolute; top: 10px; right: 10px; z-index: 100; padding: 8px 16px; background: #007acc; border: none; color: white; border-radius: 6px; font-size: 14px; cursor: pointer; }
  #playButton:hover { background: #005fa3; }
</style>
</head>
<body>
<div id="info">Recursive Folding Universe — Richie Steven Vallance © 2025</div>
<button id="playButton">Play Chant</button>

<script src="https://cdn.jsdelivr.net/npm/three@0.154.0/build/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/three@0.154.0/examples/js/controls/OrbitControls.js"></script>

<script>
// Copyright © 2025 Richie Steven Vallance. All rights reserved.
// This code and concept are proprietary to Richie Steven Vallance.
// Unauthorized use or distribution prohibited.

// Sample folding universe data — expand with your full codon and folding info
const foldingData = [
  {type: 'codon', label: 'QLI', phoneme: 'kli', x: 0, y: 0, z: 0},
  {type: 'codon', label: 'LFX', phoneme: 'lifex', x: 1.5, y: 0, z: 0},
  {type: 'codon', label: 'PRM', phoneme: 'perm', x: 3, y: 0, z: 0},
  {type: 'codon', label: 'HRM', phoneme: 'harm', x: 4.5, y: 0, z: 0},
  {type: 'fold', label: 'FOL-RCF-MTC', phoneme: 'fold arkef metka', x: 6, y: 0, z: 0},
  {type: 'codon', label: 'FOL', phoneme: 'fold', x: 6, y: 1.5, z: 1.5},
  {type: 'codon', label: 'RCF', phoneme: 'arkef', x: 6, y: 1.8, z: 1.8},
  {type: 'codon', label: 'MTC', phoneme: 'metka', x: 6, y: 2.1, z: 2.1},
];

// Three.js variables
let scene, camera, renderer, controls, audioCtx;

function init() {
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0x111111);

  camera = new THREE.PerspectiveCamera(60, window.innerWidth/window.innerHeight, 0.1, 1000);
  camera.position.set(8, 8, 12);

  renderer = new THREE.WebGLRenderer({antialias: true});
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);

  controls = new THREE.OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;

  window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth/window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });

  createPoints(foldingData);
  animate();
}

function animate() {
  requestAnimationFrame(animate);
  controls.update();
  renderer.render(scene, camera);
}

function createPoints(data) {
  const geometry = new THREE.BufferGeometry();
  const positions = [];
  const colors = [];

  data.forEach(pt => {
    positions.push(pt.x, pt.y, pt.z);
    if (pt.type === 'fold') {
      colors.push(1, 0.5, 0); // orange
    } else {
      colors.push(0.3, 0.7, 1); // blue
    }
  });

  geometry.setAttribute('position', new THREE.Float32BufferAttribute(positions, 3));
  geometry.setAttribute('color', new THREE.Float32BufferAttribute(colors, 3));

  const material = new THREE.PointsMaterial({size: 0.3, vertexColors: true});
  const points = new THREE.Points(geometry, material);
  scene.add(points);

  data.forEach(pt => {
    const sprite = makeTextSprite(`${pt.label}\n${pt.phoneme}`, {fontsize: 20, backgroundColor: {r:0, g:0, b:0, a:0.7}});
    sprite.position.set(pt.x, pt.y, pt.z);
    scene.add(sprite);
  });
}

function makeTextSprite(message, parameters) {
  parameters = parameters || {};
  const fontface = parameters.fontface || "Arial";
  const fontsize = parameters.fontsize || 18;
  const borderThickness = parameters.borderThickness || 4;
  const borderColor = parameters.borderColor || {r:0, g:0, b:0, a:1.0};
  const backgroundColor = parameters.backgroundColor || {r:255, g:255, b:255, a:1.0};

  const canvas = document.createElement('canvas');
  const context = canvas.getContext('2d');
  context.font = fontsize + "px " + fontface;

  const metrics = context.measureText(message);
  const textWidth = metrics.width;

  context.fillStyle = `rgba(${backgroundColor.r},${backgroundColor.g},${backgroundColor.b},${backgroundColor.a})`;
  context.strokeStyle = `rgba(${borderColor.r},${borderColor.g},${borderColor.b},${borderColor.a})`;

  context.lineWidth = borderThickness;
  context.fillRect(borderThickness/2, borderThickness/2, textWidth + borderThickness, fontsize * 1.2 + borderThickness);
  context.strokeRect(borderThickness/2, borderThickness/2, textWidth + borderThickness, fontsize * 1.2 + borderThickness);

  context.fillStyle = "rgba(255, 255, 255, 1.0)";
  context.fillText(message, borderThickness, fontsize + borderThickness);

  const texture = new THREE.CanvasTexture(canvas);
  texture.minFilter = THREE.LinearFilter;
  texture.magFilter = THREE.LinearFilter;

  const spriteMaterial = new THREE.SpriteMaterial({ map: texture, transparent: true });
  const sprite = new THREE.Sprite(spriteMaterial);
  sprite.scale.set(2 * fontsize / 30, fontsize / 30, 1.0);
  return sprite;
}

function initAudio() {
  audioCtx = new (window.AudioContext || window.webkitAudioContext)();
}

function phonemeToFreq(p) {
  const map = {
    kli: 440, lifex: 494, perm: 523, harm: 587,
    fold: 659, arkef: 698, metka: 740,
  };
  return map[p] || 440;
}

async function playChant() {
  if (!audioCtx) initAudio();
  for (const pt of foldingData) {
    const freq = phonemeToFreq(pt.phoneme);
    const osc = audioCtx.createOscillator();
    osc.type = 'sine';
    osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
    osc.connect(audioCtx.destination);
    osc.start();
    osc.stop(audioCtx.currentTime + 0.6);
    await new Promise(r => setTimeout(r, 650));
  }
}

document.getElementById('playButton').addEventListener('click', playChant);

init();
