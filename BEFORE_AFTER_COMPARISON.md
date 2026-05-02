# Before & After: Custom Asset Refinements

## 1. Asset Type Dropdown

### ❌ BEFORE (9 types)
```html
<select id="asset-type">
    <option value="">-- Select Type --</option>
    <option value="hair">Hair</option>
    <option value="shirt">Shirt</option>
    <option value="pants">Pants</option>
    <option value="boots">Boots</option>
    <option value="head_acc">Head Accessory</option>
    <option value="shirt_acc">Shirt Accessory</option>
    <option value="eyes">Eyes</option>
    <option value="eyebrows">Eyebrows</option>
    <option value="mouth">Mouth</option>
</select>
```

### ✅ AFTER (12 types)
```html
<select id="asset-type">
    <option value="">-- Select Type --</option>
    <option value="hair">Hair</option>
    <option value="facehair">Facial Hair</option>
    <option value="eyes">Eyes</option>
    <option value="eyebrows">Eyebrows</option>
    <option value="mouth">Mouth</option>
    <option value="facemarks">Face Marks</option>
    <option value="head_acc">Head Accessory</option>
    <option value="shirt">Shirt</option>
    <option value="pants">Pants</option>
    <option value="boots">Boots</option>
    <option value="shirt_acc">Body Accessory</option>
    <option value="full_body">Full Body</option>
</select>
```

---

## 2. Tooltip Positioning & Text

### ❌ BEFORE (Centered, generic text)
```css
.tooltip-icon:hover::after { 
    content: attr(data-tooltip); 
    position: absolute; 
    bottom: 125%; 
    left: 50%; 
    transform: translateX(-50%); 
    /* ...styles... */
}
```

```html
<span class="tooltip-icon" data-tooltip="Works best if mask textures are grayscale and brighter than usual to work on many types of colors.">?</span>
<span class="tooltip-icon" data-tooltip="These textures will not be color changed.">?</span>
<span class="tooltip-icon" data-tooltip="Tells the sorting system what pixels should be removed...">?</span>
```

### ✅ AFTER (Left-positioned, contextual tips)
```css
.tooltip-icon:hover::after { 
    content: attr(data-tooltip); 
    position: absolute; 
    right: 120%; 
    left: auto; 
    bottom: -50%; 
    /* ...styles... */
}
```

```html
<span class="tooltip-icon" data-tooltip="Defines the primary color area. Tip: Use grayscale; bright greys tint much better!">?</span>
<span class="tooltip-icon" data-tooltip="Defines the secondary color area. Tip: Great for trims, belts, or dual-tone items.">?</span>
<span class="tooltip-icon" data-tooltip="Adds static, un-tintable details. Tip: Keep this transparent where you want the colored masks to show through.">?</span>
<span class="tooltip-icon" data-tooltip="Erases pixels from items worn underneath. Tip: Use opaque pixels to punch a hole through lower layers to prevent z-fighting.">?</span>
<span class="tooltip-icon" data-tooltip="Defines the safe rendering boundary. Tip: Only necessary if your item can be shifted up/down/left/right so it doesn't slide off the body.">?</span>
```

---

## 3. Categories Menu

### ❌ BEFORE (3 categories only)
```html
<div id="screen-categories" class="ui-screen">
    <button class="back-btn" onclick="switchScreen('screen-gender', 'base')">← Back to Base</button>
    <h2>Categories</h2>
    <button onclick="switchScreen('screen-head-cats')">Head</button>
    <button onclick="switchScreen('screen-body-cats')">Body</button>
    <button onclick="switchScreen('screen-fullbody-items', 'full_body')">Full Body</button>
</div>
```

