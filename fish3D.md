---
layout: default
title: 3D Fish Model
---

# 3D Mdel Viewer
9


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
<script type="module" src="https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js"></script>

<script type="module">
const viewer = document.querySelector('#viewer');
const boneListDiv = document.getElementById('bone-list');
const searchBox = document.getElementById('search');
let bones = [];

// Update the bone list in the sidebar
function updateBoneList(list) {
  boneListDiv.innerHTML = "";
  list.forEach(bone => {
    const div = document.createElement("div");
    const checkbox = document.createElement("input");
    checkbox.type = "checkbox";
    checkbox.checked = true;
    checkbox.dataset.name = bone.name;
    checkbox.addEventListener("change", toggleBone);

    const label = document.createElement("label");
    label.textContent = bone.name;

    div.appendChild(checkbox);
    div.appendChild(label);
    boneListDiv.appendChild(div);
  });
}

// Toggle visibility of a bone
function toggleBone(event) {
  const boneName = event.target.dataset.name;
  const checked = event.target.checked;

  const bone = bones.find(b => b.name === boneName);
  if (bone) bone.object.visible = checked;
}

// Filter bones by search
searchBox.addEventListener("input", () => {
  const term = searchBox.value.toLowerCase();
  const filtered = bones.filter(b => b.name.toLowerCase().includes(term));
  updateBoneList(filtered);
});

// Collect bones after the model is fully ready
function collectBones() {
  if (!viewer.model) {
    console.log("Model object not ready yet, retrying...");
    setTimeout(collectBones, 50);
    return;
  }

  // Use a short delay to ensure scene is populated
  setTimeout(() => {
    const scene = viewer.model.scene;
    if (!scene) {
      console.log("Scene still not ready, retrying...");
      setTimeout(collectBones, 50);
      return;
    }

    bones = [];
    scene.traverse((obj) => {
      if (obj.name && obj.name.trim() !== "" && obj.type !== "Scene") {
        bones.push({ name: obj.name, object: obj });
      }
    });

    console.log("Bones loaded:", bones);
    updateBoneList(bones);
  }, 50);
}

// Start collecting bones when model fires 'load'
viewer.addEventListener('load', collectBones);
</script>
{% endraw %}







<!-- <div id="ov-container" style="width: 100%; height: 600px; border: 1px solid #ccc;"></div>

<script type="text/javascript" src="https://cdn.jsdelivr.net/gh/kovacsv/Online3DViewer@master/dist/o3dv.min.js"></script>
<script type="text/javascript" src="o3dv.min.js"></script>

<script type="text/javascript">
  document.addEventListener("DOMContentLoaded", () => {
    const viewer = new OV.EmbeddedViewer(document.getElementById('ov-container'), {
      backgroundColor : new OV.RGBAColor(255, 255, 255, 255),
      defaultColor : new OV.RGBAColor(200, 200, 200, 255),
      ui : {
        navigation : true,
        tree : true,
        properties : true,
        settings : true
      }
    });
    // viewer.ShowUI();
    viewer.LoadModelFromUrlList ([
        'https://comradeshlayer.github.io/skull.gltf'
    ]);
    // [
    //     // 'https://raw.githubusercontent.com/kovacsv/Online3DViewer/master/test/testfiles/cube/cube.gltf',
    //     'https://comradeshlayer.github.io/skull.gltf'
    // ];
    console.log(OV.EmbeddedViewer.prototype);
    console.log("Viewer script is running");
});
</script> -->

<!-- <iframe 
    src="https://3dviewer.net/#model=https://ComradeShlayer.github.io/Whole Skull (all segments).gltf" 
    width="100%" 
    height="600px" 
    style="border:none;">
</iframe> -->

<!-- <script type="module" src="https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js"></script>

<model-viewer src="/Whole Skull (all segments).gltf" alt="3D model" auto-rotate camera-controls style="width:100%; height:600px;">
</model-viewer> -->
