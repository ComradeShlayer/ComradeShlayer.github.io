---
layout: default
title: 3D Fish Model
---

# 3D Model Viewer
3

<script type="module" src="https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js"></script>

<model-viewer 
    id="skullViewer" 
    src="https://comradeshlayer.github.io/skull.gltf" 
    camera-controls 
    auto-rotate 
    style="width: 100%; height: 600px; border: 1px solid #ccc;">
</model-viewer>

<div style="margin-top: 1rem;">
  <input id="boneSearch" type="text" placeholder="Search bone..." style="width: 100%; padding: 6px;">
  
  <div style="margin: 0.5rem 0;">
    <button id="showAll">Show All</button>
    <button id="hideAll">Hide All</button>
  </div>

  <div id="boneList" style="max-height: 300px; overflow-y: auto; border: 1px solid #ddd; padding: 8px;">
    <!-- Bone checkboxes will populate here -->
  </div>
</div>

<script type="module">

const viewer = document.querySelector('#skullViewer');
const searchInput = document.querySelector('#boneSearch');
const boneListDiv = document.querySelector('#boneList');
const showAllBtn = document.querySelector('#showAll');
const hideAllBtn = document.querySelector('#hideAll');

let bones = {}; // name -> object
let checkboxes = {}; // name -> checkbox

viewer.addEventListener('load', () => {
  const scene = viewer.model.scene;

  function collectBones(obj) {
    if (obj.name) {
      bones[obj.name] = obj;

      // Create checkbox entry
      const wrapper = document.createElement('div');
      const checkbox = document.createElement('input');
      checkbox.type = 'checkbox';
      checkbox.checked = true;
      checkbox.dataset.bone = obj.name;

      const label = document.createElement('label');
      label.textContent = obj.name;
      label.style.marginLeft = '6px';

      wrapper.appendChild(checkbox);
      wrapper.appendChild(label);
      boneListDiv.appendChild(wrapper);

      // Store reference
      checkboxes[obj.name] = checkbox;

      // Toggle on change
      checkbox.addEventListener('change', () => {
        obj.visible = checkbox.checked;
      });

      // Highlight on label click
      label.addEventListener('click', () => {
        highlightBone(obj);
      });
    }
    obj.children.forEach(child => collectBones(child));
  }

  collectBones(scene);

  console.log('Bones loaded:', Object.keys(bones));
});

// Search filter
searchInput.addEventListener('input', () => {
  const query = searchInput.value.toLowerCase();
  for (const name in checkboxes) {
    const checkbox = checkboxes[name];
    const wrapper = checkbox.parentElement;
    wrapper.style.display = name.toLowerCase().includes(query) ? '' : 'none';
  }
});

// Show all
showAllBtn.addEventListener('click', () => {
  for (const name in bones) {
    bones[name].visible = true;
    checkboxes[name].checked = true;
  }
});

// Hide all
hideAllBtn.addEventListener('click', () => {
  for (const name in bones) {
    bones[name].visible = false;
    checkboxes[name].checked = false;
  }
});

// Highlight selected bone (temporary red color)
function highlightBone(bone) {
  const original = bone.material.clone();
  bone.material = bone.material.clone();
  bone.material.color.setHex(0xff0000);

  setTimeout(() => {
    bone.material = original;
  }, 1500);
}
</script>




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
