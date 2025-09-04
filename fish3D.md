---
layout: default
title: 3D Fish Model
---

# 3D Mdel Viewer
1


<div id="viewer-wrapper">
  <model-viewer id="viewer" 
    src="skull.gltf" 
    alt="Skull model" 
    camera-controls 
    auto-rotate 
    style="width:100%; height:600px;">
  </model-viewer>
</div>

<input id="search" type="text" placeholder="Search bones..." style="margin:10px; padding:5px; width:50%;">
<div id="bone-list"></div>

{% raw %}
<!-- model-viewer runtime -->
<script type="module" src="https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js"></script>

<!-- Viewer -->
<model-viewer id="viewer"
  src="{{ '/skull.gltf' | relative_url }}"
  alt="Skull model"
  camera-controls
  auto-rotate
  crossorigin="anonymous"
  style="width:100%; height:600px;">
</model-viewer>

<script type="module">
const viewer = document.querySelector('#viewer');
const boneListDiv = document.getElementById('bone-list');
const searchBox   = document.getElementById('search');

let bones = [];

// Get the internal THREE.Scene via symbol (from model-viewer FAQ)
function getThreeScene(mv) {
  const sym = Object.getOwnPropertySymbols(mv)
    .find(s => s.description === 'scene');
  return sym ? mv[sym] : null;
}

function updateBoneList(list) {
  boneListDiv.innerHTML = "";
  list.forEach(bone => {
    const row = document.createElement("div");
    const cb  = document.createElement("input");
    cb.type = "checkbox";
    cb.checked = true;
    cb.dataset.name = bone.name;
    cb.addEventListener("change", (e) => {
      bone.object.visible = e.target.checked;
    });
    const label = document.createElement("label");
    label.textContent = bone.name;
    row.appendChild(cb);
    row.appendChild(label);
    boneListDiv.appendChild(row);
  });
}

searchBox.addEventListener("input", () => {
  const term = searchBox.value.toLowerCase();
  updateBoneList(bones.filter(b => b.name.toLowerCase().includes(term)));
});

// Robust load: wait for <model-viewer> to finish, then grab the scene via symbol.
async function collectBones() {
  let scene = getThreeScene(viewer);
  if (!scene) {               // wait in small intervals until scene is ready
    for (let i = 0; i < 60; i++) {
      await new Promise(r => setTimeout(r, 100));
      scene = getThreeScene(viewer);
      if (scene) break;
    }
  }
  if (!scene) {
    console.error("Could not access three.js scene.");
    return;
  }

  bones = [];
  scene.traverse((obj) => {
    if (obj.name && obj.name.trim() !== "" && obj.type !== "Scene") {
      bones.push({ name: obj.name, object: obj });
    }
  });

  console.log("Bones loaded:", bones.map(b => b.name));
  updateBoneList(bones);
}

viewer.addEventListener('load', collectBones);
viewer.addEventListener('error', (e) => {
  console.error('Model failed to load:', e);
});
</script>
{% endraw %}
