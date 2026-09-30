<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Tiltool App & Compliance Engine - S.N.N.</title>
  <!-- Tailwind CSS for styling -->
  <script src="https://tailwindcss.com"></script>
  <!-- PDF export module -->
  <script src="https://cloudflare.com"></script>
  <style>
    body {
      background-color: #f8fafc;
      font-family: system-ui, -apple-system, sans-serif;
    }
  </style>
</head>
<body class="p-4 sm:p-6 max-w-2xl mx-auto">

  <div id="app-content" class="bg-white rounded-xl shadow-lg border border-slate-200 overflow-hidden p-6">
    
    <!-- App Header -->
    <div class="bg-sky-600 -mx-6 -mt-6 p-6 text-white text-center mb-6">
      <h1 class="text-2xl font-bold">🧱 Tiltool App & Compliance Engine</h1>
      <p class="text-xs sm:text-sm text-sky-100 mt-1">Full Materials Calculator, SANS 10107 / 10400 Audit & Costing Suite</p>
    </div>

    <form id="tilingForm" onsubmit="event.preventDefault(); runEngine();">
      
      <!-- Section 1: Surface Parameters -->
      <div class="mb-6">
        <h2 class="text-md font-semibold text-sky-700 border-b border-slate-200 pb-1 mb-3">1. Surface Measurements (Meters)</h2>
        <div class="grid grid-cols-2 gap-4">
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Length (m)</label>
            <input type="number" id="length" step="0.01" value="5.0" required class="w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-sky-500">
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Width (m)</label>
            <input type="number" id="width" step="0.01" value="4.0" required class="w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-sky-500">
          </div>
        </div>
      </div>

      <!-- Section 2: Tile Profile Properties -->
      <div class="mb-6">
        <h2 class="text-md font-semibold text-sky-700 border-b border-slate-200 pb-1 mb-3">2. Tile Dimensions & Grout</h2>
        <div class="grid grid-cols-3 gap-2">
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Tile Length (mm)</label>
            <input type="number" id="tileLength" value="600" required class="w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-sky-500">
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Tile Width (mm)</label>
            <input type="number" id="tileWidth" value="600" required class="w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-sky-500">
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Grout Joint (mm)</label>
            <input type="number" id="groutWidth" step="0.5" value="3" required class="w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-sky-500">
          </div>
        </div>
      </div>

      <!-- Section 3: Job Environment & Financial Assumptions -->
      <div class="mb-6">
        <h2 class="text-md font-semibold text-sky-700 border-b border-slate-200 pb-1 mb-3">3. Application Profile & Financials</h2>
        <div class="grid grid-cols-2 gap-4 mb-3">
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Application Environment</label>
            <select id="environment" class="w-full p-2 border border-slate-300 rounded bg-white focus:ring-2 focus:ring-sky-500">
              <option value="indoor_dry">Indoor Dry (Residential)</option>
              <option value="indoor_wet">Indoor Wet (Shower/Bathroom)</option>
              <option value="outdoor">Exterior (Exposed Balcony/Patio)</option>
              <option value="commercial">Commercial High-Traffic</option>
            </select>
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Wastage Margin (%)</label>
            <input type="number" id="wastage" value="10" required class="w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-sky-500">
          </div>
        </div>
        <div class="grid grid-cols-2 gap-4">
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Tile Price (R per m²)</label>
            <input type="number" id="tileCost" value="250" required class="w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-sky-500">
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Labour Rate (R per m²)</label>
            <input type="number" id="labourCost" value="180" required class="w-full p-2 border border-slate-300 rounded focus:ring-2 focus:ring-sky-500">
          </div>
        </div>
      </div>

      <!-- Action Buttons -->
      <div class="grid grid-cols-2 gap-4 mb-4">
        <button type="submit" class="bg-sky-600 hover:bg-sky-700 text-white font-bold py-3 px-4 rounded-lg shadow-md transition duration-150 text-sm">
          Run Compliance Audit
        </button>
        <button type="button" onclick="saveProfile()" class="bg-slate-600 hover:bg-slate-700 text-white font-bold py-3 px-4 rounded-lg shadow-md transition duration-150 text-sm">
          💾 Save Project Profile
        </button>
      </div>
    </form>

    <!-- Output Dashboard -->
    <div id="results" class="hidden mt-8 border-t border-slate-200 pt-6">
      
      <!-- SANS Report Card -->
      <div id="sansCard" class="p-4 rounded-lg border mb-6 bg-slate-50">
        <h3 class="text-sm font-bold tracking-wide uppercase mb-2">🛡️ SANS 10107 / 10400 National Audit</h3>
        <p id="sansMessage" class="text-sm"></p>
        <ul id="sansRules" class="text-xs mt-2 list-disc list-inside text-slate-600 space-y-1"></ul>
      </div>

      <!-- Material Take-off Breakdown -->
      <h3 class="text-md font-semibold text-slate-800 mb-3">📊 Materials & Quantities Take-Off</h3>
      <div class="grid grid-cols-2 sm:grid-cols-4 gap-2 mb-6">
        <div class="bg-slate-100 p-3 rounded text-center">
          <span class="block text-[10px] uppercase font-bold text-slate-500">Net Area</span>
          <span id="outNetArea" class="text-lg font-bold text-slate-800">-</span>
        </div>
        <div class="bg-slate-100 p-3 rounded text-center">
          <span class="block text-[10px] uppercase font-bold text-slate-500">With Waste Margin</span>
          <span id="outGrossArea" class="text-lg font-bold text-slate-800">-</span>
        </div>
        <div class="bg-slate-100 p-3 rounded text-center">
          <span class="block text-[10px] uppercase font-bold text-slate-500">Est. Tile Units</span>
          <span id="outTileCount" class="text-lg font-bold text-slate-800">-</span>
        </div>
        <div class="bg-slate-100 p-3 rounded text-center">
          <span class="block text-[10px] uppercase font-bold text-slate-500">Adhesive Packs</span>
          <span id="outAdhesive" class="text-lg font-bold text-slate-800">-</span>
        </div>
      </div>

      <!-- Cost Table (ZAR) -->
      <h3 class="text-md font-semibold text-slate-800 mb-2">💰 Project Valuation (ZAR)</h3>
      <table class="w-full text-left border-collapse text-sm mb-6">
        <thead>
          <tr class="bg-slate-100 border-b border-slate-200">
            <th class="p-2 font-bold text-slate-700">Description</th>
            <th class="p-2 font-bold text-slate-700 text-right">Total Cost</th>
          </tr>
        </thead>
        <tbody class="divide-y divide-slate-100">
          <tr>
            <td class="p-2 text-slate-600">Tile Stock Volume Supply</td>
            <td id="rowTileCost" class="p-2 text-right text-slate-800 font-medium">-</td>
          </tr>
          <tr>
            <td class="p-2 text-slate-600">Premium Tile Adhesive (20kg Bags)</td>
            <td id="rowAdhesiveCost" class="p-2 text-right text-slate-800 font-medium">-</td>
          </tr>
          <tr>
            <td class="p-2 text-slate-600">Tiling Specialist Labor Subcontract</td>
            <td id="rowLabourCost" class="p-2 text-right text-slate-800 font-medium">-</td>
          </tr>
          <tr class="bg-sky-50 font-bold text-base text-sky-900 border-t border-sky-200">
            <td class="p-2">Total Project Valuation (Excl. VAT)</td>
            <td id="rowTotalCost" class="p-2 text-right">-</td>
          </tr>
        </tbody>
      </table>

      <!-- Export to PDF Button -->
      <button onclick="exportToPDF()" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-2 px-4 rounded shadow transition duration-150 text-sm">
        💾 Export Official Engine Report (PDF)
      </button>
    </div>

  </div>

  <script>
    // Load saved data when browser opens
    window.onload = function() {
      if(localStorage.getItem('tiltool_profile')) {
        const data = JSON.parse(localStorage.getItem('tiltool_profile'));
        document.getElementById('length').value = data.length;
        document.getElementById('width').value = data.width;
        document.getElementById('tileLength').value = data.tileLength;
        document.getElementById('tileWidth').value = data.tileWidth;
        document.getElementById('groutWidth').value = data.groutWidth;
        document.getElementById('environment').value = data.environment;
        document.getElementById('wastage').value = data.wastage;
        document.getElementById('tileCost').value = data.tileCost;
        document.getElementById('labourCost').value = data.labourCost;
        runEngine();
      }
    };

    function saveProfile() {
      const profileData = {
        length: document.getElementById('length').value,
        width: document.getElementById('width').value,
        tileLength: document.getElementById('tileLength').value,
        tileWidth: document.getElementById('tileWidth').value,
