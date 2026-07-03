---
layout: default
title: "Does peat creep? Canvas version"
description: "Canvas version of the peat age-depth model featuring separate parameters for Clymo and Creep models."
---

# Does Peat Creep? Canvas Version

Peatlands are dynamic, deformable porous media storing over 30% of global soil carbon. Researchers in the **MATHPEAT Network** are currently investigating the evidence for peat creep, how it should be represented mathematically, and what its consequences may be for peatland age-depth structure and long-term carbon storage.

The model below is deliberately simple. It is not a full 3D landscape-scale creep model: creep mostly redistributes peat locally. Here we ask what a single representative column would look like under different vertical creep velocity profiles if unresolved downslope or marginal creep eventually exports material from that column.

This comparison version uses the same equations and controls as the main interactive post, but the plots are drawn directly with the browser's HTML `<canvas>` element rather than Chart.js.

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
          <option value="overburden" selected>1. Overburden Creep (u ∝ H, max at depth)</option>
          <option value="shear">2. Surface Shear (max at surface, 0 at bed)</option>
          <option value="activeLayer">3. Active Upper Layer (max in top 1.5m, 0 deep)</option>
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
        <label for="canvasSliderCreep" id="canvasLabelCreep">Creep Speed ($u_\mathrm{ref}$)</label>
        <small id="canvasSublabelCreep">Speed at $H_\mathrm{ref}=3\,\mathrm{m}$</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderCreep" min="0" max="10" step="0.1" value="0.5">
          <span class="value-badge" id="canvasValCreep">0.50 cm/yr</span>
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
    💡 <strong>Fitting Experiment:</strong> Increase $A_{\text{Creep}}$ (e.g. to $90\text{ g m}^{-2}\text{yr}^{-1}$) and $u_{\text{ref}}$ (e.g. to $2\text{ cm/yr}$). Notice how the Creep core thins. Now try adjusting $A_{\text{Clymo}}$ downwards to match the final 10,000-year depth. Notice that matching final depth forces you to severely underestimate true surface carbon accumulation $A$!
  </div>
</div>

<script>
const CANVAS_MODEL_CONSTANTS = {
  years: 10000,
  steps: 500,
  bulkDensity: 100,
  referenceDepth: 3,
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
  const A_kg_creep = A_g_creep / 1000.0;
  const uRefMYr = uRefCmYr / 100.0;

  if (modelType === 'overburden') {
    document.getElementById('canvasModelHelpText').innerText = 'u ∝ H: creep speeds up at depth under overburden';
    document.getElementById('canvasLabelCreep').innerText = 'Ref Creep Speed (u_ref)';
    document.getElementById('canvasSublabelCreep').innerText = 'Speed at H_ref = 3 m';
    document.getElementById('canvasTitleCoeff').innerText = 'Derived β';
  } else if (modelType === 'shear') {
    document.getElementById('canvasModelHelpText').innerText = 'u max at surface, 0 at bedrock (no-slip)';
    document.getElementById('canvasLabelCreep').innerText = 'Surface Speed (u_surf)';
    document.getElementById('canvasSublabelCreep').innerText = 'Speed at surface (0 at bed)';
    document.getElementById('canvasTitleCoeff').innerText = 'Derived γ';
  } else if (modelType === 'activeLayer') {
    document.getElementById('canvasModelHelpText').innerText = 'u constant in top 1.5m, 0 in deep peat';
    document.getElementById('canvasLabelCreep').innerText = 'Active Speed (u_active)';
    document.getElementById('canvasSublabelCreep').innerText = 'Speed in top 1.5 m active layer';
    document.getElementById('canvasTitleCoeff').innerText = 'Derived k_act';
  }

  document.getElementById('canvasValA_clymo').innerText = `${A_g_clymo} g/m²/yr`;
  document.getElementById('canvasValAlpha_clymo').innerText = `${alpha_clymo.toExponential(1)} /yr`;

  document.getElementById('canvasValA_creep').innerText = `${A_g_creep} g/m²/yr`;
  document.getElementById('canvasValAlpha_creep').innerText = `${alpha_creep.toExponential(1)} /yr`;
  document.getElementById('canvasValCreep').innerText = `${uRefCmYr.toFixed(2)} cm/yr`;

  return { modelType, A_kg_clymo, alpha_clymo, A_kg_creep, alpha_creep, uRefMYr };
}

