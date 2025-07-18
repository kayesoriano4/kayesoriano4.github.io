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
    .footer {
      margin-top: 2rem;
      text-align: center;
      font-size: 0.9rem;
      color: #888;
    }
  </style>
</head>
<body>
  <h1>Family Health Dashboard</h1>

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
    Data synced from Google Sheets or JSON file. 
  </div>

  <script>
    // Example static data (replace with dynamic fetch later)
    const healthData = [
      { date: '2025-07-17', name: 'Dad', sleep: 7.5, hrv: 85, rhr: 52, recovery: 78, strain: 12.4 },
      { date: '2025-07-17', name: 'Mom', sleep: 8.2, hrv: 92, rhr: 48, recovery: 84, strain: 10.7 },
      { date: '2025-07-17', name: 'Kaye', sleep: 6.9, hrv: 76, rhr: 60, recovery: 66, strain: 13.0 }
    ];

    const tableBody = document.querySelector('#healthTable tbody');
    healthData.forEach(entry => {
      const row = document.createElement('tr');
      row.innerHTML = `
        <td>${entry.date}</td>
        <td>${entry.name}</td>
        <td>${entry.sleep}</td>
        <td>${entry.hrv}</td>
        <td>${entry.rhr}</td>
        <td>${entry.recovery}</td>
        <td>${entry.strain}</td>
      `;
      tableBody.appendChild(row);
    });
  </script>
</body>
</html>
