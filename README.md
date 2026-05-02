# Cobblemon Historia - Character Creator

A fully interactive, web-based character creator built for the Cobblemon Historia roleplay server. This tool allows users to customize their 3D Minecraft avatars with a variety of clothing, hair, and accessories, fine-tune positions, adjust colors, and export their final skin.

---

## How to Run Locally

To run this app on your computer and see your changes instantly, you will need **VS Code** and the **Live Server** extension.

### Prerequisites
1.  Download and install [Visual Studio Code (VS Code)](https://code.visualstudio.com/).
2.  Install the **Live Server** extension by Ritwick Dey from the VS Code Extensions Marketplace.

### Launching the App
1.  Open this project folder in VS Code.
2.  Open the `index_22.html` file (or whichever is the current main HTML file).
3.  Start the server using any of these methods:
    *   **Status Bar:** Click the `Go Live` button in the bottom right corner of VS Code.
    *   **Right-Click:** Right-click inside the HTML file code and select `Open with Live Server`.
    *   **Shortcut:** Press `Alt+L, Alt+O` (Windows) or `Cmd+L, Cmd+O` (Mac).
4.  The Character Creator will automatically open in your default web browser! To stop the server, click the `Port: 5500` button in the status bar or press `Alt+L, Alt+C`.

---

## Features & Functionality

This application goes far beyond standard skin generation, offering a robust set of tools for perfect avatar customization.

### Advanced Coloring System
*   **Dynamic Skin Tones:** A custom slider effortlessly blends between 6 predefined skin tones, automatically updating the base body, eyelids, and dynamic skin layers (like scars).
*   **Primary & Secondary Colors:** Customize the main fabric color and accent colors independently. Secondary accents default to a custom gold (`#feb44d`).
*   **Independent Layer Tinting:** Every single piece of clothing and hair can be colored individually using native HTML5 Canvas compositing.

### ↕Spatial Adjustments & Displacement
*   **Up/Down Displacement:** Certain items (like mouth types, body belts, glasses, and scars) feature an `UP/DOWN` slider. You can manually adjust exactly where these items sit on the pixel grid.
*   **Exclusion Tracking:** When an item is moved vertically, its "exclusion mask" (the area it erases on the layer below it to prevent clipping) moves perfectly with it.
*   **Left/Right Flipping & Offsetting:** Asymmetric items (like scars) include a `Flip` toggle to mirror them horizontally. You can also manually slide them left and right across the face using the offset slider.

### Smart Layering & Exclusions
*   **Dynamic Z-Indexing:** Items are drawn in a strict order (e.g., skin -> pants -> shirt -> accessories). You can drag-and-drop items in the "Equipped Items" list to manually change their rendering order.
*   **Clipping Prevention:** The canvas engine uses `destination-out` composite operations to "punch holes" in lower layers. For example, wearing high boots will automatically erase the bottom pixels of your pants so they don't clip through the leather.
*   **The Slim Model Fix:** A custom post-processing pass automatically shifts clothing pixels on the arms inward by 1 pixel when the "Female (Slim)" 3px arm model is selected, fixing the classic Minecraft skin texture wrapping glitch.

### Layout Management
*   **Export/Import Layouts:** Built an outfit you love? Click `Export Layout` to download a `.json` file containing your exact character state, layer order, offsets, and colors.
*   **Shareable Configurations:** Users can share these `.json` files with friends or GMs, who can use `Import Layout` to load the exact avatar instantly.

### 3D Preview & Exporting
*   **Live 3D Viewer:** Powered by `skinview3d`, allowing you to freely rotate and inspect your character in real-time. Includes an auto-rotation toggle.
*   **One-Click Export:** Click `⬇ Export Skin` to instantly download a perfectly compiled, game-ready 64x64 `.png` Minecraft skin file.