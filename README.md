# EASI-TOOL<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="utf-8">
<title>EASIスコア計算ツール</title>
<meta name="viewport" content="width=device-width,initial-scale=1">
<style>
body{font-family:"Segoe UI",sans-serif;margin:1rem;background:#f4f7fa;color:#222;line-height:1.55}
h1{font-size:1.6rem;margin-bottom:.5rem}
table{width:100%;border-collapse:collapse;background:#fff;margin-bottom:1rem}
th,td{border:1px solid #b8c4d6;padding:.5rem;text-align:center;font-size:.95rem}
th{background:#e8eef5}
fieldset{border:1px solid #b8c4d6;padding:.8rem;margin-bottom:1rem;background:#fff}
legend{font-weight:bold;font-size:1rem;padding:0 .45rem}
select{padding:.25rem .45rem;font-size:.95rem}
button{padding:.6rem 1.3rem;font-size:.95rem;border:none;border-radius:6px;cursor:pointer}
button.calc{background:#0065c2;color:#fff;margin-right:.6rem}
button.clear{background:#949da7;color:#fff}
.result{font-size:1.3rem;font-weight:bold;margin-top:.5rem}
.coeff{font-weight:bold}
@media(min-width:640px){body{max-width:880px;margin:1rem auto}}
</style>
</head>
<body>

<h1>EASIスコア計算ツール</h1>

<fieldset>
  <legend>患者さんの年齢</legend>
  <label><input type="radio" name="age" value="child"> 7歳以下</label>
  <label style="margin-left:1rem"><input type="radio" name="age" value="adult" checked> 8歳以上</label>
  <p style="font-size:.85rem;margin-top:.35rem">※ 年齢を切り替えると係数が自動更新されます。</p>
</fieldset>

<table id="easi-table">
  <thead>
    <tr>
      <th>部位</th><th>紅斑</th><th>浮腫／丘疹</th><th>掻破痕</th><th>苔癬化</th>
      <th>面積</th><th>部位スコア</th><th>係数</th>
    </tr>
  </thead>
  <tbody>
    <tr data-region="hn">
      <td>頭頸部</td>
      <td><select class="sev"></select></td><td><select class="sev"></select></td>
      <td><select class="sev"></select></td><td><select class="sev"></select></td>
      <td><select class="area"></select></td><td class="rscore">0.0</td><td class="coeff coeff-hn">0.1</td>
    </tr>
    <tr data-region="tr">
      <td>体幹</td>
      <td><select class="sev"></select></td><td><select class="sev"></select></td>
      <td><select class="sev"></select></td><td><select class="sev"></select></td>
      <td><select class="area"></select></td><td class="rscore">0.0</td><td class="coeff coeff-tr">0.3</td>
    </tr>
    <tr data-region="ul">
      <td>上肢</td>
      <td><select class="sev"></select></td><td><select class="sev"></select></td>
      <td><select class="sev"></select></td><td><select class="sev"></select></td>
      <td><select class="area"></select></td><td class="rscore">0.0</td><td class="coeff coeff-ul">0.2</td>
    </tr>
    <tr data-region="ll">
      <td>下肢</td>
      <td><select class="sev"></select></td><td><select class="sev"></select></td>
      <td><select class="sev"></select></td><td><select class="sev"></select></td>
      <td><select class="area"></select></td><td class="rscore">0.0</td><td class="coeff coeff-ll">0.4</td>
    </tr>
  </tbody>
</table>

<button class="calc" id="calcBtn">計算する</button>
<button class="clear" id="clearBtn">クリア</button>

<div id="total" class="result">合計スコア: 0.0</div>

<script>
// ▼ オプション生成
const sevVals=[0,1,1.5,2,2.5,3], areaVals=[0,1,2,3,4,5,6];
document.querySelectorAll('select.sev').forEach(sel=>sevVals.forEach(v=>sel.innerHTML+=`<option value="${v}">${v}</option>`));
document.querySelectorAll('select.area').forEach(sel=>areaVals.forEach(v=>sel.innerHTML+=`<option value="${v}">${v}</option>`));

// ▼ 係数マップ
const coefAdult={hn:0.1,tr:0.3,ul:0.2,ll:0.4};
const coefChild={hn:0.2,tr:0.3,ul:0.2,ll:0.3};

function updateCoeffs(){
  const map=document.querySelector('input[name="age"]:checked').value==='child'?coefChild:coefAdult;
  Object.entries(map).forEach(([k,v])=>document.querySelector(`.coeff-${k}`).textContent=v.toFixed(1));
}

function clearInputs(){
  document.querySelectorAll('#easi-table select').forEach(sel=>sel.selectedIndex=0);
  document.querySelectorAll('.rscore').forEach(td=>td.textContent='0.0');
  document.getElementById('total').textContent='合計スコア: 0.0';
}

function calcScore(){
  const map=document.querySelector('input[name="age"]:checked').value==='child'?coefChild:coefAdult;
  let total=0;
  document.querySelectorAll('#easi-table tbody tr').forEach(row=>{
    const [e,i,ex,l,area]=[...row.querySelectorAll('select')].map(sel=>parseFloat(sel.value)||0);
    const coef=map[row.dataset.region];
    const rScore=(e+i+ex+l)*area*coef;
    row.querySelector('.rscore').textContent=rScore.toFixed(1);
    total+=rScore;
  });
  document.getElementById('total').textContent='合計スコア: '+total.toFixed(1);
}

// ▼ イベント登録
document.getElementById('calcBtn').addEventListener('click',calcScore);
document.getElementById('clearBtn').addEventListener('click',clearInputs);
document.querySelectorAll('input[name="age"]').forEach(r=>r.addEventListener('change',()=>{updateCoeffs();clearInputs();}));

// ▼ 初期化
updateCoeffs();
</script>

</body>
</html>
