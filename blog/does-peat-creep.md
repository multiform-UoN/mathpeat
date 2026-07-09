---
layout: default
title: "Does peat creep?"
description: "Interactive mathematical model exploring peat age-depth profiles under alternative creep profiles and depth-scaling laws."
---

# Does Peat Creep?

Peatlands are dynamic, deformable porous media storing over 30% of global soil carbon. Researchers in the **MATHPEAT Network** are currently investigating the evidence for peat creep, how it should be represented mathematically, and what its consequences may be for peatland age-depth structure and long-term carbon storage.

The model below is deliberately simple. It is not a full 3D landscape-scale creep model: creep mostly redistributes peat locally. Here we ask what a single representative column would look like under different vertical creep profiles and depth-scaling assumptions if unresolved downslope or marginal creep eventually exports material from that column.

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

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

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
    position: relative;
    background: #ffffff;
    border: 1px solid #d1d5da;
    border-radius: 6px;
    padding: 1rem;
    min-height: 380px;
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
        <label for="sliderA_clymo">Initial Accumulation ($A_0$)</label>
        <small>Present-day surface peat input flux</small>
        <div class="slider-row">
          <input type="range" id="sliderA_clymo" min="20" max="150" step="5" value="60">
          <span class="value-badge" id="valA_clymo">60 g/m²/yr</span>
        </div>
      </div>

      <div class="control-field">
        <label for="sliderAlpha_clymo">Decay Rate ($\alpha$)</label>
        <small>First-order catotelm decay rate</small>
        <div class="slider-row">
          <input type="range" id="sliderAlpha_clymo" min="0.00001" max="0.0005" step="0.00001" value="0.0001">
          <span class="value-badge" id="valAlpha_clymo">1.0e-4 /yr</span>
        </div>
      </div>

      <div class="control-field">
        <label for="sliderBeta_clymo">Temporal Decay ($\beta$)</label>
        <small>Accumulation decay rate ($A(a) = A_0 e^{-\beta a}$)</small>
        <div class="slider-row">
          <input type="range" id="sliderBeta_clymo" min="-0.0002" max="0.0002" step="0.00001" value="0">
          <span class="value-badge" id="valBeta_clymo">0.0e+0 /yr</span>
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
        <label for="selectProfile">Vertical Creep Profile</label>
        <small id="profileHelpText">Choose where in the peat column lateral motion is active</small>
        <select id="selectProfile">
          <option value="uniform" selected>Uniform / plug-like profile</option>
          <option value="shear">Linear shear-zone profile</option>
        </select>
      </div>

      <div class="control-field">
        <label for="selectScaling">Depth Scaling of Measured Speed</label>
        <small id="scalingHelpText">How today's measured surface speed is reconstructed for shallower past peat</small>
        <select id="selectScaling">
          <option value="constant" selected>Constant with depth: u(H) = u_meas</option>
          <option value="linear">Overburden-scaled: u(H) ∝ H</option>
          <option value="quadratic">Viscous-film scaling: u(H) ∝ H²</option>
          <option value="inverse">Resistance-limited: u(H) ∝ 1/H</option>
        </select>
      </div>

      <div class="control-field">
        <label for="sliderA_creep">Accumulation ($A_{\text{Creep}}$)</label>
        <small>True surface input flux</small>
        <div class="slider-row">
          <input type="range" id="sliderA_creep" min="20" max="150" step="5" value="60">
          <span class="value-badge" id="valA_creep">60 g/m²/yr</span>
        </div>
      </div>

      <div class="control-field">
        <label for="sliderAlpha_creep">Decay Rate ($\alpha_{\text{Creep}}$)</label>
        <small>True catotelm decay rate</small>
        <div class="slider-row">
          <input type="range" id="sliderAlpha_creep" min="0.00001" max="0.0005" step="0.00001" value="0.0001">
          <span class="value-badge" id="valAlpha_creep">1.0e-4 /yr</span>
        </div>
      </div>

      <div class="control-field">
        <label for="sliderCreep" id="labelCreep">Measured Surface Creep Speed ($u_\mathrm{meas}$)</label>
        <small id="sublabelCreep">Present-day surface speed at final simulated depth</small>
        <div class="slider-row">
          <input type="range" id="sliderCreep" min="0" max="10" step="0.1" value="0.5">
          <span class="value-badge" id="valCreep">0.50 cm/yr</span>
        </div>
      </div>

      <div class="control-field" id="containerShearDepth" style="display: none; background: #fff8f8; padding: 0.6rem; border-radius: 4px; border: 1px solid #f5c6cb;">
        <label for="sliderShearDepth">Shear-Zone Depth ($H_{\text{shear}}$)</label>
        <small>Depth where the linear creep profile reaches zero</small>
        <div class="slider-row">
          <input type="range" id="sliderShearDepth" min="0.2" max="5.0" step="0.1" value="1.0">
          <span class="value-badge" id="valShearDepth">1.0 m</span>
        </div>
      </div>
    </div>
  </div>

  <div class="summary-metrics">
    <div class="metric-card">
      <div class="metric-title">Clymo / Yu Depth (10,000 yrs)</div>
      <div class="metric-value" id="metricDecayOnly">-</div>
    </div>
    <div class="metric-card creep-card">
      <div class="metric-title">With Creep Depth (10,000 yrs)</div>
      <div class="metric-value" id="metricWithCreep">-</div>
    </div>
    <div class="metric-card">
      <div class="metric-title">Depth Discrepancy</div>
      <div class="metric-value" id="metricReduction">-</div>
    </div>
    <div class="metric-card">
      <div class="metric-title" id="titleCoeff">Derived Coeff</div>
      <div class="metric-value" id="metricCoeff">-</div>
    </div>
  </div>

  <div class="charts-grid">
    <div class="chart-wrapper">
      <h4 style="margin-top:0; margin-bottom: 0.5rem; color:#157878; text-align:center;">Estimated Depth Profile ($H$, meters)</h4>
      <div style="position: relative; height: 330px;">
        <canvas id="peatDepthChart"></canvas>
      </div>
    </div>
    <div class="chart-wrapper">
      <h4 style="margin-top:0; margin-bottom: 0.5rem; color:#157878; text-align:center;">Cumulative Dry Mass Profile ($M$, kg/m²)</h4>
      <div style="position: relative; height: 330px;">
        <canvas id="peatMassChart"></canvas>
      </div>
    </div>
  </div>

  <div class="model-notes">
    💡 <strong>Fitting Experiment:</strong> Increase $A_{\text{Creep}}$ (e.g. to $90\text{ g m}^{-2}\text{yr}^{-1}$) and $u_{\text{meas}}$ (e.g. to $2\text{ cm/yr}$). Notice how the Creep core thins. Now try adjusting $A_0$ downwards, or setting a negative $\beta$ (e.g., $-4.0\times 10^{-5}$/yr) on the Clymo/Yu model to match the depth. Notice that the creep effect (material loss at depth) can be mathematically absorbed/reproduced by assuming an accumulation rate that increased over time (negative $\beta$)!
  </div>
