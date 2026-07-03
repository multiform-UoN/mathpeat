---
layout: default
title: "Does peat creep?"
description: "Interactive mathematical model exploring peat age-depth profiles and peat creep."
---

# Does Peat Creep?

Peatlands are dynamic, deformable porous media storing over 30% of global soil carbon. Researchers in the **MATHPEAT Network** are currently investigating the evidence for peat creep, how it should be represented mathematically, and what its consequences may be for peatland age-depth structure and long-term carbon storage.

The model below is deliberately simple. It is not a full landscape-scale creep model: creep mostly redistributes peat locally. Here we ask what a single representative column would look like if unresolved downslope or marginal creep eventually exports material from that column.

---

## Simple Column Model

Let $M(a)$ be cumulative peat dry mass per unit area at cohort age $a$, measured in $\mathrm{kg\,m^{-2}}$. The estimated peat depth is

$$H(a) = \frac{M(a)}{\rho_b},$$

where the fixed bulk density used in the demo is $\rho_b = 100\,\mathrm{kg\,m^{-3}}$.

Without creep, the classic accumulation-decay balance is

$$\frac{dM}{da} = A - \alpha M, \qquad M(0)=0,$$

where $A$ is the accumulation flux and $\alpha$ is the first-order decay rate. The default values are $A=60\,\mathrm{g\,m^{-2}\,yr^{-1}}$ and $\alpha=10^{-4}\,\mathrm{yr^{-1}}$.

For creep, the visible slider controls a **reference lateral creep speed** $u_\mathrm{ref}$ in $\mathrm{cm\,yr^{-1}}$. To turn this into a one-column export term, we assume that the lateral creep speed scales with peat depth,

$$u(H) = u_\mathrm{ref}\frac{H}{H_\mathrm{ref}},$$

and that net export from the representative column has fractional rate $u(H)/L$, where $L$ is an effective downslope or marginal export length. This gives

$$\frac{dM}{da} = A - \alpha M - \beta(u_\mathrm{ref}) M^2,$$

with

$$\beta(u_\mathrm{ref}) = \frac{u_\mathrm{ref}}{L H_\mathrm{ref} \rho_b}.$$

In this demo $H_\mathrm{ref}=3\,\mathrm{m}$ and $L=100\,\mathrm{m}$. These assumptions keep the control physically readable while preserving the intended simple nonlinear loss term.

---

## Interactive Age-Depth & Cumulative Mass Model

