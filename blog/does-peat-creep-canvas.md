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

1. **Vertical velocity profile:** both profile choices restrict lateral creep to an active upper layer of thickness $H_{\text{active}}$ ($H_{\text{plug}}$ or $H_{\text{shear}}$):
   - **Uniform / plug-like profile:** peat in the active layer moves downslope at a uniform velocity. Below this depth, the peat is stable (no creep).
   - **Linear shear-zone profile:** speed is maximum at the surface and decreases linearly to zero at depth $H_{\text{shear}}$. Below this depth, the peat is stable.
2. **Depth scaling of measured speed:** the user sets the present-day surface creep speed measured at the final simulated depth. The model then reconstructs what that speed would have been when the peat column was shallower using one of four simple hypotheses: constant, proportional to $H$, proportional to $H^2$, or proportional to $1/H$.

<div class="chart-wrapper" style="margin-top: 1.5rem; padding: 1.5rem; text-align: center; border: 1px solid #d1d5da; border-radius: 6px;">
  <h4 style="margin-top:0; margin-bottom: 1rem; color:#157878; text-align:center; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;">Creep Velocity Profiles: Plug vs. Shear Flow</h4>
  <svg viewBox="0 0 600 220" width="100%" height="auto" style="max-width: 600px; display: block; margin: 0 auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;">
    <defs>
      <!-- Gradients -->
      <linearGradient id="activePeat" x1="0" y1="0" x2="0" y2="1">
        <stop offset="0%" stop-color="#fdf2f2" />
        <stop offset="100%" stop-color="#f5d6d6" />
      </linearGradient>
      <linearGradient id="stablePeat" x1="0" y1="0" x2="0" y2="1">
        <stop offset="0%" stop-color="#f6f8fa" />
        <stop offset="100%" stop-color="#e1e4e8" />
      </linearGradient>
      <!-- Arrowhead -->
      <marker id="arrow" viewBox="0 0 10 10" refX="6" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
        <path d="M 0 1.5 L 8 5 L 0 8.5 z" fill="#d9534f" />
      </marker>
    </defs>

    <!-- PLUG FLOW (Left) -->
    <g transform="translate(10, 10)">
      <!-- Title -->
      <text x="120" y="20" text-anchor="middle" font-weight="bold" font-size="13" fill="#24292e">Uniform Plug Flow</text>
      
      <!-- Peat Column Box -->
      <!-- Active Layer -->
      <rect x="70" y="40" width="100" height="70" fill="url(#activePeat)" stroke="#e86c6c" stroke-width="1.5" rx="4" />
      <!-- Stable Layer -->
      <rect x="70" y="110" width="100" height="70" fill="url(#stablePeat)" stroke="#b0b7be" stroke-width="1.5" rx="4" />
      
      <!-- Ground Line -->
      <line x1="40" y1="180" x2="200" y2="180" stroke="#586069" stroke-width="2" stroke-dasharray="4 3" />
      
      <!-- Velocity Profile Vectors -->
      <!-- Profile Outline -->
      <path d="M 70 40 L 130 40 L 130 110 L 70 110" fill="none" stroke="#d9534f" stroke-dasharray="3 3" stroke-width="1.5" />
      
      <!-- Arrows -->
      <line x1="70" y1="55" x2="124" y2="55" stroke="#d9534f" stroke-width="2" marker-end="url(#arrow)" />
      <line x1="70" y1="75" x2="124" y2="75" stroke="#d9534f" stroke-width="2" marker-end="url(#arrow)" />
      <line x1="70" y1="95" x2="124" y2="95" stroke="#d9534f" stroke-width="2" marker-end="url(#arrow)" />
      <!-- Zero speed symbols -->
      <circle cx="70" cy="130" r="2.5" fill="#586069" />
      <circle cx="70" cy="155" r="2.5" fill="#586069" />
      
      <!-- Labels -->
      <text x="175" y="78" font-size="11" fill="#d9534f" font-weight="600">Active Layer</text>
      <text x="175" y="93" font-size="10" fill="#6a737d">(Creeping)</text>
      <text x="175" y="145" font-size="11" fill="#586069" font-weight="600">Stable Layer</text>
      <text x="175" y="160" font-size="10" fill="#6a737d">(Stationary)</text>
      
      <!-- Dimension Lines -->
      <line x1="50" y1="40" x2="50" y2="110" stroke="#24292e" stroke-width="1" />
      <line x1="46" y1="40" x2="54" y2="40" stroke="#24292e" stroke-width="1" />
      <line x1="46" y1="110" x2="54" y2="110" stroke="#24292e" stroke-width="1" />
      <text x="35" y="80" text-anchor="middle" font-size="11" fill="#24292e" transform="rotate(-90 35 80)">H_active</text>

      <text x="100" y="34" text-anchor="middle" font-size="10" fill="#d9534f" font-weight="bold">u = u_surf</text>
    </g>

    <!-- SHEAR FLOW (Right) -->
    <g transform="translate(310, 10)">
      <!-- Title -->
      <text x="120" y="20" text-anchor="middle" font-weight="bold" font-size="13" fill="#24292e">Linear Shear Flow</text>
      
      <!-- Peat Column Box -->
      <!-- Active Layer -->
      <rect x="70" y="40" width="100" height="70" fill="url(#activePeat)" stroke="#e86c6c" stroke-width="1.5" rx="4" />
      <!-- Stable Layer -->
      <rect x="70" y="110" width="100" height="70" fill="url(#stablePeat)" stroke="#b0b7be" stroke-width="1.5" rx="4" />
      
      <!-- Ground Line -->
      <line x1="40" y1="180" x2="200" y2="180" stroke="#586069" stroke-width="2" stroke-dasharray="4 3" />
      
      <!-- Velocity Profile Vectors -->
      <!-- Profile Outline -->
      <path d="M 70 40 L 130 40 L 70 110" fill="none" stroke="#d9534f" stroke-dasharray="3 3" stroke-width="1.5" />
      
      <!-- Arrows -->
      <line x1="70" y1="55" x2="112" y2="55" stroke="#d9534f" stroke-width="2" marker-end="url(#arrow)" />
      <line x1="70" y1="75" x2="95" y2="75" stroke="#d9534f" stroke-width="2" marker-end="url(#arrow)" />
      <line x1="70" y1="95" x2="78" y2="95" stroke="#d9534f" stroke-width="2" marker-end="url(#arrow)" />
      <!-- Zero speed symbols -->
      <circle cx="70" cy="130" r="2.5" fill="#586069" />
      <circle cx="70" cy="155" r="2.5" fill="#586069" />
      
      <!-- Labels -->
      <text x="175" y="78" font-size="11" fill="#d9534f" font-weight="600">Active Layer</text>
      <text x="175" y="93" font-size="10" fill="#6a737d">(Creeping)</text>
      <text x="175" y="145" font-size="11" fill="#586069" font-weight="600">Stable Layer</text>
      <text x="175" y="160" font-size="10" fill="#6a737d">(Stationary)</text>
      
      <!-- Dimension Lines -->
      <line x1="50" y1="40" x2="50" y2="110" stroke="#24292e" stroke-width="1" />
      <line x1="46" y1="40" x2="54" y2="40" stroke="#24292e" stroke-width="1" />
      <line x1="46" y1="110" x2="54" y2="110" stroke="#24292e" stroke-width="1" />
      <text x="35" y="80" text-anchor="middle" font-size="11" fill="#24292e" transform="rotate(-90 35 80)">H_active</text>

      <text x="100" y="34" text-anchor="middle" font-size="10" fill="#d9534f" font-weight="bold">u decreases to 0</text>
    </g>
  </svg>
