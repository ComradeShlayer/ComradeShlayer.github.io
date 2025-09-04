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

<!-- Controls -->
<div style="margin: 10px 0;">
  <input id="search" type="text" placeholder="Search bones..."
         style="margin-bottom:5px; padding:5px; width:60%;">
  <div>
    <button id="show-all">Show All</button>
    <button id="hide-all">Hide All</button>
    <a href="{{ '/skull.gltf' | relative_url }}" download
       style="margin-left:10px;">Download Model</a>
  </div>
</div>

<!-- Scrollable list container -->
<div id="bone-list"
     style="max-height:300px; overflow-y:auto; border:1px solid #ccc; padding:5px; width:60%; margin-top:10px;">
</div>

<style>
  /* Each bone row is flex so checkbox + label stay on one line */
  #bone-list .bone-item {
    display: flex;
    align-items: center;
    margin-bottom: 4px;
  }
  #bone-list label {
    margin-left: 6px;
    white-space: nowrap;
  }
</style>

<script type="module">
const viewer = document.querySelector('#viewer');
const boneListDiv = document.getElementById('bone-list');
const searchBox   = document.getElementById('search');
const showAllBtn  = document.getElementById('show-all');
const hideAllBtn  = document.getElementById('hide-all');

let bones = [];

// Nodes we want to ignore completely
const IGNORE_NAMES = new Set([
  "Pivot",
  "Target",
  "Layer_0",
  "Whole_Skull_(all_segments)_Models"
]);

// Get the internal THREE.Scene via symbol (from model-viewer team FAQ)
function getThreeScene(mv) {
  const sym = Object.getOwnPropertySymbols(mv)
    .find(s => s.description === 'scene');
  return sym ? mv[sym] : null;
}


function updateBoneList(list) {
  boneListDiv.innerHTML = "";
  list.forEach(bone => {
    const div = document.createElement("div");
    div.className = "bone-item";

    const checkbox = document.createElement("input");
    checkbox.type = "checkbox";
    checkbox.checked = bone.object.visible;
    checkbox.dataset.name = bone.name;
    checkbox.addEventListener("change", toggleBone);

    const label = document.createElement("label");
    label.textContent = bone.name;

    div.appendChild(checkbox);
    div.appendChild(label);
    boneListDiv.appendChild(div);
  });
}

// Toggle single bone
function toggleBone(event) {
  const boneName = event.target.dataset.name;
  const checked = event.target.checked;
  const bone = bones.find(b => b.name === boneName);
  if (bone) bone.object.visible = checked;
}

// Show/hide all
showAllBtn.addEventListener("click", () => {
  bones.forEach(b => b.object.visible = true);
  updateBoneList(bones);
});
hideAllBtn.addEventListener("click", () => {
  bones.forEach(b => b.object.visible = false);
  updateBoneList(bones);
});

// Search filter
searchBox.addEventListener("input", () => {
  const term = searchBox.value.toLowerCase();
  const filtered = bones.filter(b => b.name.toLowerCase().includes(term));
  updateBoneList(filtered);
});

// Collect bones once model is loaded
async function collectBones() {
  let scene = getThreeScene(viewer);
  for (let i = 0; i < 60 && !scene; i++) {
    await new Promise(r => setTimeout(r, 100));
    scene = getThreeScene(viewer);
  }
  if (!scene) {
    console.error("Could not access three.js scene.");
    return;
  }

  bones = [];
  scene.traverse((obj) => {
    if (obj.name && obj.name.trim() !== "" && obj.type !== "Scene") {
      if (!IGNORE_NAMES.has(obj.name)) {
        bones.push({ name: obj.name, object: obj });
      }
    }
  });

  console.log("Bones loaded:", bones.map(b => b.name));
  updateBoneList(bones);
}

viewer.addEventListener('load', collectBones);
</script>
{% endraw %}