function solveCanvasODE(params) {
  const { modelType, A_kg_clymo, alpha_clymo, A_kg_creep, alpha_creep, uRefMYr } = params;
  const dt = CANVAS_MODEL_CONSTANTS.years / CANVAS_MODEL_CONSTANTS.steps;
  const ages = [];
  const depthDecayOnly = [];
  const depthWithCreep = [];
  const massDecayOnly = [];
  const massWithCreep = [];
  let M_creep = 0;

  const M_baseline_creep_final = (A_kg_creep / alpha_creep) * (1 - Math.exp(-alpha_creep * CANVAS_MODEL_CONSTANTS.years));
  const beta = uRefMYr / (CANVAS_MODEL_CONSTANTS.exportLength * CANVAS_MODEL_CONSTANTS.referenceDepth * CANVAS_MODEL_CONSTANTS.bulkDensity);
  const gamma = uRefMYr / CANVAS_MODEL_CONSTANTS.exportLength;
  const k_act = uRefMYr / CANVAS_MODEL_CONSTANTS.exportLength;
  const M_act = 1.5 * CANVAS_MODEL_CONSTANTS.bulkDensity;

  for (let i = 0; i <= CANVAS_MODEL_CONSTANTS.steps; i++) {
    const age = i * dt;
    ages.push(age);

    const M_baseline = (A_kg_clymo / alpha_clymo) * (1 - Math.exp(-alpha_clymo * age));
    massDecayOnly.push(M_baseline);
    depthDecayOnly.push(M_baseline / CANVAS_MODEL_CONSTANTS.bulkDensity);

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
          return A_kg_creep - alpha_creep * m - k_act * Math.min(m, M_act);
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
  ['canvasSliderA_clymo', 'canvasSliderAlpha_clymo', 'canvasSliderA_creep', 'canvasSliderAlpha_creep', 'canvasSliderCreep'].forEach(id => {
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

#### **Model 1: Overburden Creep ($u \propto H$)**
- Velocity increases with total depth $H$: $u(H) = u_{\text{ref}} \frac{H}{H_{\text{ref}}}$.
- Export flux is quadratic in mass: $\beta M^2$, where $\beta = \frac{u_{\text{ref}}}{L H_{\text{ref}} \rho_b}$.
- **ODE:** $\frac{dM(a)}{da} = A - \alpha M(a) - \beta M(a)^2$. Creep loss accelerates at depth.

#### **Model 2: Surface-Weighted Shear ($u_{\text{surf}}$ at top, $0$ at bed)**
- Velocity is maximum at surface ($u_{\text{surf}}$) and decreases linearly to $0$ at bedrock ($M_{\text{tot}}$): $u(M) = u_{\text{surf}} \left(1 - \frac{M}{M_{\text{tot}}}\right)$.
- **ODE:** $\frac{dM(a)}{da} = A - \alpha M(a) - \gamma M(a) \left(1 - \frac{M(a)}{M_{\text{tot}}}\right)$, where $\gamma = \frac{u_{\text{surf}}}{L}$.
- Deep peat near bedrock stops creeping, resuming pure decay kinetics!

#### **Model 3: Active Upper Layer Creep ($u_0$ in top $1.5\,\mathrm{m}$, $0$ deep)**
- Creep occurs at constant speed $u_0$ only in the upper active layer ($H \le 1.5\,\mathrm{m}$, $M \le M_{\text{active}} = 150\text{ kg m}^{-2}$).
- **ODE:** $\frac{dM(a)}{da} = A - \alpha M(a) - k_{\text{active}} \min(M, M_{\text{active}})$, where $k_{\text{active}} = \frac{u_0}{L}$.
- Peat buried below $1.5\,\mathrm{m}$ stops creeping, preserving its profile shape.

---

### 3. The Equifinality & Inverse Problem Challenge

When analyzing an observed radiocarbon-dated core from a field site, scientists face an **inverse problem** (fitting $A$ and $\alpha$ from measured age-depth points).

If lateral creep is active in the bog but omitted from the inversion model:
- The observer forces a decay-only Clymo curve to pass through the observed 10,000-year depth.
- This forces the estimated accumulation parameter $A_{\text{Clymo}}$ to be significantly **lower** than the true input rate $A_{\text{Creep}}$ (or forces estimated decay $\alpha_{\text{Clymo}}$ to be artificially elevated).
- Consequently, standard decay-only core interpretations can lead to substantial **underestimation of historical carbon sequestration rates** in creeping peatlands.
