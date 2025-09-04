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

<!-- solution 2 -->
<iframe
  src="https://3dviewer.net/#model={{ 'https://YOUR_USERNAME.github.io/YOUR_REPO/skull.gltf' | url_encode }}"
  style="width:100%;height:600px;border:none;"
  allowfullscreen
  loading="lazy">
</iframe>


<!-- solution 3 -->
<script src="https://cdn.jsdelivr.net/gh/kovacsv/Online3DViewer@latest/dist/o3dv.min.js"></script>

<!-- Drop a container anywhere you want -->
<div class="online_3d_viewer"
     style="width:100%;height:600px;border:1px solid #ccc;"
     model="{{ '/skull.gltf' | relative_url }}">
</div>






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
