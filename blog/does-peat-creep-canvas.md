---
layout: default
title: "Does peat creep? Canvas version"
description: "Canvas version of the peat age-depth model featuring alternative creep profiles and depth-scaling laws."
---

# Does Peat Creep? Canvas Version

Peatlands are dynamic, deformable porous media storing over 30% of global soil carbon. Researchers in the **MATHPEAT Network** are currently investigating the evidence for peat creep, how it should be represented mathematically, and what its consequences may be for peatland age-depth structure and long-term carbon storage.

The model below is deliberately simple. It is not a full 3D landscape-scale creep model: creep mostly redistributes peat locally. Here we ask what a single representative column would look like under different vertical creep profiles and depth-scaling assumptions if unresolved downslope or marginal creep eventually exports material from that column.

This comparison version uses the same equations and controls as the main interactive post, but the plots are drawn directly with the browser's HTML `<canvas>` element rather than Chart.js.

---

## Simple Column Model & Creep Formulations

Let $M(a)$ be cumulative peat dry mass per unit area at cohort age $a$, measured in $\mathrm{kg\,m^{-2}}$. The estimated peat depth is

$$H(a) = \frac{M(a)}{\rho_b},$$

where fixed dry bulk density is $\rho_b = 100\,\mathrm{kg\,m^{-3}}$.

Without creep, the classic accumulation-decay balance (*Clymo 1984*) is

$$\frac{dM}{da} = A - \alpha M, \qquad M(0)=0,$$

where $A$ is the accumulation flux (default $60\,\mathrm{g\,m^{-2}\,yr^{-1}}$) and $\alpha$ is the first-order decay rate (default $10^{-4}\,\mathrm{yr^{-1}}$).

### Two Independent Creep Choices
The creep model separates two assumptions that are often mixed together:

1. **Vertical velocity profile:** either a uniform plug-like profile, where all represented peat creeps at the same effective speed, or a linear shear-zone profile, where speed is maximum at the surface and decreases to zero at a tunable depth $H_{\text{shear}}$.
2. **Depth scaling of measured speed:** the user sets the present-day surface creep speed measured at the final simulated depth. The model then reconstructs what that speed would have been when the peat column was shallower using one of four simple hypotheses: constant, proportional to $H$, proportional to $H^2$, or proportional to $1/H$.

---

## The Reverse-Engineering Challenge (Equifinality)

In field peatland science, scientists extract a physical peat core, measure its depth $H$, and perform Radiocarbon ($^{14}\text{C}$) dating to get age $a$ at depth $H$. 

