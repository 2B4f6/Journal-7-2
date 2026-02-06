const form = document.getElementById("trade-form");
const table = document.getElementById("trade-table");

let trades = JSON.parse(localStorage.getItem("trades")) || [];

function save() {
  localStorage.setItem("trades", JSON.stringify(trades));
}

function render() {
  table.innerHTML = "";
  trades.forEach((t, i) => {
    const r = (t.profitLoss / t.risk).toFixed(2);

    table.innerHTML += `
      <tr>
        <td>${new Date(t.date).toLocaleDateString()}</td>
        <td class="${t.profitLoss >= 0 ? "profit" : "loss"}">${t.profitLoss}</td>
        <td>${t.risk}</td>
        <td>${r}R</td>
        <td>${t.strategy || "-"}</td>
        <td><button onclick="removeTrade(${i})">X</button></td>
      </tr>
    `;
  });
}

form.onsubmit = e => {
  e.preventDefault();
  const data = new FormData(form);

  trades.push({
    date: new Date().toISOString(),
    profitLoss: +data.get("profitLoss"),
    risk: +data.get("risk"),
    strategy: data.get("strategy")
  });

  save();
  form.reset();
  render();
};

window.removeTrade = i => {
  trades.splice(i, 1);
  save();
  render();
};

render();
