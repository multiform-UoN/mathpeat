---
layout: default
title: "Does peat creep?"
description: "Interactive mathematical model exploring peat age-depth profiles and reverse-engineering equifinality challenges."
---

# Does Peat Creep?

Peatlands are dynamic, deformable porous media storing over 30% of global soil carbon. Researchers in the **MATHPEAT Network** are currently investigating the evidence for peat creep, how it should be represented mathematically, and what its consequences may be for peatland age-depth structure and long-term carbon storage.

The model below is deliberately simple. It is not a full 3D landscape-scale creep model: creep mostly redistributes peat locally. Here we ask what a single representative column would look like under different vertical creep velocity profiles if unresolved downslope or marginal creep eventually exports material from that column.

---

## The Reverse-Engineering Challenge (Equifinality)

In field peatland science, scientists extract a physical peat core, measure its depth $H$, and perform Radiocarbon ($^{14}\text{C}$) dating to get age $a$ at depth $H$. 

**The Challenge:** A given observed age-depth profile could be fitted by TWO completely different mechanisms:
1. **Classic Clymo Model (Decay Only):** Fitted with a lower surface accumulation rate $A_{\text{Clymo}}$ or higher decay rate $\alpha_{\text{Clymo}}$.
2. **Peat Creep Model (Decay + Export):** Driven by a higher true surface accumulation rate $A_{\text{Creep}}$, standard decay $\alpha_{\text{Creep}}$, and active lateral creep $u_{\text{ref}}$.

If creep is ignored, fitting an observed profile with only Clymo's equation will **underestimate true carbon accumulation rates ($A$)** and misattribute lateral creep loss to decomposition!

Try adjusting the separate parameters below to see if you can "fit" the Clymo model to match the Creep model's final depth!

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
    <!-- Clymo Model Panel -->
    <div class="param-group param-group-clymo">
      <div class="param-group-header">
        <span class="param-group-title clymo">Clymo Model (Decay Only)</span>
        <span class="param-badge param-badge-clymo">Teal Line</span>
      </div>

      <div class="control-field">
        <label for="sliderA_clymo">Accumulation ($A_{\text{Clymo}}$)</label>
        <small>Surface peat input flux</small>
        <div class="slider-row">
          <input type="range" id="sliderA_clymo" min="20" max="150" step="5" value="60">
          <span class="value-badge" id="valA_clymo">60 g/m²/yr</span>
        </div>
      </div>

      <div class="control-field">
        <label for="sliderAlpha_clymo">Decay Rate ($\alpha_{\text{Clymo}}$)</label>
        <small>First-order catotelm decay rate</small>
        <div class="slider-row">
          <input type="range" id="sliderAlpha_clymo" min="0.00001" max="0.0005" step="0.00001" value="0.0001">
          <span class="value-badge" id="valAlpha_clymo">1.0e-4 /yr</span>
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
        <label for="selectModel">Creep Mechanism</label>
        <small id="modelHelpText">Select vertical velocity profile</small>
        <select id="selectModel">
          <option value="overburden" selected>1. Overburden Creep (u ∝ H, max at depth)</option>
          <option value="shear">2. Surface Shear (max at surface, 0 at bed)</option>
          <option value="activeLayer">3. Active Upper Layer (max in top 1.5m, 0 deep)</option>
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
        <label for="sliderCreep" id="labelCreep">Creep Speed ($u_\mathrm{ref}$)</label>
        <small id="sublabelCreep">Speed at $H_\mathrm{ref}=3\,\mathrm{m}$</small>
        <div class="slider-row">
          <input type="range" id="sliderCreep" min="0" max="10" step="0.1" value="0.5">
          <span class="value-badge" id="valCreep">0.50 cm/yr</span>
        </div>
      </div>
    </div>
  </div>

  <div class="summary-metrics">
    <div class="metric-card">
      <div class="metric-title">Decay Only Depth (10,000 yrs)</div>
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
    💡 <strong>Fitting Experiment:</strong> Increase $A_{\text{Creep}}$ (e.g. to $90\text{ g m}^{-2}\text{yr}^{-1}$) and $u_{\text{ref}}$ (e.g. to $2\text{ cm/yr}$). Notice how the Creep core thins. Now try adjusting $A_{\text{Clymo}}$ downwards to match the final 10,000-year depth. Notice that matching final depth forces you to severely underestimate true surface carbon accumulation $A$!
  </div>
</div>

<script>
let depthChartInstance = null;
let massChartInstance = null;

const MODEL_CONSTANTS = {
  years: 10000,
  steps: 500,
  bulkDensity: 100,      // kg/m3
  referenceDepth: 3,     // m
  exportLength: 100      // m
};