**The Challenge:** A given observed age-depth profile could be fitted by TWO completely different mechanisms:
1. **Classic Clymo Model (Decay Only):** Fitted with a lower surface accumulation rate $A_{\text{Clymo}}$ or higher decay rate $\alpha_{\text{Clymo}}$.
2. **Peat Creep Model (Decay + Export):** Driven by a higher true surface accumulation rate $A_{\text{Creep}}$, standard decay $\alpha_{\text{Creep}}$, and active lateral creep $u_{\text{meas}}$.

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
        <label for="canvasSelectProfile">Vertical Creep Profile</label>
        <small id="canvasProfileHelpText">Choose where in the peat column lateral motion is active</small>
        <select id="canvasSelectProfile">
          <option value="uniform" selected>Uniform / plug-like profile</option>
          <option value="shear">Linear shear-zone profile</option>
        </select>
      </div>

      <div class="control-field">
        <label for="canvasSelectScaling">Depth Scaling of Measured Speed</label>
        <small id="canvasScalingHelpText">How today's measured surface speed is reconstructed for shallower past peat</small>
        <select id="canvasSelectScaling">
          <option value="constant" selected>Constant with depth: u(H) = u_meas</option>
          <option value="linear">Overburden-scaled: u(H) ∝ H</option>
          <option value="quadratic">Viscous-film scaling: u(H) ∝ H²</option>
          <option value="inverse">Resistance-limited: u(H) ∝ 1/H</option>
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
        <label for="canvasSliderCreep" id="canvasLabelCreep">Measured Surface Creep Speed ($u_\mathrm{meas}$)</label>
        <small id="canvasSublabelCreep">Present-day surface speed at final simulated depth</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderCreep" min="0" max="10" step="0.1" value="0.5">
          <span class="value-badge" id="canvasValCreep">0.50 cm/yr</span>
        </div>
      </div>

      <div class="control-field" id="canvasContainerShearDepth" style="display: none; background: #fff8f8; padding: 0.6rem; border-radius: 4px; border: 1px solid #f5c6cb;">
        <label for="canvasSliderShearDepth">Shear-Zone Depth ($H_{\text{shear}}$)</label>
        <small>Depth where the linear creep profile reaches zero</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderShearDepth" min="0.2" max="5.0" step="0.1" value="1.0">
          <span class="value-badge" id="canvasValShearDepth">1.0 m</span>
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
    💡 <strong>Fitting Experiment:</strong> Increase $A_{\text{Creep}}$ (e.g. to $90\text{ g m}^{-2}\text{yr}^{-1}$) and $u_{\text{meas}}$ (e.g. to $2\text{ cm/yr}$). Notice how the Creep core thins. Now try adjusting $A_{\text{Clymo}}$ downwards to match the final 10,000-year depth. Notice that matching final depth can force a decay-only interpretation to underestimate true surface carbon accumulation $A$.
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
  const profileType = document.getElementById('canvasSelectProfile').value;
  const scalingType = document.getElementById('canvasSelectScaling').value;

  const A_g_clymo = parseFloat(document.getElementById('canvasSliderA_clymo').value);
  const alpha_clymo = parseFloat(document.getElementById('canvasSliderAlpha_clymo').value);
  const A_kg_clymo = A_g_clymo / 1000.0;

  const A_g_creep = parseFloat(document.getElementById('canvasSliderA_creep').value);
  const alpha_creep = parseFloat(document.getElementById('canvasSliderAlpha_creep').value);
  const uRefCmYr = parseFloat(document.getElementById('canvasSliderCreep').value);
  const shearSlider = document.getElementById('canvasSliderShearDepth');

  const A_kg_creep = A_g_creep / 1000.0;
  const uRefMYr = uRefCmYr / 100.0;
  const M_noCreepFinal = (A_kg_creep / alpha_creep) * (1 - Math.exp(-alpha_creep * CANVAS_MODEL_CONSTANTS.years));
  const maxShearDepth = Math.max(0.2, M_noCreepFinal / CANVAS_MODEL_CONSTANTS.bulkDensity);
  shearSlider.max = maxShearDepth.toFixed(1);
  if (parseFloat(shearSlider.value) > maxShearDepth) {
    shearSlider.value = maxShearDepth.toFixed(1);
  }
  const hShear = parseFloat(shearSlider.value);

  const containerShear = document.getElementById('canvasContainerShearDepth');
  containerShear.style.display = profileType === 'shear' ? 'block' : 'none';

  document.getElementById('canvasProfileHelpText').innerText =
    profileType === 'uniform'
      ? 'All represented peat moves with the same effective lateral speed'
      : 'Surface speed decreases linearly to zero at the chosen shear-zone depth';

  const scalingHelp = {
    constant: 'Same measured speed throughout the growth history',
    linear: 'Driving stress grows with peat thickness or overburden',
    quadratic: 'Newtonian/lubrication-style viscous film scaling',
    inverse: 'Fixed shallow driving layer resisted by a thicker peat column'
  };
  document.getElementById('canvasScalingHelpText').innerText = scalingHelp[scalingType];
  document.getElementById('canvasLabelCreep').innerText = 'Measured Surface Creep Speed (u_meas)';
  document.getElementById('canvasSublabelCreep').innerText = 'Present-day surface speed at final simulated depth';
  document.getElementById('canvasTitleCoeff').innerText = 'Creep Setup';

  document.getElementById('canvasValA_clymo').innerText = `${A_g_clymo} g/m²/yr`;
  document.getElementById('canvasValAlpha_clymo').innerText = `${alpha_clymo.toExponential(1)} /yr`;

  document.getElementById('canvasValA_creep').innerText = `${A_g_creep} g/m²/yr`;
  document.getElementById('canvasValAlpha_creep').innerText = `${alpha_creep.toExponential(1)} /yr`;
  document.getElementById('canvasValCreep').innerText = `${uRefCmYr.toFixed(2)} cm/yr`;
  document.getElementById('canvasValShearDepth').innerText = `${hShear.toFixed(1)} m`;

  return { profileType, scalingType, A_kg_clymo, alpha_clymo, A_kg_creep, alpha_creep, uRefMYr, hShear };
}

