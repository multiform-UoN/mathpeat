---
layout: default
title: "Does peat creep? Canvas version"
description: "Canvas version of the peat age-depth model featuring 3 distinct physical creep mechanisms."
---

# Does Peat Creep? Canvas Version

Peatlands are dynamic, deformable porous media storing over 30% of global soil carbon. Researchers in the **MATHPEAT Network** are currently investigating the evidence for peat creep, how it should be represented mathematically, and what its consequences may be for peatland age-depth structure and long-term carbon storage.

The model below is deliberately simple. It is not a full 3D landscape-scale creep model: creep mostly redistributes peat locally. Here we ask what a single representative column would look like under different vertical creep velocity profiles if unresolved downslope or marginal creep eventually exports material from that column.

This comparison version uses the same equations and controls as the main interactive post, but the plots are drawn directly with the browser's HTML `<canvas>` element rather than Chart.js.

---

## Simple Column Model & 3 Physical Creep Formulations

Let $M(a)$ be cumulative peat dry mass per unit area at cohort age $a$, measured in $\mathrm{kg\,m^{-2}}$. The estimated peat depth is

$$H(a) = \frac{M(a)}{\rho_b},$$

where fixed dry bulk density is $\rho_b = 100\,\mathrm{kg\,m^{-3}}$.

Without creep, the classic accumulation-decay balance (*Clymo 1984*) is

$$\frac{dM}{da} = A - \alpha M, \qquad M(0)=0,$$

where $A$ is the accumulation flux (default $60\,\mathrm{g\,m^{-2}\,yr^{-1}}$) and $\alpha$ is the first-order decay rate (default $10^{-4}\,\mathrm{yr^{-1}}$).

### 3 Physical Creep Velocity Profiles
You can choose between **3 distinct physical mechanisms** for how creep velocity varies vertically through the column:

1. **Constant Deep Creep ($u \propto H$):** Creep speed increases with total overburden mass $M$. The slider specifies the creep speed observed at the final 10,000-year core depth ($u_{\text{final}}$). Export scales quadratically ($\beta M^2$). Creep loss accelerates at depth.
2. **Linear Shear Profile (Gravity-Driven Shear):** Maximum creep speed $u_{\text{surf}}$ at the surface, decreasing linearly to $0$ at bedrock ($H_{\text{tot}}$). Deep peat near bedrock stops creeping and follows pure decay kinetics.
3. **Surface-Driven Processes (e.g. Freeze-Thaw / Active Layer):** Creep speed is maximum at the surface ($u_{\text{surf}}$) and decreases linearly to $0$ at a tunable active layer depth $H_{\text{active}}$ (default $1.0\,\mathrm{m}$). Peat buried deeper below $H_{\text{active}}$ stops creeping.

---

## The Reverse-Engineering Challenge (Equifinality)

In field peatland science, scientists extract a physical peat core, measure its depth $H$, and perform Radiocarbon ($^{14}\text{C}$) dating to get age $a$ at depth $H$. 

**The Challenge:** A given observed age-depth profile could be fitted by TWO completely different mechanisms:
1. **Classic Clymo Model (Decay Only):** Fitted with a lower surface accumulation rate $A_{\text{Clymo}}$ or higher decay rate $\alpha_{\text{Clymo}}$.
2. **Peat Creep Model (Decay + Export):** Driven by a higher true surface accumulation rate $A_{\text{Creep}}$, standard decay $\alpha_{\text{Creep}}$, and active lateral creep $u_{\text{ref}}$.

If creep is ignored, fitting an observed profile with only Clymo's equation will **underestimate true carbon accumulation rates ($A$)** and misattribute lateral creep loss to decomposition!

---

## Interactive Age-Depth & Cumulative Mass Model

