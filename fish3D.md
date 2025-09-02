---
layout: default
title: 3D Fish Model
---

# 3D Model Viewer


<div id="ov-container" style="width: 100%; height: 600px; border: 1px solid #ccc;"></div>

<script type="text/javascript" src="o3dv.min.js"></script>
<script type="text/javascript">
  document.addEventListener("DOMContentLoaded", () => {
    const viewer = new OV.Init3DViewerElement(document.getElementById('ov-container'), {
      backgroundColor : new OV.RGBAColor(255, 255, 255, 255), // white background
      ui : {
        navigation : true,  // orbit/pan/zoom controls
        tree : true,        // model structure tree (for selecting/deselecting parts!)
        properties : true,  // material/mesh info
        settings : true     // shading, projections, etc.
      }
    });
    viewer.LoadModelFromUrlList ([
      'Whole Skull (all segments).gltf'
    ]);
  });
</script>

<!-- <iframe 
    src="https://3dviewer.net/#model=https://ComradeShlayer.github.io/Whole Skull (all segments).gltf" 
    width="100%" 
    height="600px" 
    style="border:none;">
</iframe> -->

<script type="module" src="https://unpkg.com/@google/model-viewer/dist/model-viewer.min.js"></script>

<model-viewer src="/Whole Skull (all segments).gltf" alt="3D model" auto-rotate camera-controls style="width:100%; height:600px;">
</model-viewer>