</div>

<script>
let depthChartInstance = null;
let massChartInstance = null;

const MODEL_CONSTANTS = {
  years: 10000,
  steps: 500,
  bulkDensity: 100,      // kg/m3
  exportLength: 100      // m
};

function readInputs() {
  const profileType = document.getElementById('selectProfile').value;
  const scalingType = document.getElementById('selectScaling').value;

  // Clymo/Yu parameters
  const A_g_clymo = parseFloat(document.getElementById('sliderA_clymo').value);
  const alpha_clymo = parseFloat(document.getElementById('sliderAlpha_clymo').value);
  const beta_clymo = parseFloat(document.getElementById('sliderBeta_clymo').value);
  const A_kg_clymo = A_g_clymo / 1000.0;

  // Creep parameters
  const A_g_creep = parseFloat(document.getElementById('sliderA_creep').value);
  const alpha_creep = parseFloat(document.getElementById('sliderAlpha_creep').value);
  const uRefCmYr = parseFloat(document.getElementById('sliderCreep').value);
  const shearSlider = document.getElementById('sliderShearDepth');

  const A_kg_creep = A_g_creep / 1000.0;
  const uRefMYr = uRefCmYr / 100.0;
  const M_noCreepFinal = (A_kg_creep / alpha_creep) * (1 - Math.exp(-alpha_creep * MODEL_CONSTANTS.years));
  const maxShearDepth = Math.max(0.2, M_noCreepFinal / MODEL_CONSTANTS.bulkDensity);
  shearSlider.max = maxShearDepth.toFixed(1);
  if (parseFloat(shearSlider.value) > maxShearDepth) {
    shearSlider.value = maxShearDepth.toFixed(1);
  }
  const hShear = parseFloat(shearSlider.value);

  const containerShear = document.getElementById('containerShearDepth');
  containerShear.style.display = profileType === 'shear' ? 'block' : 'none';

  document.getElementById('profileHelpText').innerText =
    profileType === 'uniform'
      ? 'All represented peat moves with the same effective lateral speed'
      : 'Surface speed decreases linearly to zero at the chosen shear-zone depth';

  const scalingHelp = {
    constant: 'Same measured speed throughout the growth history',
    linear: 'Driving stress grows with peat thickness or overburden',
    quadratic: 'Newtonian/lubrication-style viscous film scaling',
    inverse: 'Fixed shallow driving layer resisted by a thicker peat column'
  };
  document.getElementById('scalingHelpText').innerText = scalingHelp[scalingType];
  document.getElementById('labelCreep').innerText = 'Measured Surface Creep Speed (u_meas)';
  document.getElementById('sublabelCreep').innerText = 'Present-day surface speed at final simulated depth';
  document.getElementById('titleCoeff').innerText = 'Creep Setup';

  document.getElementById('valA_clymo').innerText = `${A_g_clymo} g/m²/yr`;
  document.getElementById('valAlpha_clymo').innerText = `${alpha_clymo.toExponential(1)} /yr`;
  document.getElementById('valBeta_clymo').innerText = `${beta_clymo.toExponential(1)} /yr`;

  document.getElementById('valA_creep').innerText = `${A_g_creep} g/m²/yr`;
  document.getElementById('valAlpha_creep').innerText = `${alpha_creep.toExponential(1)} /yr`;
  document.getElementById('valCreep').innerText = `${uRefCmYr.toFixed(2)} cm/yr`;
  document.getElementById('valShearDepth').innerText = `${hShear.toFixed(1)} m`;

  return { profileType, scalingType, A_kg_clymo, alpha_clymo, beta_clymo, A_kg_creep, alpha_creep, uRefMYr, hShear };
}

