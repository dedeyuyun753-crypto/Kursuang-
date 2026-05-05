# Kursuang-
Aplikasi konverter uang 
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Currency Converter Pro</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: 'Poppins', sans-serif;
      background: linear-gradient(135deg, #3a0ca3, #4361ee);
      display: flex;
      flex-direction: column;
      align-items: center;
      margin: 0;
      color: white;
    }

    .card {
      background: rgba(255,255,255,0.1);
      padding: 20px;
      border-radius: 20px;
      backdrop-filter: blur(10px);
      box-shadow: 0 10px 25px rgba(0,0,0,0.2);
      width: 320px;
      margin-top: 20px;
      text-align: center;
    }

    input, select, button {
      width: 100%;
      padding: 10px;
      margin: 6px 0;
      border-radius: 10px;
      border: none;
      font-size: 16px;
    }

    button {
      background: #f72585;
      color: white;
      cursor: pointer;
      font-weight: bold;
    }

    button:hover {
      background: #b5179e;
    }

    #hasil {
      margin-top: 10px;
      font-weight: bold;
    }

    .swap {
      background: #4cc9f0;
    }

    canvas {
      margin-top: 20px;
      background: white;
      border-radius: 10px;
    }

    ul {
      text-align: left;
      padding: 0;
      list-style: none;
      max-height: 120px;
      overflow-y: auto;
    }

    li {
      font-size: 14px;
      margin: 3px 0;
    }
  </style>
</head>
<body>

<div class="card">
  <h2>💱 Converter Pro</h2>

  <input type="number" id="jumlah" placeholder="Masukkan jumlah">

  <select id="from">
    <option value="IDR">IDR</option>
    <option value="JPY">JPY</option>
    <option value="USD">USD</option>
    <option value="EUR">EUR</option>
  </select>

  <button class="swap" onclick="swap()">🔄 Tukar</button>

  <select id="to">
    <option value="JPY">JPY</option>
    <option value="IDR">IDR</option>
    <option value="USD">USD</option>
    <option value="EUR">EUR</option>
  </select>

  <button onclick="konversi()">Konversi</button>

  <div id="hasil"></div>

  <h3>📊 Grafik Kurs</h3>
  <canvas id="chart"></canvas>

  <h3>📜 Riwayat</h3>
  <ul id="history"></ul>
</div>

<script>
let chart;

function swap() {
  let from = document.getElementById("from");
  let to = document.getElementById("to");

  let temp = from.value;
  from.value = to.value;
  to.value = temp;
}

async function konversi() {
  let jumlah = document.getElementById("jumlah").value;
  let from = document.getElementById("from").value;
  let to = document.getElementById("to").value;

  if (!jumlah) return alert("Isi jumlah dulu!");

  document.getElementById("hasil").innerHTML = "Loading...";

  try {
    let res = await fetch(`https://open.er-api.com/v6/latest/${from}`);
    let data = await res.json();

    let rate = data.rates[to];
    let hasil = jumlah * rate;

    let text = `${jumlah} ${from} = ${hasil.toFixed(2)} ${to}`;
    document.getElementById("hasil").innerHTML = text;

    tambahHistory(text);
    loadChart(from, to);

  } catch {
    document.getElementById("hasil").innerHTML = "Error ambil kurs";
  }
}

function tambahHistory(text) {
  let ul = document.getElementById("history");
  let li = document.createElement("li");
  li.textContent = text;
  ul.prepend(li);

  if (ul.children.length > 5) {
    ul.removeChild(ul.lastChild);
  }
}

async function loadChart(from, to) {
  try {
    let res = await fetch(`https://api.frankfurter.app/latest?from=${from}&to=${to}`);
    let data = await res.json();

    let labels = ["Hari ini"];
    let values = [data.rates[to]];

    if (chart) chart.destroy();

    let ctx = document.getElementById("chart").getContext("2d");

    chart = new Chart(ctx, {
      type: "line",
      data: {
        labels: labels,
        datasets: [{
          label: `${from} ke ${to}`,
          data: values,
          borderWidth: 2
        }]
      }
    });

  } catch {}
}
</script>

</body>
</html>