function readInputs() {
  const modelType = document.getElementById('selectModel').value;
  
  // Clymo parameters
  const A_g_clymo = parseFloat(document.getElementById('sliderA_clymo').value);
  const alpha_clymo = parseFloat(document.getElementById('sliderAlpha_clymo').value);
  const A_kg_clymo = A_g_clymo / 1000.0;

  // Creep parameters
  const A_g_creep = parseFloat(document.getElementById('sliderA_creep').value);
  const alpha_creep = parseFloat(document.getElementById('sliderAlpha_creep').value);
  const uRefCmYr = parseFloat(document.getElementById('sliderCreep').value);
  const A_kg_creep = A_g_creep / 1000.0;
  const uRefMYr = uRefCmYr / 100.0;

  // Update dynamic labels
  if (modelType === 'overburden') {
    document.getElementById('modelHelpText').innerText = 'u ∝ H: creep speeds up at depth under overburden';
    document.getElementById('labelCreep').innerText = 'Ref Creep Speed (u_ref)';
    document.getElementById('sublabelCreep').innerText = 'Speed at H_ref = 3 m';
    document.getElementById('titleCoeff').innerText = 'Derived β';
  } else if (modelType === 'shear') {
    document.getElementById('modelHelpText').innerText = 'u max at surface, 0 at bedrock (no-slip)';
    document.getElementById('labelCreep').innerText = 'Surface Speed (u_surf)';
    document.getElementById('sublabelCreep').innerText = 'Speed at surface (0 at bed)';
    document.getElementById('titleCoeff').innerText = 'Derived γ';
  } else if (modelType === 'activeLayer') {
    document.getElementById('modelHelpText').innerText = 'u constant in top 1.5m, 0 in deep peat';
    document.getElementById('labelCreep').innerText = 'Active Speed (u_active)';
    document.getElementById('sublabelCreep').innerText = 'Speed in top 1.5 m active layer';
    document.getElementById('titleCoeff').innerText = 'Derived k_act';
  }

  document.getElementById('valA_clymo').innerText = `${A_g_clymo} g/m²/yr`;
  document.getElementById('valAlpha_clymo').innerText = `${alpha_clymo.toExponential(1)} /yr`;

  document.getElementById('valA_creep').innerText = `${A_g_creep} g/m²/yr`;
  document.getElementById('valAlpha_creep').innerText = `${alpha_creep.toExponential(1)} /yr`;
  document.getElementById('valCreep').innerText = `${uRefCmYr.toFixed(2)} cm/yr`;

  return { modelType, A_kg_clymo, alpha_clymo, A_kg_creep, alpha_creep, uRefMYr };
}

function solveODE(params) {
  const { modelType, A_kg_clymo, alpha_clymo, A_kg_creep, alpha_creep, uRefMYr } = params;
  const dt = MODEL_CONSTANTS.years / MODEL_CONSTANTS.steps;
  const ages = [];
  const depthDecayOnly = [];
  const depthWithCreep = [];
  const massDecayOnly = [];
  const massWithCreep = [];

  let M_creep = 0;

  const M_baseline_creep_final = (A_kg_creep / alpha_creep) * (1 - Math.exp(-alpha_creep * MODEL_CONSTANTS.years));
  const beta = uRefMYr / (MODEL_CONSTANTS.exportLength * MODEL_CONSTANTS.referenceDepth * MODEL_CONSTANTS.bulkDensity);
  const gamma = uRefMYr / MODEL_CONSTANTS.exportLength;
  const k_act = uRefMYr / MODEL_CONSTANTS.exportLength;
  const M_act = 1.5 * MODEL_CONSTANTS.bulkDensity;

  for (let i = 0; i <= MODEL_CONSTANTS.steps; i++) {
    const age = i * dt;
    ages.push(age);

    // 1. Clymo baseline profile (Decay Only)
    const M_baseline = (A_kg_clymo / alpha_clymo) * (1 - Math.exp(-alpha_clymo * age));
    massDecayOnly.push(M_baseline);
    depthDecayOnly.push(M_baseline / MODEL_CONSTANTS.bulkDensity);

    // 2. Creep profile (Decay + Creep Export)
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
      depthWithCreep.push(M_creep / MODEL_CONSTANTS.bulkDensity);
    }
  }

  let derivedCoeffStr = '';
  if (modelType === 'overburden') derivedCoeffStr = `${beta.toExponential(2)} m²/kg/yr`;
  else if (modelType === 'shear') derivedCoeffStr = `${gamma.toExponential(2)} /yr`;
  else if (modelType === 'activeLayer') derivedCoeffStr = `${k_act.toExponential(2)} /yr`;

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
            label: 'Clymo Model (Decay Only)',
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
  document.getElementById('selectModel').addEventListener('change', updatePlot);
  ['sliderA_clymo', 'sliderAlpha_clymo', 'sliderA_creep', 'sliderAlpha_creep', 'sliderCreep'].forEach(id => {
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