function solveODE(params) {
  const { profileType, scalingType, A_kg_clymo, alpha_clymo, beta_clymo, A_kg_creep, alpha_creep, uRefMYr, hShear } = params;
  const dt = MODEL_CONSTANTS.years / MODEL_CONSTANTS.steps;
  const ages = [];
  const depthDecayOnly = [];
  const depthWithCreep = [];
  const massDecayOnly = [];
  const massWithCreep = [];

  let M_creep = 0;

  const M_baseline_creep_final = (A_kg_creep / alpha_creep) * (1 - Math.exp(-alpha_creep * MODEL_CONSTANTS.years));
  let H_final_est = Math.max(0.1, M_baseline_creep_final / MODEL_CONSTANTS.bulkDensity);
  const maxIter = 5;
  const H_min = 0.1;

  for (let iter = 0; iter < maxIter; iter++) {
    M_creep = 0;
    massWithCreep.length = 0;
    depthWithCreep.length = 0;
    ages.length = 0;
    massDecayOnly.length = 0;
    depthDecayOnly.length = 0;

    for (let i = 0; i <= MODEL_CONSTANTS.steps; i++) {
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
        depthDecayOnly.push(M_baseline / MODEL_CONSTANTS.bulkDensity);
      }

      if (i === 0) {
        massWithCreep.push(0);
        depthWithCreep.push(0);
      } else {
        const f = (m) => {
          const H = Math.max(0, m / MODEL_CONSTANTS.bulkDensity);
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
          const exportRate = (surfaceSpeed / MODEL_CONSTANTS.exportLength) * profileFactor;
          return A_kg_creep - alpha_creep * m - exportRate * m;
        };

        const k1 = f(M_creep);
        const k2 = f(M_creep + 0.5 * dt * k1);
        const k3 = f(M_creep + 0.5 * dt * k2);
        const k4 = f(M_creep + dt * k3);
        M_creep += (dt / 6.0) * (k1 + 2 * k2 + 2 * k3 + k4);
        if (M_creep < 0) M_creep = 0;

        massWithCreep.push(M_creep);
        depthWithCreep.push(M_creep / MODEL_CONSTANTS.bulkDensity);
      }
    }
    H_final_est = Math.max(H_min, M_creep / MODEL_CONSTANTS.bulkDensity);
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

function createOrUpdateChart(existingChart, canvasId, yAxisLabel, labels, dataBaseline, dataCreep, unitFormatter) {
  const ctx = document.getElementById(canvasId).getContext('2d');
  
  if (existingChart) {
    existingChart.data.labels = labels;
    existingChart.data.datasets[0].data = dataBaseline;
    existingChart.data.datasets[1].data = dataCreep;
    existingChart.update();
    return existingChart;
  } else {
    return new Chart(ctx, {
      type: 'line',
      data: {
        labels: labels,
        datasets: [
          {
            label: 'Clymo / Yu Model (Decay Only)',
            data: dataBaseline,
            borderColor: '#157878',
            backgroundColor: 'rgba(21, 120, 120, 0.1)',
            borderWidth: 3,
            fill: false,
            tension: 0.2,
            pointRadius: 0,
            pointHoverRadius: 5
          },
          {
            label: 'Creep Model (Decay + Export)',
            data: dataCreep,
            borderColor: '#d9534f',
            backgroundColor: 'rgba(217, 83, 79, 0.1)',
            borderDash: [6, 4],
            borderWidth: 3,
            fill: false,
            tension: 0.2,
            pointRadius: 0,
            pointHoverRadius: 5
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        interaction: {
          mode: 'index',
          intersect: false
        },
        plugins: {
          title: { display: false },
          tooltip: {
            callbacks: {
              label: function(context) {
                return `${context.dataset.label}: ${unitFormatter(context.raw)}`;
              }
            }
          }
        },
        scales: {
          x: {
            reverse: true,
            title: {
              display: true,
              text: 'Peat age (years)',
              font: { weight: 'bold' }
            },
            grid: { color: '#eaeaea' }
          },
          y: {
            reverse: true,
            title: {
              display: true,
              text: yAxisLabel,
              font: { weight: 'bold' }
            },
            grid: { color: '#eaeaea' }
          }
        }
      }
    });
  }
}

function updatePlot() {
  const params = readInputs();
  const data = solveODE(params);
  const finalBaselineDepth = data.depthDecayOnly[data.depthDecayOnly.length - 1];
  const finalCreepDepth = data.depthWithCreep[data.depthWithCreep.length - 1];
  const finalBaselineMass = data.massDecayOnly[data.massDecayOnly.length - 1];
  const finalCreepMass = data.massWithCreep[data.massWithCreep.length - 1];
  const diffPct = ((finalBaselineDepth - finalCreepDepth) / finalBaselineDepth) * 100;

  document.getElementById('metricDecayOnly').innerText = `${finalBaselineDepth.toFixed(2)} m (${finalBaselineMass.toFixed(0)} kg/m²)`;
  document.getElementById('metricWithCreep').innerText = `${finalCreepDepth.toFixed(2)} m (${finalCreepMass.toFixed(0)} kg/m²)`;
  document.getElementById('metricReduction').innerText = `${diffPct >= 0 ? '-' : '+'}${Math.abs(diffPct).toFixed(1)}%`;
  document.getElementById('metricCoeff').innerText = data.derivedCoeffStr;

  depthChartInstance = createOrUpdateChart(
    depthChartInstance,
    'peatDepthChart',
    'Estimated depth (m)',
    data.ages,
    data.depthDecayOnly,
    data.depthWithCreep,
    (val) => `${val.toFixed(2)} m depth`
  );

  massChartInstance = createOrUpdateChart(
    massChartInstance,
    'peatMassChart',
    'Cumulative mass (kg/m²)',
    data.ages,
    data.massDecayOnly,
    data.massWithCreep,
    (val) => `${val.toFixed(0)} kg/m²`
  );
}

document.addEventListener('DOMContentLoaded', () => {
  document.getElementById('selectProfile').addEventListener('change', updatePlot);
  document.getElementById('selectScaling').addEventListener('change', updatePlot);
  ['sliderA_clymo', 'sliderAlpha_clymo', 'sliderBeta_clymo', 'sliderA_creep', 'sliderAlpha_creep', 'sliderCreep', 'sliderShearDepth'].forEach(id => {
    document.getElementById(id).addEventListener('input', updatePlot);
  });
  updatePlot();
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
