<!doctype html>
<html lang="de">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>McSkinsCreat</title>
  <meta name="description" content="McSkinsCreat – Erstelle Minecraft-Skins online: Pixel-Tools, 3D-Vorschau, Body/Outer Layer, Import/Download." />

  <!-- SkinView3D (Three.js + Controls enthalten) -->
  <script src="https://unpkg.com/skinview3d@3.0.0/bundle/skinview3d.bundle.js"></script>

  <style>
    :root{
      --bg: #f2f2f2;
      --panel: #3e3e3e;
      --panel2:#2f2f2f;
      --panelBorder:#5a5a5a;
      --accent:#ff7a00;
      --text:#111;
      --shadow: 0 10px 25px rgba(0,0,0,.12);
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
      background:#ddd;
      color:var(--text);
    }

    /* ===== Top header like SkinDex ===== */
    .topbar{
      position:relative;
      height:92px;
      background: url("mc_bg.png") center/cover no-repeat;
      border-bottom: 4px solid rgba(0,0,0,.15);
      overflow:hidden;
    }
    .topbar::after{
      content:"";
      position:absolute; inset:0;
      background: linear-gradient(to bottom, rgba(255,255,255,.25), rgba(255,255,255,.55));
      pointer-events:none;
    }
    .brand{
      position:absolute;
      left:50%;
      top:18px;
      transform:translateX(-50%);
      font-weight:900;
      letter-spacing:.5px;
      font-size:42px;
      color:var(--accent);
      text-shadow: 0 3px 0 rgba(0,0,0,.15);
      z-index:2;
      display:flex;
      align-items:flex-end;
      gap:12px;
      white-space:nowrap;
    }
    .brand .sub{
      font-size:14px;
      color:#333;
      font-weight:700;
      margin-bottom:6px;
      opacity:.9;
    }
    .zombie{
      position:absolute;
      left:50%;
      top:-6px;
      transform:translateX(-50%);
      height:110px;
      z-index:3;
      pointer-events:none;
      filter: drop-shadow(0 10px 10px rgba(0,0,0,.25));
    }

    .navrow{
      position:absolute;
      left:0; right:0;
      bottom:0;
      z-index:4;
      display:flex;
      align-items:center;
      justify-content:center;
      gap:20px;
      padding:10px 14px;
      background: rgba(255,255,255,.55);
      backdrop-filter: blur(6px);
      border-top: 1px solid rgba(0,0,0,.08);
    }
    .navrow a{
      text-decoration:none;
      color:#111;
      font-weight:800;
      font-size:14px;
      padding:6px 8px;
      border-radius:8px;
    }
    .navrow a:hover{ background: rgba(255,122,0,.18); }
    .search{
      display:flex; align-items:center; gap:8px;
      margin-left:10px;
    }
    .search select, .search input{
      height:32px;
      border-radius:10px;
      border:1px solid rgba(0,0,0,.18);
      padding:0 10px;
      background:rgba(255,255,255,.85);
      outline:none;
      font-weight:700;
    }
    .search input{ width:280px; max-width:40vw; }

    /* ===== Main editor layout ===== */
    .wrap{
      display:grid;
      grid-template-columns: 86px 1fr 320px;
      gap:16px;
      padding:18px;
      max-width: 1200px;
      margin: 0 auto;
    }

    /* Left toolbar */
    .tools{
      background: var(--panel);
      border: 2px solid var(--panelBorder);
      border-radius: 10px;
      box-shadow: var(--shadow);
      padding:8px;
      display:flex;
      flex-direction:column;
      gap:8px;
      height: calc(100vh - 92px - 36px);
      min-height: 520px;
    }
    .toolbtn{
      height:64px;
      border-radius:8px;
      border: 2px solid rgba(255,255,255,.15);
      background: #4b4b4b;
      color:white;
      font-weight:900;
      cursor:pointer;
      display:grid;
      place-items:center;
      user-select:none;
      position:relative;
    }
    .toolbtn small{ font-size:11px; font-weight:800; opacity:.85; }
    .toolbtn.active{
      outline: 3px solid var(--accent);
      background:#5a5a5a;
    }
    .toolgrid2{
      display:grid;
      grid-template-columns: 1fr 1fr;
      gap:8px;
    }
    .toolbtn.mini{
      height:56px;
    }
    .reset{
      margin-top:auto;
      height:36px;
      border-radius:10px;
      background:#5b5b5b;
      border:1px solid rgba(255,255,255,.18);
      color:#fff;
      font-weight:900;
      cursor:pointer;
    }
    .reset:hover{ filter:brightness(1.08); }

    /* Center viewer */
    .center{
      background:#eee;
      border-radius: 12px;
      box-shadow: var(--shadow);
      padding:14px;
      border: 1px solid rgba(0,0,0,.12);
      min-height: 520px;
      position:relative;
    }
    .viewerBox{
      width:100%;
      height: 520px;
      border-radius:12px;
      background:#f7f7f7;
      border:1px solid rgba(0,0,0,.12);
      overflow:hidden;
      position:relative;
    }
    .hint{
      position:absolute;
      left:14px;
      bottom:14px;
      background: rgba(0,0,0,.65);
      color:#fff;
      padding:10px 12px;
      border-radius:12px;
      font-size:12px;
      font-weight:700;
      max-width: 440px;
    }
    .hint code{ background: rgba(255,255,255,.12); padding:2px 6px; border-radius:8px; }

    /* Right panel */
    .right{
      background: #efefef;
      border-radius: 12px;
      box-shadow: var(--shadow);
      padding:14px;
      border: 1px solid rgba(0,0,0,.12);
      min-height: 520px;
      display:flex;
      flex-direction:column;
      gap:12px;
    }
    .card{
      background:#fff;
      border:1px solid rgba(0,0,0,.12);
      border-radius:12px;
      padding:12px;
    }
    .row{
      display:flex;
      align-items:center;
      justify-content:space-between;
      gap:10px;
      flex-wrap:wrap;
    }
    .label{ font-weight:900; }
    .pill{
      padding:8px 10px;
      border-radius:12px;
      border:1px solid rgba(0,0,0,.12);
      background:#f6f6f6;
      font-weight:900;
      cursor:pointer;
    }
    .pill.active{
      background: rgba(255,122,0,.14);
      border-color: rgba(255,122,0,.35);
      color:#111;
    }
    .parts{
      display:grid;
      grid-template-columns: repeat(3, 1fr);
      gap:8px;
      margin-top:10px;
    }
    .part{
      height:48px;
      border-radius:10px;
      border:2px solid rgba(255,122,0,.55);
      background:#fff;
      cursor:pointer;
      display:grid;
      place-items:center;
      font-weight:900;
    }
    .part.active{
      background: rgba(255,122,0,.12);
      outline: 2px solid rgba(255,122,0,.75);
    }

    /* Bottom buttons */
    .bottomBtns{
      display:grid;
      grid-template-columns: 1fr 1fr 1fr;
      gap:10px;
      margin-top:auto;
    }
    .bigbtn{
      height:58px;
      border-radius:12px;
      border: 2px solid rgba(0,0,0,.15);
      background: #4b4b4b;
      color:#fff;
      font-weight:1000;
      cursor:pointer;
    }
    .bigbtn:hover{ filter:brightness(1.08); }
    .bigbtn.primary{
      background: var(--accent);
      border-color: rgba(0,0,0,.12);
      color:#1a1a1a;
    }

    /* Small inputs */
    input[type="range"]{ width: 160px; }
    input[type="color"]{ width: 56px; height: 40px; border:none; background:transparent; cursor:pointer; }
    .hex{
      font-weight:900;
      background:#111;
      color:#fff;
      padding:10px 12px;
      border-radius:999px;
      min-width:100px;
      text-align:center;
      border:1px solid rgba(255,255,255,.2);
    }

    @media (max-width: 980px){
      .wrap{ grid-template-columns: 86px 1fr; grid-template-rows:auto auto; }
      .right{ grid-column: 1 / -1; }
      .viewerBox{ height: 420px; }
      .tools{ height:auto; min-height: unset; }
    }
  </style>
