# Custom Asset Importer/Exporter Guide

## Overview

Your Character Creator app now supports **Custom Asset Creation and Sharing**! Users can create their own pixel art textures, package them into shareable `.zip` files, and import other users' custom assets directly into the app.

---

## Features Implemented

### 1. **Create Custom Asset**
- Open the "🛠️ Create Custom Asset" modal from the top-left control panel
- Name your asset (e.g., "coolpants", "customhair")
- Select the asset type (Hair, Shirt, Pants, Boots, Head Accessory, Shirt Accessory, Eyes, Eyebrows, Mouth)
- Upload PNG texture files for up to 5 layers:
  - **Mask 1**: Primary color mask (grayscale, works best bright)
  - **Mask 2**: Secondary color mask (optional)
  - **Detail**: Color-locked texture details
  - **Exclude**: Defines pixel removal for layering
  - **Inclusion**: For displacement/offset effects
- Click "Export Asset" to download a `.zip` file

### 2. **Import Custom Asset**
- Click the "📦 Import Asset" button in the top-left
- Select a `.zip` file created by the exporter or shared by another user
- The asset is extracted and automatically:
  - Added to the appropriate category in the left panel
  - Cached in browser memory for instant loading
  - Ready to equip on your character

### 3. **Technical Integration**
- **No core rendering changes**: The feature seamlessly integrates with existing canvas rendering
- **Blob-based loading**: Custom assets are stored as Blob URLs in the `customAssetCache`
- **Lazy intercept**: `loadImageAsync()` now checks the cache before loading from the server
- **Dynamic UI**: Custom assets appear in existing or new category grids

---

## How to Use

### Creating a Custom Asset

1. Prepare your PNG textures (64×64 pixels recommended for consistency)
2. Click **"🛠️ Create Custom Asset"** button
3. Fill in the form:
   - **Asset Name**: `coolpants` (no spaces, alphanumeric)
   - **Asset Type**: Choose from the dropdown
4. Upload your PNG files (at least one is required)
   - Hover over the **"?"** icon to see tooltip descriptions
5. Click **"Export Asset"**
6. Your file will download as `coolpants_cc.zip`

### Sharing & Importing

1. Share the `.zip` file with other users (Discord, email, cloud storage, etc.)
2. They click **"📦 Import Asset"** and select your `.zip`
3. The asset appears in their character creator instantly!
4. They can equip it like any other item

---

## File Structure (Inside `.zip`)

When you export an asset named "coolpants", the `.zip` contains:

```
coolpants_cc.zip
├── asset_info.json          (metadata)
├── coolpants_mask.png       (primary color mask)
├── coolpants_mask2.png      (secondary color mask - optional)
├── coolpants_detail.png     (detail layer - optional)
├── coolpants_exclude.png    (exclusion mask - optional)
└── coolpants_inclus.png     (inclusion layer - optional)
```

### `asset_info.json` Format
```json
{
  "name": "coolpants",
  "type": "pants"
}
```

---

## Layer Type Reference

| Layer Type | Description | Best For |
|---|---|---|
| **Mask 1** | Primary tintable layer | Base color of the item |
| **Mask 2** | Secondary tintable layer (2-color items) | Stripes, secondary color sections |
| **Detail** | Color-locked texture | Seams, patterns, non-tinted decorations |
| **Exclude** | Defines pixel clipping/removal | Preventing overlap with other layers |
| **Inclusion** | Displacement/offset effects | Items that need spatial adjustment |

---

## Technical Details

### New Global Objects

- **`customAssetCache`**: `{fileName: blobUrl}`
  - Stores loaded PNG files as Blob URLs
  - Checked first in `loadImageAsync()` before network requests

### New Functions

#### `openAssetCreatorModal()`
Opens the asset creation modal.

#### `closeAssetCreatorModal()`
Closes modal and resets form fields.

#### `exportCustomAsset()`
- Reads uploaded files from form inputs
- Creates JSZip instance with renamed files
- Generates `asset_info.json`
- Triggers download of `.zip` file