### ✅ AFTER (4 categories with Custom Assets)
```html
<div id="screen-categories" class="ui-screen">
    <button class="back-btn" onclick="switchScreen('screen-gender', 'base')">← Back to Base</button>
    <h2>Categories</h2>
    <button onclick="switchScreen('screen-head-cats')">Head</button>
    <button onclick="switchScreen('screen-body-cats')">Body</button>
    <button onclick="switchScreen('screen-fullbody-items', 'full_body')">Full Body</button>
    <button onclick="switchScreen('screen-custom-assets', null)">Custom Assets</button>
</div>

<!-- NEW SCREEN -->
<div id="screen-custom-assets" class="ui-screen">
    <button class="back-btn" onclick="switchScreen('screen-categories', 'base')">← Back to Categories</button>
    <h2>Custom Assets</h2>
    <div id="custom-asset-grid" class="equip-grid"></div>
</div>
```

---

## 4. Asset Type Mappings

### ❌ BEFORE (9 types only)
```javascript
const assetTypeToLayerKey = {
    'hair': 'hair',
    'shirt': 'shirt',
    'pants': 'pants',
    'boots': 'boots',
    'head_acc': 'head_acc',
    'shirt_acc': 'shirt_acc',
    'eyes': 'eyes',
    'eyebrows': 'eyebrows',
    'mouth': 'mouth'
};
```

### ✅ AFTER (12 types + focus mapping for icon generation)
```javascript
const assetTypeToLayerKey = {
    'hair': 'hair',
    'facehair': 'facehair',
    'eyes': 'eyes',
    'eyebrows': 'eyebrows',
    'mouth': 'mouth',
    'facemarks': 'head_acc',
    'head_acc': 'head_acc',
    'shirt': 'shirt',
    'pants': 'pants',
    'boots': 'boots',
    'shirt_acc': 'shirt_acc',
    'full_body': 'full_body'
};

// NEW: Maps asset types to 3D camera focus
const assetTypeToFocus = {
    'hair': 'head',
    'facehair': 'head',
    'eyes': 'head',
    'eyebrows': 'head',
    'mouth': 'head',
    'facemarks': 'head',
    'head_acc': 'head',
    'shirt': 'bodytop',
    'shirt_acc': 'bodytop',
    'pants': 'bot',
    'boots': 'bot',
    'full_body': 'body'
};
```

---

## 5. Export Function

### ❌ BEFORE (No icon generation)
```javascript
async function exportCustomAsset() {
    const assetName = document.getElementById('asset-name').value.trim();
    const assetType = document.getElementById('asset-type').value;
    
    // Validation...
    
    const zip = new JSZip();
    
    // File inputs to check
    const fileInputs = [
        { id: 'file-mask1', suffix: '_mask' },
        { id: 'file-mask2', suffix: '_mask2' },
        // ...more files...
    ];
    
    let hasFiles = false;
    
    // Read uploaded files and add to zip
    for (const fileInput of fileInputs) {
        const input = document.getElementById(fileInput.id);
        if (input.files && input.files.length > 0) {
            const file = input.files[0];
            const fileName = `${assetName}${fileInput.suffix}.png`;
            const arrayBuffer = await file.arrayBuffer();
            zip.file(fileName, arrayBuffer);
            hasFiles = true;
        }
    }
    
    // Create asset_info.json
    const assetInfo = { name: assetName, type: assetType };
    zip.file('asset_info.json', JSON.stringify(assetInfo, null, 2));
    
    // Generate and download zip
    try {
        const blob = await zip.generateAsync({ type: 'blob' });
        const url = URL.createObjectURL(blob);
        const link = document.createElement('a');
        link.href = url;
        link.download = `${assetName}_cc.zip`;
        link.click();
        URL.revokeObjectURL(url);
        alert(`Asset exported successfully as ${assetName}_cc.zip!`);
        closeAssetCreatorModal();
    } catch (err) {
        console.error('Error generating ZIP:', err);
        alert('Error exporting asset. Please try again.');
    }
}
```