Adjust the parameters below to compare the classic **decay only** profile against the simple **decay plus creep export** profile over 10,000 years. Both the **Estimated Depth Profile** (in meters) and the **Cumulative Dry Mass Profile** ($\mathrm{kg\,m^{-2}}$) are displayed below.

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

  .controls-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 1.25rem;
    margin-bottom: 1.5rem;
  }

  .control-card {
    background: #ffffff;
    border: 1px solid #d1d5da;
    border-radius: 6px;
    padding: 1rem;
    box-shadow: 0 1px 3px rgba(0,0,0,0.05);
  }

  .control-card label {
    display: block;
    font-weight: 600;
    margin-bottom: 0.25rem;
    color: #157878;
  }

  .control-card small {
    color: #666;
  }

  .slider-row {
    display: flex;
    align-items: center;
    gap: 0.75rem;
    margin-top: 0.5rem;
  }

  .slider-row input[type="range"] {
    flex: 1;
    cursor: pointer;
  }

  .value-badge {
    background: #eef7f7;
    color: #157878;
    border: 1px solid #bce1e1;
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
    min-width: 190px;
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
    font-size: 1.25rem;
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
    min-height: 400px;
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
  <div class="controls-grid">
    <div class="control-card">
      <label for="sliderA">Accumulation Rate ($A$)</label>
      <small>Surface peat input flux</small>
      <div class="slider-row">
        <input type="range" id="sliderA" min="20" max="150" step="5" value="60">
        <span class="value-badge" id="valA">60 g/m²/yr</span>
      </div>
    </div>

    <div class="control-card">
      <label for="sliderAlpha">Decay Rate ($\alpha$)</label>
      <small>First-order catotelm decay rate</small>
      <div class="slider-row">
        <input type="range" id="sliderAlpha" min="0.00001" max="0.0005" step="0.00001" value="0.0001">
        <span class="value-badge" id="valAlpha">1.0e-4 /yr</span>
      </div>
    </div>

    <div class="control-card">
      <label for="sliderCreep">Reference Creep Speed ($u_\mathrm{ref}$)</label>
      <small>Lateral speed at $H_\mathrm{ref}=3\,\mathrm{m}$, converted internally to export</small>
      <div class="slider-row">
        <input type="range" id="sliderCreep" min="0" max="2" step="0.02" value="0.5">
        <span class="value-badge" id="valCreep">0.50 cm/yr</span>
      </div>
    </div>
  </div>

  <div class="summary-metrics">
    <div class="metric-card">
      <div class="metric-title">Decay Only at 10,000 yrs</div>
      <div class="metric-value" id="metricDecayOnly">-</div>
    </div>
    <div class="metric-card creep-card">
      <div class="metric-title">With Creep at 10,000 yrs</div>
      <div class="metric-value" id="metricWithCreep">-</div>
    </div>
    <div class="metric-card">
      <div class="metric-title">Reduction</div>
      <div class="metric-value" id="metricReduction">-</div>
    </div>
    <div class="metric-card">
      <div class="metric-title">Derived $\beta$</div>
      <div class="metric-value" id="metricBeta">-</div>
    </div>
  </div>

  <div class="charts-grid">
    <div class="chart-wrapper">
      <h4 style="margin-top:0; margin-bottom: 0.5rem; color:#157878; text-align:center;">Estimated Depth Profile ($H$, meters)</h4>
      <div style="position: relative; height: 350px;">
        <canvas id="peatDepthChart"></canvas>
      </div>
    </div>
    <div class="chart-wrapper">
      <h4 style="margin-top:0; margin-bottom: 0.5rem; color:#157878; text-align:center;">Cumulative Dry Mass Profile ($M$, kg/m²)</h4>
      <div style="position: relative; height: 350px;">
        <canvas id="peatMassChart"></canvas>
      </div>
    </div>
  </div>

  <div class="model-notes">
    <strong>How to read the plots:</strong>
    <ul>
      <li><strong>X-axis:</strong> peat cohort age, reversed so present day is at the right and older peat extends leftward.</li>
      <li><strong>Y-axes:</strong> inverted so surface ($0$) is at the top. Left plot shows Depth ($H$, m); right plot shows Cumulative Mass ($M$, $\mathrm{kg\,m^{-2}}$).</li>
      <li><strong>Solid teal line:</strong> classic Clymo model (decay only).</li>
      <li><strong>Dashed red line:</strong> model with depth-dependent creep export term.</li>
    </ul>
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
  const A_g = parseFloat(document.getElementById('sliderA').value);
  const alpha = parseFloat(document.getElementById('sliderAlpha').value);
  const uRefCmYr = parseFloat(document.getElementById('sliderCreep').value);
  const A_kg = A_g / 1000.0;
  const uRefMYr = uRefCmYr / 100.0;
  const beta = uRefMYr / (
    MODEL_CONSTANTS.exportLength *
    MODEL_CONSTANTS.referenceDepth *
    MODEL_CONSTANTS.bulkDensity
  );

  document.getElementById('valA').innerText = `${A_g} g/m²/yr`;
  document.getElementById('valAlpha').innerText = `${alpha.toExponential(1)} /yr`;
  document.getElementById('valCreep').innerText = `${uRefCmYr.toFixed(2)} cm/yr`;

  return { A_kg, alpha, uRefCmYr, beta };
}

