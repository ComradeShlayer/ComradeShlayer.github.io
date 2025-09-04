---
layout: default
title: 3D Fish Model
---

# 3D Model Viewer
2

<div id="viewer-wrapper">
  <model-viewer id="viewer" 
    src="{{ '/skull.gltf' | relative_url }}" 
    alt="Skull model" 
    camera-controls 
    auto-rotate 
    crossorigin="anonymous"
    style="width:100%; height:600px;">
  </model-viewer>
</div>

<!-- Controls -->
<div style="margin: 10px 0; width: 100%;">
  <input id="search" type="text" placeholder="Search bones..."
         style="margin-bottom:5px; padding:5px; width:60%;">
  <div style="margin-top:6px;">
    <button id="show-all">Show All</button>
    <button id="hide-all">Hide All</button>
    <a id="download-link" href="{{ '/skull.gltf' | relative_url }}" download
       style="margin-left:10px;">Download Model</a>
  </div>
</div>

<!-- Scrollable list container -->
<div id="bone-list"
     style="max-height:300px; overflow-y:auto; border:1px solid #ccc; padding:8px; width:60%; margin-top:10px;">
</div>

<style>
  /* Each bone row is a single line */
  #bone-list .bone-item {
    display: flex;
    align-items: center;
    margin-bottom: 6px;
    padding: 2px 4px;
    border-radius: 4px;
  }
  #bone-list .bone-item:hover {
    background: rgba(0,0,0,0.03);
  }
  #bone-list label {
    margin-left: 8px;
    white-space: nowrap;
  }

  /* make the controls and list stack nicely on small screens */
  @media (max-width: 800px) {
    #bone-list, input#search { width: 100%; }
  }
</style>

{% raw %}
<!-- model-viewer runtime -->
<script type="module" src="https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js"></script>

<script type="module">


const viewer = document.querySelector('#viewer');
const boneListDiv = document.getElementById('bone-list');
const searchBox   = document.getElementById('search');
const showAllBtn  = document.getElementById('show-all');
const hideAllBtn  = document.getElementById('hide-all');

// Debug: confirm we have exactly one of each element
console.log("DOM checks:",
  "viewer:", !!viewer,
  "search:", !!searchBox,
  "bone-list:", !!boneListDiv,
  "show-all:", !!showAllBtn,
  "hide-all:", !!hideAllBtn
);

let bones = [];

// Names to exclude entirely
const IGNORE_NAMES = new Set([
  "Pivot",
  "Target",
  "Layer_0",
  "Whole_Skull_(all_segments)_Models"
]);

// Helper: get the internal three.js scene using the model-viewer symbol
function getThreeScene(mv) {
  const sym = Object.getOwnPropertySymbols(mv).find(s => s.description === 'scene');
  return sym ? mv[sym] : null;
}

function makeBoneRow(bone) {
  const div = document.createElement("div");
  div.className = "bone-item";

  const checkbox = document.createElement("input");
  checkbox.type = "checkbox";
  checkbox.checked = bone.object.visible;
  checkbox.dataset.name = bone.name;
  checkbox.addEventListener("change", (e) => {
    bone.object.visible = e.target.checked;
  });

  const label = document.createElement("label");
  label.textContent = bone.name;

  div.appendChild(checkbox);
  div.appendChild(label);
  return div;
}

function updateBoneList(list) {
  boneListDiv.innerHTML = "";
  if (!list || list.length === 0) {
    const p = document.createElement('div');
    p.textContent = "(no bones found)";
    p.style.opacity = "0.7";
    boneListDiv.appendChild(p);
    return;
  }
  list.forEach(b => boneListDiv.appendChild(makeBoneRow(b)));
}

// Show/hide all utilities
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
  const term = searchBox.value.trim().toLowerCase();
  if (!term) {
    updateBoneList(bones);
    return;