{% raw %}
<style>
  .model-container {
    background: #f8f9fa;
    border: 1px solid #e1e4e8;
    border-radius: 8px;
    padding: 1.5rem;
    margin: 1.5rem 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  }

  .controls-split {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 1.5rem;
    margin-bottom: 1.5rem;
  }

  .param-group {
    background: #ffffff;
    border: 1px solid #d1d5da;
    border-radius: 6px;
    padding: 1.25rem;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }

  .param-group-clymo {
    border-top: 4px solid #157878;
  }

  .param-group-creep {
    border-top: 4px solid #d9534f;
  }

  .param-group-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 1rem;
    padding-bottom: 0.5rem;
    border-bottom: 1px solid #eaeaea;
  }

  .param-group-title {
    font-weight: 700;
    font-size: 1rem;
  }

  .param-group-title.clymo { color: #157878; }
  .param-group-title.creep { color: #d9534f; }

  .param-badge {
    font-size: 0.75rem;
    font-weight: bold;
    padding: 0.2rem 0.6rem;
    border-radius: 4px;
  }

  .param-badge-clymo { background: #eef7f7; color: #157878; border: 1px solid #bce1e1; }
  .param-badge-creep { background: #fdf2f2; color: #d9534f; border: 1px solid #f5c6cb; }

  .control-field {
    margin-bottom: 1rem;
  }

  .control-field label {
    display: block;
    font-weight: 600;
    font-size: 0.9rem;
    margin-bottom: 0.2rem;
    color: #24292e;
  }

  .control-field small {
    color: #666;
    display: block;
    font-size: 0.8rem;
    margin-bottom: 0.4rem;
  }

  .control-field select {
    width: 100%;
    padding: 0.4rem 0.6rem;
    border-radius: 4px;
    border: 1px solid #bce1e1;
    background: #ffffff;
    font-weight: 600;
    color: #d9534f;
    cursor: pointer;
  }

  .slider-row {
    display: flex;
    align-items: center;
    gap: 0.75rem;
  }

  .slider-row input[type="range"] {
    flex: 1;
    cursor: pointer;
  }

  .value-badge {
    background: #f6f8fa;
    color: #24292e;
    border: 1px solid #d1d5da;
    border-radius: 4px;
    padding: 0.2rem 0.5rem;
    font-family: monospace;
    font-weight: bold;
    min-width: 95px;
    text-align: center;
    font-size: 0.85rem;
  }

  .summary-metrics {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
    margin-bottom: 1.5rem;
  }

  .metric-card {
    flex: 1;
    min-width: 180px;
    background: #ffffff;
    border-left: 4px solid #157878;
    border-radius: 4px;
    padding: 0.75rem 1rem;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }

  .metric-card.creep-card {
    border-left-color: #d9534f;
  }

  .metric-title {
    font-size: 0.82rem;
    color: #586069;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }

  .metric-value {
    font-size: 1.2rem;
    font-weight: bold;
    color: #24292e;
    margin-top: 0.25rem;
  }

  .charts-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
    gap: 1.5rem;
  }

  .chart-wrapper {
    background: #ffffff;
    border: 1px solid #d1d5da;
    border-radius: 6px;
    padding: 1rem;
  }

  .canvas-element {
    display: block;
    width: 100%;
    height: 360px;
  }

  .model-notes {
    font-size: 0.85rem;
    color: #586069;
    margin-top: 1rem;
    line-height: 1.5;
    background: #ffffff;
    padding: 0.75rem 1rem;
    border-radius: 6px;
    border: 1px solid #e1e4e8;
  }
</style>

<div class="model-container">
  <div class="controls-split">
    <!-- Clymo Model Panel -->
    <div class="param-group param-group-clymo">
      <div class="param-group-header">
        <span class="param-group-title clymo">Clymo Model (Decay Only)</span>
        <span class="param-badge param-badge-clymo">Teal Line</span>
      </div>

      <div class="control-field">
        <label for="canvasSliderA_clymo">Accumulation ($A_{\text{Clymo}}$)</label>
        <small>Surface peat input flux</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderA_clymo" min="20" max="150" step="5" value="60">
          <span class="value-badge" id="canvasValA_clymo">60 g/m²/yr</span>
        </div>
      </div>

      <div class="control-field">
        <label for="canvasSliderAlpha_clymo">Decay Rate ($\alpha_{\text{Clymo}}$)</label>
        <small>First-order catotelm decay rate</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderAlpha_clymo" min="0.00001" max="0.0005" step="0.00001" value="0.0001">
          <span class="value-badge" id="canvasValAlpha_clymo">1.0e-4 /yr</span>
        </div>
      </div>
    </div>

    <!-- Creep Model Panel -->
    <div class="param-group param-group-creep">
      <div class="param-group-header">
        <span class="param-group-title creep">Creep Model (Decay + Export)</span>
        <span class="param-badge param-badge-creep">Dashed Red Line</span>
      </div>

      <div class="control-field">
        <label for="canvasSelectModel">Creep Mechanism</label>
        <small id="canvasModelHelpText">Select vertical velocity profile</small>
        <select id="canvasSelectModel">
          <option value="overburden" selected>1. Constant Deep Creep (u ∝ H, speed at final depth)</option>
          <option value="shear">2. Linear Shear Profile (gravity-driven shear, 0 at bed)</option>
          <option value="activeLayer">3. Surface-Driven Processes (e.g. freeze-thaw, active layer)</option>
        </select>
      </div>

      <div class="control-field">
        <label for="canvasSliderA_creep">Accumulation ($A_{\text{Creep}}$)</label>
        <small>True surface input flux</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderA_creep" min="20" max="150" step="5" value="60">
          <span class="value-badge" id="canvasValA_creep">60 g/m²/yr</span>
        </div>
      </div>

      <div class="control-field">
        <label for="canvasSliderAlpha_creep">Decay Rate ($\alpha_{\text{Creep}}$)</label>
        <small>True catotelm decay rate</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderAlpha_creep" min="0.00001" max="0.0005" step="0.00001" value="0.0001">
          <span class="value-badge" id="canvasValAlpha_creep">1.0e-4 /yr</span>
        </div>
      </div>

      <div class="control-field">
        <label for="canvasSliderCreep" id="canvasLabelCreep">Creep Speed ($u_\mathrm{final}$)</label>
        <small id="canvasSublabelCreep">Speed observed at final 10k-yr depth</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderCreep" min="0" max="10" step="0.1" value="0.5">
          <span class="value-badge" id="canvasValCreep">0.50 cm/yr</span>
        </div>
      </div>

      <!-- Conditional Active Layer Depth Slider (Model 3) -->
      <div class="control-field" id="canvasContainerActiveDepth" style="display: none; background: #fff8f8; padding: 0.6rem; border-radius: 4px; border: 1px solid #f5c6cb;">
        <label for="canvasSliderActiveDepth">Active Creep Depth ($H_{\text{active}}$)</label>
        <small>Depth where creep velocity drops to 0</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderActiveDepth" min="0.2" max="5.0" step="0.1" value="1.0">
          <span class="value-badge" id="canvasValActiveDepth">1.0 m</span>
        </div>
      </div>
    </div>
  </div>

  <div class="summary-metrics">
    <div class="metric-card">
      <div class="metric-title">Decay Only Depth (10,000 yrs)</div>
      <div class="metric-value" id="canvasMetricDecayOnly">-</div>
    </div>
    <div class="metric-card creep-card">
      <div class="metric-title">With Creep Depth (10,000 yrs)</div>
      <div class="metric-value" id="canvasMetricWithCreep">-</div>
    </div>
    <div class="metric-card">
      <div class="metric-title">Depth Discrepancy</div>
      <div class="metric-value" id="canvasMetricReduction">-</div>
    </div>
    <div class="metric-card">
      <div class="metric-title" id="canvasTitleCoeff">Derived Coeff</div>
      <div class="metric-value" id="canvasMetricCoeff">-</div>
    </div>
  </div>

  <div class="charts-grid">
    <div class="chart-wrapper">
      <h4 style="margin-top:0; margin-bottom: 0.5rem; color:#157878; text-align:center;">Estimated Depth Profile ($H$, meters)</h4>
      <canvas id="peatDepthCanvas" class="canvas-element"></canvas>
    </div>
    <div class="chart-wrapper">
      <h4 style="margin-top:0; margin-bottom: 0.5rem; color:#157878; text-align:center;">Cumulative Dry Mass Profile ($M$, kg/m²)</h4>
      <canvas id="peatMassCanvas" class="canvas-element"></canvas>
    </div>
  </div>

  <div class="model-notes">
    💡 <strong>Fitting Experiment:</strong> Increase $A_{\text{Creep}}$ (e.g. to $90\text{ g m}^{-2}\text{yr}^{-1}$) and $u_{\text{final}}$ (e.g. to $2\text{ cm/yr}$). Notice how the Creep core thins. Now try adjusting $A_{\text{Clymo}}$ downwards to match the final 10,000-year depth. Notice that matching final depth forces you to severely underestimate true surface carbon accumulation $A$!
  </div>
</div>

<script>
const CANVAS_MODEL_CONSTANTS = {
  years: 10000,
  steps: 500,
  bulkDensity: 100,
  exportLength: 100
};

function readCanvasInputs() {
  const modelType = document.getElementById('canvasSelectModel').value;
  
  const A_g_clymo = parseFloat(document.getElementById('canvasSliderA_clymo').value);
  const alpha_clymo = parseFloat(document.getElementById('canvasSliderAlpha_clymo').value);
  const A_kg_clymo = A_g_clymo / 1000.0;

  const A_g_creep = parseFloat(document.getElementById('canvasSliderA_creep').value);
  const alpha_creep = parseFloat(document.getElementById('canvasSliderAlpha_creep').value);
  const uRefCmYr = parseFloat(document.getElementById('canvasSliderCreep').value);
  const hActive = parseFloat(document.getElementById('canvasSliderActiveDepth').value);

  const A_kg_creep = A_g_creep / 1000.0;
  const uRefMYr = uRefCmYr / 100.0;

  const containerActive = document.getElementById('canvasContainerActiveDepth');

  if (modelType === 'overburden') {
    containerActive.style.display = 'none';
    document.getElementById('canvasModelHelpText').innerText = 'u ∝ H: speed scales with overburden, slider is speed at final depth';
    document.getElementById('canvasLabelCreep').innerText = 'Final Depth Creep Speed (u_final)';
    document.getElementById('canvasSublabelCreep').innerText = 'Speed observed at final 10k-yr depth';
    document.getElementById('canvasTitleCoeff').innerText = 'Derived β';
  } else if (modelType === 'shear') {
    containerActive.style.display = 'none';
    document.getElementById('canvasModelHelpText').innerText = 'u max at surface, linear shear down to 0 at bedrock (no-slip)';
    document.getElementById('canvasLabelCreep').innerText = 'Surface Speed (u_surf)';
    document.getElementById('canvasSublabelCreep').innerText = 'Speed at surface (0 at bed)';
    document.getElementById('canvasTitleCoeff').innerText = 'Derived γ';
  } else if (modelType === 'activeLayer') {
    containerActive.style.display = 'block';
    document.getElementById('canvasModelHelpText').innerText = 'u max at surface, linear down to 0 at tunable active depth H_active';
    document.getElementById('canvasLabelCreep').innerText = 'Surface Speed (u_surf)';
    document.getElementById('canvasSublabelCreep').innerText = 'Speed at surface (0 at H_active)';
    document.getElementById('canvasTitleCoeff').innerText = 'Derived k_act';
  }

  document.getElementById('canvasValA_clymo').innerText = `${A_g_clymo} g/m²/yr`;
  document.getElementById('canvasValAlpha_clymo').innerText = `${alpha_clymo.toExponential(1)} /yr`;

  document.getElementById('canvasValA_creep').innerText = `${A_g_creep} g/m²/yr`;
  document.getElementById('canvasValAlpha_creep').innerText = `${alpha_creep.toExponential(1)} /yr`;
  document.getElementById('canvasValCreep').innerText = `${uRefCmYr.toFixed(2)} cm/yr`;
  document.getElementById('canvasValActiveDepth').innerText = `${hActive.toFixed(1)} m`;

  return { modelType, A_kg_clymo, alpha_clymo, A_kg_creep, alpha_creep, uRefMYr, hActive };
}

function solveCanvasODE(params) {
  const { modelType, A_kg_clymo, alpha_clymo, A_kg_creep, alpha_creep, uRefMYr, hActive } = params;
  const dt = CANVAS_MODEL_CONSTANTS.years / CANVAS_MODEL_CONSTANTS.steps;
  const ages = [];
  const depthDecayOnly = [];
  const depthWithCreep = [];
  const massDecayOnly = [];
  const massWithCreep = [];
  let M_creep = 0;

  const M_baseline_creep_final = (A_kg_creep / alpha_creep) * (1 - Math.exp(-alpha_creep * CANVAS_MODEL_CONSTANTS.years));
  const gamma = uRefMYr / CANVAS_MODEL_CONSTANTS.exportLength;
  const k_act = uRefMYr / CANVAS_MODEL_CONSTANTS.exportLength;
  const M_act = hActive * CANVAS_MODEL_CONSTANTS.bulkDensity;

  let M_final_est = M_baseline_creep_final * 0.8;
  let beta = uRefMYr / (CANVAS_MODEL_CONSTANTS.exportLength * Math.max(10, M_final_est));

  const maxIter = (modelType === 'overburden') ? 3 : 1;

  for (let iter = 0; iter < maxIter; iter++) {
    if (modelType === 'overburden') {
      beta = uRefMYr / (CANVAS_MODEL_CONSTANTS.exportLength * Math.max(10, M_final_est));
    }

    M_creep = 0;
    massWithCreep.length = 0;
    depthWithCreep.length = 0;
    ages.length = 0;
    massDecayOnly.length = 0;
    depthDecayOnly.length = 0;

    for (let i = 0; i <= CANVAS_MODEL_CONSTANTS.steps; i++) {
      const age = i * dt;
      if (iter === maxIter - 1) ages.push(age);

      const M_baseline = (A_kg_clymo / alpha_clymo) * (1 - Math.exp(-alpha_clymo * age));
      if (iter === maxIter - 1) {
        massDecayOnly.push(M_baseline);
        depthDecayOnly.push(M_baseline / CANVAS_MODEL_CONSTANTS.bulkDensity);
      }

      if (i === 0) {
        massWithCreep.push(0);
        depthWithCreep.push(0);
      } else {
        const f = (m) => {
          if (modelType === 'overburden') {
            return A_kg_creep - alpha_creep * m - beta * m * m;
          } else if (modelType === 'shear') {
            const M_bed = Math.max(m, M_baseline_creep_final * 0.85);
            const factor = Math.max(0, 1 - m / M_bed);
            return A_kg_creep - alpha_creep * m - gamma * m * factor;
          } else if (modelType === 'activeLayer') {
            const factor = Math.max(0, 1 - m / M_act);
            return A_kg_creep - alpha_creep * m - k_act * m * factor;
          }
        };

        const k1 = f(M_creep);
        const k2 = f(M_creep + 0.5 * dt * k1);
        const k3 = f(M_creep + 0.5 * dt * k2);
        const k4 = f(M_creep + dt * k3);
        M_creep += (dt / 6.0) * (k1 + 2 * k2 + 2 * k3 + k4);
        if (M_creep < 0) M_creep = 0;

        massWithCreep.push(M_creep);
        depthWithCreep.push(M_creep / CANVAS_MODEL_CONSTANTS.bulkDensity);
      }
    }
    M_final_est = M_creep;
  }

  let derivedCoeffStr = '';
  if (modelType === 'overburden') derivedCoeffStr = `${beta.toExponential(2)} m²/kg/yr`;
  else if (modelType === 'shear') derivedCoeffStr = `${gamma.toExponential(2)} /yr`;
  else if (modelType === 'activeLayer') derivedCoeffStr = `${k_act.toExponential(2)} /yr`;

  return { ages, depthDecayOnly, depthWithCreep, massDecayOnly, massWithCreep, derivedCoeffStr };
}

function drawSeries(ctx, data, plot, colour, dashed) {
  ctx.save();
  ctx.strokeStyle = colour;
  ctx.lineWidth = 3;
  ctx.setLineDash(dashed ? [8, 6] : []);
  ctx.beginPath();
  data.ages.forEach((age, index) => {
    const x = plot.left + plot.width - (age / CANVAS_MODEL_CONSTANTS.years) * plot.width;
    const y = plot.top + (data.series[index] / plot.maxY) * plot.height;
    if (index === 0) {
      ctx.moveTo(x, y);
    } else {
      ctx.lineTo(x, y);
    }
  });
  ctx.stroke();
  ctx.restore();
}

function drawSingleCanvasPlot(canvasId, yAxisLabel, seriesBaseline, seriesCreep, ages) {
  const canvas = document.getElementById(canvasId);
  const wrapper = canvas.parentElement;
  const ratio = window.devicePixelRatio || 1;
  const cssWidth = Math.max(wrapper.clientWidth - 32, 280);
  const cssHeight = 340;
  canvas.width = cssWidth * ratio;
  canvas.height = cssHeight * ratio;
  canvas.style.width = `${cssWidth}px`;
  canvas.style.height = `${cssHeight}px`;

  const ctx = canvas.getContext('2d');
  ctx.setTransform(ratio, 0, 0, ratio, 0, 0);
  ctx.clearRect(0, 0, cssWidth, cssHeight);

  const maxY = Math.max(...seriesBaseline, ...seriesCreep) * 1.08;
  const plot = {
    left: 65,
    right: 20,
    top: 25,
    bottom: 55
  };
  plot.width = cssWidth - plot.left - plot.right;
  plot.height = cssHeight - plot.top - plot.bottom;
  plot.maxY = Math.max(maxY, 0.1);

  ctx.fillStyle = '#ffffff';
  ctx.fillRect(0, 0, cssWidth, cssHeight);

  ctx.strokeStyle = '#d8dee4';
  ctx.lineWidth = 1;
  ctx.fillStyle = '#586069';
  ctx.font = '11px -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif';
  ctx.textAlign = 'right';
  ctx.textBaseline = 'middle';

  for (let i = 0; i <= 5; i++) {
    const y = plot.top + (i / 5) * plot.height;
    const val = (i / 5) * plot.maxY;
    ctx.beginPath();
    ctx.moveTo(plot.left, y);
    ctx.lineTo(plot.left + plot.width, y);
    ctx.stroke();
    ctx.fillText(val.toFixed(val >= 100 ? 0 : 1), plot.left - 8, y);
  }

  ctx.textAlign = 'center';
  ctx.textBaseline = 'top';
  for (let i = 0; i <= 5; i++) {
    const x = plot.left + (i / 5) * plot.width;
    const age = (1 - i / 5) * CANVAS_MODEL_CONSTANTS.years;
    ctx.beginPath();
    ctx.moveTo(x, plot.top);
    ctx.lineTo(x, plot.top + plot.height);
    ctx.stroke();
    ctx.fillText(Math.round(age).toString(), x, plot.top + plot.height + 8);
  }

  ctx.strokeStyle = '#24292e';
  ctx.lineWidth = 1.5;
  ctx.beginPath();
  ctx.moveTo(plot.left, plot.top);
  ctx.lineTo(plot.left, plot.top + plot.height);
  ctx.lineTo(plot.left + plot.width, plot.top + plot.height);
  ctx.stroke();

  drawSeries(ctx, { ages: ages, series: seriesBaseline }, plot, '#157878', false);
  drawSeries(ctx, { ages: ages, series: seriesCreep }, plot, '#d9534f', true);

  ctx.fillStyle = '#24292e';
  ctx.font = '12px -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif';
  ctx.textAlign = 'center';
  ctx.fillText('Peat age (years)', plot.left + plot.width / 2, cssHeight - 16);

  ctx.save();
  ctx.translate(16, plot.top + plot.height / 2);
  ctx.rotate(-Math.PI / 2);
  ctx.fillText(yAxisLabel, 0, 0);
  ctx.restore();

  ctx.textAlign = 'left';
  ctx.textBaseline = 'middle';
  ctx.fillStyle = '#157878';
  ctx.fillRect(plot.left + 10, plot.top + 10, 20, 3);
  ctx.fillStyle = '#24292e';
  ctx.fillText('Clymo model', plot.left + 35, plot.top + 11);
  ctx.strokeStyle = '#d9534f';
  ctx.setLineDash([8, 6]);
  ctx.beginPath();
  ctx.moveTo(plot.left + 115, plot.top + 11);
  ctx.lineTo(plot.left + 135, plot.top + 11);
  ctx.stroke();
  ctx.setLineDash([]);
  ctx.fillText('Creep model', plot.left + 142, plot.top + 11);
}

function updateCanvasPlot() {
  const params = readCanvasInputs();
  const data = solveCanvasODE(params);
  const finalBaselineDepth = data.depthDecayOnly[data.depthDecayOnly.length - 1];
  const finalCreepDepth = data.depthWithCreep[data.depthWithCreep.length - 1];
  const finalBaselineMass = data.massDecayOnly[data.massDecayOnly.length - 1];
  const finalCreepMass = data.massWithCreep[data.massWithCreep.length - 1];
  const diffPct = ((finalBaselineDepth - finalCreepDepth) / finalBaselineDepth) * 100;

  document.getElementById('canvasMetricDecayOnly').innerText = `${finalBaselineDepth.toFixed(2)} m (${finalBaselineMass.toFixed(0)} kg/m²)`;
  document.getElementById('canvasMetricWithCreep').innerText = `${finalCreepDepth.toFixed(2)} m (${finalCreepMass.toFixed(0)} kg/m²)`;
  document.getElementById('canvasMetricReduction').innerText = `${diffPct >= 0 ? '-' : '+'}${Math.abs(diffPct).toFixed(1)}%`;
  document.getElementById('canvasMetricCoeff').innerText = data.derivedCoeffStr;

  drawSingleCanvasPlot('peatDepthCanvas', 'Estimated depth (m)', data.depthDecayOnly, data.depthWithCreep, data.ages);
  drawSingleCanvasPlot('peatMassCanvas', 'Cumulative mass (kg/m²)', data.massDecayOnly, data.massWithCreep, data.ages);
}

document.addEventListener('DOMContentLoaded', () => {
  document.getElementById('canvasSelectModel').addEventListener('change', updateCanvasPlot);
  ['canvasSliderA_clymo', 'canvasSliderAlpha_clymo', 'canvasSliderA_creep', 'canvasSliderAlpha_creep', 'canvasSliderCreep', 'canvasSliderActiveDepth'].forEach(id => {
    document.getElementById(id).addEventListener('input', updateCanvasPlot);
  });
  window.addEventListener('resize', updateCanvasPlot);
  updateCanvasPlot();
});
</script>
{% endraw %}

---

## Mathematical, Physical & Peatland Science Foundations

### 1. Peatland Science & Eulerian vs. Lagrangian Views
Standard 1D peat accumulation models (such as *Clymo 1984*) split the peat column into an upper oxic layer (acrotelm) and a lower water-saturated anoxic layer (catotelm). Organic matter enters the catotelm at surface accumulation flux $A$ ($\text{g m}^{-2}\text{yr}^{-1}$) and undergoes slow anaerobic decay at a first-order rate $\alpha$ ($\text{yr}^{-1}$).

Under steady-state conditions over 10,000 years:
- **Lagrangian View (Cohort Age $a$):** Tracks a peat layer deposited $a$ years ago.
- **Eulerian View (Calendar Time $t$):** Tracks total column growth over $t$ years.
Because environmental inputs are steady, $M(a) \equiv M(t)$. All curves start at present day ($a=0$) with slope $\left.\frac{dM}{da}\right|_{a=0} = A$ because fresh surface peat has not yet accumulated depth or decayed.

### 2. Mathematical Derivation of the 3 Creep Mechanisms

In a 2D/3D continuum, mass conservation is $\frac{\partial M}{\partial t} + \nabla \cdot \left(\mathbf{u} M\right) = A - \alpha M$. Over representative column export length $L = 100\text{ m}$, the 3 creep velocity profiles correspond to:

#### **Model 1: Constant Deep Creep ($u \propto H$, speed at final depth)**
- Creep speed scales with depth $H$: $u(H) = u_{\text{final}} \frac{M}{M_{\text{final}}}$, where $u_{\text{final}}$ is the user-selected creep speed observed at the final 10,000-year core depth.
- Export flux is quadratic in mass: $\beta M^2$, where $\beta = \frac{u_{\text{final}}}{L M_{\text{final}}}$.
- **ODE:** $\frac{dM(a)}{da} = A - \alpha M(a) - \beta M(a)^2$. Creep loss accelerates at depth.

#### **Model 2: Linear Shear Profile (Gravity-Driven Shear)**
- Velocity is maximum at surface ($u_{\text{surf}}$) and decreases linearly to $0$ at bedrock ($M_{\text{tot}}$): $u(M) = u_{\text{surf}} \left(1 - \frac{M}{M_{\text{tot}}}\right)$.
- **ODE:** $\frac{dM(a)}{da} = A - \alpha M(a) - \gamma M(a) \left(1 - \frac{M(a)}{M_{\text{tot}}}\right)$, where $\gamma = \frac{u_{\text{surf}}}{L}$.
- Deep peat near bedrock stops creeping, resuming pure decay kinetics!

#### **Model 3: Surface-Driven Processes (e.g. Freeze-Thaw / Active Layer)**
- Creep velocity is maximum at the surface ($u_{\text{surf}}$) and decreases linearly to $0$ at a tunable active depth $H_{\text{active}}$ ($M_{\text{active}} = H_{\text{active}} \rho_b$).
- **ODE:** For $M \le M_{\text{active}}$, $\frac{dM(a)}{da} = A - \alpha M(a) - k_{\text{active}} M(a) \left(1 - \frac{M(a)}{M_{\text{active}}}\right)$, where $k_{\text{active}} = \frac{u_{\text{surf}}}{L}$. Below $H_{\text{active}}$, creep velocity is $0$.
- Peat buried deeper than $H_{\text{active}}$ stops creeping.

---

### 3. The Equifinality & Inverse Problem Challenge

When analyzing an observed radiocarbon-dated core from a field site, scientists face an **inverse problem** (fitting $A$ and $\alpha$ from measured age-depth points).

If lateral creep is active in the bog but omitted from the inversion model:
- The observer forces a decay-only Clymo curve to pass through the observed 10,000-year depth.
- This forces the estimated accumulation parameter $A_{\text{Clymo}}$ to be significantly **lower** than the true input rate $A_{\text{Creep}}$ (or forces estimated decay $\alpha_{\text{Clymo}}$ to be artificially elevated).
- Consequently, standard decay-only core interpretations can lead to substantial **underestimation of historical carbon sequestration rates** in creeping peatlands.
