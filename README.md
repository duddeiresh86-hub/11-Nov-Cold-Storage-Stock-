<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Cold Storage Stock Report</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/chartjs-plugin-datalabels"></script>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background-color: #f4fdfd;
    }

    header {
      background: linear-gradient(135deg, #00c6ff, #007f6d);
      padding: 20px 30px 15px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    }

    header h1 {
      margin: 0;
      color: #fff;
      font-size: 26px;
      text-shadow: 1px 1px 3px rgba(0,0,0,0.2);
    }

    #top-menu {
      display: flex;
      gap: 10px;
      margin-top: 15px;
      flex-wrap: wrap;
    }

    .cold-item {
      padding: 10px 18px;
      border: none;
      background-color: #fff;
      cursor: pointer;
      border-radius: 25px;
      font-weight: bold;
      color: #007f6d;
      border: 2px solid #b2e2d5;
      transition: all 0.3s ease;
    }

    .cold-item:hover {
      background-color: #e0f9f4;
      transform: scale(1.05);
      box-shadow: 0 3px 6px rgba(0,0,0,0.1);
    }

    .cold-item.active {
      background-color: #00a68c;
      color: #fff;
    }

    #content {
      padding: 30px;
    }

    .box {
      background-color: white;
      border-radius: 10px;
      padding: 20px;
      box-shadow: 0 0 10px rgba(0,0,0,0.05);
      margin-bottom: 30px;
    }

    h2 {
      margin-top: 0;
      color: #00665c;
      font-size: 22px;
      background: #e0f9f4;
      padding: 8px 15px;
      border-radius: 8px;
      display: inline-block;
    }

    h3 {
      color: #008060;
      margin-top: 20px;
      font-size: 18px;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 20px;
      border-radius: 10px;
      overflow: hidden;
      box-shadow: 0 2px 8px rgba(0,0,0,0.05);
    }

    th, td {
      border: 1px solid #ccc;
      padding: 10px;
      text-align: center;
    }

    th {
      background-color: #dff6f0;
      color: #004d40;
      font-weight: bold;
    }

    tr:nth-child(even) {
      background-color: #f9fdfc;
    }

    td.total {
      background: linear-gradient(135deg, #00796b, #004d40);
      color: #fff;
      font-weight: bold;
      font-size: 16px;
      box-shadow: inset 0 2px 4px rgba(0,0,0,0.2);
      letter-spacing: 0.5px;
    }

    .charts-row {
      display: flex;
      justify-content: space-around;
      gap: 20px;
      flex-wrap: wrap;
    }

    .chart-container {
      width: 400px;
      height: 400px;
      flex-shrink: 0;
    }

    canvas {
      max-width: 100%;
      max-height: 100%;
    }

    @media (max-width: 900px) {
      .charts-row {
        flex-direction: column;
        align-items: center;
      }

      .chart-container {
        width: 90vw;
        max-width: 400px;
        height: 300px;
        margin-bottom: 30px;
      }
    }

    .box table {
      display: block;
      overflow-x: auto;
      white-space: nowrap;
    }
  </style>
</head>
<body>

<header>
  <h1>Cold Storage Stock Report</h1>
  <div id="top-menu">
    <button class="cold-item active" onclick="showData('boraste', this)">Boraste</button>
    <button class="cold-item" onclick="showData('bandhan', this)">Bandhan</button>
    <button class="cold-item" onclick="showData('jk', this)">J K</button>
    <button class="cold-item" onclick="showData('hastai', this)">Hastai</button>
  </div>
</header>

<div id="content">
  <div class="box" id="data-box"></div>
  <div class="charts-row">
    <div class="chart-container">
      <canvas id="handChart"></canvas>
    </div>
    <div class="chart-container">
      <canvas id="kgChart"></canvas>
    </div>
  </div>
</div>

<script>
  const dataSet = {
    boraste: {
      name: "BORASTE COLD STOCK (11-11-2025)",
      kg13: { "4H": 19, "5H": 258, "6H": 1625, "8H": 513 },
      kg14: { "4H": 0, "5H": 0, "6H": 220, "8H": 110 },
      kg7: {},
      kg5: {}
    },
    bandhan: {
      name: "BANDHAN COLD STOCK (11-11-2025)",
      kg14: { "4H": 307, "5H": 1334, "6H": 1870, "8H": 606 },
      kg13: { "4H": 249, "5H": 504, "6H": 1121, "8H": 432 },
      kg5: { "3H": 9 },
      kg7: { "4H": 386, "5H": 739, "6H": 2931, "8H": 1268 }
    },
    jk: {
      name: "J K COLD STOCK (11-11-2025)",
      kg13: { "4H": 72, "5H": 268, "6H": 610, "8H": 186 },
      kg14: { "4H": 73, "5H": 534, "6H": 1632, "8H": 470 },
      kg7: {},
      kg5: {}
    },
    hastai: {
      name: "HASTAI COLD STOCK (11-11-2025)",
      kg13: {},
      kg14: { "4H": 72, "5H": 544, "6H": 707, "8H": 214 },
      kg7: {},
      kg5: {}
    }
  };

  let kgChartInstance;
  let handChartInstance;

  function showData(key, btn) {
    document.querySelectorAll('.cold-item').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');

    const storage = dataSet[key];
    const box = document.getElementById('data-box');
    box.innerHTML = `<h2>${storage.name}</h2>`;

    const kgTotals = {};
    const handTotals = {};
    const kgLabels = { kg13: "Roshana 13 KG", kg14: "Roshana 14 KG", kg7: "Laibaah 7 KG", kg5: "Haniya 5 KG" };
    const handOrder = ["3H","4H","5H","6H","8H"];

    for (let kg in kgLabels) {
      const data = storage[kg];
      if (Object.keys(data).length === 0) continue;

      const total = Object.values(data).reduce((a,b)=>a+b,0);
      kgTotals[kgLabels[kg]] = total;

      let html = `<h3>${kgLabels[kg]}</h3>
        <table>
          <tr>${handOrder.filter(h => data[h] !== undefined).map(h => `<th>${h}</th>`).join('')}</tr>
          <tr>${handOrder.filter(h => data[h] !== undefined).map(h => `<td>${data[h]}</td>`).join('')}</tr>
          <tr><td class="total" colspan="${Object.keys(data).length}">TOTAL: ${total}</td></tr>
        </table>`;
      box.innerHTML += html;

      for (let hand in data) {
        handTotals[hand] = (handTotals[hand] || 0) + data[hand];
      }
    }

    if (Object.keys(kgTotals).length === 0) {
      box.innerHTML += `<p>No data available for this cold storage.</p>`;
    }

    const grandTotal = Object.values(kgTotals).reduce((a,b)=>a+b,0);
    box.innerHTML += `<div style="
       background:#007f6d;
       color:white;
       padding:10px 15px;
       border-radius:8px;
       margin:15px 0;
       font-weight:bold;
       font-size:18px;">
       Grand Total: ${grandTotal} Boxes
     </div>`;

    const kgCtx = document.getElementById("kgChart").getContext("2d");
    if (kgChartInstance) kgChartInstance.destroy();

    kgChartInstance = new Chart(kgCtx, {
      type: "pie",
      data: {
        labels: Object.keys(kgTotals),
        datasets: [{
          data: Object.values(kgTotals),
          backgroundColor: ['#3498db', '#2ecc71', '#f1c40f', '#e67e22']
        }]
      },
      options: {
        responsive: true,
        plugins: {
          datalabels: { formatter: (value)=>value, color:'#fff', font:{weight:'bold', size:14} },
          title: { display:true, text:"Total Boxes by KG Category", font:{size:22,weight:'bold'} },
          legend: { position:'bottom' }
        }
      },
      plugins: [ChartDataLabels]
    });

    const handCtx = document.getElementById("handChart").getContext("2d");
    if (handChartInstance) handChartInstance.destroy();

    const handLabels = handOrder.filter(h => handTotals[h] !== undefined);
    const handData = handLabels.map(h => handTotals[h]);

    handChartInstance = new Chart(handCtx, {
      type: "bar",
      data: {
        labels: handLabels,
        datasets: [ {
          label: "Total Boxes",
          data: handData,
          backgroundColor: ['#3498db', '#2ecc71', '#f39c12', '#9b59b6', '#e74c3c']
        } ]
      },
      options: {
        responsive:true,
        maintainAspectRatio:false,
        plugins: {
          datalabels: { anchor:'end', align:'top', formatter:Math.round, font:{weight:'bold', size:12} },
          title: { display:true, text:"Total Boxes by Hand (H)", font:{size:22,weight:'bold'} },
          legend: { display:false }
        },
        scales: {
          x: { grid:{ display:false } },
          y: { beginAtZero:true, grid:{ display:false }, ticks:{ precision:0 } }
        }
      },
      plugins: [ChartDataLabels]
    });
  }

  window.onload = () => showData('boraste', document.querySelector('.cold-item'));
</script>

</body>
</html>
