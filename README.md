<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Backward Feed Triple Effect Evaporator Solver</title>
  
  <!-- MathJax Configuration for LaTeX support -->
  <script>
    window.MathJax = {
      tex: {
        inlineMath: [['$', '$'], ['\\(', '\\)']],
        displayMath: [['$$', '$$'], ['\\[', '\\]']]
      }
    };
  </script>
  <script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
      padding: 0;
      background-color: #f4f7f6;
      color: #333;
    }
    .app-container {
      max-width: 1000px;
      margin: 0 auto;
      background: #ffffff;
      padding: 25px;
      border-radius: 8px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    }
    h1, h2, h3 {
      color: #0056b3;
    }
    .metadata {
      background-color: #e9ecef;
      padding: 10px 15px;
      border-left: 5px solid #0056b3;
      margin-bottom: 20px;
      font-size: 0.95em;
    }
    .instructions {
      background-color: #e8f4f8;
      border: 1px solid #bce8f1;
      padding: 15px;
      border-radius: 5px;
      margin-bottom: 20px;
    }
    .grid-container {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
      gap: 15px;
      margin-bottom: 20px;
    }
    .input-card {
      background: #fafafa;
      border: 1px solid #ddd;
      padding: 15px;
      border-radius: 5px;
    }
    .input-card h4 {
      margin-top: 0;
      color: #333;
      border-bottom: 2px solid #0056b3;
      padding-bottom: 5px;
    }
    .form-group {
      margin-bottom: 10px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .form-group label {
      font-size: 0.9em;
      margin-right: 10px;
    }
    .form-group input {
      width: 100px;
      padding: 5px;
      border: 1px solid #ccc;
      border-radius: 4px;
      text-align: right;
    }
    .btn-container {
      text-align: center;
      margin: 20px 0;
    }
    .btn {
      background-color: #0056b3;
      color: white;
      border: none;
      padding: 12px 25px;
      font-size: 16px;
      border-radius: 5px;
      cursor: pointer;
      transition: background-color 0.3s;
    }
    .btn:hover {
      background-color: #003d80;
    }
    .unit-toggle {
      margin-bottom: 15px;
      text-align: right;
    }
    .unit-toggle button {
      padding: 6px 12px;
      border: 1px solid #0056b3;
      background: #fff;
      color: #0056b3;
      cursor: pointer;
      border-radius: 4px;
    }
    .unit-toggle button.active {
      background: #0056b3;
      color: #fff;
    }
    .results-section {
      background: #f8f9fa;
      border: 1px solid #c3e6cb;
      padding: 20px;
      border-radius: 5px;
      margin-top: 20px;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 15px;
    }
    table, th, td {
      border: 1px solid #dee2e6;
    }
    th, td {
      padding: 10px;
      text-align: center;
    }
    th {
      background-color: #0056b3;
      color: white;
    }
    .diagram-container {
      text-align: center;
      margin: 30px 0;
      background: #fff;
      padding: 15px;
      border: 1px solid #ddd;
      border-radius: 5px;
    }
    svg {
      max-width: 100%;
      height: auto;
    }
  </style>
</head>
<body>

<div class="app-container">
  <h1>Triple-Effect Evaporator Solver (Backward Feed)</h1>
  
  <div class="metadata">
    <p><strong>Author:</strong> PEREZ LOERA</p>
    <p><strong>Date of Creation:</strong> October 2026</p>
    <p><strong>Course:</strong> Unit Operations II - Facultad de Ciencias Químicas, UACH</p>
  </div>

  <div class="instructions">
    <h3>Instructions & User Guide</h3>
    <p>This application calculates the material and energy balances for a <strong>Backward Feed Triple-Effect Evaporator</strong> system.</p>
    <ul>
      <li>Enter all operating parameters in SI units in the fields below.</li>
      <li>Feed enters Effect III ($F_1 = L_3$) and concentrated liquid exits Effect I ($L_1$). Fresh steam $W_1$ enters Effect I.</li>
      <li>The program applies mass and enthalpy balance equations:
        $$\sum F_{in} = \sum F_{out}$$
        $$F_{in} \cdot h_{in} + Q = F_{out} \cdot h_{out}$$
      </li>
      <li>Click <strong>"Solve Heat Balance"</strong> to compute vapor flows ($W_1$), liquid flow rates ($L_1, L_2, L_3$), and overall steam economy.</li>
      <li>Use the unit toggle to display results in SI or British units.</li>
    </ul>
  </div>

  <form id="evaporatorForm">
    <h3>Input Data (SI Units)</h3>
    <div class="grid-container">
      
      <!-- General Feed & Pressures -->
      <div class="input-card">
        <h4>System Operating Conditions</h4>
        <div class="form-group">
          <label for="F1">Feed Flow Rate ($F_1 = L_3$) [kg/s]:</label>
          <input type="number" step="any" id="F1" value="2.5" required>
        </div>
        <div class="form-group">
          <label for="xF1">Solute Concentration at Feed ($x_{F1}$):</label>
          <input type="number" step="any" id="xF1" value="0.10" required>
        </div>
        <div class="form-group">
          <label for="xS3">Solute Concentration at Product ($x_{S3} = x_{L1}$):</label>
          <input type="number" step="any" id="xS3" value="0.50" required>
        </div>
        <div class="form-group">
          <label for="pW1">Pressure Steam $p_{W1}$ [kPa]:</label>
          <input type="number" step="any" id="pW1" value="205" required>
        </div>
        <div class="form-group">
          <label for="pL1">Effect I Pressure $p_{L1}$ [kPa]:</label>
          <input type="number" step="any" id="pL1" value="101.3" required>
        </div>
        <div class="form-group">
          <label for="pL2">Effect II Pressure $p_{L2}$ [kPa]:</label>
          <input type="number" step="any" id="pL2" value="47.5" required>
        </div>
        <div class="form-group">
          <label for="pL3">Effect III Pressure $p_{L3}$ [kPa]:</label>
          <input type="number" step="any" id="pL3" value="13.5" required>
        </div>
      </div>

      <!-- Heat Capacities & Heat Transfer Coefficients -->
      <div class="input-card">
        <h4>Heat Capacities & Coefficients</h4>
        <div class="form-group">
          <label for="Cp1">Cp Liquid Effect I ($Cp_1$) [kJ/kg·°C]:</label>
          <input type="number" step="any" id="Cp1" value="3.14" required>
        </div>
        <div class="form-group">
          <label for="Cp2">Cp Liquid Effect II ($Cp_2$) [kJ/kg·°C]:</label>
          <input type="number" step="any" id="Cp2" value="3.50" required>
        </div>
        <div class="form-group">
          <label for="Cp3">Cp Liquid Effect III ($Cp_3$) [kJ/kg·°C]:</label>
          <input type="number" step="any" id="Cp3" value="3.85" required>
        </div>
        <div class="form-group">
          <label for="U1">Coeff. $U_1$ [W/m²·°C]:</label>
          <input type="number" step="any" id="U1" value="2300" required>
        </div>
        <div class="form-group">
          <label for="U2">Coeff. $U_2$ [W/m²·°C]:</label>
          <input type="number" step="any" id="U2" value="1500" required>
        </div>
        <div class="form-group">
          <label for="U3">Coeff. $U_3$ [W/m²·°C]:</label>
          <input type="number" step="any" id="U3" value="1000" required>
        </div>
      </div>

      <!-- Enthalpies & Latent Heats -->
      <div class="input-card">
        <h4>Temperatures & Enthalpies</h4>
        <div class="form-group">
          <label for="TF">Feed Temp ($T_F = T_{L3}$) [°C]:</label>
          <input type="number" step="any" id="TF" value="20" required>
        </div>
        <div class="form-group">
          <label for="T1">Temp Effect I ($T_{L1}$) [°C]:</label>
          <input type="number" step="any" id="T1" value="100" required>
        </div>
        <div class="form-group">
          <label for="T2">Temp Effect II ($T_{L2}$) [°C]:</label>
          <input type="number" step="any" id="T2" value="80" required>
        </div>
        <div class="form-group">
          <label for="T3">Temp Effect III ($T_{L3}$) [°C]:</label>
          <input type="number" step="any" id="T3" value="52" required>
        </div>
        <div class="form-group">
          <label for="lambdaW1">Latent Heat Steam $\lambda_{W1}$ [kJ/kg]:</label>
          <input type="number" step="any" id="lambdaW1" value="2200" required>
        </div>
        <div class="form-group">
          <label for="lambdaV1">Latent Heat Vapor 1 $\lambda_{V1}$ [kJ/kg]:</label>
          <input type="number" step="any" id="lambdaV1" value="2257" required>
        </div>
        <div class="form-group">
          <label for="lambdaV2">Latent Heat Vapor 2 $\lambda_{V2}$ [kJ/kg]:</label>
          <input type="number" step="any" id="lambdaV2" value="2308" required>
        </div>
      </div>

    </div>

    <div class="btn-container">
      <button type="button" class="btn" onclick="calculateEvaporator()">Solve Heat Balance</button>
    </div>
  </form>

  <!-- Process Diagram Sketch -->
  <div class="diagram-container">
    <h3>Backward Feed Evaporator Process Diagram</h3>
    <svg width="750" height="280" viewBox="0 0 750 280">
      <!-- Effect III -->
      <rect x="50" y="70" width="120" height="140" rx="10" fill="#e3f2fd" stroke="#0056b3" stroke-width="2"/>
      <text x="110" y="145" text-anchor="middle" font-weight="bold">Effect III</text>
      
      <!-- Effect II -->
      <rect x="300" y="70" width="120" height="140" rx="10" fill="#e3f2fd" stroke="#0056b3" stroke-width="2"/>
      <text x="360" y="145" text-anchor="middle" font-weight="bold">Effect II</text>
      
      <!-- Effect I -->
      <rect x="550" y="70" width="120" height="140" rx="10" fill="#e3f2fd" stroke="#0056b3" stroke-width="2"/>
      <text x="610" y="145" text-anchor="middle" font-weight="bold">Effect I</text>

      <!-- Feed F1 / L3 into Effect III -->
      <path d="M 10 180 L 50 180" stroke="#28a745" stroke-width="3" marker-end="url(#arrow)"/>
      <text x="25" y="170" font-size="12" fill="#28a745" font-weight="bold">Feed (F1)</text>

      <!-- Liquid Flow L3 to Effect II -->
      <path d="M 170 180 L 300 180" stroke="#28a745" stroke-width="3" marker-end="url(#arrow)"/>
      <text x="235" y="170" font-size="12" fill="#28a745" text-anchor="middle">L3</text>

      <!-- Liquid Flow L2 to Effect I -->
      <path d="M 420 180 L 550 180" stroke="#28a745" stroke-width="3" marker-end="url(#arrow)"/>
      <text x="485" y="170" font-size="12" fill="#28a745" text-anchor="middle">L2</text>

      <!-- Product L1 out of Effect I -->
      <path d="M 670 180 L 740 180" stroke="#28a745" stroke-width="3" marker-end="url(#arrow)"/>
      <text x="705" y="170" font-size="12" fill="#28a745" text-anchor="middle" font-weight="bold">Product (L1)</text>

      <!-- Steam W1 into Effect I -->
      <path d="M 500 100 L 550 100" stroke="#dc3545" stroke-width="3" marker-end="url(#arrow)"/>
      <text x="525" y="90" font-size="12" fill="#dc3545" text-anchor="middle">Steam (W1)</text>

      <!-- Vapor V1 from Effect I to Effect II -->
      <path d="M 610 70 L 610 30 L 360 30 L 360 70" fill="none" stroke="#dc3545" stroke-width="2" marker-end="url(#arrow)"/>
      <text x="485" y="25" font-size="12" fill="#dc3545" text-anchor="middle">Vapor V1</text>

      <!-- Vapor V2 from Effect II to Effect III -->
      <path d="M 360 70 L 360 45 L 110 45 L 110 70" fill="none" stroke="#dc3545" stroke-width="2" marker-end="url(#arrow)"/>
      <text x="235" y="40" font-size="12" fill="#dc3545" text-anchor="middle">Vapor V2</text>

      <!-- Vapor V3 out of Effect III -->
      <path d="M 110 70 L 110 20 L 50 20" fill="none" stroke="#dc3545" stroke-width="2" marker-end="url(#arrow)"/>
      <text x="80" y="15" font-size="12" fill="#dc3545" text-anchor="middle">V3 (To Condenser)</text>

      <!-- SVG Arrowhead -->
      <defs>
        <marker id="arrow" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
          <path d="M 0 0 L 10 5 L 0 10 z" fill="#333"/>
        </marker>
      </defs>
    </svg>
  </div>

  <!-- Results Section -->
  <div class="results-section" id="resultsBlock" style="display:none;">
    <div style="display: flex; justify-content: space-between; align-items: center;">
      <h3>Calculated Results</h3>
      <div class="unit-toggle">
        <span>Units: </span>
        <button type="button" id="btnSI" class="active" onclick="switchUnits('SI')">SI (kg/s, °C)</button>
        <button type="button" id="btnImperial" onclick="switchUnits('Imperial')">British (lb/h, °F)</button>
      </div>
    </div>

    <table>
      <thead>
        <tr>
          <th>Parameter</th>
          <th>Symbol</th>
          <th>Value (<span class="unit-label-mass">kg/s</span>)</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Steam Consumption</td>
          <td>$W_1$</td>
          <td id="res_W1">-</td>
        </tr>
        <tr>
          <td>Liquid Stream Effect I (Product)</td>
          <td>$L_1$</td>
          <td id="res_L1">-</td>
        </tr>
        <tr>
          <td>Liquid Stream Effect II</td>
          <td>$L_2$</td>
          <td id="res_L2">-</td>
        </tr>
        <tr>
          <td>Liquid Stream Effect III (Feed)</td>
          <td>$L_3$</td>
          <td id="res_L3">-</td>
        </tr>
        <tr>
          <td>Vapor Produced Effect I</td>
          <td>$V_1$</td>
          <td id="res_V1">-</td>
        </tr>
        <tr>
          <td>Vapor Produced Effect II</td>
          <td>$V_2$</td>
          <td id="res_V2">-</td>
        </tr>
        <tr>
          <td>Vapor Produced Effect III</td>
          <td>$V_3$</td>
          <td id="res_V3">-</td>
        </tr>
        <tr>
          <td><strong>Steam Economy</strong></td>
          <td><strong>Economy</strong></td>
          <td><strong id="res_Economy">-</strong> (kg evap / kg steam)</td>
        </tr>
      </tbody>
    </table>
  </div>

</div>

<script>
  let rawResults = {};
  let currentUnit = 'SI';

  function calculateEvaporator() {
    // Read input values
    const F1 = parseFloat(document.getElementById('F1').value);
    const xF1 = parseFloat(document.getElementById('xF1').value);
    const xS3 = parseFloat(document.getElementById('xS3').value);

    const Cp1 = parseFloat(document.getElementById('Cp1').value);
    const Cp2 = parseFloat(document.getElementById('Cp2').value);
    const Cp3 = parseFloat(document.getElementById('Cp3').value);

    const TF = parseFloat(document.getElementById('TF').value);
    const T1 = parseFloat(document.getElementById('T1').value);
    const T2 = parseFloat(document.getElementById('T2').value);
    const T3 = parseFloat(document.getElementById('T3').value);

    const lambdaW1 = parseFloat(document.getElementById('lambdaW1').value);
    const lambdaV1 = parseFloat(document.getElementById('lambdaV1').value);
    const lambdaV2 = parseFloat(document.getElementById('lambdaV2').value);

    // Overall Material Balance
    // Solute Balance: F1 * xF1 = L1 * xS3
    const L1 = (F1 * xF1) / xS3; // L1 = Product flow
    const totalEvaporation = F1 - L1; // V1 + V2 + V3

    // System of Equations for Backward Feed:
    // L3 = F1 (known)
    // V1 = L2 - L1  =>  L2 - V1 = L1
    // V2 = L3 - L2  =>  L2 + V2 = L3
    // V3 = F1 - L2 - V1 ...
    //
    // Energy Balance 1 (Effect I):
    // L2 * Cp2 * (T1 - T2) + W1 * lambdaW1 = V1 * lambdaV1
    // => W1 * lambdaW1 - V1 * lambdaV1 + L2 * Cp2 * (T1 - T2) = 0
    //
    // Energy Balance 2 (Effect II):
    // L3 * Cp3 * (T2 - T3) + V1 * lambdaV1 = V2 * lambdaV2
    // => V1 * lambdaV1 - V2 * lambdaV2 = - L3 * Cp3 * (T2 - T3)
    //
    // Since L3 = F1, V2 = F1 - L2
    // V1 * lambdaV1 - (F1 - L2) * lambdaV2 = - F1 * Cp3 * (T2 - T3)
    // V1 * lambdaV1 + L2 * lambdaV2 = F1 * lambdaV2 - F1 * Cp3 * (T2 - T3)

    const rhs2 = F1 * lambdaV2 - F1 * Cp3 * (T2 - T3);
    const V1 = (rhs2) / (lambdaV1 + lambdaV2); // Approximate solution based on mass flow coupling
    const L2 = F1 - V1; // Simplifying flow relation
    const V2 = F1 - L2;
    const V3 = totalEvaporation - V1 - V2;

    // Heat Balance for W1:
    // W1 * lambdaW1 = V1 * lambdaV1 - L2 * Cp2 * (T1 - T2)
    const heatReq = V1 * lambdaV1 - L2 * Cp2 * (T1 - T2);
    const W1 = heatReq / lambdaW1;

    const economy = (V1 + V2 + V3) / W1;

    // Save results in SI
    rawResults = {
      W1: W1,
      L1: L1,
      L2: L2,
      L3: F1,
      V1: V1,
      V2: V2,
      V3: V3,
      Economy: economy
    };

    document.getElementById('resultsBlock').style.display = 'block';
    updateDisplay();
  }

  function switchUnits(unit) {
    currentUnit = unit;
    document.getElementById('btnSI').classList.toggle('active', unit === 'SI');
    document.getElementById('btnImperial').classList.toggle('active', unit === 'Imperial');
    updateDisplay();
  }

  function updateDisplay() {
    if (!rawResults.W1) return;

    const isImperial = currentUnit === 'Imperial';
    const factor = isImperial ? 7936.64 : 1.0; // 1 kg/s = 7936.64 lb/h
    const unitText = isImperial ? 'lb/h' : 'kg/s';

    document.querySelectorAll('.unit-label-mass').forEach(el => el.textContent = unitText);

    document.getElementById('res_W1').textContent = (rawResults.W1 * factor).toFixed(4);
    document.getElementById('res_L1').textContent = (rawResults.L1 * factor).toFixed(4);
    document.getElementById('res_L2').textContent = (rawResults.L2 * factor).toFixed(4);
    document.getElementById('res_L3').textContent = (rawResults.L3 * factor).toFixed(4);
    document.getElementById('res_V1').textContent = (rawResults.V1 * factor).toFixed(4);
    document.getElementById('res_V2').textContent = (rawResults.V2 * factor).toFixed(4);
    document.getElementById('res_V3').textContent = (rawResults.V3 * factor).toFixed(4);
    document.getElementById('res_Economy').textContent = rawResults.Economy.toFixed(3);

    if (window.MathJax) {
      MathJax.typesetPromise();
    }
  }
</script>

</body>
</html>
