<!DOCTYPE html>
<!-- Dynamic Styling (responsive honeycomb picker + mobile keyboard-safe modal) -->
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0, viewport-fit=cover"
    />
    <title>Dynamic Styling</title>
<!-- 122820250838AM SATURDAY TESTING -->
    <style>
      *,
      *::before,
      *::after {
        box-sizing: border-box;
      }

      body {
        font-size: 24px;
        background-color: black;
        color: red;
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 20px;
      }

      h1,
      h2 {
        margin-top: 20px;
        color: red;
      }

      .controls {
        margin-bottom: 20px;
        padding: 15px;
        background-color: #333;
        border-radius: 8px;
        display: flex;
        flex-wrap: wrap;
        gap: 20px;
        align-items: center;
        justify-content: center;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
      }

      .controls.hidden {
        display: none;
      }

      .controls label {
        color: white;
        font-family: sans-serif;
        font-size: 16px;
        margin-bottom: 5px;
        display: block;
      }

      .controls > div {
        flex: 1 1 auto;
        min-width: 180px;
      }

      .controls select,
      .controls input[type="number"] {
        width: 100%;
        padding: 8px;
        border-radius: 4px;
        border: 1px solid #666;
        background-color: #555;
        color: white;
        font-size: 16px;
      }

      .toggle-button {
        padding: 12px 20px;
        background-color: #007bff;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 18px;
        margin-top: 20px;
        transition: background-color 0.3s ease;
      }
      .toggle-button:hover {
        background-color: #0056b3;
      }

      a {
        color: lightblue;
        text-decoration: none;
        margin-bottom: 10px;
        display: inline-block;
      }
      a:hover {
        text-decoration: underline;
      }

      #fullscreenButton {
        background-color: #0056b3;
      }
      #fullscreenButton:hover {
        background-color: #00448f;
      }

      /* ---------- Color control rows (buttons replacing fields) ---------- */
      .colorControlRow {
        display: grid;
        grid-template-columns: 1fr auto;
        gap: 10px;
        align-items: end;
      }

      .pickBtn {
        width: 100%;
        padding: 10px 12px;
        border-radius: 6px;
        border: 1px solid #777;
        background: #666;
        color: #fff;
        cursor: pointer;
        font-size: 16px;
      }
      .pickBtn:hover {
        background: #717171;
      }

      .swatchLine {
        display: flex;
        align-items: center;
        gap: 10px;
        margin-top: 8px;
      }

      .swatch {
        width: 22px;
        height: 22px;
        border-radius: 4px;
        border: 1px solid #999;
        background: #000;
        display: inline-block;
      }

      .hexLabel {
        color: #ddd;
        font-size: 14px;
        font-family: Arial, sans-serif;
        word-break: break-word;
      }

      /* ---------- W3Schools-style picker modal ---------- */
      .pickerOverlay {
        position: fixed;
        inset: 0;
        background: rgba(0, 0, 0, 0.6);
        display: none;
        align-items: center;
        justify-content: center;
        padding: 16px;
        z-index: 9999;

        /* Use dynamic viewport height where supported */
        height: 100dvh;
      }
      .pickerOverlay.open {
        display: flex;
      }

      .pickerPanel {
        width: min(680px, 100%);
        background: #ffffff;
        border-radius: 14px;
        padding: 14px 14px 12px;
        box-shadow: 0 18px 48px rgba(0, 0, 0, 0.35);

        /* KEY: keep the picker inside the visible browser area */
        max-height: calc(100dvh - 32px);
        overflow: auto;
        -webkit-overflow-scrolling: touch;
      }

      .pickerTitle {
        font-family: Arial, sans-serif;
        text-align: center;
        font-size: clamp(22px, 6.2vw, 44px);
        margin: 6px 0 10px;
        font-weight: 500;
        color: #111;
      }

      .hexWrap {
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 6px 0 10px;
      }

      /* Smaller, responsive “honeycomb” */
      .hexGrid {
        /* This clamp makes the honeycomb shrink on phones and grow on desktops */
        --hex: clamp(18px, 4.2vw, 32px);
        --gap: clamp(3px, 1vw, 6px);

        display: flex;
        flex-direction: column;
        gap: var(--gap);
        user-select: none;
        touch-action: manipulation;
      }

      .hexRow {
        display: flex;
        justify-content: center;
        gap: var(--gap);
      }
      .hexRow.offset {
        padding-left: calc((var(--hex) / 2) + (var(--gap) / 2));
      }

      .hex {
        width: var(--hex);
        height: calc(var(--hex) * 0.92);
        border: 0;
        padding: 0;
        background: var(--c, #000);
        cursor: pointer;
        clip-path: polygon(25% 6%, 75% 6%, 100% 50%, 75% 94%, 25% 94%, 0% 50%);
        outline: none;
        position: relative;
      }

      .hex:focus-visible {
        outline: 3px solid #111;
        outline-offset: 2px;
      }

      .hex.selected::after {
        content: "";
        position: absolute;
        inset: 3px;
        border: 3px solid #fff;
        border-radius: 10px;
        clip-path: polygon(25% 6%, 75% 6%, 100% 50%, 75% 94%, 25% 94%, 0% 50%);
        box-shadow: 0 0 0 2px rgba(0, 0, 0, 0.35);
      }

      .pickerSubTitle {
        font-family: Arial, sans-serif;
        text-align: center;
        font-size: clamp(22px, 6.2vw, 44px);
        margin: 8px 0 8px;
        font-weight: 500;
        color: #111;
      }

      /* “Enter a color” area stays visible (and gets extra room above keyboard) */
      .enterArea {
        position: sticky;
        bottom: 0;
        background: #fff;
        padding: 10px 0 2px;
        border-top: 1px solid #eee;
      }

      .enterRow {
        display: grid;
        grid-template-columns: 1fr auto;
        gap: 10px;
        align-items: center;
        width: min(520px, 100%);
        margin: 0 auto;
      }

      .enterRow input {
        width: 100%;
        font-size: clamp(16px, 4.4vw, 22px);
        padding: 10px 12px;
        border: 1px solid #cfcfcf;
        border-radius: 4px;
        outline: none;
      }

      .enterRow button {
        font-size: clamp(16px, 4.4vw, 22px);
        padding: 10px 16px;
        border-radius: 4px;
        border: 1px solid #cfcfcf;
        background: #f2f2f2;
        cursor: pointer;
      }
      .enterRow button:hover {
        background: #e8e8e8;
      }

      .wrongInput {
        text-align: center;
        font-family: Arial, sans-serif;
        color: #c00000;
        margin: 8px 0 0;
        min-height: 22px;
        font-size: 16px;
      }

      .pickerFooter {
        display: flex;
        justify-content: center;
        gap: 10px;
        margin-top: 10px;
      }

      .pickerFooter .closeBtn {
        font-size: 16px;
        padding: 8px 14px;
        border-radius: 6px;
        border: 1px solid #cfcfcf;
        background: #fff;
        cursor: pointer;
      }
      .pickerFooter .closeBtn:hover {
        background: #f6f6f6;
      }
    </style>
  </head>

  <body>
    <h1>X_X</h1>
    <a href="javascript: location.reload();" aria-label="Reload this page">reload this page</a>

    <h1 id="myH1">X_x</h1>
    <h2 id="myH2" contenteditable="true" aria-label="Editable H2 Text">X_x</h2>

    <button id="fullscreenButton" class="toggle-button" aria-label="Toggle fullscreen mode">
      Enter Fullscreen
    </button>

    <button id="toggleMenuButton" class="toggle-button" aria-expanded="false" aria-controls="styleControls">
      Show Menu
    </button>

    <!-- Default menus set to hidden -->
    <div class="controls hidden" id="styleControls">
      <div>
        <div class="colorControlRow">
          <div><label for="bgPickBtn">Background Color:</label></div>
          <button id="bgPickBtn" class="pickBtn" type="button" data-target="bg">Pick a Color</button>
        </div>
        <div class="swatchLine">
          <span class="swatch" id="bgSwatch" aria-hidden="true"></span>
          <span class="hexLabel" id="bgHexLabel">#000000</span>
        </div>
      </div>

      <div>
        <div class="colorControlRow">
          <div><label for="h1PickBtn">H1 Color:</label></div>
          <button id="h1PickBtn" class="pickBtn" type="button" data-target="h1">Pick a Color</button>
        </div>
        <div class="swatchLine">
          <span class="swatch" id="h1Swatch" aria-hidden="true"></span>
          <span class="hexLabel" id="h1HexLabel">#ff0000</span>
        </div>
      </div>

      <div>
        <div class="colorControlRow">
          <div><label for="h2PickBtn">H2 Color:</label></div>
          <button id="h2PickBtn" class="pickBtn" type="button" data-target="h2">Pick a Color</button>
        </div>
        <div class="swatchLine">
          <span class="swatch" id="h2Swatch" aria-hidden="true"></span>
          <span class="hexLabel" id="h2HexLabel">#ff0000</span>
        </div>
      </div>

      <div>
        <label for="bodyFontSelector">Body Font:</label>
        <select id="bodyFontSelector" aria-label="Select body font family">
          <option value="Arial, sans-serif">Arial</option>
          <option value="Verdana, sans-serif">Verdana</option>
          <option value="Georgia, serif">Georgia</option>
          <option value='"Times New Roman", serif'>Times New Roman</option>
          <option value='"Courier New", monospace'>Courier New</option>
          <option value="Impact, sans-serif">Impact</option>
          <option value="cursive">Cursive (Browser Default)</option>
          <option value="fantasy">Fantasy (Browser Default)</option>
        </select>
      </div>

      <div>
        <label for="h1FontSelector">H1 Font:</label>
        <select id="h1FontSelector" aria-label="Select H1 font family">
          <option value="Arial, sans-serif">Arial</option>
          <option value="Verdana, sans-serif">Verdana</option>
          <option value="Georgia, serif">Georgia</option>
          <option value='"Times New Roman", serif'>Times New Roman</option>
          <option value='"Courier New", monospace'>Courier New</option>
          <option value="Impact, sans-serif">Impact</option>
          <option value="cursive">Cursive (Browser Default)</option>
          <option value="fantasy">Fantasy (Browser Default)</option>
        </select>
      </div>

      <div>
        <label for="h2FontSelector">H2 Font:</label>
        <select id="h2FontSelector" aria-label="Select H2 font family">
          <option value="Arial, sans-serif">Arial</option>
          <option value="Verdana, sans-serif">Verdana</option>
          <option value="Georgia, serif">Georgia</option>
          <option value='"Times New Roman", serif'>Times New Roman</option>
          <option value='"Courier New", monospace'>Courier New</option>
          <option value="Impact, sans-serif">Impact</option>
          <option value="cursive">Cursive (Browser Default)</option>
          <option value="fantasy">Fantasy (Browser Default)</option>
        </select>
      </div>

      <div>
        <label for="bodyFontSizeInput">Body Font Size (px):</label>
        <input type="number" id="bodyFontSizeInput" value="24" min="1" aria-label="Set body font size in pixels" />
      </div>

      <div>
        <label for="h1FontSizeInput">H1 Font Size (px):</label>
        <input type="number" id="h1FontSizeInput" value="48" min="1" aria-label="Set H1 font size in pixels" />
      </div>

      <div>
        <label for="h2FontSizeInput">H2 Font Size (px):</label>
        <input type="number" id="h2FontSizeInput" value="32" min="1" aria-label="Set H2 font size in pixels" />
      </div>
    </div>

    <!-- Picker modal -->
    <div class="pickerOverlay" id="pickerOverlay" role="dialog" aria-modal="true" aria-labelledby="pickerTitle">
      <div class="pickerPanel" id="pickerPanel">
        <div class="pickerTitle" id="pickerTitle">Pick a Color:</div>

        <div class="hexWrap">
          <div class="hexGrid" id="hexGrid" aria-label="Color palette"></div>
        </div>

        <div class="pickerSubTitle">Or Enter a Color:</div>

        <div class="enterArea" id="enterArea">
          <div class="enterRow">
            <input
              id="manualColorInput"
              type="text"
              placeholder="Color value"
              autocomplete="off"
              inputmode="text"
            />
            <button id="okColorBtn" type="button">OK</button>
          </div>
          <div class="wrongInput" id="wrongInputMsg" aria-live="polite"></div>

          <div class="pickerFooter">
            <button class="closeBtn" id="closePickerBtn" type="button">Close</button>
          </div>
        </div>
      </div>
    </div>

    <script>
      // ---------- Elements ----------
      const bodyElement = document.body;
      const h1Element = document.getElementById("myH1");
      const h2Element = document.getElementById("myH2");

      const bodyFontSelector = document.getElementById("bodyFontSelector");
      const h1FontSelector = document.getElementById("h1FontSelector");
      const h2FontSelector = document.getElementById("h2FontSelector");

      const bodyFontSizeInput = document.getElementById("bodyFontSizeInput");
      const h1FontSizeInput = document.getElementById("h1FontSizeInput");
      const h2FontSizeInput = document.getElementById("h2FontSizeInput");

      const toggleMenuButton = document.getElementById("toggleMenuButton");
      const styleControls = document.getElementById("styleControls");
      const fullscreenButton = document.getElementById("fullscreenButton");

      const bgSwatch = document.getElementById("bgSwatch");
      const h1Swatch = document.getElementById("h1Swatch");
      const h2Swatch = document.getElementById("h2Swatch");
      const bgHexLabel = document.getElementById("bgHexLabel");
      const h1HexLabel = document.getElementById("h1HexLabel");
      const h2HexLabel = document.getElementById("h2HexLabel");

      const pickerOverlay = document.getElementById("pickerOverlay");
      const pickerPanel = document.getElementById("pickerPanel");
      const hexGrid = document.getElementById("hexGrid");
      const manualColorInput = document.getElementById("manualColorInput");
      const okColorBtn = document.getElementById("okColorBtn");
      const closePickerBtn = document.getElementById("closePickerBtn");
      const wrongInputMsg = document.getElementById("wrongInputMsg");

      // ---------- State ----------
      const state = { bg: "#000000", h1: "#ff0000", h2: "#ff0000" };
      let activeTarget = "bg";
      let lastFocusedEl = null;

      // ---------- Helpers ----------
      const clamp01 = (x) => Math.min(1, Math.max(0, x));

      function hslToHex(h, s, l) {
        h = ((h % 360) + 360) % 360;
        s = clamp01(s);
        l = clamp01(l);

        const c = (1 - Math.abs(2 * l - 1)) * s;
        const x = c * (1 - Math.abs(((h / 60) % 2) - 1));
        const m = l - c / 2;

        let r = 0, g = 0, b = 0;
        if (h < 60) [r, g, b] = [c, x, 0];
        else if (h < 120) [r, g, b] = [x, c, 0];
        else if (h < 180) [r, g, b] = [0, c, x];
        else if (h < 240) [r, g, b] = [0, x, c];
        else if (h < 300) [r, g, b] = [x, 0, c];
        else [r, g, b] = [c, 0, x];

        const toHex = (v) => Math.round((v + m) * 255).toString(16).padStart(2, "0");
        return `#${toHex(r)}${toHex(g)}${toHex(b)}`.toLowerCase();
      }

      function normalizeHex(str) {
        if (!str) return null;
        let s = String(str).trim().toLowerCase();
        if (s.startsWith("#")) s = s.slice(1);
        if (/^[0-9a-f]{3}$/.test(s)) s = s.split("").map((ch) => ch + ch).join("");
        if (/^[0-9a-f]{6}$/.test(s)) return "#" + s;
        return null;
      }

      function isValidCssColor(input) {
        const s = String(input || "").trim();
        if (!s) return false;
        if (normalizeHex(s)) return true;
        const el = document.createElement("span");
        el.style.color = "";
        el.style.color = s;
        return el.style.color !== "";
      }

      function applyStyles() {
        bodyElement.style.backgroundColor = state.bg;
        h1Element.style.color = state.h1;
        h2Element.style.color = state.h2;

        bodyElement.style.fontFamily = bodyFontSelector.value;
        h1Element.style.fontFamily = h1FontSelector.value;
        h2Element.style.fontFamily = h2FontSelector.value;

        bodyElement.style.fontSize = `${bodyFontSizeInput.value}px`;
        h1Element.style.fontSize = `${h1FontSizeInput.value}px`;
        h2Element.style.fontSize = `${h2FontSizeInput.value}px`;
      }

      function syncColorWidgets() {
        bgSwatch.style.background = state.bg;
        h1Swatch.style.background = state.h1;
        h2Swatch.style.background = state.h2;

        bgHexLabel.textContent = state.bg;
        h1HexLabel.textContent = state.h1;
        h2HexLabel.textContent = state.h2;
      }

      function setColor(target, color) {
        state[target] = color;
        applyStyles();
        syncColorWidgets();
      }

      // ---------- Honeycomb palette ----------
      const rowLengths = [6, 7, 8, 9, 10, 11, 12, 11, 10, 9, 8, 7, 6];

      function buildPalette() {
        hexGrid.innerHTML = "";
        const totalRows = rowLengths.length;

        for (let r = 0; r < totalRows; r++) {
          const row = document.createElement("div");
          row.className = "hexRow" + (r % 2 === 1 ? " offset" : "");
          const cols = rowLengths[r];

          const tRow = r / (totalRows - 1);
          const light = 0.18 + tRow * 0.64;
          const sat = 0.92 - Math.abs(tRow - 0.5) * 0.18;

          for (let c = 0; c < cols; c++) {
            const tCol = cols === 1 ? 0.5 : c / (cols - 1);
            const hue = 220 - tRow * 220 + tCol * 360;
            const color = hslToHex(hue, sat, light);

            const btn = document.createElement("button");
            btn.type = "button";
            btn.className = "hex";
            btn.style.setProperty("--c", color);
            btn.dataset.color = color;
            btn.setAttribute("aria-label", `Select ${color}`);

            btn.addEventListener("click", () => {
              manualColorInput.value = color;
              highlightSelected(color);
              wrongInputMsg.textContent = "";
            });

            row.appendChild(btn);
          }

          hexGrid.appendChild(row);
        }
      }

      function highlightSelected(colorLike) {
        const normalized = normalizeHex(colorLike) || null;
        hexGrid.querySelectorAll(".hex").forEach((b) => b.classList.remove("selected"));
        if (!normalized) return;
        const match = hexGrid.querySelector(`.hex[data-color="${normalized}"]`);
        if (match) match.classList.add("selected");
      }

      // ---------- Keyboard-safe modal behavior ----------
      function adjustPickerForKeyboard() {
        if (!pickerOverlay.classList.contains("open")) return;

        const vv = window.visualViewport;
        if (!vv) {
          // fallback: keep it within 100dvh (CSS already does most of this)
          pickerOverlay.style.paddingBottom = "16px";
          pickerPanel.style.maxHeight = "calc(100dvh - 32px)";
          return;
        }

        // vv.height is the visible area not covered by the keyboard/browser UI
        const visibleH = Math.max(200, vv.height);
        pickerPanel.style.maxHeight = `${Math.max(200, visibleH - 32)}px`;

        // Add a little padding so the panel isn't jammed against the bottom
        const kbApprox = Math.max(0, window.innerHeight - vv.height - vv.offsetTop);
        pickerOverlay.style.paddingBottom = `${kbApprox + 16}px`;

        // When keyboard is open, pushing content upward helps
        pickerOverlay.style.alignItems = kbApprox > 0 ? "flex-start" : "center";
      }

      function openPicker(target) {
        activeTarget = target;
        wrongInputMsg.textContent = "";
        lastFocusedEl = document.activeElement;

        manualColorInput.value = state[target];
        highlightSelected(state[target]);

        pickerOverlay.classList.add("open");
        adjustPickerForKeyboard();

        // focus the input; also scroll it into view so keyboard won't cover it
        setTimeout(() => {
          manualColorInput.focus({ preventScroll: true });
          manualColorInput.scrollIntoView({ block: "center", behavior: "smooth" });
        }, 0);
      }

      function closePicker() {
        pickerOverlay.classList.remove("open");
        wrongInputMsg.textContent = "";

        // reset any inline adjustments
        pickerOverlay.style.paddingBottom = "16px";
        pickerOverlay.style.alignItems = "center";
        pickerPanel.style.maxHeight = "calc(100dvh - 32px)";

        if (lastFocusedEl && typeof lastFocusedEl.focus === "function") lastFocusedEl.focus();
      }

      function confirmPickerValue() {
        const raw = manualColorInput.value.trim();
        if (!isValidCssColor(raw)) {
          wrongInputMsg.textContent = "Wrong Input";
          return;
        }
        const normalized = normalizeHex(raw);
        setColor(activeTarget, normalized || raw);
        closePicker();
      }

      // Keep picker sized correctly when the mobile keyboard opens/closes
      if (window.visualViewport) {
        window.visualViewport.addEventListener("resize", adjustPickerForKeyboard);
        window.visualViewport.addEventListener("scroll", adjustPickerForKeyboard);
      } else {
        window.addEventListener("resize", adjustPickerForKeyboard);
      }

      manualColorInput.addEventListener("focus", () => {
        adjustPickerForKeyboard();
        setTimeout(() => manualColorInput.scrollIntoView({ block: "center", behavior: "smooth" }), 50);
      });

      // Close if click outside panel
      pickerOverlay.addEventListener("click", (e) => {
        if (e.target === pickerOverlay) closePicker();
      });

      closePickerBtn.addEventListener("click", closePicker);
      okColorBtn.addEventListener("click", confirmPickerValue);

      manualColorInput.addEventListener("keydown", (e) => {
        if (e.key === "Enter") confirmPickerValue();
        if (e.key === "Escape") closePicker();
      });

      document.addEventListener("keydown", (e) => {
        if (!pickerOverlay.classList.contains("open")) return;
        if (e.key === "Escape") closePicker();
      });

      // Hook picker buttons
      document.querySelectorAll(".pickBtn").forEach((btn) => {
        btn.addEventListener("click", () => openPicker(btn.dataset.target));
      });

      // ---------- Menu toggle ----------
      toggleMenuButton.addEventListener("click", () => {
        const isHidden = styleControls.classList.toggle("hidden");
        toggleMenuButton.textContent = isHidden ? "Show Menu" : "Hide Menu";
        toggleMenuButton.setAttribute("aria-expanded", String(!isHidden));
      });

      // ---------- Fullscreen toggle ----------
      fullscreenButton.addEventListener("click", () => {
        if (!document.fullscreenElement) {
          document.documentElement.requestFullscreen()
            .then(() => (fullscreenButton.textContent = "Exit Fullscreen"))
            .catch((err) => console.error(`Error enabling fullscreen: ${err.message} (${err.name})`));
        } else {
          document.exitFullscreen()
            .then(() => (fullscreenButton.textContent = "Enter Fullscreen"))
            .catch((err) => console.error(`Error exiting fullscreen: ${err.message} (${err.name})`));
        }
      });

      document.addEventListener("fullscreenchange", () => {
        fullscreenButton.textContent = document.fullscreenElement ? "Exit Fullscreen" : "Enter Fullscreen";
      });

      // ---------- Font/size listeners ----------
      bodyFontSelector.addEventListener("change", applyStyles);
      h1FontSelector.addEventListener("change", applyStyles);
      h2FontSelector.addEventListener("change", applyStyles);

      bodyFontSizeInput.addEventListener("input", applyStyles);
      h1FontSizeInput.addEventListener("input", applyStyles);
      h2FontSizeInput.addEventListener("input", applyStyles);

      // ---------- Init ----------
      document.addEventListener("DOMContentLoaded", () => {
        buildPalette();
        applyStyles();
        syncColorWidgets();
      });
    </script>
  </body>
</html>
