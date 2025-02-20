---
title: Pegging engine
tags: []
slug: /tools/peg-engine
navbar_path: []
---

<div>
  <h1>Pegging Engine</h1>
  <p>
    A simple tool for generating laser-cuttable pegboards and other mounting
    panels.
  </p>
  <noscript>
    Sorry, but your browser appears to not support JavaScript. Please upgrade to
    Internet Explorer 3.0 or Netscape Navigator 2.0 to use this applet.
  </noscript>
  <form id="generator-input">
    <div>
      <div>
        Units
        <select class="generator-units" name="length-scaling">
          <option value="1" checked>mm</option>
          <option value="10">cm</option>
          <option value="1000">m</option>
          <option value="25.4">in</option>
          <option value="304.8">ft</option>
        </select>
      </div>
      <div class="sheet-params">
        <h2>Sheet parameters</h2>
        <p>(custom shapes besides rounded rectangles Coming Soon™)</p>
        <div>
          <label>
            Width
            <input
              class="shared-units"
              name="sheet-width"
              type="number"
              value="300"
            />
          </label>
          <label>
            Horizontal padding
            <input
              class="shared-units"
              name="sheet-h-padding"
              type="number"
              value="10"
            />
          </label>
        </div>
        <div>
          <label>
            Height
            <input
              class="shared-units"
              name="sheet-height"
              type="number"
              value="420"
            />
          </label>
          <label>
            Vertical padding
            <input
              class="shared-units"
              name="sheet-v-padding"
              type="number"
              value="10"
            />
          </label>
        </div>
        <div>
          <label>
            Corner fillet radius
            <input
              class="shared-units"
              name="sheet-fillet-radius"
              type="number"
              value="20"
            />
          </label>
        </div>
      </div>
      <div class="grid-params">
        <h2>Grid parameters</h2>
        <div>
          Horizontal parity
          <label>
            <input name="sheet-h-parity" type="radio" value="auto" checked />
            Auto
          </label>
          <label>
            <input name="sheet-h-parity" type="radio" value="even" /> Even
          </label>
          <label>
            <input name="sheet-h-parity" type="radio" value="odd" /> Odd
          </label>
        </div>
        <div>
          Vertical parity
          <label>
            <input name="sheet-v-parity" type="radio" value="auto" checked />
            Auto
          </label>
          <label>
            <input name="sheet-v-parity" type="radio" value="even" /> Even
          </label>
          <label>
            <input name="sheet-v-parity" type="radio" value="odd" /> Odd
          </label>
        </div>
        <label>
          Grid pattern
          <select name="grid-pattern">
            <option value="std-pegboard">
              Standard pegboard (1" pitch, ¼" holes)
            </option>
            <option value="skadis">IKEA SKÅDIS pegboard</option>
            <!--option value="wall-control">
                  1" pegboard with ¼" holes and 1" slots (Wall Control style)
                </option-->
            <option value="optical-table">
              Metric optical breadboard (M6 pilot holes, 25mm pitch)
            </option>
            <option value="molle-half">Rigid MOLLE panel (half holes)</option>
            <option value="molle-full">
              Rigid MOLLE panel (full holes, 3mm bars)
            </option>
            <option value="icc-smc">ICC Structured Media Center</option>
          </select>
        </label>
      </div>
    </div>
    <input type="hidden" name="debug-mode" id="debug-mode" value="1" />
  </form>
  <h2>Output</h2>
  <p id="generator-summary"></p>
  <div>
    <button onclick="doSVGDownload()">Download SVG</button>
  </div>
  <div id="generator-output" style="margin: 20px">
    <div
      style="
        margin-left: auto;
        margin-right: auto;
        max-width: 500px;
        max-height: 500px;
      "
    >
      <svg
        style="width: 100%; height: 100%; max-width: 500px; max-height: 500px"
      >
        <style>
          * {
            fill: transparent;
            stroke-width: 1px;
          }
        </style>
        <rect class="sheet" stroke="black" />
        <g class="holes"></g>
      </svg>
    </div>
  </div>
  <h2>Materials advice</h2>
  <ul>
    <li>Standard pegboards are usually 3/16-1/4" thick hardboard.</li>
    <li>IKEA sells SKÅDIS as ~3/16" thick fiberboard with acrylic paint.</li>
    <li>
      Optical breadboards are meant to be dimensionally accurate and rigid.
      Thus, they are typically tapped from 0.5-0.75" thick aluminum or steel
      slabs. However, some breadboards appear to be sold in acrylic, a much
      laser-cutter-friendlier material. Since 1/2" may cause some laser systems
      to slope a lot, you may want to consider cutting multiple sheets,
      laminating or screwing them together, and then threading the holes through
      the stacked sheet.
    </li>
    <li>
      Commercially-sold rigid MOLLE panels are often cut from 3/16" polyethylene
      aluminum, or plywood sheets, and then marked up a whole bunch because
      they're Tactical™. If you are choosing alternative materials, you should
      generally prefer less-brittle, more flexible materials.
    </li>
  </ul>