</div>

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
    <!-- Clymo / Yu Model Panel -->
    <div class="param-group param-group-clymo">
      <div class="param-group-header">
        <span class="param-group-title clymo">Clymo / Yu Model (Decay Only)</span>
        <span class="param-badge param-badge-clymo">Teal Line</span>
      </div>

      <div class="control-field">
        <label for="canvasSliderA_clymo">Initial Accumulation ($A_0$)</label>
        <small>Present-day surface peat input flux</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderA_clymo" min="20" max="150" step="5" value="60">
          <span class="value-badge" id="canvasValA_clymo">60 g/m²/yr</span>
        </div>
      </div>

      <div class="control-field">
        <label for="canvasSliderAlpha_clymo">Decay Rate ($\alpha$)</label>
        <small>First-order catotelm decay rate</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderAlpha_clymo" min="0.00001" max="0.0005" step="0.00001" value="0.0001">
          <span class="value-badge" id="canvasValAlpha_clymo">1.0e-4 /yr</span>
        </div>
      </div>

      <div class="control-field">
        <label for="canvasSliderBeta_clymo">Temporal Decay ($\beta$)</label>
        <small>Accumulation decay rate ($A(a) = A_0 e^{-\beta a}$)</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderBeta_clymo" min="-0.0002" max="0.0002" step="0.00001" value="0">
          <span class="value-badge" id="canvasValBeta_clymo">0.0e+0 /yr</span>
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

      <div class="control-field" id="canvasContainerShearDepth" style="background: #fff8f8; padding: 0.6rem; border-radius: 4px; border: 1px solid #f5c6cb;">
        <label for="canvasSliderShearDepth" id="canvasLabelShearDepth">Active Creep Depth (H<sub>active</sub>)</label>
        <small id="canvasDescShearDepth">Depth limit for lateral motion</small>
        <div class="slider-row">
          <input type="range" id="canvasSliderShearDepth" min="0.2" max="5.0" step="0.1" value="1.0">
          <span class="value-badge" id="canvasValShearDepth">1.0 m</span>
        </div>
      </div>
    </div>
  </div>

  <div class="summary-metrics">
    <div class="metric-card">
      <div class="metric-title">Clymo / Yu Depth (10,000 yrs)</div>
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
    <div class="chart-wrapper">
      <h4 style="margin-top:0; margin-bottom: 0.5rem; color:#157878; text-align:center;">Integrated Lateral Volume Flux ($Q$, m²/yr)</h4>
      <canvas id="peatFluxCanvas" class="canvas-element"></canvas>
    </div>
  </div>

  <div class="model-notes" style="border-left: 4px solid #157878; background: #fdfefe; padding: 1rem; border-radius: 6px;">
    <h3 style="margin-top: 0; color: #157878; font-size: 1.1rem;">🔍 Step-by-Step Equifinality Challenge (Try It!)</h3>
    <p style="margin-bottom: 0.8rem; font-size: 0.9rem;">Peatland science and inverse modeling are plagued by <strong>unidentifiability</strong>. Can you distinguish a creeping bog from climate change? Try this:</p>
    <ol style="margin-bottom: 0; padding-left: 1.2rem; font-size: 0.85rem; line-height: 1.5;">
      <li style="margin-bottom: 0.5rem;"><strong>Set up a creeping bog</strong>: In the red panel (Creep Model), increase true surface accumulation $A_{\text{Creep}}$ to <code>90 g/m²/yr</code> and measured creep speed $u_{\text{meas}}$ to <code>2.00 cm/yr</code>. Notice how the dashed red line (Creep model) thins out compared to the baseline.</li>
      <li style="margin-bottom: 0.5rem;"><strong>Scenario A: Ignored Creep (Clymo fit)</strong>: Keep $\beta = 0$ on the left panel. To match the final depth of the creeping bog, you are forced to drag $A_0$ down to <code>~52 g/m²/yr</code>. If you ignore creep, your model severely underestimates historical carbon sequestration!</li>
      <li style="margin-bottom: 0;"><strong>Scenario B: Hiding Creep in Climate Change (Yu fit)</strong>: Keep the creep profile active. Now, set $A_0$ to <code>90 g/m²/yr</code> (matching the true creep input) on the left panel, and adjust the temporal change slider $\beta$ to <code>-5.0e-5 /yr</code>. Notice how the solid teal line and the dashed red line match almost perfectly! 
      <br><em>The mathematical catch:</em> A creeping bog behaves identically to a bog where accumulation increased exponentially over time. Without independent mechanical measurements, creep is completely hidden in the climate reconstruction!</li>
    </ol>
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
  const beta_clymo = parseFloat(document.getElementById('canvasSliderBeta_clymo').value);
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
  containerShear.style.display = 'block'; 

  const labelActiveDepth = document.getElementById('canvasLabelShearDepth');
  const descActiveDepth = document.getElementById('canvasDescShearDepth');
  if (profileType === 'shear') {
    labelActiveDepth.innerHTML = 'Active Creep Depth: Shear-Zone Depth (H<sub>shear</sub>)';
    descActiveDepth.innerText = 'Depth where the linear creep velocity profile reaches zero';
    document.getElementById('canvasProfileHelpText').innerText = 'Surface speed decreases linearly to zero at the chosen active depth';
  } else {
    labelActiveDepth.innerHTML = 'Active Creep Depth: Plug-Flow Limit (H<sub>plug</sub>)';
    descActiveDepth.innerText = 'Depth below which there is no creep (plug flow only in the upper layer)';
    document.getElementById('canvasProfileHelpText').innerText = 'All peat in the active upper layer moves with the same effective speed';
  }

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
  document.getElementById('canvasValBeta_clymo').innerText = `${beta_clymo.toExponential(1)} /yr`;

  document.getElementById('canvasValA_creep').innerText = `${A_g_creep} g/m²/yr`;
  document.getElementById('canvasValAlpha_creep').innerText = `${alpha_creep.toExponential(1)} /yr`;
  document.getElementById('canvasValCreep').innerText = `${uRefCmYr.toFixed(2)} cm/yr`;
  document.getElementById('canvasValShearDepth').innerText = `${hShear.toFixed(1)} m`;

  return { profileType, scalingType, A_kg_clymo, alpha_clymo, beta_clymo, A_kg_creep, alpha_creep, uRefMYr, hShear };
}

