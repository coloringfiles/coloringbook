# coloringbook
Online Coloring Book And Drawing Tool
### How to Build an Advanced Online Coloring Book App with File Uploads (JPG, PNG, & PDF)
Interactive web applications that engage users creatively are highly valuable assets for educational portals, entertainment sites, and portfolio platforms. Creating a digital coloring book platform used to require complex backend architectures or heavy, outdated plugins. Today, modern HTML5 Canvas, native JavaScript APIs, and front-end PDF rendering libraries make it possible to build a fully capable, free online coloring application that runs completely in the user’s web browser.

**View Demo Coloring Book**: [Online Coloring Book](https://coloringfiles.com/color-online)

<img width="1333" height="965" alt="Image" src="https://github.com/user-attachments/assets/98155682-bdd0-4967-a007-b9749c444183" />
<img width="1340" height="968" alt="Image" src="https://github.com/user-attachments/assets/f8ee12cb-1bc7-44a4-be58-cc21e96a0a3b" />

The script below provides a comprehensive, production-ready solution. It features an integrated UI that allows users to upload their own JPG or PNG line art, and dynamically processes PDF pages into clear canvas templates. Equipped with a custom flood-fill algorithm that handles color blending smoothly and a built-in export mechanism, this code offers a strong framework for any web developer looking to launch a web-based drawing tool.
**View Demo Coloring Book**: [Online Coloring Book](https://coloringfiles.com/color-online)
### The Complete Production Code (index.html)
You can save the following code as an .html file (e.g., coloring-app.html) and open it directly in any modern browser. It loads the light-weight pdf.js library via CDN to handle PDF documents seamlessly on the client side.
View more find [coloring pages](https://coloringfiles.com/ColoringPages) from coloringfiles.


Codes//


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Professional Coloring Page Studio</title>
    
    <!-- Third-party JS Libraries for PDF import/export -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.16.105/pdf.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

    <style>
        /* Modern Light Theme & Self-contained System Fonts */
        :root {
            --bg-color: #f4f6f9;
            --panel-bg: #ffffff;
            --border-color: #e0e4ec;
            --primary-color: #3b82f6;
            --primary-hover: #2563eb;
            --text-main: #1e293b;
            --text-muted: #64748b;
            --shadow-sm: 0 2px 4px rgba(0,0,0,0.05);
            --shadow-md: 0 4px 12px rgba(0,0,0,0.08);
            --radius-md: 8px;
            --radius-lg: 12px;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
            -webkit-user-select: none;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-main);
            display: flex;
            flex-direction: column;
            min-height: 100vh;
            overflow-x: hidden;
        }

        /* Header Toolbar */
        header.top-toolbar {
            background-color: var(--panel-bg);
            border-bottom: 1px solid var(--border-color);
    max-width: 1100px;
    margin: 0 auto;
    width: 100%;
            padding: 10px 20px;
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
            align-items: center;
            justify-content: space-between;
            box-shadow: var(--shadow-sm);
            z-index: 100;
        }

        .toolbar-group {
            display: flex;
            align-items: center;
            gap: 8px;
            flex-wrap: wrap;
        }

        .btn {
            background-color: #f8fafc;
            border: 1px solid var(--border-color);
            color: var(--text-main);
            padding: 8px 14px;
            font-size: 13px;
            font-weight: 600;
            border-radius: var(--radius-md);
            cursor: pointer;
            transition: all 0.2s ease;
            display: inline-flex;
            align-items: center;
            gap: 6px;
        }

        .btn:hover {
            background-color: #eff6ff;
            border-color: var(--primary-color);
            color: var(--primary-color);
        }

        .btn-primary {
            background-color: var(--primary-color);
            color: #ffffff;
            border-color: var(--primary-color);
        }

        .btn-primary:hover {
            background-color: var(--primary-hover);
            color: #ffffff;
        }

        .file-input-wrapper {
            position: relative;
            overflow: hidden;
            display: inline-block;
        }

        .file-input-wrapper input[type=file] {
            font-size: 100px;
            position: absolute;
            left: 0;
            top: 0;
            opacity: 0;
            cursor: pointer;
        }

        /* Main Container */
        .app-container {
            display: flex;
            flex: 1;
            gap: 20px;
            padding: 20px;
            max-width: 1100px;
            margin: 0 auto;
            width: 100%;
        }

        /* Workspace (Left Side) - Centered Bounded Box */
        .workspace {
            flex: 1;
            display: flex;
            justify-content: center;
            align-items: center;
            background: #ffffff;
            border: 1px solid var(--border-color);
            border-radius: var(--radius-lg);
            box-shadow: var(--shadow-md);
            width: 100%;
            max-width: 670px;
            height: 620px;
            position: relative;
            overflow: hidden;
            margin: 0 auto;
        }

        .canvas-viewport {
            width: 100%;
            height: 100%;
            justify-content: center;
            align-items: center;
            position: relative;
            cursor: crosshair;
            overflow: hidden;
        }

        canvas#coloringCanvas {
            display: block;
            background-color: #ffffff;
            box-shadow: 0 0 12px rgba(0,0,0,0.08);
            transform-origin: 0 0;
            cursor: crosshair;
        }

        /* Sticky Sidebar (Right Side) */
        .sidebar {
            width: 320px;
            background-color: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: var(--radius-lg);
            padding: 20px;
            box-shadow: var(--shadow-md);
            display: flex;
            flex-direction: column;
            gap: 20px;
            position: sticky;
            top: 20px;
            height: fit-content;
            max-height: calc(100vh - 120px);
            overflow-y: auto;
        }

        .sidebar-section {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .sidebar-title {
            font-size: 14px;
            font-weight: 700;
            color: var(--text-muted);
            text-transform: uppercase;
            letter-spacing: 0.5px;
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 6px;
        }

        .tool-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 8px;
        }

        .tool-btn {
            padding: 10px;
            font-size: 12px;
            font-weight: 600;
            text-align: center;
            border: 1px solid var(--border-color);
            border-radius: var(--radius-md);
            background-color: #f8fafc;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .tool-btn.active, .tool-btn:hover {
            background-color: #eff6ff;
            border-color: var(--primary-color);
            color: var(--primary-color);
        }

        .option-group {
            display: flex;
            gap: 10px;
            align-items: center;
        }

        .color-palette-grid {
            display: grid;
            grid-template-columns: repeat(6, 1fr);
            gap: 6px;
        }

        .color-swatch {
            aspect-ratio: 1;
            border-radius: 6px;
            cursor: pointer;
            border: 2px solid transparent;
            transition: transform 0.1s ease, border-color 0.2s ease;
        }

        .color-swatch:hover {
            transform: scale(1.1);
        }

        .color-swatch.active {
            border-color: #1e293b;
            transform: scale(1.15);
        }

        .custom-color-picker {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-top: 5px;
        }

        .custom-color-picker input[type="color"] {
            -webkit-appearance: none;
            border: none;
            width: 36px;
            height: 36px;
            border-radius: var(--radius-md);
            cursor: pointer;
            background: none;
        }

        .custom-color-picker input[type="color"]::-webkit-color-swatch-wrapper {
            padding: 0;
        }

        .custom-color-picker input[type="color"]::-webkit-color-swatch {
            border: 1px solid var(--border-color);
            border-radius: var(--radius-md);
        }

        /* Footer Link */
        footer.app-footer {
            background-color: var(--panel-bg);
            border-top: 1px solid var(--border-color);
            padding: 15px;
            text-align: center;
            margin-top: auto;
        }

        .footer-link {
            color: var(--primary-color);
            font-weight: 600;
            text-decoration: none;
            font-size: 14px;
            transition: color 0.2s ease;
            display: inline-flex;
            align-items: center;
            gap: 6px;
        }

        .footer-link:hover {
            color: var(--primary-hover);
            text-decoration: underline;
        }

        /* Responsive Layout Adjustments */
        @media (max-width: 992px) {
            .app-container {
                flex-direction: column;
                align-items: center;
            }
            .sidebar {
                width: 100%;
                max-width: 670px;
                position: relative;
                top: 0;
            }
            .workspace {
                width: 100%;
                height: 500px;
            }
        }
    </style>
</head>
<body>

    <!-- Top Navigation & Actions Toolbar -->
    <header class="top-toolbar">
        <div class="toolbar-group">
            <div class="file-input-wrapper">
                <button class="btn btn-primary">📁 Upload File</button>
                <input type="file" id="fileUploader" accept="image/jpeg, image/png, image/webp, application/pdf">
            </div>
            <button class="btn" id="btnUndo" title="Undo Action">↩ Undo</button>
            <button class="btn" id="btnRedo" title="Redo Action">↪ Redo</button>
        </div>

        <div class="toolbar-group">
            <button class="btn" id="btnZoomIn" title="Zoom In">+</button>
            <button class="btn" id="btnZoomOut" title="Zoom Out">-</button>
            <button class="btn" id="btnFit" title="Fit to Screen">🔍  Fit</button>
            <button class="btn" id="btnResetImage" title="Reset Colors">🔄 Reset Colors</button>
            <button class="btn" id="btnNewPage" title="Clear Canvas">📄 New Page</button>
        </div>

        <div class="toolbar-group">
            <button class="btn btn-primary" id="btnExportPNG">💾 Export PNG</button>
            <button class="btn btn-primary" id="btnExportPDF">📄 Export PDF</button>
        </div>
    </header>

    <!-- Main Workspace and Tools Sidebar -->
    <div class="app-container">
        <!-- Left: Workspace Area -->
        <main class="workspace" id="workspaceContainer">
            <div class="canvas-viewport" id="canvasViewport">
                <canvas id="coloringCanvas"></canvas>
            </div>
        </main>

        <!-- Right: Tools Sidebar -->
        <aside class="sidebar">
            <!-- Tools Section -->
            <div class="sidebar-section">
                <div class="sidebar-title">Tools</div>
                <div class="tool-grid">
                    <button class="tool-btn active" id="toolFill">Fill</button>
                    <button class="tool-btn" id="toolBrush">Brush</button>
                    <button class="tool-btn" id="toolPicker">Picker</button>
                </div>
            </div>

            <!-- Brush Options -->
            <div class="sidebar-section" id="brushOptionsSection" style="display: none;">
                <div class="sidebar-title">Brush Size</div>
                <div class="option-group">
                    <input type="range" id="brushSize" min="1" max="50" value="10" style="width: 100%;">
                    <span id="brushSizeVal">10px</span>
                </div>
            </div>

            <!-- Fill Type Section -->
            <div class="sidebar-section">
                <div class="sidebar-title">Fill Mode</div>
                <div class="tool-grid" style="grid-template-columns: 1fr 1fr;">
                    <button class="tool-btn active" id="modeSolid">Solid</button>
                    <button class="tool-btn" id="modeGradient">Gradient</button>
                </div>
            </div>

            <!-- Color Palette (36 Preset Colors) -->
            <div class="sidebar-section">
                <div class="sidebar-title">36 Color Palette</div>
                <div class="color-palette-grid" id="colorPalette"></div>
                <div class="custom-color-picker">
                    <input type="color" id="customColorInput" value="#3b82f6">
                    <label for="customColorInput" style="font-size: 13px; font-weight: 600; cursor: pointer;">Custom Color</label>
                </div>
            </div>
        </aside>
    </div>

    <!-- Footer -->
    <footer class="app-footer">
        <a href="https://coloringfiles.com/ColoringPages" target="_blank" rel="noopener noreferrer" class="footer-link">🎨 View More Coloring Pages </a>
    </footer>

    <!-- Application Script -->
    <script>
        (function() {
            'use strict';

            // Global State Management
            const state = {
                currentTool: 'fill',
                fillMode: 'solid',
                selectedColor: '#3b82f6',
                brushSize: 10,
                tolerance: 80,
                scale: 1,
                minScale: 1,
                panX: 0,
                panY: 0,
                undoStack: [],
                redoStack: [],
                maxUndoSteps: 20
            };

            // Preset 36 Colors for Palette
            const colorPalette = [
                '#ffffff', '#e2e8f0', '#94a3b8', '#475569', '#1e293b', '#000000',
                '#ef4444', '#f97316', '#f59e0b', '#eab308', '#84cc16', '#22c55e',
                '#10b981', '#14b8a6', '#06b6d4', '#0ea5e9', '#3b82f6', '#6366f1',
                '#8b5cf6', '#a855f7', '#d946ef', '#ec4899', '#f43f5e', '#fb7185',
                '#fca5a5', '#fdba74', '#fde047', '#86efac', '#93c5fd', '#c084fc',
                '#f472b6', '#78350f', '#9a3412', '#b45309', '#15803d', '#1e3a8a'
            ];

            // DOM Elements
            const canvas = document.getElementById('coloringCanvas');
            const ctx = canvas.getContext('2d', { willReadFrequently: true });
            const viewport = document.getElementById('canvasViewport');
            const workspace = document.getElementById('workspaceContainer');
            const fileUploader = document.getElementById('fileUploader');

            let originalImageData = null;

            // Initialize App
            window.addEventListener('DOMContentLoaded', () => {
                setupPalette();
                setupEventListeners();
                initBlankCanvas(600, 700);
            });

            function setupPalette() {
                const paletteContainer = document.getElementById('colorPalette');
                paletteContainer.innerHTML = '';
                colorPalette.forEach((hex) => {
                    const swatch = document.createElement('div');
                    swatch.className = 'color-swatch' + (hex.toLowerCase() === state.selectedColor.toLowerCase() ? ' active' : '');
                    swatch.style.backgroundColor = hex;
                    swatch.dataset.color = hex;
                    swatch.addEventListener('click', () => selectColor(hex, swatch));
                    paletteContainer.appendChild(swatch);
                });
            }

            function selectColor(hex, targetSwatch) {
                state.selectedColor = hex;
                document.querySelectorAll('.color-swatch').forEach(s => s.classList.remove('active'));
                if(targetSwatch) targetSwatch.classList.add('active');
                document.getElementById('customColorInput').value = hexToRgbHex(hex);
            }

            function hexToRgbHex(hex) {
                if(/^#[0-9A-F]{6}$/i.test(hex)) return hex;
                return '#3b82f6';
            }

            function initBlankCanvas(width, height) {
                canvas.width = width;
                canvas.height = height;
                ctx.fillStyle = '#ffffff';
                ctx.fillRect(0, 0, width, height);

                ctx.strokeStyle = '#000000';
                ctx.lineWidth = 4;
                ctx.strokeRect(20, 20, width - 40, height - 40);

                saveOriginalState();
                resetZoomFit();
                saveUndoState();
            }

            function saveOriginalState() {
                originalImageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
            }

            function updateCanvasTransform() {
                canvas.style.transform = `translate(${state.panX}px, ${state.panY}px) scale(${state.scale})`;
            }

            // Perfect Dual-Axis Center & Fit (Contain scaling)
            function resetZoomFit() {
                if (!canvas.width || !canvas.height) return;

                const containerWidth = workspace.clientWidth;
                const containerHeight = workspace.clientHeight;

                // Calculate scale factor for 'contain' (fits within both width and height)
                const scaleX = containerWidth / canvas.width;
                const scaleY = containerHeight / canvas.height;
                const scale = Math.min(scaleX, scaleY);

                state.minScale = scale;
                state.scale = scale;

                // Equal Centering on Both Horizontal (X) and Vertical (Y) Axes
                state.panX = (containerWidth - canvas.width * scale) / 2;
                state.panY = (containerHeight - canvas.height * scale) / 2;

                updateCanvasTransform();
            }

            function setupEventListeners() {
                window.addEventListener('resize', resetZoomFit);

                // Toolbar Buttons
                document.getElementById('btnUndo').addEventListener('click', undo);
                document.getElementById('btnRedo').addEventListener('click', redo);
                document.getElementById('btnZoomIn').addEventListener('click', () => zoomAtPoint(1.2, viewport.clientWidth/2, viewport.clientHeight/2));
                document.getElementById('btnZoomOut').addEventListener('click', () => zoomAtPoint(0.8, viewport.clientWidth/2, viewport.clientHeight/2));
                document.getElementById('btnFit').addEventListener('click', resetZoomFit);
                document.getElementById('btnResetImage').addEventListener('click', resetImageColors);
                document.getElementById('btnNewPage').addEventListener('click', () => initBlankCanvas(600, 700));

                // Tools selection
                document.getElementById('toolFill').addEventListener('click', (e) => setTool('fill', e.target));
                document.getElementById('toolBrush').addEventListener('click', (e) => setTool('brush', e.target));
                document.getElementById('toolPicker').addEventListener('click', (e) => setTool('picker', e.target));

                // Mode Selection
                document.getElementById('modeSolid').addEventListener('click', (e) => setMode('solid', e.target));
                document.getElementById('modeGradient').addEventListener('click', (e) => setMode('gradient', e.target));

                // Custom Color Input
                document.getElementById('customColorInput').addEventListener('input', (e) => {
                    selectColor(e.target.value, null);
                });

                // Brush Size
                const brushSlider = document.getElementById('brushSize');
                brushSlider.addEventListener('input', (e) => {
                    state.brushSize = parseInt(e.target.value, 10);
                    document.getElementById('brushSizeVal').textContent = `${state.brushSize}px`;
                });

                // File Upload
                fileUploader.addEventListener('change', handleFileUpload);

                // Canvas Mouse Zoom
                viewport.addEventListener('wheel', (e) => {
                    e.preventDefault();
                    const rect = viewport.getBoundingClientRect();
                    const mouseX = e.clientX - rect.left;
                    const mouseY = e.clientY - rect.top;
                    const zoomFactor = e.deltaY < 0 ? 1.15 : 0.85;
                    zoomAtPoint(zoomFactor, mouseX, mouseY);
                }, { passive: false });

                // Canvas Interactions
                canvas.addEventListener('mousedown', handleCanvasMouseDown);
                window.addEventListener('mouseup', handleCanvasMouseUp);
                canvas.addEventListener('mousemove', handleCanvasMouseMove);

                // Export Options
                document.getElementById('btnExportPNG').addEventListener('click', exportPNG);
                document.getElementById('btnExportPDF').addEventListener('click', exportPDF);
            }

            function setTool(toolName, targetBtn) {
                state.currentTool = toolName;
                document.querySelectorAll('.tool-grid .tool-btn').forEach(btn => {
                    if (['Fill', 'Brush', 'Picker'].includes(btn.textContent)) btn.classList.remove('active');
                });
                targetBtn.classList.add('active');

                document.getElementById('brushOptionsSection').style.display = (toolName === 'brush') ? 'flex' : 'none';
            }

            function setMode(modeName, targetBtn) {
                state.fillMode = modeName;
                document.getElementById('modeSolid').classList.remove('active');
                document.getElementById('modeGradient').classList.remove('active');
                targetBtn.classList.add('active');
            }

            function zoomAtPoint(factor, clientX, clientY) {
                let newScale = state.scale * factor;
                if (newScale < state.minScale) newScale = state.minScale;
                if (newScale > 5) newScale = 5;

                const scaleChange = newScale - state.scale;
                state.panX -= (clientX - state.panX) * (scaleChange / state.scale);
                state.panY -= (clientY - state.panY) * (scaleChange / state.scale);
                state.scale = newScale;

                updateCanvasTransform();
            }

            function handleFileUpload(e) {
                const file = e.target.files[0];
                if (!file) return;

                const fileType = file.type;

                if (fileType === 'application/pdf') {
                    renderPDFFile(file);
                } else if (fileType.startsWith('image/')) {
                    renderImageFile(file);
                } else {
                    alert('Unsupported file format. Please upload JPG, PNG, WEBP or PDF.');
                }
            }

            function renderImageFile(file) {
                const reader = new FileReader();
                reader.onload = (event) => {
                    const img = new Image();
                    img.onload = () => {
                        canvas.width = img.width;
                        canvas.height = img.height;
                        ctx.drawImage(img, 0, 0);
                        saveOriginalState();
                        setTimeout(resetZoomFit, 30);
                        saveUndoState();
                    };
                    img.src = event.target.result;
                };
                reader.readAsDataURL(file);
            }

            function renderPDFFile(file) {
                const fileReader = new FileReader();
                fileReader.onload = function() {
                    const typedarray = new Uint8Array(this.result);
                    pdfjsLib.getDocument(typedarray).promise.then(pdf => {
                        pdf.getPage(1).then(page => {
                            const viewportPage = page.getViewport({ scale: 2.0 });
                            canvas.width = viewportPage.width;
                            canvas.height = viewportPage.height;
                            const renderContext = {
                                canvasContext: ctx,
                                viewport: viewportPage
                            };
                            page.render(renderContext).promise.then(() => {
                                saveOriginalState();
                                setTimeout(resetZoomFit, 30);
                                saveUndoState();
                            });
                        });
                    });
                };
                fileReader.readAsArrayBuffer(file);
            }

            let isPainting = false;

            function handleCanvasMouseDown(e) {
                if (e.button !== 0) return;
                const coords = getCanvasCoordinates(e);

                if (state.currentTool === 'fill') {
                    executeFloodFill(coords.x, coords.y);
                    saveUndoState();
                } else if (state.currentTool === 'brush') {
                    isPainting = true;
                    ctx.beginPath();
                    ctx.moveTo(coords.x, coords.y);
                    drawBrush(coords.x, coords.y);
                } else if (state.currentTool === 'picker') {
                    pickColor(coords.x, coords.y);
                }
            }

            function handleCanvasMouseMove(e) {
                if (isPainting && state.currentTool === 'brush') {
                    const coords = getCanvasCoordinates(e);
                    drawBrush(coords.x, coords.y);
                }
            }

            function handleCanvasMouseUp() {
                if (isPainting) {
                    isPainting = false;
                    ctx.closePath();
                    saveUndoState();
                }
            }

            function getCanvasCoordinates(e) {
                const rect = canvas.getBoundingClientRect();
                const x = Math.floor((e.clientX - rect.left) * (canvas.width / rect.width));
                const y = Math.floor((e.clientY - rect.top) * (canvas.height / rect.height));
                return { x, y };
            }

            function drawBrush(x, y) {
                const origPixel = getPixelColorData(originalImageData, x, y);
                const luma = 0.299 * origPixel.r + 0.587 * origPixel.g + 0.114 * origPixel.b;
                if (luma < 80) return;

                ctx.lineWidth = state.brushSize;
                ctx.lineCap = 'round';
                ctx.lineJoin = 'round';
                ctx.strokeStyle = state.selectedColor;
                ctx.lineTo(x, y);
                ctx.stroke();
                ctx.beginPath();
                ctx.moveTo(x, y);
            }

            function pickColor(x, y) {
                const imgData = ctx.getImageData(x, y, 1, 1).data;
                const hex = "#" + ((1 << 24) + (imgData[0] << 16) + (imgData[1] << 8) + imgData[2]).toString(16).slice(1);
                selectColor(hex, null);
            }

            function executeFloodFill(startX, startY) {
                const width = canvas.width;
                const height = canvas.height;
                const imgData = ctx.getImageData(0, 0, width, height);
                const data = imgData.data;
                const origData = originalImageData.data;

                const targetIdx = (startY * width + startX) * 4;
                const targetR = data[targetIdx];
                const targetG = data[targetIdx + 1];
                const targetB = data[targetIdx + 2];
                const targetA = data[targetIdx + 3];

                const startLuma = 0.299 * origData[targetIdx] + 0.587 * origData[targetIdx + 1] + 0.114 * origData[targetIdx + 2];
                if (startLuma < 80) return;

                const fillColor = parseHexColor(state.selectedColor);

                if (colorMatch(data, targetIdx, fillColor, 0)) return;

                const queue = [startX, startY];
                const visited = new Uint8Array(width * height);

                while (queue.length > 0) {
                    const cy = queue.pop();
                    const cx = queue.pop();
                    const idx = (cy * width + cx) * 4;

                    if (cx < 0 || cx >= width || cy < 0 || cy >= height) continue;
                    if (visited[cy * width + cx]) continue;

                    const origLuma = 0.299 * origData[idx] + 0.587 * origData[idx + 1] + 0.114 * origData[idx + 2];
                    if (origLuma < 80) continue;

                    if (colorMatch(data, idx, { r: targetR, g: targetG, b: targetB, a: targetA }, state.tolerance)) {
                        visited[cy * width + cx] = 1;

                        if (state.fillMode === 'solid') {
                            data[idx] = fillColor.r;
                            data[idx + 1] = fillColor.g;
                            data[idx + 2] = fillColor.b;
                            data[idx + 3] = 255;
                        } else {
                            const dist = Math.sqrt((cx - startX) ** 2 + (cy - startY) ** 2);
                            const factor = Math.min(dist / 150, 1);
                            data[idx] = Math.floor(fillColor.r * (1 - factor * 0.4));
                            data[idx + 1] = Math.floor(fillColor.g * (1 - factor * 0.4));
                            data[idx + 2] = Math.floor(fillColor.b * (1 - factor * 0.4));
                            data[idx + 3] = 255;
                        }

                        queue.push(cx + 1, cy);
                        queue.push(cx - 1, cy);
                        queue.push(cx, cy + 1);
                        queue.push(cx, cy - 1);
                    }
                }

                ctx.putImageData(imgData, 0, 0);
            }

            function colorMatch(data, idx, color, tolerance) {
                return (
                    Math.abs(data[idx] - color.r) <= tolerance &&
                    Math.abs(data[idx + 1] - color.g) <= tolerance &&
                    Math.abs(data[idx + 2] - color.b) <= tolerance
                );
            }

            function parseHexColor(hex) {
                const c = parseInt(hex.replace('#', ''), 16);
                return { r: (c >> 16) & 255, g: (c >> 8) & 255, b: c & 255 };
            }

            function getPixelColorData(imgData, x, y) {
                const idx = (y * imgData.width + x) * 4;
                return {
                    r: imgData.data[idx],
                    g: imgData.data[idx + 1],
                    b: imgData.data[idx + 2],
                    a: imgData.data[idx + 3]
                };
            }

            function saveUndoState() {
                if (state.undoStack.length >= state.maxUndoSteps) {
                    state.undoStack.shift();
                }
                state.undoStack.push(ctx.getImageData(0, 0, canvas.width, canvas.height));
                state.redoStack = [];
            }

            function undo() {
                if (state.undoStack.length > 1) {
                    state.redoStack.push(state.undoStack.pop());
                    const lastState = state.undoStack[state.undoStack.length - 1];
                    ctx.putImageData(lastState, 0, 0);
                }
            }

            function redo() {
                if (state.redoStack.length > 0) {
                    const nextState = state.redoStack.pop();
                    state.undoStack.push(nextState);
                    ctx.putImageData(nextState, 0, 0);
                }
            }

            function resetImageColors() {
                if (originalImageData) {
                    ctx.putImageData(originalImageData, 0, 0);
                    saveUndoState();
                }
            }

            function exportPNG() {
                const link = document.createElement('a');
                link.download = 'coloring-page.png';
                link.href = canvas.toDataURL('image/png');
                link.click();
            }

            function exportPDF() {
                const { jsPDF } = window.jspdf;
                const pdf = new jsPDF({
                    orientation: canvas.width > canvas.height ? 'landscape' : 'portrait',
                    unit: 'px',
                    format: [canvas.width, canvas.height]
                });

                const imgData = canvas.toDataURL('image/png');
                pdf.addImage(imgData, 'PNG', 0, 0, canvas.width, canvas.height);
                pdf.save('coloring-page.pdf');
            }

        })();
    </script>
</body>
</html>
