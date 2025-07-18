<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Family Health Dashboard</title>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      margin: 0;
      padding: 2rem;
      background: #f5f8fa;
      color: #333;
    }
    h1 {
      text-align: center;
      color: #2c3e50;
      margin-bottom: 1rem;
    }
    .controls {
      text-align: center;
      margin-bottom: 1.5rem;
    }
    button {
      padding: 0.6rem 1.2rem;
      margin: 0.3rem;
      font-size: 1rem;
      background: #3498db;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      transition: background 0.3s;
    }
    button:hover {
      background: #2980b9;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      background: white;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      overflow: hidden;
    }
    th, td {
      padding: 14px;
      border: 1px solid #ddd;
      text-align: center;
    }
    th {
      background-color: #34495e;
      color: white;
    }
    tr:nth-child(even) {
      background-color: #f9f9f9;
    }
    td[contenteditable="true"] {
      background-color: #fffce8;
    }
    .footer {
      margin-top: 2rem;
      text-align: center;
      font-size: 0.9rem;
      color: #888;
    }
    .status-good {
      color: green;
      font-weight: bold;
    }
    .status-bad {
      color: red;
      font-weight: bold;
    }
    .tooltip {
      position: relative;
      cursor: help;
    }
    .tooltip:hover::after {
      content: attr(data-tooltip);
      position: absolute;
      background: #333;
      color: #fff;
      padding: 5px 8px;
      border-radius: 5px;
      font-size: 0.75rem;
      top: 120%;
      left: 50%;
      transform: translateX(-50%);
      white-space: nowrap;
      z-index: 1;
    }
  </style>
</head>
<body>
  <h1>Family Health Dashboard</h1>

  <div class="controls">
    <button onclick="addRow()">➕ Add Row</button>
    <button onclick="saveData()">💾 Save</button>
    <button onclick="loadData()">🔄 Restore</button>
    <button onclick="clearData()">🗑️ Clear Saved</button>
  </div>

  <table id="healthTable">
    <thead>
      <tr>
        <th>Date</th>
        <th>Name</th>
        <th>Sleep (hrs)</th>
        <th>HRV</th>
        <th>Resting HR</th>
        <th>Recovery (%)</th>
        <th>Strain</th>
        <th>Status</th>
      </tr>
    </thead>
    <tbody>
      <!-- Data will be inserted here -->
    </tbody>
  </table>

  <div class="footer">
    Data is editable and saved locally in your browser. Cloud sync is coming soon.
  </div>

  <script>
    const initialData = [
      { date: '2025-07-17', name: 'Dad', sleep: 7.5, hrv: 85, rhr: 52, recovery: 78, strain: 12.4 },
      { date: '2025-07-17', name: 'Mom', sleep: 8.2, hrv: 92, rhr: 48, recovery: 84, strain: 10.7 },
      { date: '2025-07-17', name: 'Kaye', sleep: 6.9, hrv: 76, rhr: 60, recovery: 66, strain: 13.0 }
    ];

    const tableBody = document.querySelector('#healthTable tbody');

    function evaluateHealth(entry) {
      const sleep = parseFloat(entry.sleep);
      const hrv = parseFloat(entry.hrv);
      const rhr = parseFloat(entry.rhr);
      const recovery = parseFloat(entry.recovery);
      const strain = parseFloat(entry.strain);

      const sleepGood = sleep >= 7;
      const hrvGood = hrv >= 70;
      const rhrGood = rhr <= 60;
      const recoveryGood = recovery >= 70;
      const strainOk = strain <= 14;

      const good = (sleepGood && hrvGood && rhrGood && recoveryGood && strainOk);
      const tooltip = `Sleep: ${sleep} | HRV: ${hrv} | RHR: ${rhr} | Recovery: ${recovery} | Strain: ${strain}`;

      return {
        status: good ? '✅ Good' : '⚠️ Needs Attention',
        className: good ? 'status-good' : 'status-bad',
        tooltip
      };
    }

    function renderTable(data) {
      tableBody.innerHTML = '';
      data.forEach(entry => addRow(entry));
    }

    function addRow(entry = {}) {
      const row = document.createElement('tr');
      const { status, className, tooltip } = evaluateHealth(entry);
      row.innerHTML = `
        <td contenteditable="true">${entry.date || ''}</td>
        <td contenteditable="true">${entry.name || ''}</td>
        <td contenteditable="true">${entry.sleep || ''}</td>
        <td contenteditable="true">${entry.hrv || ''}</td>
        <td contenteditable="true">${entry.rhr || ''}</td>
        <td contenteditable="true">${entry.recovery || ''}</td>
        <td contenteditable="true">${entry.strain || ''}</td>
        <td class="tooltip ${className}" data-tooltip="${tooltip}">${status}</td>
      `;
      tableBody.appendChild(row);
    }

    function saveData() {
      const rows = tableBody.querySelectorAll('tr');
      const saved = [];
      rows.forEach(row => {
        const cells = row.querySelectorAll('td');
        saved.push({
          date: cells[0].innerText,
          name: cells[1].innerText,
          sleep: cells[2].innerText,
          hrv: cells[3].innerText,
          rhr: cells[4].innerText,
          recovery: cells[5].innerText,
          strain: cells[6].innerText
        });
      });
      localStorage.setItem('healthData', JSON.stringify(saved));
      alert('✅ Data saved locally!');
    }

    function loadData() {
      const saved = localStorage.getItem('healthData');
      if (saved) {
        renderTable(JSON.parse(saved));
      } else {
        renderTable(initialData);
      }
    }

    function clearData() {
      localStorage.removeItem('healthData');
      renderTable(initialData);
    }

    window.onload = loadData;
  </script>
</body>
</html>
