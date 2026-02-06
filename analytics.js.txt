const trades = JSON.parse(localStorage.getItem("trades")) || [];

const rTrades = trades.map(t => ({
  ...t,
  R: t.profitLoss / t.risk
}));

const wins = rTrades.filter(t => t.R > 0);
const losses = rTrades.filter(t => t.R < 0);

const winRate = wins.length / (wins.length + losses.length || 1);

const avgWin = wins.reduce((a,b)=>a+b.R,0)/(wins.length||1);
const avgLoss = Math.abs(losses.reduce((a,b)=>a+b.R,0)/(losses.length||1));

const expectancy = (winRate * avgWin) - ((1-winRate) * avgLoss);

const pf = Math.abs(
  wins.reduce((a,b)=>a+b.R,0) /
  losses.reduce((a,b)=>a+b.R,0)
);

document.getElementById("trades").innerText = rTrades.length;
document.getElementById("winrate").innerText = (winRate*100).toFixed(1)+"%";
document.getElementById("expectancy").innerText = expectancy.toFixed(2)+"R";
document.getElementById("pf").innerText = pf.toFixed(2);

let cumR = 0;
const equity = [];
const labels = [];

rTrades.forEach(t => {
  cumR += t.R;
  equity.push(cumR);
  labels.push(new Date(t.date).toLocaleDateString());
});

new Chart(document.getElementById("equity"), {
  type: "line",
  data: {
    labels,
    datasets: [{
      data: equity,
      borderWidth: 2,
      tension: 0.3
    }]
  },
  options: { plugins:{legend:{display:false}} }
});