### ✅ AFTER (With 3D icon generation)
```javascript
async function exportCustomAsset() {
    const assetName = document.getElementById('asset-name').value.trim();
    const assetType = document.getElementById('asset-type').value;
    
    // Validation...
    
    const zip = new JSZip();
    
    // File inputs to check
    const fileInputs = [
        { id: 'file-mask1', suffix: '_mask' },
        { id: 'file-mask2', suffix: '_mask2' },
        // ...more files...
    ];
    
    let hasFiles = false;
    let mask1File = null;
    let mask2File = null;
    
    // Read uploaded files and add to zip
    for (const fileInput of fileInputs) {
        const input = document.getElementById(fileInput.id);
        if (input.files && input.files.length > 0) {
            const file = input.files[0];
            const fileName = `${assetName}${fileInput.suffix}.png`;
            const arrayBuffer = await file.arrayBuffer();
            zip.file(fileName, arrayBuffer);
            hasFiles = true;
            
            // Store file references for icon generation
            if (fileInput.id === 'file-mask1') mask1File = file;
            if (fileInput.id === 'file-mask2') mask2File = file;
        }
    }
    
    if (!hasFiles) {
        alert('Please upload at least one PNG file.');
        return;
    }
    
    // Create asset_info.json
    const assetInfo = { name: assetName, type: assetType };
    zip.file('asset_info.json', JSON.stringify(assetInfo, null, 2));
    
    // ✨ NEW: Generate 3D icon
    try {
        console.log(`Generating 3D icon for ${assetName}...`);
        const focus = assetTypeToFocus[assetType] || 'head';
        const iconBlob = await generateCustomAssetIcon(assetName, mask1File, mask2File, focus);
        zip.file(`icon_${assetName}.png`, iconBlob);
        console.log(`Icon generated for ${assetName}`);
    } catch (err) {
        console.error('Error generating icon:', err);
        alert('Warning: Icon generation failed, but asset will be exported without icon.');
    }
    
    // Generate and download zip
    try {
        const blob = await zip.generateAsync({ type: 'blob' });
        const url = URL.createObjectURL(blob);
        const link = document.createElement('a');
        link.href = url;
        link.download = `${assetName}_cc.zip`;
        link.click();
        URL.revokeObjectURL(url);
        alert(`Asset exported successfully as ${assetName}_cc.zip!`);
        closeAssetCreatorModal();
    } catch (err) {
        console.error('Error generating ZIP:', err);
        alert('Error exporting asset. Please try again.');
    }
}

// ✨ NEW: Icon generation function
async function generateCustomAssetIcon(assetName, mask1File, mask2File, focus) {
    // Creates 64x64 2D canvas with base character
    // Draws mask1 and mask2 on top
    // Creates hidden 128x128 SkinView3D
    // Applies camera based on focus type
    // Renders and crops to 100x100
    // Returns PNG blob
    // ...implementation...
}

// ✨ NEW: Helper to convert files to data URLs
function fileToDataUrl(file) {
    return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = () => resolve(reader.result);
        reader.onerror = reject;
        reader.readAsDataURL(file);
    });
}
```

---

## 6. Import Function

### ❌ BEFORE (Random section creation)
```javascript
async function importCustomAsset(event) {
    const file = event.target.files[0];
    // Parse ZIP and metadata...
    
    // Extract PNG files
    const pngFiles = [];
    for (const [fileName, fileObj] of Object.entries(loaded.files)) {
        if (fileName.endsWith('.png')) {
            const blob = await fileObj.async('blob');
            const blobUrl = URL.createObjectURL(blob);
            customAssetCache[fileName] = blobUrl;
            pngFiles.push(fileName);
        }
    }
    
    // Determine layer key
    const layerKey = assetTypeToLayerKey[assetType];
    
    // ❌ BEFORE: Creates new section for each asset type
    let grid = findOrCreateEquipGridForLayer(layerKey, assetType);
    
    // Create button with mask1 preview
    const button = document.createElement('button');
    button.className = 'accessory-btn';
    
    const mask1 = pngFiles.find(f => f.includes('_mask.png') && !f.includes('_mask2'));
    if (mask1) {
        const img = document.createElement('img');
        img.src = customAssetCache[mask1];  // ❌ Raw texture, not 3D icon
        img.alt = assetName;
        button.appendChild(img);
    } else {
        button.textContent = assetName.substring(0, 3).toUpperCase();
        button.style.fontSize = '12px';
    }
    
    grid.appendChild(button);
}

function findOrCreateEquipGridForLayer(layerKey, assetType) {
    let grid = document.querySelector(`[data-layer-key="${layerKey}"]`);
    if (!grid) {
        // Creates new custom category section
        const container = document.getElementById('left-panel');
        const section = document.createElement('div');
        // ...creates title, grid, appends to left panel...
    }
    return grid;
}
```