</div>
<script>
  const SVG_STD_PEGBOARD = `<circle cx="12.7" cy="12.7" r="3.175" stroke="red" stroke-width="1px"/>`;
  const SVG_SKADIS_HOLE =
    `<g transform="translate(10 0)">` +
    (`<path stroke="red" stroke-width="1px"` +
      (`d="M 2.5 5 ` +
        `v 10 ` +
        `a 2.5 2.5 0 0 1 -5 0 ` +
        `v -10 ` +
        `a 2.5 2.5 0 0 1 5 0"`) +
      `/>`) +
    `</g>`;
  const SVG_OPTICAL_TABLE_UNTHREADED = `<circle cx="12.5" cy="12.5" r="5.5" stroke="red" stroke-width="1px"/>`;
  const SVG_MOLLE_HALF = `<rect x="2.5" y="10" width="35" height="30" rx="5" stroke="red" stroke-width="1px"/>`;
  const SVG_MOLLE_FULL = `<rect x="2.5" y="2.5" width="35" height="22" rx="5" stroke="red" stroke-width="1px" />`;
  const ICC_SMC = (function generateICCSMC() {
    // these are in inches
    const SQUARE_SIZE = 6;
    const N_HOLES_ON_SIDE = 13;
    const HOLE_PITCH = SQUARE_SIZE / (N_HOLES_ON_SIDE - 1);
    const MARGIN = 0.625 / 2;
    const HOLE_RAD_MM = 0.125 * 25.4; // assumed quantity
    const TESS_SIZE_MM = 25.4 * (2 * MARGIN + SQUARE_SIZE);
    const hHoleXs = Array(N_HOLES_ON_SIDE)
      .fill(null)
      .map((_, i) => i * HOLE_PITCH);
    const vHoleYs = Array(N_HOLES_ON_SIDE - 2)
      .fill(null)
      .map((_, i) => (i + 1) * HOLE_PITCH);
    function round(x) {
      return Math.round(x * 1000) / 1000;
    }
    const holeTransformsInches = [
      hHoleXs.map((x) => [MARGIN + x, MARGIN]), // top
      hHoleXs.map((x) => [MARGIN + x, MARGIN + SQUARE_SIZE]), // bottom
      vHoleYs.map((y) => [MARGIN, MARGIN + y]), // left
      vHoleYs.map((y) => [MARGIN + SQUARE_SIZE, MARGIN + y]), // right
    ].flat(1);
    const holeTransforms = holeTransformsInches.map(([x, y]) => [
      round(x * 25.4),
      round(y * 25.4),
    ]);
    const holes = holeTransforms
      .map(
        ([x, y]) =>
          `<circle cx="${x}" cy="${y}" r="${HOLE_RAD_MM}" stroke="red" stroke-width="1px"/>`,
      )
      .join("");
    console.log(holes);
    return {
      w: TESS_SIZE_MM,
      h: TESS_SIZE_MM,
      tess: [[holes]],
    };
  })();
  const PATTERNS = {
    "std-pegboard": {
      w: 25.4,
      h: 25.4,
      tess: [[SVG_STD_PEGBOARD]],
    },
    skadis: {
      w: 20,
      h: 20,
      tess: [
        [SVG_SKADIS_HOLE, ""],
        ["", SVG_SKADIS_HOLE],
      ],
    },
    "optical-table": {
      w: 25,
      h: 25,
      tess: [[SVG_OPTICAL_TABLE_UNTHREADED]],
    },
    "molle-half": {
      w: 38,
      h: 50,
      tess: [[SVG_MOLLE_HALF]],
    },
    "molle-full": {
      w: 38,
      h: 25,
      tess: [[SVG_MOLLE_FULL]],
    },
    "icc-smc": ICC_SMC,
  };
  class Debouncer {
    constructor({ minPeriodMs }) {
      this.state = {
        tag: "ready",
      };
      this.minPeriodMs = minPeriodMs;
    }
    execute(action) {
      const now = Date.now();
      console.debug("execute", this.state);
      switch (this.state.tag) {
        case "ready":
          this.state = {
            tag: "cooldown",
            nextReady: now + this.minPeriodMs,
            queuedAction: null,
          };
          setTimeout(() => {
            this.advance();
          }, this.minPeriodMs);
          action();
          break;
        case "cooldown":
          this.state.queuedAction = action;
          this.advance();
          break;
      }
    }
    advance() {
      console.debug("advance", this.state);
      const now = new Date();
      switch (this.state.tag) {
        case "cooldown":
          if (now >= this.state.nextReady) {
            const queuedAction = this.state.queuedAction;
            this.state = { tag: "ready" };
            if (queuedAction) {
              queuedAction();
            }
          }
          break;
        default:
          break;
      }
    }
  }
  /** Holder for everything */
  class LaserPatternGenerator {
    constructor({ form, svg, summary }) {
      this.form = form;
      this.svg = svg;
      this.summary = summary;
      this.debouncer = new Debouncer({ minPeriodMs: 200 });
      const inputs = form.querySelectorAll(
        ".sheet-params input, .sheet-params input, .grid-params input, .grid-params select",
      );
      console.debug("hooking input event on form inputs", inputs);
      inputs.forEach((e) =>
        e.addEventListener("input", () => {
          this.updateSVG();
        }),
      );
      console.debug("hooking change event for unit selector", inputs);
      const units = form.querySelector(".generator-units");
      var prevUnitScale = Number(units.value);
      units.addEventListener("change", (ev) => {
        const newUnitScale = Number(units.value);
        console.debug(
          "performing units change from",
          prevUnitScale,
          "to",
          newUnitScale,
        );
        this.updateUnits(prevUnitScale / newUnitScale);
        prevUnitScale = newUnitScale;
      });
    }
    updateUnits(multiplier) {
      this.form.querySelectorAll(".shared-units").forEach((e) => {
        const rawVal = Number(e.value) * multiplier;
        const roundTo = 100000;
        e.value = Math.round((rawVal + Number.EPSILON) * roundTo) / roundTo;
      });
    }
    updateSVG() {
      this.debouncer.execute(() => this._updateSVG());
    }
    _updateSVG() {
      console.log("_updateSVG");
      const formData = new FormData(this.form);
      const holes = this.svg.querySelector(".holes");
      const rect = this.svg.querySelector(".sheet");
      const scaling = Number(formData.get("length-scaling"));
      const params = {
        debug: formData.get("debug-mode") == "1",
        w: Number(formData.get("sheet-width")) * scaling,
        xpad: Number(formData.get("sheet-h-padding")) * scaling,
        xpar: formData.get("sheet-h-parity"),
        h: Number(formData.get("sheet-height")) * scaling,
        ypad: Number(formData.get("sheet-v-padding")) * scaling,
        ypar: formData.get("sheet-v-parity"),
        fillet: Number(formData.get("sheet-fillet-radius")) * scaling,
        pattern: PATTERNS[formData.get("grid-pattern")],
      };
      console.debug("Rendering SVG with params", params);
      // resize the sheet
      rect.setAttribute("x", 0);
      rect.setAttribute("y", 0);
      rect.setAttribute("width", params.w);
      rect.setAttribute("height", params.h);
      rect.setAttribute("rx", params.fillet);
      rect.setAttribute("ry", params.fillet);
      this.svg.setAttribute("width", `${params.w / 10}cm`);
      this.svg.setAttribute("height", `${params.h / 10}cm`);
      this.svg.setAttribute("viewBox", `0 0 ${params.w} ${params.h}`);
      // clean the old holes
      holes.replaceChildren();
      // calculate the grid
      const ptn = params.pattern;
      const xGrid = packAxis(params.w, ptn.w, params.xpar, params.xpad);
      const yGrid = packAxis(params.h, ptn.h, params.ypar, params.ypad);
      console.debug("Calculated grid axes", xGrid, yGrid);
      const strs = [];
      // put in the new holes
      for (let i = 0; i < xGrid.count; i++) {
        for (let j = 0; j < yGrid.count; j++) {
          const x = xGrid.step * i + xGrid.offset;
          const y = yGrid.step * j + yGrid.offset;
          const xrepeat = ptn.tess[0].length;
          const yrepeat = ptn.tess.length;
          const tesspattern = ptn.tess[j % yrepeat][i % xrepeat];
          strs.push(`<g transform="translate(${x} ${y})">`);
          strs.push(tesspattern);
          strs.push("</g>");
        }
      }
      holes.innerHTML = strs.join();
      this.summary.innerText = `Grid is ${xGrid.count} × ${yGrid.count} (${xGrid.count * yGrid.count} cells)`;
    }
  }
  function packAxis(lenLimit, lenCell, parity, padding) {
    var count = Math.floor((lenLimit - 2 * padding) / lenCell);
    switch (parity) {
      case "auto":
        break;
      case "odd":
        if (count % 2 == 0) {
          count--;
        }
        break;
      case "even":
        if (count % 2 == 1) {
          count--;
        }
        break;
      default:
        throw new TypeError(`unknown parity ${parity}`);
    }
    return {
      count,
      offset: (lenLimit - count * lenCell) / 2,
      step: lenCell,
    };
  }
  const laserPatternGenerator = new LaserPatternGenerator({
    form: document.querySelector("#generator-input"),
    svg: document.querySelector("#generator-output svg"),
    summary: document.querySelector("#generator-summary"),
  });
  laserPatternGenerator.updateSVG();
  function doSVGDownload() {
    const data = laserPatternGenerator.svg.outerHTML;
    var blob = new Blob([data], { type: "image/svg+xml;charset=utf-8" });
    var url = URL.createObjectURL(blob);
    var downloadLink = document.createElement("a");
    downloadLink.href = url;
    downloadLink.download = "pattern.svg";
    document.body.appendChild(downloadLink);
    downloadLink.click();
    document.body.removeChild(downloadLink);
  }
</script>