#### `importCustomAsset(event)`
- Loads `.zip` file using JSZip
- Parses `asset_info.json` for metadata
- Extracts PNGs and creates Blob URLs
- Caches them in `customAssetCache`
- Dynamically creates button in appropriate grid
- Hooks up `equipPart()` or `toggleAccessory()` handlers

#### `findOrCreateEquipGridForLayer(layerKey, assetType)`
- Finds existing grid or creates new custom category
- Returns grid element for button insertion

#### Modified `loadImageAsync(url)`
```javascript
// Now checks customAssetCache first:
if (customAssetCache[url]) {
    // Use cached Blob URL
    img.src = customAssetCache[url];
} else {
    // Load from server
    img.src = url;
}
```

### CSS Classes

- `.modal-overlay`: Dark frosted glass backdrop (blur + semi-transparent)
- `.modal-container`: Main modal box with border and padding
- `.file-upload-row`: Flex layout for file input + label + tooltip icon
- `.tooltip-icon`: Styled "?" with hover tooltip display
- `.modal-button-group`: Button group with flex layout

---

## Tooltip Descriptions

Hover over the **"?"** icon next to each file input to see:

| Field | Tooltip |
|---|---|
| **Mask 1** | "Works best if mask textures are grayscale and brighter than usual to work on many types of colors." |
| **Mask 2** | "Works best if mask textures are grayscale and brighter than usual to work on many types of colors." |
| **Detail** | "These textures will not be color changed." |
| **Exclude** | "Tells the sorting system what pixels should be removed from other layers when they are sorted above them so there is no weird clipping." |
| **Inclusion** | "Optional: Only needed if the asset is supposed to be displaced." |

---

## Workflow Example

### Creator's Workflow:
1. Design pixel art in Aseprite, Piskel, or Photoshop (64×64)
2. Export layers as PNG files
3. Open "Create Custom Asset" modal
4. Name it "mystichair", set type to "Hair"
5. Upload `mystichair_mask.png` + `mystichair_detail.png`
6. Click "Export Asset" → saves `mystichair_cc.zip`
7. Share file on community Discord

### User's Workflow:
1. Download `mystichair_cc.zip` from community
2. Click "📦 Import Asset"
3. Select the zip file
4. Boom! "Mystic Hair" now appears in the Hair section
5. Click to equip and test on character

---

## Troubleshooting

| Issue | Solution |
|---|---|
| "Invalid asset file: missing asset_info.json" | Make sure you're importing a file exported by the creator, not a random ZIP |
| Assets don't appear after import | Check browser console (F12) for errors; ensure PNG filenames match the export format |
| Can't export: "Please upload at least one PNG file" | Select at least one PNG file before clicking Export |
| Asset button shows blank | Happens if Mask 1 isn't included; the fallback shows first 3 letters of asset name |

---

## Code Integration Points

### Where It Hooks In:

1. **HTML Changes**:
   - Added JSZip CDN in `<head>`
   - Added CSS for modal styling
   - Added two buttons to `#layout-controls`
   - Added modal HTML before closing `</script>`

2. **JavaScript Changes**:
   - Global `customAssetCache` object
   - Modified `loadImageAsync()` to check cache
   - New functions for modal + export/import logic
   - No changes to core rendering (`drawTintedLayer`, `renderSkin`, etc.)

3. **Preservation**:
   - Canvas rendering untouched ✓
   - charState structure unchanged ✓
   - Active layer order logic unmodified ✓
   - Existing equip/toggle functions reused ✓

---

## Browser Compatibility

- **JSZip**: Works in all modern browsers (Chrome, Firefox, Safari, Edge)
- **Blob URLs**: Supported in all modern browsers
- **File API**: Requires modern browser (IE11+ with polyfills)

---

## Next Steps (Optional Enhancements)

- Add preview canvas in modal showing how items will look
- Implement asset versioning system
- Create community asset repository
- Add auto-naming suggestions based on layer analysis
- Support for `.psd` or other formats via conversion

---

## Questions or Issues?

Check the browser console (`F12 → Console`) for debug messages when exporting/importing assets.

Enjoy creating and sharing custom assets! 🎨