### ✅ AFTER (Centralized custom assets grid)
```javascript
async function importCustomAsset(event) {
    const file = event.target.files[0];
    // Parse ZIP and metadata...
    
    // Extract PNG files
    const pngFiles = [];
    let iconUrl = null;  // ✨ NEW: Look for generated icon
    for (const [fileName, fileObj] of Object.entries(loaded.files)) {
        if (fileName.endsWith('.png')) {
            const blob = await fileObj.async('blob');
            const blobUrl = URL.createObjectURL(blob);
            customAssetCache[fileName] = blobUrl;
            pngFiles.push(fileName);
            
            // ✨ NEW: Look for the icon file
            if (fileName === `icon_${assetName}.png`) {
                iconUrl = blobUrl;
            }
        }
    }
    
    // Determine layer key
    const layerKey = assetTypeToLayerKey[assetType];
    
    // ✅ AFTER: Always use centralized custom-asset-grid
    let grid = document.getElementById('custom-asset-grid');
    if (!grid) {
        alert('Custom Assets grid not found. Please navigate to Custom Assets screen first.');
        return;
    }
    
    // Create button with 3D icon preview
    const button = document.createElement('button');
    button.className = 'accessory-btn';
    
    // ✨ NEW: Use generated icon if available
    if (iconUrl) {
        const img = document.createElement('img');
        img.src = iconUrl;  // ✨ 3D rendered icon
        img.alt = assetName;
        button.appendChild(img);
    } else {
        // Fallback to mask or initials
        const mask1 = pngFiles.find(f => f.includes('_mask.png') && !f.includes('_mask2'));
        if (mask1 && customAssetCache[mask1]) {
            const img = document.createElement('img');
            img.src = customAssetCache[mask1];
            img.alt = assetName;
            button.appendChild(img);
        } else {
            button.textContent = assetName.substring(0, 3).toUpperCase();
            button.style.fontSize = '12px';
        }
    }
    
    // Support all 12 asset types
    if (['hair', 'facehair', 'shirt', 'pants', 'boots', 'eyes', 'eyebrows', 'mouth', 'full_body'].includes(layerKey)) {
        button.onclick = () => equipPart(layerKey, assetName);
    } else if (layerKey.startsWith('head_acc') || layerKey.startsWith('shirt_acc')) {
        button.onclick = () => toggleAccessory(layerKey, assetName, button);
    }
    
    grid.appendChild(button);
}

// ❌ REMOVED: findOrCreateEquipGridForLayer
// No longer needed - all assets go to centralized grid
```

---

## Summary of Changes

| Aspect | Before | After |
|--------|--------|-------|
| **Asset Types** | 9 | 12 (+facehair, facemarks, full_body) |
| **Tooltip Position** | Centered | Left-aligned |
| **Tooltip Text** | Generic | Contextual with tips |
| **Custom Assets UI** | Scattered sections | Centralized menu screen |
| **Icon Generation** | None | 3D rendered with camera focus |
| **Icon Format** | Mask texture | Professional 100×100 PNG |
| **Import Location** | Random placement | Dedicated custom-asset-grid |
| **Button Handlers** | 7 types supported | 12 types supported |

---

**Result: A more professional, organized, and user-friendly custom asset system!** ✨