function solveCanvasODE(params) {
  const { profileType, scalingType, A_kg_clymo, alpha_clymo, A_kg_creep, alpha_creep, uRefMYr, hShear } = params;
  const dt = CANVAS_MODEL_CONSTANTS.years / CANVAS_MODEL_CONSTANTS.steps;
  const ages = [];
  const depthDecayOnly = [];
  const depthWithCreep = [];
  const massDecayOnly = [];
  const massWithCreep = [];
  let M_creep = 0;

  const M_baseline_creep_final = (A_kg_creep / alpha_creep) * (1 - Math.exp(-alpha_creep * CANVAS_MODEL_CONSTANTS.years));
  let H_final_est = Math.max(0.1, M_baseline_creep_final / CANVAS_MODEL_CONSTANTS.bulkDensity);
  const maxIter = 5;
  const H_min = 0.1;

  for (let iter = 0; iter < maxIter; iter++) {
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
          const H = Math.max(0, m / CANVAS_MODEL_CONSTANTS.bulkDensity);
          const H_ref = Math.max(H_min, H_final_est);
          let surfaceSpeed = uRefMYr;
          if (scalingType === 'linear') {
            surfaceSpeed = uRefMYr * H / H_ref;
          } else if (scalingType === 'quadratic') {
            surfaceSpeed = uRefMYr * Math.pow(H / H_ref, 2);
          } else if (scalingType === 'inverse') {
            surfaceSpeed = uRefMYr * H_ref / Math.max(H, H_min);
          }
          const profileFactor = profileType === 'shear'
            ? Math.max(0, 1 - H / Math.max(H_min, hShear))
            : 1;
          const exportRate = (surfaceSpeed / CANVAS_MODEL_CONSTANTS.exportLength) * profileFactor;
          return A_kg_creep - alpha_creep * m - exportRate * m;
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
    H_final_est = Math.max(H_min, M_creep / CANVAS_MODEL_CONSTANTS.bulkDensity);
  }

  const profileLabel = profileType === 'uniform' ? 'uniform' : `linear to ${hShear.toFixed(1)} m`;
  const scalingLabels = {
    constant: 'constant u',
    linear: 'u ∝ H',
    quadratic: 'u ∝ H²',
    inverse: 'u ∝ 1/H'
  };
  const derivedCoeffStr = `${profileLabel}; ${scalingLabels[scalingType]}`;

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
  document.getElementById('canvasSelectProfile').addEventListener('change', updateCanvasPlot);
  document.getElementById('canvasSelectScaling').addEventListener('change', updateCanvasPlot);
  ['canvasSliderA_clymo', 'canvasSliderAlpha_clymo', 'canvasSliderA_creep', 'canvasSliderAlpha_creep', 'canvasSliderCreep', 'canvasSliderShearDepth'].forEach(id => {
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

### 2. Mathematical Derivation of the Creep Closure

In a 2D/3D continuum, mass conservation is $\frac{\partial M}{\partial t} + \nabla \cdot \left(\mathbf{u} M\right) = A - \alpha M$. In this one-column demo, unresolved lateral divergence is approximated by an export term

$$\frac{dM}{da} = A - \alpha M - \frac{u_{\text{eff}}(H)}{L}M,$$

where $L=100\text{ m}$ is a representative export length. The user-controlled speed $u_{\text{meas}}$ is interpreted as the present-day surface creep speed measured when the simulated core has its final depth $H_{\text{final}}$.

#### **Vertical Profile Choices**
- **Uniform / plug-like profile:** the represented peat column has one effective lateral speed. This approximates bulk translation, block-like motion, or a depth-averaged export closure where the lower boundary does not impose a strong no-slip shear profile.
- **Linear shear-zone profile:** speed is maximum at the surface and decreases linearly to zero at $H_{\text{shear}}$. This represents a no-slip or pinned lower boundary at the base of the mobile layer. If $H_{\text{shear}}$ is close to the full core depth, it approximates full-column gravity shear. If $H_{\text{shear}}$ is shallow, it represents surface-driven processes such as freeze-thaw, water-table movement, or seasonal swelling and shrinking.

#### **Depth-Scaling Choices for the Measured Surface Speed**
The depth-scaling selector asks how the present-day measured speed should be extrapolated backward when the peat column was thinner:

- **Constant with depth, $u(H)=u_{\text{meas}}$:** effective driving and resistance scale together, or the measured motion is controlled mainly by boundary conditions and slope rather than total peat thickness.
- **Overburden-scaled, $u(H)\propto H$:** driving stress grows with peat thickness or buoyant overburden. This is a simple gravity-loading closure when resistance is approximately linear.
- **Viscous-film scaling, $u(H)\propto H^2$:** a Newtonian/lubrication-style idealisation with no slip at the base and stress accumulating through the mobile layer. It gives much slower creep when the bog was shallow and faster creep as it thickens.
- **Resistance-limited, $u(H)\propto 1/H$:** a shallow driving layer, such as an unsaturated or seasonally active layer, drags a thicker passive body. The thicker the peat column, the larger the viscous resistance, so the same shallow forcing produces slower motion.

These are not competing claims about the true law; they are deliberately simple closures for exploring how different physical hypotheses alter the inferred age-depth profile.

---

### 3. The Equifinality & Inverse Problem Challenge

When analyzing an observed radiocarbon-dated core from a field site, scientists face an **inverse problem** (fitting $A$ and $\alpha$ from measured age-depth points).

If lateral creep is active in the bog but omitted from the inversion model:
- The observer forces a decay-only Clymo curve to pass through the observed 10,000-year depth.
- This forces the estimated accumulation parameter $A_{\text{Clymo}}$ to be significantly **lower** than the true input rate $A_{\text{Creep}}$ (or forces estimated decay $\alpha_{\text{Clymo}}$ to be artificially elevated).
- Consequently, standard decay-only core interpretations can lead to substantial **underestimation of historical carbon sequestration rates** in creeping peatlands.