function getIntegratedFlux(H, surfaceSpeed, profileType, hShear) {
  if (profileType === 'uniform') {
    return surfaceSpeed * Math.min(H, hShear);
  } else { // shear
    if (H <= hShear) {
      return surfaceSpeed * H * (1 - H / (2 * hShear));
    } else {
      return surfaceSpeed * hShear / 2.0;
    }
  }
}

function solveCanvasODE(params) {
  const { profileType, scalingType, A_kg_clymo, alpha_clymo, beta_clymo, A_kg_creep, alpha_creep, uRefMYr, hShear } = params;
  const dt = CANVAS_MODEL_CONSTANTS.years / CANVAS_MODEL_CONSTANTS.steps;
  const ages = [];
  const depthDecayOnly = [];
  const depthWithCreep = [];
  const massDecayOnly = [];
  const massWithCreep = [];
  const fluxDecayOnly = new Array(CANVAS_MODEL_CONSTANTS.steps + 1).fill(0);
  const fluxSpatial = [];
  const fluxHistoricalCohort = [];
  const fluxHistorical = [];
  let M_creep = 0;

  const M_baseline_creep_final = (A_kg_creep / alpha_creep) * (1 - Math.exp(-alpha_creep * CANVAS_MODEL_CONSTANTS.years));
  let H_final_est = Math.max(0.1, M_baseline_creep_final / CANVAS_MODEL_CONSTANTS.bulkDensity);
  const maxIter = 5;
  const H_min = 0.1;

  for (let iter = 0; iter < maxIter; iter++) {
    M_creep = 0;
    massWithCreep.length = 0;
    depthWithCreep.length = 0;
    fluxHistorical.length = 0;
    ages.length = 0;
    massDecayOnly.length = 0;
    depthDecayOnly.length = 0;

    for (let i = 0; i <= CANVAS_MODEL_CONSTANTS.steps; i++) {
      const age = i * dt;
      if (iter === maxIter - 1) ages.push(age);

      let M_baseline;
      if (Math.abs(alpha_clymo - beta_clymo) < 1e-9) {
        M_baseline = A_kg_clymo * age;
      } else {
        M_baseline = (A_kg_clymo / (alpha_clymo - beta_clymo)) * (1 - Math.exp(-(alpha_clymo - beta_clymo) * age));
      }
      if (iter === maxIter - 1) {
        massDecayOnly.push(M_baseline);
        depthDecayOnly.push(M_baseline / CANVAS_MODEL_CONSTANTS.bulkDensity);
      }

      if (i === 0) {
        massWithCreep.push(0);
        depthWithCreep.push(0);
        fluxHistorical.push(0);
      } else {
        const currentH = Math.max(0, M_creep / CANVAS_MODEL_CONSTANTS.bulkDensity);
        const H_ref = Math.max(H_min, H_final_est);
        let currentSurfaceSpeed = uRefMYr;
        if (scalingType === 'linear') {
          currentSurfaceSpeed = uRefMYr * currentH / H_ref;
        } else if (scalingType === 'quadratic') {
          currentSurfaceSpeed = uRefMYr * Math.pow(currentH / H_ref, 2);
        } else if (scalingType === 'inverse') {
          currentSurfaceSpeed = uRefMYr * H_ref / Math.max(currentH, H_min);
        }
        
        const fluxHist = getIntegratedFlux(currentH, currentSurfaceSpeed, profileType, hShear);

        const f = (m) => {
          const H = Math.max(0, m / CANVAS_MODEL_CONSTANTS.bulkDensity);
          const H_ref_int = Math.max(H_min, H_final_est);
          let surfaceSpeed = uRefMYr;
          if (scalingType === 'linear') {
            surfaceSpeed = uRefMYr * H / H_ref_int;
          } else if (scalingType === 'quadratic') {
            surfaceSpeed = uRefMYr * Math.pow(H / H_ref_int, 2);
          } else if (scalingType === 'inverse') {
            surfaceSpeed = uRefMYr * H_ref_int / Math.max(H, H_min);
          }
          const profileFactor = profileType === 'shear'
            ? Math.max(0, 1 - H / Math.max(H_min, hShear))
            : (H <= hShear ? 1.0 : 0.0);
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
        fluxHistorical.push(fluxHist);
      }
    }
    H_final_est = Math.max(H_min, M_creep / CANVAS_MODEL_CONSTANTS.bulkDensity);
  }

  for (let i = 0; i <= CANVAS_MODEL_CONSTANTS.steps; i++) {
    const H_cohort = depthWithCreep[i];
    const fSpatial = getIntegratedFlux(H_cohort, uRefMYr, profileType, hShear);
    fluxSpatial.push(fSpatial);

    const fHist = fluxHistorical[CANVAS_MODEL_CONSTANTS.steps - i];
    fluxHistoricalCohort.push(fHist);
  }

  const profileLabel = profileType === 'uniform' ? `uniform to ${hShear.toFixed(1)} m` : `linear to ${hShear.toFixed(1)} m`;
  const scalingLabels = {
    constant: 'constant u',
    linear: 'u ∝ H',
    quadratic: 'u ∝ H²',
    inverse: 'u ∝ 1/H'
  };
  const derivedCoeffStr = `${profileLabel}; ${scalingLabels[scalingType]}`;

  return { ages, depthDecayOnly, depthWithCreep, massDecayOnly, massWithCreep, fluxDecayOnly, fluxSpatial, fluxHistoricalCohort, derivedCoeffStr };
}

function drawSingleCanvasPlot(canvasId, yAxisLabel, seriesBaseline, seriesCreep, ages, seriesCreepHist = null) {
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

  const maxValToScan = seriesCreepHist ? Math.max(...seriesBaseline, ...seriesCreep, ...seriesCreepHist) : Math.max(...seriesBaseline, ...seriesCreep);
  const maxY = maxValToScan * 1.08;
  const plot = {
    left: 65,
    right: 20,
    top: 35,
    bottom: 55
  };
  plot.width = cssWidth - plot.left - plot.right;
  plot.height = cssHeight - plot.top - plot.bottom;
  plot.maxY = Math.max(maxY, 0.0001);

  ctx.fillStyle = '#ffffff';
  ctx.fillRect(0, 0, cssWidth, cssHeight);

  ctx.strokeStyle = '#d8dee4';
  ctx.lineWidth = 1;
  ctx.fillStyle = '#586069';
  ctx.font = '11px -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif';
  ctx.textAlign = 'right';
  ctx.textBaseline = 'middle';

  const isFlux = canvasId === 'peatFluxCanvas';

  for (let i = 0; i <= 5; i++) {
    const frac = i / 5;
    const y = isFlux ? (plot.top + (1 - frac) * plot.height) : (plot.top + frac * plot.height);
    const val = frac * plot.maxY;
    ctx.beginPath();
    ctx.moveTo(plot.left, y);
    ctx.lineTo(plot.left + plot.width, y);
    ctx.stroke();

    let labelVal;
    if (plot.maxY >= 100) labelVal = val.toFixed(0);
    else if (plot.maxY >= 1) labelVal = val.toFixed(1);
    else if (plot.maxY >= 0.1) labelVal = val.toFixed(2);
    else labelVal = val.toFixed(4);
    ctx.fillText(labelVal, plot.left - 8, y);
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

  ctx.save();
  ctx.strokeStyle = '#157878';
  ctx.lineWidth = 3;
  ctx.beginPath();
  ages.forEach((age, index) => {
    const x = plot.left + plot.width - (age / CANVAS_MODEL_CONSTANTS.years) * plot.width;
    const valRatio = seriesBaseline[index] / plot.maxY;
    const y = isFlux ? (plot.top + (1.0 - valRatio) * plot.height) : (plot.top + valRatio * plot.height);
    if (index === 0) ctx.moveTo(x, y);
    else ctx.lineTo(x, y);
  });
  ctx.stroke();
  ctx.restore();

  ctx.save();
  ctx.strokeStyle = '#d9534f';
  ctx.lineWidth = 3;
  ctx.setLineDash([6, 4]);
  ctx.beginPath();
  ages.forEach((age, index) => {
    const x = plot.left + plot.width - (age / CANVAS_MODEL_CONSTANTS.years) * plot.width;
    const valRatio = seriesCreep[index] / plot.maxY;
    const y = isFlux ? (plot.top + (1.0 - valRatio) * plot.height) : (plot.top + valRatio * plot.height);
    if (index === 0) ctx.moveTo(x, y);
    else ctx.lineTo(x, y);
  });
  ctx.stroke();
  ctx.restore();

  if (isFlux && seriesCreepHist) {
    ctx.save();
    ctx.strokeStyle = '#f0ad4e';
    ctx.lineWidth = 3;
    ctx.setLineDash([2, 3]);
    ctx.beginPath();
    ages.forEach((age, index) => {
      const x = plot.left + plot.width - (age / CANVAS_MODEL_CONSTANTS.years) * plot.width;
      const valRatio = seriesCreepHist[index] / plot.maxY;
      const y = plot.top + (1.0 - valRatio) * plot.height;
      if (index === 0) ctx.moveTo(x, y);
      else ctx.lineTo(x, y);
    });
    ctx.stroke();
    ctx.restore();
  }

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
  ctx.fillRect(plot.left + 10, plot.top - 20, 15, 3);
  ctx.fillStyle = '#24292e';
  ctx.font = '10px -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif';
  ctx.fillText('Clymo/Yu model', plot.left + 30, plot.top - 19);

  ctx.strokeStyle = '#d9534f';
  ctx.lineWidth = 2.5;
  ctx.setLineDash([5, 3]);
  ctx.beginPath();
  ctx.moveTo(plot.left + 135, plot.top - 19);
  ctx.lineTo(plot.left + 150, plot.top - 19);
  ctx.stroke();
  ctx.setLineDash([]);
  ctx.fillStyle = '#24292e';
  ctx.fillText(isFlux ? 'Spatial Cumulative Flux (Present Day)' : 'Creep model', plot.left + 157, plot.top - 19);

  if (isFlux) {
    ctx.strokeStyle = '#f0ad4e';
    ctx.lineWidth = 2.5;
    ctx.setLineDash([1.5, 2]);
    ctx.beginPath();
    ctx.moveTo(plot.left + 10, plot.top - 8);
    ctx.lineTo(plot.left + 25, plot.top - 8);
    ctx.stroke();
    ctx.setLineDash([]);
    ctx.fillStyle = '#24292e';
    ctx.fillText('Historical Total Flux (Over Time)', plot.left + 30, plot.top - 7);
  }
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
  drawSingleCanvasPlot('peatFluxCanvas', 'Integrated lateral flux (m²/yr)', data.fluxDecayOnly, data.fluxSpatial, data.ages, data.fluxHistoricalCohort);
}

document.addEventListener('DOMContentLoaded', () => {
  document.getElementById('canvasSelectProfile').addEventListener('change', updateCanvasPlot);
  document.getElementById('canvasSelectScaling').addEventListener('change', updateCanvasPlot);
  ['canvasSliderA_clymo', 'canvasSliderAlpha_clymo', 'canvasSliderBeta_clymo', 'canvasSliderA_creep', 'canvasSliderAlpha_creep', 'canvasSliderCreep', 'canvasSliderShearDepth'].forEach(id => {
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

### 2. Temporal Variations: The Yu et al. (2003) & Belyea & Malmer (2004) Model
Clymo's model assumes a constant peat addition rate $A$ through time. To allow for climate-driven or developmental variations, the model by *Yu et al. (2003)* and *Belyea & Malmer (2004)* permits the initial accumulation rate of cohorts to vary exponentially back through calendar time. Expressed in terms of cohort age $a$ today, the remaining mass of a cohort of age $a$ today is $m(a) = A_0 e^{-(\alpha - \beta) a}$.

Integrating this from age $0$ to $a$ today yields the cumulative peat mass profile:

$$M(a) = \frac{A_0}{\alpha - \beta} \left( 1 - e^{-(\alpha - \beta) a} \right), \qquad \text{for } \alpha \neq \beta$$

and $M(a) = A_0 a$ when $\alpha = \beta$. 

Here, $A_0$ is the present-day surface accumulation flux, $\alpha$ is the decomposition rate, and $\beta$ controls the temporal accumulation change. Note that this Lagrangian age-depth profile within a single core today is mathematically identical to Clymo's constant-accumulation equation but with a shifted effective decay rate, $\alpha_{\text{eff}} = \alpha - \beta$. This is physically distinct from the calendar-time growth history of the total bog height over time, which is $H(t) = \frac{p}{\alpha - \beta}(e^{-\beta t} - e^{-\alpha t})$.

A positive $\beta$ represents a peatland where initial accumulation was higher in the past (decreasing towards the present, resulting in a thicker bottom). A negative $\beta$ represents a peatland where initial accumulation was lower in the past (increasing towards the present, resulting in a thinner bottom).

### 3. Mathematical Derivation of the Creep Closure

In a 2D/3D continuum, mass conservation is $\frac{\partial M}{\partial t} + \nabla \cdot \left(\mathbf{u} M\right) = A - \alpha M$. In this one-column demo, unresolved lateral divergence is approximated by an export term

$$\frac{dM}{da} = A - \alpha M - \frac{u_{\text{eff}}(H)}{L}M,$$

where $L=100\text{ m}$ is a representative export length. The user-controlled speed $u_{\text{meas}}$ is interpreted as the present-day surface creep speed measured when the simulated core has its final depth $H_{\text{final}}$.

#### **Vertical Profile Choices**
Both profile choices assume that lateral creep is restricted to an active upper layer of thickness $H_{\text{active}}$ (denoted $H_{\text{plug}}$ or $H_{\text{shear}}$):
- **Uniform / plug-like profile:** peat in the active layer ($0 \le z \le H_{\text{plug}}$) moves downslope at a uniform velocity. Below this depth, the peat is stable (no creep).
- **Linear shear-zone profile:** speed is maximum at the surface and decreases linearly to zero at depth $H_{\text{shear}}$. Below this depth, the peat is stable.

#### **Integrated Lateral Flux**
Integrating the lateral velocity profile $u(z)$ over the depth of the peat column gives the integrated lateral volume flux $Q$ (in $\mathrm{m^2\,yr^{-1}}$):

$$Q = \int_0^H u(z) \, dz$$

We can analyze this integral in two distinct ways:

1. **Spatial Cumulative Flux (Present Day):**
   * **Mathematics:** This is an indefinite integral over depth $z$ evaluated at the final present time ($t = T_{\text{final}}$):
     $$Q_{\text{spatial}}(z) = \int_0^z u(T_{\text{final}}, s) \, ds \qquad \text{for } z \in [0, H_{\text{final}}]$$
     where $z = H(a)$ is the depth of the cohort of age $a$ today. 
   * **Physics:** It answers: *"In the modern peat column today, how much lateral creep flux is carried by the upper layers down to depth $z$?"* Because the surface ($z = 0$, cohort age 0) has a thickness of 0, this cumulative value starts at $0$ today and increases to the total column flux at the bottom of the core (age 10,000).

2. **Historical Total Flux (Over Time):**
   * **Mathematics:** This is a definite integral over the entire peat thickness $H(t)$ at varying historical times $t \in [0, T_{\text{final}}]$:
     $$Q_{\text{historical}}(t) = \int_0^{H(t)} u(t, z) \, dz$$
     where $t = T_{\text{final}} - a$ is the historical time when the oldest cohort was $a$ years old.
   * **Physics:** It answers: *"At any historical time $t$ in the bog's history, what was the total lateral creep flux integrated across the entire profile?"* Because the bog is mature today (present, age 0), this total flux is at its **maximum** today. It only drops to $0$ at the initial start of the bog 10,000 years ago (age 10,000).

For the two profile types under an active layer of depth $H_{\text{active}}$ (where $u_{\text{surf}}$ is the surface speed):
- **Uniform profile:**
  $$Q = u_{\text{surf}} \min(H, H_{\text{active}})$$
- **Linear shear profile:**
  $$Q = \begin{cases} 
  u_{\text{surf}} H \left(1 - \frac{H}{2 H_{\text{active}}}\right) & \text{for } H \le H_{\text{active}} \\
  u_{\text{surf}} \frac{H_{\text{active}}}{2} & \text{for } H > H_{\text{active}}
  \end{cases}$$

This lateral volume flux can be converted to a lateral dry mass flux (in $\mathrm{kg\,m^{-1}\,yr^{-1}}$) by multiplying by the constant bulk density: $F_{\text{lateral}} = Q \cdot \rho_b$.

#### **Depth-Scaling Choices for the Measured Surface Speed**
The depth-scaling selector asks how the present-day measured speed should be extrapolated backward when the peat column was thinner:
- **Constant with depth, $u(H)=u_{\text{meas}}$:** effective driving and resistance scale together, or the measured motion is controlled mainly by boundary conditions and slope rather than total peat thickness.
- **Overburden-scaled, $u(H)\propto H$:** driving stress grows with peat thickness or buoyant overburden. This is a simple gravity-loading closure when resistance is approximately linear.
- **Viscous-film scaling, $u(H)\propto H^2$:** a Newtonian/lubrication-style idealisation with no slip at the base and stress accumulating through the mobile layer. It gives much slower creep when the bog was shallow and faster creep as it thickens.
- **Resistance-limited, $u(H)\propto 1/H$:** a shallow driving layer, such as an unsaturated or seasonally active layer, drags a thicker passive body. The thicker the peat column, the larger the viscous resistance, so the same shallow forcing produces slower motion.

These are not competing claims about the true law; they are deliberately simple closures for exploring how different physical hypotheses alter the inferred age-depth profile.

---

### 4. The Equifinality & Inverse Problem Challenge

When analyzing an observed radiocarbon-dated core from a field site, scientists face an **inverse problem** (fitting parameters from measured age-depth points).

If lateral creep is active in the bog but omitted from the inversion model:
- The observer forces a decay-only Clymo curve to pass through the observed 10,000-year depth.
- This forces the estimated accumulation parameter $A_{\text{Clymo}}$ to be significantly **lower** than the true input rate $A_{\text{Creep}}$ (or forces estimated decay $\alpha_{\text{Clymo}}$ to be artificially elevated).

#### **Hiding Creep in the Climate Inversion (Yu/Belyea Model)**
If scientists use the more flexible Yu / Belyea model to fit the core (instead of Clymo's constant-$A$ model), they introduce the temporal parameter $\beta$. Because lateral creep exports peat and thins the profile at depth, **the effects of creep can be completely absorbed into the inferred temporal history**. 
- A creeping bog fitted with a decay-only Yu model will yield an artificial **negative $\beta$** (interpreting the creep-induced thinning as a historical trend of increasing accumulation towards the present). 
- This hides the mechanical loss within an incorrect climate-change reconstruction!

#### **Sampling Bias in Core Selection**
In field campaigns, researchers typically sample cores from the deepest part of the peatland (e.g., the plateau of a raised bog). This introduces a systematic **sampling bias**: by choosing the deepest and thickest plateaus, scientists preferentially sample exactly the locations where the peat column is oldest, yet where lateral creep stress and peat loss over millennia are also most active. 

---

### References
* **Belyea, L.R. & Malmer, N. (2004).** *Carbon sequestration in peatland: patterns and mechanisms of response to climate change.* Global Change Biology, **10**, 1043–1052. [doi:10.1111/j.1529-8817.2003.00783.x](https://doi.org/10.1111/j.1529-8817.2003.00783.x)
* **Clymo, R.S. (1984).** *The limits to peat growth.* Philosophical Transactions of the Royal Society of London. Series B, Biological Sciences, **303**, 605–654. [doi:10.1098/rstb.1984.0002](https://doi.org/10.1098/rstb.1984.0002)
* **Yu, Z., Vitt, D.H., Campbell, I.D., et al. (2003).** *Understanding Holocene peat accumulation pattern of continental fens in western Canada.* Canadian Journal of Botany, **81**, 267–282. [doi:10.1139/b03-016](https://doi.org/10.1139/b03-016)
