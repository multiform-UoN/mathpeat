---
layout: default
title: "Does peat creep?"
description: "Interactive mathematical model exploring peat age-depth profiles and peat creep advection."
---

# Does Peat Creep?

Peatlands are dynamic, deformable porous media storing over 30% of global soil carbon. While classic peat accumulation models (such as Clymo 1984) focus primarily on surface accumulation and sub-surface organic matter decay, researchers in the **MATHPEAT Network** are currently investigating the physical evidence, mathematical formulations, and ecological consequences of **peat creep** — the slow, gravity-driven downslope flow or unconfined lateral spreading of peat bodies.

---

## Bridge for Peat Scientists & Mathematicians

To bridge field observations with mathematical modeling, we compare the classic decay model with an advective creep model:

| Concept | Peat Scientist View | Mathematician View |
| :--- | :--- | :--- |
| **Surface Accumulation ($A$)** | Annual organic matter passing from acrotelm to catotelm | Constant source term ($A = 60 \text{ g m}^{-2}\text{yr}^{-1}$) |
| **Sub-surface Decay ($\alpha$)** | Slow anaerobic decomposition of peat matrix | Linear sink term ($-\alpha M$, $\alpha \approx 10^{-4} \text{ yr}^{-1}$) |
| **Peat Creep ($k_{\text{creep}}$)** | Downslope shear flow under overburden weight | Non-linear advective loss term ($-k_{\text{creep}} M^2$) |
| **Age-Depth Profile** | Cumulative mass/depth of peat layer aged $a$ years | Solution $M(a)$ of 1D differential equation |
| **Max Peat Thickness** | Asymptotic steady-state depth limit | Stable fixed point $M^*$ of Riccati ODE |

---

## Mathematical Formulation

### 1. Classic Clymo Decay Model (Linear ODE)
In standard peatland science, cumulative mass $M(a)$ ($\text{kg m}^{-2}$) at cohort age $a$ (years) is governed by constant surface input $A$ and linear decay $\alpha$:

$$\frac{dM(a)}{da} = A - \alpha M(a), \quad M(0) = 0$$

Integrating over age $a \in [0, 10000]$ yields the classic concave age-depth curve:
$$M(a) = \frac{A}{\alpha}\left(1 - e^{-\alpha a}\right)$$
As $a \to \infty$, the maximum column mass approaches $M_\infty = A/\alpha$.

### 2. Peat Creep Model (Riccati ODE)
Peat behaves as a soft, fluid-saturated visco-plastic porous body. On slopes or unconfined margins, overburden mass $M$ induces shear stresses causing lateral spreading (advection). In a 1D vertical column, this lateral advective flux scales with total column weight/thickness $M$, turning into a **quadratic removal term** $-k_{\text{creep}} M^2$:

$$\frac{dM(a)}{da} = A - \alpha M(a) - k_{\text{creep}} M(a)^2, \quad M(0) = 0$$

This non-linear **Riccati ODE** accelerates mass loss at depth. The maximum equilibrium peat mass is reduced to the positive root:
$$M^* = \frac{-\alpha + \sqrt{\alpha^2 + 4 A k_{\text{creep}}}}{2 k_{\text{creep}}}$$

---

## Interactive Age-Depth Profile Model

Adjust the parameters below to compare the classic **Decay Only** profile against the **With Peat Creep** profile over a 10,000-year timescale.