</head>

<body>
  <div class="topbar">
    <img class="zombie" src="zombie.png" alt="Zombie" />
    <div class="brand">
      <div>McSkinsCreat</div>
      <div class="sub">Skin Editor</div>
    </div>

    <div class="navrow">
      <a href="#top">Top</a>
      <a href="#latest">Latest</a>
      <a href="#recent">Recently Commented</a>
      <a href="#editor" style="background:rgba(255,122,0,.18);">Editor</a>
      <a href="#upload">Upload</a>
      <a href="#grabber">Skin Grabber</a>

      <div class="search">
        <select>
          <option>Skins</option>
          <option>Users</option>
          <option>Tags</option>
        </select>
        <input id="searchInput" placeholder="Suche..." />
      </div>
    </div>
  </div>

  <div class="wrap" id="editor">
    <!-- LEFT TOOLBAR -->
    <aside class="tools">
      <div class="toolbtn active" data-tool="brush" title="Pinsel">
        ✏️<br><small>Pinsel</small>
      </div>
      <div class="toolbtn" data-tool="eraser" title="Radierer">
        🧽<br><small>Radierer</small>
      </div>
      <div class="toolbtn" data-tool="picker" title="Pipette">
        🧪<br><small>Pipette</small>
      </div>
      <div class="toolbtn" data-tool="fill" title="Füllen">
        🪣<br><small>Füllen</small>
      </div>

      <div class="toolgrid2">
        <div class="toolbtn mini" id="undoBtn" title="Undo">↶<br><small>Undo</small></div>
        <div class="toolbtn mini" id="redoBtn" title="Redo">↷<br><small>Redo</small></div>
        <div class="toolbtn mini" id="zoomInBtn" title="Zoom In">🔍➕<br><small>Zoom</small></div>
        <div class="toolbtn mini" id="zoomOutBtn" title="Zoom Out">🔍➖<br><small>Zoom</small></div>
      </div>

      <button class="reset" id="resetBtn">Reset Skin</button>
    </aside>

    <!-- CENTER VIEWER -->
    <main class="center">
      <div class="viewerBox" id="viewerBox"></div>
      <div class="hint">
        <div><b>Drehen:</b> Maus ziehen (oder Touch).</div>
        <div><b>Malen:</b> <code>Shift</code> halten + klicken/ziehen (damit Rotation nicht stört).</div>
        <div><b>Download:</b> unten „Download“ → PNG → Minecraft importieren.</div>
      </div>
    </main>

    <!-- RIGHT PANEL -->
    <aside class="right">
      <div class="card">
        <div class="row" style="align-items:flex-start;">
          <div>
            <div class="label">Farbe</div>
            <div style="margin-top:10px; display:flex; gap:10px; align-items:center;">
              <input id="colorInput" type="color" value="#123456" />
              <div id="hexOut" class="hex">#123456</div>
            </div>
            <div style="margin-top:12px;" class="row">
              <div class="label">Pinselgröße</div>
              <input id="brushSize" type="range" min="1" max="8" value="1" />
            </div>
            <div style="margin-top:12px;" class="row">
              <div class="label">Rotate Auto</div>
              <button class="pill" id="autoRotateBtn">Aus</button>
            </div>
          </div>
        </div>
      </div>

      <div class="card">
        <div class="row">
          <button class="pill active" id="layerBodyBtn">Body</button>
          <button class="pill" id="layerOuterBtn">Outer layer</button>
        </div>

        <div class="parts">
          <div class="part active" data-part="all">All</div>
          <div class="part" data-part="head">Head</div>
          <div class="part" data-part="body">Body</div>
          <div class="part" data-part="armL">Arm L</div>
          <div class="part" data-part="armR">Arm R</div>
          <div class="part" data-part="legs">Legs</div>
        </div>

        <div style="margin-top:10px; font-size:12px; opacity:.85; font-weight:700;">
          (Parts sind fürs “Fokus”-Drehen – du kannst trotzdem überall malen.)
        </div>
      </div>

      <div class="bottomBtns">
        <button class="bigbtn" id="uploadBtn" title="Später: echte Online-Galerie">Upload</button>
        <button class="bigbtn primary" id="downloadBtn">Download</button>
        <button class="bigbtn" id="importBtn">Import</button>
      </div>

      <input id="fileInput" type="file" accept="image/png" style="display:none;" />
    </aside>
  </div>

  <script>
    // ====== Assets (hier anpassen falls Dateinamen anders sind) ======
    const ASSETS = {
      defaultSkin: "https://raw.githubusercontent.com/bs-community/skinview3d/master/examples/steve.png"
      // Oder lokal: "default_skin.png"
    };

    // ====== Skin canvas (64x64 PNG) ======
    const SKIN_W = 64, SKIN_H = 64;
    const skinCanvas = document.createElement("canvas");
    skinCanvas.width = SKIN_W;
    skinCanvas.height = SKIN_H;
    const skinCtx = skinCanvas.getContext("2d", { willReadFrequently: true });

    // Undo/Redo
    const undoStack = [];
    const redoStack = [];
    function pushUndo() {
      undoStack.push(skinCtx.getImageData(0,0,SKIN_W,SKIN_H));
      if (undoStack.length > 40) undoStack.shift();
      redoStack.length = 0;
    }
    function undo() {
      if (!undoStack.length) return;
      redoStack.push(skinCtx.getImageData(0,0,SKIN_W,SKIN_H));
      const img = undoStack.pop();
      skinCtx.putImageData(img,0,0);
      refreshViewerSkin();
    }
    function redo() {
      if (!redoStack.length) return;
      undoStack.push(skinCtx.getImageData(0,0,SKIN_W,SKIN_H));
      const img = redoStack.pop();
      skinCtx.putImageData(img,0,0);
      refreshViewerSkin();
    }

    // ====== Viewer setup ======
    const viewerBox = document.getElementById("viewerBox");
    const canvas3d = document.createElement("canvas");
    canvas3d.style.width = "100%";
    canvas3d.style.height = "100%";
    viewerBox.appendChild(canvas3d);

    const skinViewer = new skinview3d.SkinViewer({
      canvas: canvas3d,
      width: viewerBox.clientWidth,
      height: viewerBox.clientHeight,
      skin: ASSETS.defaultSkin,
      background: null
    });

    // Controls like screenshot: rotate/zoom
    skinViewer.controls.enableZoom = true;
    skinViewer.controls.enablePan = false;
    skinViewer.controls.rotateSpeed = 0.9;

    // Lighting nicer
    skinViewer.light.setPosition(0, 100, 100);
    skinViewer.camera.position.set(0, 18, 45);

    // Resize
    const ro = new ResizeObserver(() => {
      const w = viewerBox.clientWidth, h = viewerBox.clientHeight;
      skinViewer.setSize(w, h);
    });
    ro.observe(viewerBox);

    // ====== Load initial skin into our canvas ======
    async function loadInitialSkinToCanvas(url) {
      const img = new Image();
      img.crossOrigin = "anonymous";
      img.onload = () => {
        skinCtx.clearRect(0,0,SKIN_W,SKIN_H);
        skinCtx.imageSmoothingEnabled = false;
        skinCtx.drawImage(img, 0,0,SKIN_W,SKIN_H);
        pushUndo();
        refreshViewerSkin();
      };
      img.src = url;
    }
    loadInitialSkinToCanvas(ASSETS.defaultSkin);

    function refreshViewerSkin() {
      // (simple but works) – load skin from our canvas dataURL
      const url = skinCanvas.toDataURL("image/png");
      skinViewer.loadSkin(url, {model: "default"});
    }

    // ====== Tools + color ======
    let currentTool = "brush";
    let currentColor = "#123456";
    let brushSize = 1;
    let layerMode = "body"; // body / outer
    let autoRotate = false;

    const hexOut = document.getElementById("hexOut");
    const colorInput = document.getElementById("colorInput");
    const brushSizeInput = document.getElementById("brushSize");

    colorInput.addEventListener("input", (e) => {
      currentColor = e.target.value.toLowerCase();
      hexOut.textContent = currentColor.toUpperCase();
    });
    brushSizeInput.addEventListener("input", (e) => {
      brushSize = Number(e.target.value);
    });

    document.querySelectorAll(".toolbtn[data-tool]").forEach(btn => {
      btn.addEventListener("click", () => {
        document.querySelectorAll(".toolbtn[data-tool]").forEach(b => b.classList.remove("active"));
        btn.classList.add("active");
        currentTool = btn.dataset.tool;
      });
    });

    // Layer toggles (für Demo: wir “merken” nur Mode; echte outer-layer-mapping kommt später)
    const layerBodyBtn = document.getElementById("layerBodyBtn");
    const layerOuterBtn = document.getElementById("layerOuterBtn");
    layerBodyBtn.addEventListener("click", () => {
      layerMode = "body";
      layerBodyBtn.classList.add("active");
      layerOuterBtn.classList.remove("active");
    });
    layerOuterBtn.addEventListener("click", () => {
      layerMode = "outer";
      layerOuterBtn.classList.add("active");
      layerBodyBtn.classList.remove("active");
    });

    // Focus parts (dreht Kamera grob)
    let focusPart = "all";
    document.querySelectorAll(".part").forEach(p => {
      p.addEventListener("click", () => {
        document.querySelectorAll(".part").forEach(x => x.classList.remove("active"));
        p.classList.add("active");
        focusPart = p.dataset.part;
        focusCameraToPart(focusPart);
      });
    });

    function focusCameraToPart(part){
      // simple presets
      const cam = skinViewer.camera;
      if(part === "head") cam.position.set(0, 26, 34);
      else if(part === "body") cam.position.set(0, 18, 42);
      else if(part === "armL") cam.position.set(-14, 18, 42);
      else if(part === "armR") cam.position.set(14, 18, 42);
      else if(part === "legs") cam.position.set(0, 8, 42);
      else cam.position.set(0, 18, 45);
    }

    // Auto rotate button
    const autoRotateBtn = document.getElementById("autoRotateBtn");
    autoRotateBtn.addEventListener("click", () => {
      autoRotate = !autoRotate;
      skinViewer.animation.paused = !autoRotate; // simple: uses internal tick anyway
      skinViewer.controls.autoRotate = autoRotate;
      skinViewer.controls.autoRotateSpeed = 1.2;
      autoRotateBtn.textContent = autoRotate ? "An" : "Aus";
      autoRotateBtn.classList.toggle("active", autoRotate);
    });

    // Undo/redo/zoom/reset
    document.getElementById("undoBtn").addEventListener("click", undo);
    document.getElementById("redoBtn").addEventListener("click", redo);
    document.getElementById("zoomInBtn").addEventListener("click", () => skinViewer.camera.position.multiplyScalar(0.92));
    document.getElementById("zoomOutBtn").addEventListener("click", () => skinViewer.camera.position.multiplyScalar(1.08));

    document.getElementById("resetBtn").addEventListener("click", async () => {
      await loadInitialSkinToCanvas(ASSETS.defaultSkin);
    });

    // ====== Painting by clicking on model (Raycast -> UV -> paint pixel) ======
    // IMPORTANT: Rotate normally. Paint when SHIFT is held (so you can still rotate like in screenshot).
    let isPointerDown = false;
    let isPainting = false;

    function hexToRgba(hex){
      const h = hex.replace("#","");
      const r = parseInt(h.slice(0,2),16);
      const g = parseInt(h.slice(2,4),16);
      const b = parseInt(h.slice(4,6),16);
      return [r,g,b,255];
    }

    function getPointerNDC(ev){
      const rect = canvas3d.getBoundingClientRect();
      const x = ((ev.clientX - rect.left) / rect.width) * 2 - 1;
      const y = -(((ev.clientY - rect.top) / rect.height) * 2 - 1);
      return {x,y};
    }

    // Find meshes in viewer (skinview3d builds playerObject with meshes)
    function getAllMeshes(obj){
      const meshes = [];
      obj.traverse?.((n) => { if(n.isMesh) meshes.push(n); });
      return meshes;
    }

    function paintAtUV(uv, tool){
      // uv: 0..1, but texture is 64x64
      const px = Math.floor(uv.x * SKIN_W);
      const py = Math.floor((1 - uv.y) * SKIN_H);

      pushUndo();

      if(tool === "picker"){
        const d = skinCtx.getImageData(px, py, 1, 1).data;
        const hx = "#" + [d[0],d[1],d[2]].map(v => v.toString(16).padStart(2,"0")).join("");
        currentColor = hx;
        colorInput.value = hx;
        hexOut.textContent = hx.toUpperCase();
        return;
      }

      if(tool === "fill"){
        floodFill(px, py, currentColor);
        refreshViewerSkin();
        return;
      }

      const size = brushSize;
      const [r,g,b,a] = tool === "eraser" ? [0,0,0,0] : hexToRgba(currentColor);

      skinCtx.imageSmoothingEnabled = false;
      for(let dy=0; dy<size; dy++){
        for(let dx=0; dx<size; dx++){
          const x = px + dx;
          const y = py + dy;
          if(x<0||y<0||x>=SKIN_W||y>=SKIN_H) continue;
          const img = skinCtx.getImageData(x,y,1,1);
          img.data[0]=r; img.data[1]=g; img.data[2]=b; img.data[3]=a;
          skinCtx.putImageData(img,x,y);
        }
      }
      refreshViewerSkin();
    }

    function sameColor(a,b){
      return a[0]===b[0] && a[1]===b[1] && a[2]===b[2] && a[3]===b[3];
    }

    function floodFill(x,y,hex){
      const target = skinCtx.getImageData(x,y,1,1).data;
      const fill = new Uint8ClampedArray(hexToRgba(hex));
      if(sameColor(target, fill)) return;

      const img = skinCtx.getImageData(0,0,SKIN_W,SKIN_H);
      const data = img.data;

      function idx(px,py){ return (py*SKIN_W+px)*4; }

      const startIdx = idx(x,y);
      const t = data.slice(startIdx, startIdx+4);

      const q = [[x,y]];
      const seen = new Set();

      while(q.length){
        const [cx,cy] = q.pop();
        const key = cx+","+cy;
        if(seen.has(key)) continue;
        seen.add(key);

        if(cx<0||cy<0||cx>=SKIN_W||cy>=SKIN_H) continue;
        const i = idx(cx,cy);
        if(data[i]!==t[0] || data[i+1]!==t[1] || data[i+2]!==t[2] || data[i+3]!==t[3]) continue;

        data[i]=fill[0]; data[i+1]=fill[1]; data[i+2]=fill[2]; data[i+3]=fill[3];

        q.push([cx+1,cy]); q.push([cx-1,cy]); q.push([cx,cy+1]); q.push([cx,cy-1]);
      }

      skinCtx.putImageData(img,0,0);
    }

    function tryPaint(ev){
      // Only paint when SHIFT is held (so rotation stays like screenshot)
      if(!ev.shiftKey) return;

      const ndc = getPointerNDC(ev);
      const raycaster = new skinview3d.THREE.Raycaster();
      raycaster.setFromCamera(ndc, skinViewer.camera);

      const meshes = getAllMeshes(skinViewer.playerObject);
      const hits = raycaster.intersectObjects(meshes, true);

      if(!hits.length) return;
      const hit = hits[0];
      if(!hit.uv) return;

      // NOTE: layerMode ("outer") is just a toggle for now – full mapping can be added später.
      paintAtUV(hit.uv, currentTool);
    }

    canvas3d.addEventListener("pointerdown", (ev) => {
      isPointerDown = true;
      isPainting = ev.shiftKey; // paint mode only if shift is held
      if(isPainting) tryPaint(ev);
    });
    canvas3d.addEventListener("pointermove", (ev) => {
      if(!isPointerDown) return;
      if(isPainting) tryPaint(ev);
    });
    window.addEventListener("pointerup", () => {
      isPointerDown = false;
      isPainting = false;
    });

    // ====== Download / Import / Upload (Upload ist Platzhalter ohne Server) ======
    document.getElementById("downloadBtn").addEventListener("click", () => {
      const a = document.createElement("a");
      a.download = "McSkinsCreat_skin.png";
      a.href = skinCanvas.toDataURL("image/png");
      a.click();
    });

    const fileInput = document.getElementById("fileInput");
    document.getElementById("importBtn").addEventListener("click", () => fileInput.click());
    fileInput.addEventListener("change", (e) => {
      const f = e.target.files?.[0];
      if(!f) return;
      const img = new Image();
      img.onload = () => {
        if(img.width !== 64 || img.height !== 64){
          alert("Bitte eine Minecraft-Skin PNG in 64x64 hochladen.");
          return;
        }
        pushUndo();
        skinCtx.clearRect(0,0,SKIN_W,SKIN_H);
        skinCtx.drawImage(img,0,0);
        refreshViewerSkin();
      };
      img.src = URL.createObjectURL(f);
      fileInput.value = "";
    });

    document.getElementById("uploadBtn").addEventListener("click", () => {
      alert(
        "Upload (öffentlich) braucht einen Server/DB.\n" +
        "Wenn du willst, baue ich dir als nächstes ein kostenloses Backend (ohne Firebase),\n" +
        "damit alle User Skins veröffentlichen und sehen können."
      );
    });

    // Small search (Demo)
    document.getElementById("searchInput").addEventListener("keydown", (e) => {
      if(e.key === "Enter") alert("Suche ist Demo. Später kann das echte Skin-Suche werden: " + e.target.value);
    });
  </script>
</body>
</html>