function solveODE(params) {
  const { A_kg, alpha, beta } = params;
  const dt = MODEL_CONSTANTS.years / MODEL_CONSTANTS.steps;
  const ages = [];
  const depthDecayOnly = [];
  const depthWithCreep = [];
  const massDecayOnly = [];
  const massWithCreep = [];
  let M_creep = 0;

  for (let i = 0; i <= MODEL_CONSTANTS.steps; i++) {
    const age = i * dt;
    ages.push(age);

    const M_baseline = (A_kg / alpha) * (1 - Math.exp(-alpha * age));
    massDecayOnly.push(M_baseline);
    depthDecayOnly.push(M_baseline / MODEL_CONSTANTS.bulkDensity);

    if (i === 0) {
      massWithCreep.push(0);
      depthWithCreep.push(0);
    } else {
      const f = (M) => A_kg - alpha * M - beta * M * M;
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

  return { ages, depthDecayOnly, depthWithCreep, massDecayOnly, massWithCreep };
}

function createOrUpdateChart(existingChart, canvasId, titleText, yAxisLabel, labels, dataBaseline, dataCreep, unitFormatter) {
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
            label: 'Decay only',
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
            label: 'Decay + creep export',
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
          title: {
            display: false
          },
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
  document.getElementById('metricReduction').innerText = `${diffPct.toFixed(1)}%`;
  document.getElementById('metricBeta').innerText = `${params.beta.toExponential(2)} m²/kg/yr`;

  depthChartInstance = createOrUpdateChart(
    depthChartInstance,
    'peatDepthChart',
    'Estimated Depth Profile',
    'Estimated depth (m)',
    data.ages,
    data.depthDecayOnly,
    data.depthWithCreep,
    (val) => `${val.toFixed(2)} m depth`
  );

  massChartInstance = createOrUpdateChart(
    massChartInstance,
    'peatMassChart',
    'Cumulative Dry Mass Profile',
    'Cumulative mass (kg/m²)',
    data.ages,
    data.massDecayOnly,
    data.massWithCreep,
    (val) => `${val.toFixed(0)} kg/m²`
  );
}

document.addEventListener('DOMContentLoaded', () => {
  ['sliderA', 'sliderAlpha', 'sliderCreep'].forEach(id => {
    document.getElementById(id).addEventListener('input', updatePlot);
  });
  updatePlot();
});
</script>
{% endraw %}

---

## Mathematical, Physical & Peatland Science Foundations

### 1. Peatland Science Context
Standard 1D peat accumulation models (such as *Clymo 1984*) split the peat column into an upper oxic layer (acrotelm) and a lower water-saturated anoxic layer (catotelm). Organic matter enters the catotelm at surface accumulation flux $A$ ($\text{g m}^{-2}\text{yr}^{-1}$) and undergoes slow anaerobic decay at a first-order rate $\alpha$ ($\text{yr}^{-1}$).

However, peat is not a rigid solid: it is a **deformable, fluid-saturated soft porous medium**. On sloped terrain or unconfined bog margins, the weight of the peat column generates gravitational shear stresses ($\tau \approx \rho g H \sin\theta$). This induces slow downslope or lateral **peat creep** — a rheological flow process that exports mass laterally from the local vertical column.

### 2. Physical & Spatial Derivation of Column Creep Export
In a 2D/3D continuum, local mass conservation for cumulative peat mass per unit area $M(x, t)$ is:
$$\frac{\partial M}{\partial t} + \nabla \cdot \left(\mathbf{u} M\right) = A - \alpha M$$
where $\mathbf{u}$ is the depth-averaged lateral creep velocity. 

To represent unresolved lateral creep in a **single 1D representative column**:
1. We assume the lateral creep velocity scales linearly with column depth $H$:
   $$u(H) = u_{\text{ref}} \frac{H}{H_{\text{ref}}}$$
   where $u_{\text{ref}}$ is the reference lateral speed (in $\text{cm yr}^{-1}$) at reference depth $H_{\text{ref}} = 3\text{ m}$.
2. Over a characteristic bog export length scale $L = 100\text{ m}$, the fractional lateral export rate is $\frac{u(H)}{L}$.
3. Expressing depth in terms of dry mass ($H = M / \rho_b$, with bulk density $\rho_b = 100\text{ kg m}^{-3}$), the lateral mass loss per unit area per year becomes:
   $$\text{Export Flux} = M \cdot \frac{u(H)}{L} = M \cdot \frac{u_{\text{ref}} M}{L H_{\text{ref}} \rho_b} = \beta M^2$$
   where the effective non-linear creep export coefficient $\beta$ is:
   $$\beta(u_{\text{ref}}) = \frac{u_{\text{ref}}}{L \cdot H_{\text{ref}} \cdot \rho_b} \quad \left[\text{m}^2 \text{kg}^{-1}\text{yr}^{-1}\right]$$

### 3. Mathematical Properties of the Riccati ODE
The governing equation for cumulative peat mass $M(a)$ at cohort age $a$ is a non-linear **Riccati ODE**:
$$\frac{dM(a)}{da} = A - \alpha M(a) - \beta M(a)^2, \quad M(0) = 0$$

- **Linear Decay vs. Non-linear Creep**: Linear decay ($-\alpha M$) dominates at shallow depths, whereas non-linear creep ($-\beta M^2$) dominates at greater depths/masses.
- **Asymptotic Thickness & Carbon Storage**: While the classic Clymo model reaches a maximum mass $M_{\infty} = A / \alpha$, the creep ODE caps the sustainable column mass at a lower positive fixed point $M^*$:
  $$M^* = \frac{-\alpha + \sqrt{\alpha^2 + 4 A \beta}}{2 \beta}$$
- **Numerical Integration**: The profile $M(a)$ across $a \in [0, 10000\text{ yr}]$ is computed numerically using a 4th-order Runge-Kutta (RK4) integration scheme with $\Delta a = 20\text{ years}$.
