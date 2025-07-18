<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Family Health Dashboard</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 1rem;
      background: #f4f4f4;
    }
    h1 {
      text-align: center;
      color: #2c3e50;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      background: white;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    th, td {
      padding: 12px;
      border: 1px solid #ddd;
      text-align: center;
    }
    th {
      background-color: #2c3e50;
      color: white;
    }
    tr:nth-child(even) {
      background-color: #f9f9f9;
    }
    td[contenteditable="true"] {
      background-color: #fffbe6;
    }
    .footer {
      margin-top: 2rem;
      text-align: center;
      font-size: 0.9rem;
      color: #888;
    }
    .controls {
      text-align: center;
      margin: 1rem 0;
    }
    button {
      padding: 0.5rem 1rem;
      margin: 0.2rem;
      font-size: 1rem;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <h1>Family Health Dashboard</h1>

  <div class="controls">
    <button onclick="addRow()">Add Row</button>
    <button onclick="saveData()">Save</button>
    <button onclick="loadData()">Restore</button>
    <button onclick="clearData()">Clear Saved</button>
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
      </tr>
    </thead>
    <tbody>
      <!-- Data will be inserted here -->
    </tbody>
  </table>

  <div class="footer">
    Data manually editable. Saved locally in browser.
  </div>

  <script>
    const initialData = [
      { date: '2025-07-17', name: 'Dad', sleep: 7.5, hrv: 85, rhr: 52, recovery: 78, strain: 12.4 },
      { date: '2025-07-17', name: 'Mom', sleep: 8.2, hrv: 92, rhr: 48, recovery: 84, strain: 10.7 },
      { date: '2025-07-17', name: 'Kaye', sleep: 6.9, hrv: 76, rhr: 60, recovery: 66, strain: 13.0 }
    ];

    const tableBody = document.querySelector('#healthTable tbody');

    function renderTable(data) {
      tableBody.innerHTML = '';
      data.forEach(entry => addRow(entry));
    }

    function addRow(entry = {}) {
      const row = document.createElement('tr');
      row.innerHTML = `
        <td contenteditable="true">${entry.date || ''}</td>
        <td contenteditable="true">${entry.name || ''}</td>
        <td contenteditable="true">${entry.sleep || ''}</td>
        <td contenteditable="true">${entry.hrv || ''}</td>
        <td contenteditable="true">${entry.rhr || ''}</td>
        <td contenteditable="true">${entry.recovery || ''}</td>
        <td contenteditable="true">${entry.strain || ''}</td>
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
      alert('Data saved locally!');
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

    // Load on page load
    window.onload = loadData;
  </script>
</body>
</html>
