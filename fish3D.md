---
layout: default
title: 3D Fish Model
---

# 3D Model Viewer
1

<h1>3D Skull Viewer</h1>

<model-viewer 
    id="skullViewer" 
    src="https://comradeshlayer.github.io/skull.glb" 
    camera-controls 
    auto-rotate 
    style="width: 100%; height: 600px;">
</model-viewer>

<br>

<label for="boneSearch">Search & toggle bone:</label>
<input list="boneList" id="boneSearch" placeholder="Type bone name..." />
<datalist id="boneList"></datalist>
<button id="toggleBone">Toggle Visibility</button>

<script type="module">
import '@google/model-viewer';

const viewer = document.querySelector('#skullViewer');
const input = document.querySelector('#boneSearch');
const datalist = document.querySelector('#boneList');
const toggleBtn = document.querySelector('#toggleBone');

let bones = {}; // name -> object reference

viewer.addEventListener('load', () => {
    const scene = viewer.model.scene;

    // Recursively collect all named nodes
    function collectBones(obj) {
        if (obj.name) {
            bones[obj.name] = obj;
            const option = document.createElement('option');
            option.value = obj.name;
            datalist.appendChild(option);
        }
        obj.children.forEach(child => collectBones(child));
    }

    collectBones(scene);

    console.log('Bones loaded:', Object.keys(bones));
});

toggleBtn.addEventListener('click', () => {
    const name = input.value;
    if (name in bones) {
        bones[name].visible = !bones[name].visible;
    } else {
        alert('Bone not found!');
    }
});
</script>




<div id="ov-container" style="width: 100%; height: 600px; border: 1px solid #ccc;"></div>

<!-- <script type="text/javascript" src="https://cdn.jsdelivr.net/gh/kovacsv/Online3DViewer@master/dist/o3dv.min.js"></script> -->
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
</script>

<!-- <iframe 
    src="https://3dviewer.net/#model=https://ComradeShlayer.github.io/Whole Skull (all segments).gltf" 
    width="100%" 
    height="600px" 
    style="border:none;">
</iframe> -->

<!-- <script type="module" src="https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js"></script>

<model-viewer src="/Whole Skull (all segments).gltf" alt="3D model" auto-rotate camera-controls style="width:100%; height:600px;">
</model-viewer> -->