<!-- Chart.js CDN -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

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
    min-width: 85px;
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
    min-width: 200px;
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
    font-size: 1.35rem;
    font-weight: bold;
    color: #24292e;
    margin-top: 0.25rem;
  }

  .chart-wrapper {
    position: relative;
    background: #ffffff;
    border: 1px solid #d1d5da;
    border-radius: 6px;
    padding: 1rem;
    min-height: 420px;
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
    <!-- Accumulation Rate (A) -->
    <div class="control-card">
      <label for="sliderA">Accumulation Rate ($A$)</label>
      <small style="color: #666;">Surface peat input flux</small>
      <div class="slider-row">
        <input type="range" id="sliderA" min="20" max="150" step="5" value="60">
        <span class="value-badge" id="valA">60 g/m²/yr</span>
      </div>
    </div>

    <!-- Linear Decay Rate (alpha) -->
    <div class="control-card">
      <label for="sliderAlpha">Decay Rate ($\alpha$)</label>
      <small style="color: #666;">Catotelm linear decay rate</small>
      <div class="slider-row">
        <input type="range" id="sliderAlpha" min="0.00001" max="0.0005" step="0.00001" value="0.0001">
        <span class="value-badge" id="valAlpha">1.0e-4 /yr</span>
      </div>
    </div>

    <!-- Creep Rate Coefficient (k_creep) -->
    <div class="control-card">
      <label for="sliderCreep">Creep Rate ($k_{\text{creep}}$)</label>
      <small style="color: #666;">Non-linear advective loss coefficient</small>
      <div class="slider-row">
        <input type="range" id="sliderCreep" min="0" max="0.000001" step="0.00000002" value="0.0000002">
        <span class="value-badge" id="valCreep">2.0e-7</span>
      </div>
    </div>
  </div>

  <div class="summary-metrics">
    <div class="metric-card">
      <div class="metric-title">Decay Only Mass (at 10,000 yrs)</div>
      <div class="metric-value" id="metricDecayOnly">-</div>
    </div>
    <div class="metric-card creep-card">
      <div class="metric-title">With Creep Mass (at 10,000 yrs)</div>
      <div class="metric-value" id="metricWithCreep">-</div>
    </div>
    <div class="metric-card">
      <div class="metric-title">Mass Reduction by Creep</div>
      <div class="metric-value" id="metricReduction">-</div>
    </div>
  </div>

  <div class="chart-wrapper">
    <canvas id="peatChart"></canvas>
  </div>

  <div class="model-notes">
    📊 <strong>How to read the plot:</strong>
    <ul>
      <li><strong>X-Axis (Peat Age):</strong> Represents the age of peat cohorts from present day ($0\text{ yr}$) down to $10,000\text{ years}$.</li>
      <li><strong>Y-Axis (Cumulative Mass):</strong> Inverted so that $0$ is at the top (bog surface), increasing downwards to represent cumulative mass ($\text{kg m}^{-2}$) and estimated depth ($\approx 100 \text{ kg m}^{-3}$ bulk density).</li>
      <li><strong>Solid Teal Line:</strong> Classic Clymo model (Decay only).</li>
      <li><strong>Dashed Red Line:</strong> Advective Peat Creep model. Notice how creep causes older peat layers at depth to compress and thin faster, capping total bog thickness.</li>
    </ul>
  </div>
</div>

<script>
/**
 * Interactive Peat Creep Model Logic
 * Structure:
 * 1. readInputs(): Read parameters from HTML sliders
 * 2. solveODE(): Compute numerical solution using RK4
 * 3. updatePlot(): Update Chart.js chart instance
 */

let chartInstance = null;

// 1. Read User Inputs
function readInputs() {
  const A_g = parseFloat(document.getElementById('sliderA').value); // g/m²/yr
  const alpha = parseFloat(document.getElementById('sliderAlpha').value); // /yr
  const k_creep = parseFloat(document.getElementById('sliderCreep').value); // quadratic coefficient

  // Convert A from g/m²/yr to kg/m²/yr for numerical precision
  const A_kg = A_g / 1000.0;

  // Update DOM displays
  document.getElementById('valA').innerText = `${A_g} g/m²/yr`;
  document.getElementById('valAlpha').innerText = `${alpha.toExponential(1)} /yr`;
  document.getElementById('valCreep').innerText = `${k_creep.toExponential(1)}`;

  return { A_kg, alpha, k_creep };
}

// 2. Numerical ODE Solution (4th Order Runge-Kutta)
function solveODE(params) {
  const { A_kg, alpha, k_creep } = params;
  const T_max = 10000; // years
  const steps = 100;
  const dt = T_max / steps;

  const ages = [];
  const massDecayOnly = [];
  const massWithCreep = [];

  let M_creep = 0;

  for (let i = 0; i <= steps; i++) {
    const age = i * dt;
    ages.push(age);

    // Analytical solution for decay-only baseline: M = (A/alpha) * (1 - exp(-alpha * t))
    const M_baseline = (A_kg / alpha) * (1 - Math.exp(-alpha * age));
    massDecayOnly.push(M_baseline);

    // RK4 integration for non-linear creep ODE: dM/dt = A - alpha*M - k_creep*M^2
    if (i === 0) {
      massWithCreep.push(0);
    } else {
      const f = (M) => A_kg - alpha * M - k_creep * M * M;
      
      const k1 = f(M_creep);
      const k2 = f(M_creep + 0.5 * dt * k1);
      const k3 = f(M_creep + 0.5 * dt * k2);
      const k4 = f(M_creep + dt * k3);

      M_creep += (dt / 6.0) * (k1 + 2 * k2 + 2 * k3 + k4);
      if (M_creep < 0) M_creep = 0;

      massWithCreep.push(M_creep);
    }
  }

  return { ages, massDecayOnly, massWithCreep };
}

// 3. Render / Update Chart
function updatePlot() {
  const params = readInputs();
  const data = solveODE(params);

  // Update summary metrics
  const finalBaseline = data.massDecayOnly[data.massDecayOnly.length - 1];
  const finalCreep = data.massWithCreep[data.massWithCreep.length - 1];
  const diffPct = ((finalBaseline - finalCreep) / finalBaseline) * 100;

  document.getElementById('metricDecayOnly').innerText = `${finalBaseline.toFixed(1)} kg/m² (${(finalBaseline/100).toFixed(2)} m)`;
  document.getElementById('metricWithCreep').innerText = `${finalCreep.toFixed(1)} kg/m² (${(finalCreep/100).toFixed(2)} m)`;
  document.getElementById('metricReduction').innerText = `${diffPct.toFixed(1)}%`;

  const ctx = document.getElementById('peatChart').getContext('2d');

  if (chartInstance) {
    chartInstance.data.labels = data.ages;
    chartInstance.data.datasets[0].data = data.massDecayOnly;
    chartInstance.data.datasets[1].data = data.massWithCreep;
    chartInstance.update();
  } else {
    chartInstance = new Chart(ctx, {
      type: 'line',
      data: {
        labels: data.ages,
        datasets: [
          {
            label: 'Classic Model (Decay Only)',
            data: data.massDecayOnly,
            borderColor: '#157878',
            backgroundColor: 'rgba(21, 120, 120, 0.1)',
            borderWidth: 3,
            fill: false,
            tension: 0.2,
            pointRadius: 0,
            pointHoverRadius: 5
          },
          {
            label: 'With Peat Creep (Decay + Non-linear Advection)',
            data: data.massWithCreep,
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
          intersect: false,
        },
        plugins: {
          title: {
            display: true,
            text: 'Peat Age vs Cumulative Mass Profile (10,000 Years)',
            font: { size: 16, weight: 'bold' }
          },
          tooltip: {
            callbacks: {
              label: function(context) {
                const mass = context.raw;
                const depthEst = (mass / 100).toFixed(2);
                return `${context.dataset.label}: ${mass.toFixed(1)} kg/m² (~${depthEst} m depth)`;
              }
            }
          }
        },
        scales: {
          x: {
            title: {
              display: true,
              text: 'Peat Age (years)',
              font: { weight: 'bold' }
            },
            grid: { color: '#eaeaea' }
          },
          y: {
            reverse: true, // Invert Y-axis so surface (0) is at top
            title: {
              display: true,
              text: 'Cumulative Peat Mass (kg/m²) [Surface = 0 at Top]',
              font: { weight: 'bold' }
            },
            grid: { color: '#eaeaea' }
          }
        }
      }
    });
  }
}

// Attach event listeners to sliders
document.addEventListener('DOMContentLoaded', () => {
  ['sliderA', 'sliderAlpha', 'sliderCreep'].forEach(id => {
    document.getElementById(id).addEventListener('input', updatePlot);
  });
  updatePlot();
});
</script>

---

## Modularization Note for Site Maintainers

This demo is self-contained within this single `.md` file for easy deployment on GitHub Pages.

To split this into separate assets for future website demos:
- Move `.model-container` CSS to `style.css` or `/assets/css/model.css`.
- Move the JS logic (`readInputs`, `solveODE`, `updatePlot`) into `/assets/js/peat-creep.js`.
- Simply reference `<script src="/assets/js/peat-creep.js"></script>` in the Markdown post.
