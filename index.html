<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Análise de Viabilidade — RX Contrastado</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif; background: #f5f4f1; color: #1a1a1a; min-height: 100vh; padding: 1.5rem; font-size: 14px; }
  h1 { font-size: 18px; font-weight: 600; margin-bottom: 4px; }
  .subtitle { font-size: 12px; color: #6b6b6b; margin-bottom: 1.5rem; }
  .card { background: #fff; border: 1px solid #e5e3de; border-radius: 12px; }
  .proc-wrap { margin-bottom: 1.25rem; }
  .proc-label { font-size: 12px; color: #6b6b6b; margin-bottom: 8px; }
  .proc-btns { display: flex; flex-wrap: wrap; gap: 6px; }
  .pb { background: #f0ede8; border: 1px solid #ddd; border-radius: 8px; padding: 5px 11px; font-size: 11px; cursor: pointer; color: #555; white-space: nowrap; font-family: inherit; transition: all .15s; }
  .pb:hover { background: #e8e4de; }
  .pb.act { background: #fff; border-color: #333; color: #1a1a1a; font-weight: 600; }
  .layout { display: grid; grid-template-columns: 270px minmax(0,1fr); gap: 16px; margin-bottom: 16px; align-items: start; }
  .input-card { padding: 16px; }
  .input-card h2 { font-size: 13px; font-weight: 600; margin-bottom: 12px; }
  .cr { display: flex; align-items: center; gap: 8px; padding: 8px 0; border-bottom: 1px solid #f0ede8; }
  .cr:last-of-type { border-bottom: none; }
  .cr label { flex: 1; font-size: 12px; color: #555; }
  .cr input { width: 85px; text-align: right; border: 1px solid #ddd; border-radius: 6px; padding: 5px 8px; font-size: 13px; font-family: inherit; background: #fafaf8; transition: border-color .15s; }
  .cr input:focus { outline: none; border-color: #888; background: #fff; }
  .total-row { display: flex; justify-content: space-between; align-items: baseline; padding: 10px 0 0; border-top: 1px solid #e8e5e0; margin-top: 8px; }
  .total-label { font-size: 12px; font-weight: 600; }
  #l-tot { font-size: 22px; font-weight: 700; color: #c73232; }
  .ref-box { margin-top: 12px; padding: 10px; background: #f7f5f1; border-radius: 8px; }
  .ref-box p:first-child { font-size: 11px; color: #999; margin-bottom: 3px; }
  #l-ref { font-size: 11px; color: #555; line-height: 1.7; }
  .counters { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin-top: 12px; }
  .counter { border-radius: 8px; padding: 10px; text-align: center; }
  .c-ok { background: #eaf5ee; }
  .c-no { background: #fdeaea; }
  .counter p:first-child { font-size: 11px; margin-bottom: 3px; }
  .counter p:last-child { font-size: 24px; font-weight: 700; }
  .c-ok p { color: #1d6f3a; }
  .c-no p { color: #c73232; }
  .chart-card { padding: 16px; }
  .chart-card h2 { font-size: 13px; font-weight: 600; margin-bottom: 8px; }
  .legend { display: flex; gap: 16px; margin-bottom: 10px; font-size: 11px; color: #666; flex-wrap: wrap; }
  .legend span { display: flex; align-items: center; gap: 5px; }
  .dot { width: 10px; height: 10px; border-radius: 2px; display: inline-block; }
  #cw { position: relative; width: 100%; }
  .table-card { overflow: hidden; }
  .table-header { padding: 10px 16px; border-bottom: 1px solid #e5e3de; display: flex; justify-content: space-between; align-items: center; }
  .table-header p:first-child { font-size: 13px; font-weight: 600; }
  .table-header p:last-child { font-size: 11px; color: #999; }
  .table-wrap { overflow-x: auto; }
  table { width: 100%; border-collapse: collapse; font-size: 12px; table-layout: fixed; min-width: 500px; }
  colgroup col:nth-child(1) { width: 33%; }
  colgroup col:nth-child(2) { width: 17%; }
  colgroup col:nth-child(3) { width: 17%; }
  colgroup col:nth-child(4) { width: 16%; }
  colgroup col:nth-child(5) { width: 17%; }
  thead tr { background: #f7f5f1; }
  th { padding: 7px 16px; text-align: left; font-weight: 600; color: #666; font-size: 11px; text-transform: uppercase; letter-spacing: .3px; }
  th:not(:first-child) { text-align: right; }
  th:last-child { text-align: center; }
  tbody tr { border-bottom: 1px solid #f0ede8; transition: background .1s; }
  tbody tr:hover { background: #fafaf8; }
  td { padding: 8px 16px; }
  td:not(:first-child) { text-align: right; }
  td:last-child { text-align: center; }
  .badge { font-size: 11px; padding: 2px 9px; border-radius: 999px; font-weight: 600; display: inline-block; }
  .b-ok { background: #eaf5ee; color: #1d6f3a; }
  .b-wa { background: #fef3e2; color: #9a6000; }
  .b-no { background: #fdeaea; color: #c73232; }
  .note { margin-top: 1rem; padding: 12px 16px; background: #fef9ed; border: 1px solid #f0d890; border-radius: 10px; font-size: 12px; color: #7a5800; line-height: 1.6; }
  @media (max-width: 680px) {
    .layout { grid-template-columns: 1fr; }
    body { padding: 1rem; }
  }
</style>
</head>
<body>

<h1>Análise de Viabilidade — RX Contrastado</h1>
<p class="subtitle">PROIMAGEM Exames e Consultas · Insira os custos reais para identificar o piso mínimo de negociação com cada convênio</p>

<div class="proc-wrap">
  <p class="proc-label">Selecione o procedimento</p>
  <div class="proc-btns" id="btns"></div>
</div>

<div class="layout">
  <div class="card input-card">
    <h2>Custo real do procedimento</h2>
    <div class="cr"><label>Contraste / medicamento</label><input type="number" id="i-mat" min="0" step="10" oninput="go()"></div>
    <div class="cr"><label>Honorário médico</label><input type="number" id="i-hm" min="0" step="5" oninput="go()"></div>
    <div class="cr"><label>Custo operacional</label><input type="number" id="i-co" min="0" step="5" oninput="go()"></div>
    <div class="cr"><label>Filme / outros</label><input type="number" id="i-ot" min="0" step="5" oninput="go()"></div>
    <div class="total-row">
      <span class="total-label">Custo mínimo viável</span>
      <span id="l-tot">R$ 0</span>
    </div>
    <div class="ref-box">
      <p>Referência atual no sistema</p>
      <p id="l-ref"></p>
    </div>
    <div class="counters">
      <div class="counter c-ok"><p>Convênios viáveis</p><p id="l-ok">-</p></div>
      <div class="counter c-no"><p>Convênios inviáveis</p><p id="l-nk">-</p></div>
    </div>
  </div>

  <div class="card chart-card">
    <h2 id="cht-ttl"></h2>
    <div class="legend">
      <span><span class="dot" style="background:#2a8a55;"></span>Acima do custo</span>
      <span><span class="dot" style="background:#c73232;"></span>Abaixo do custo</span>
      <span><span style="display:inline-block;width:18px;border-top:2px dashed #e05a2b;vertical-align:middle;"></span>&nbsp;Custo mínimo</span>
    </div>
    <div id="cw" style="height:280px;"><canvas id="mc" aria-label="Gráfico comparativo de valores por convênio"></canvas></div>
  </div>
</div>

<div class="card table-card">
  <div class="table-header">
    <p>Detalhe por convênio</p>
    <p>Ordenado por valor pago (maior → menor)</p>
  </div>
  <div class="table-wrap">
    <table>
      <colgroup><col><col><col><col><col></colgroup>
      <thead>
        <tr>
          <th>Convênio</th>
          <th style="text-align:right;">Valor pago</th>
          <th style="text-align:right;">Custo mínimo</th>
          <th style="text-align:right;">Margem R$</th>
          <th style="text-align:center;">Situação</th>
        </tr>
      </thead>
      <tbody id="tbody"></tbody>
    </table>
  </div>
</div>

<p class="note">
  ⚠ <strong>Atenção:</strong> Os valores pré-carregados são referência do sistema atual (planilha 339). Atualize os campos com os custos reais praticados hoje — especialmente contraste e honorários — para uma análise precisa. O PETROBRAS na Urografia Excretora (R$ 119,32) provavelmente não inclui o material, que pode ser fornecido separadamente.
</p>

<script>
const procs=[
  {name:'Histerossalpingografia',ref:{hm:70,co:42,filme:7,material:890},conv:[
    ['ASSEC/CEHOP',997.52],['ASSEM',1003.00],['CAPESAUDE',992.32],['CASEMBRAPA',1017.79],
    ['CASSE',1013.77],['CASSIND',687.23],['CODEVASF',1055.13],['FACHESF',998.90],
    ['FUSEX',1030.00],['PARTICULAR Cartão',700],['PARTICULAR PIX',600],['PETROBRAS',1001.49],
    ['PLAN ASSISTE',988.89],['SAÚDE CAIXA',1030.87],['SMS',859],['VALE',959.02]
  ]},
  {name:'Urografia Excretora',ref:{hm:70,co:42,filme:16,material:679},conv:[
    ['ASSEC/CEHOP',792.68],['ASSEM',805.39],['CASEMBRAPA',819.52],['CASSE',882.54],
    ['CASSI',504.24],['CASSIND',350.50],['CODEVASF',859.06],['FACHESF',800.93],
    ['FUSEX',834.05],['PARTICULAR Cartão',350],['PARTICULAR PIX',320],['PETROBRAS',119.32],
    ['PLAN ASSISTE',784.90],['POSTAL SAÚDE',801.32],['SAÚDE CAIXA',828.25],['VALE',751.28]
  ]},
  {name:'Uretrocistografia Miccional',ref:{hm:70,co:42,filme:12,material:641},conv:[
    ['ASSEC/CEHOP',752.92],['ASSEM',765.04],['CAPESAUDE',730.32],['CASEMBRAPA',756.62],
    ['CASSE',850.22],['CASSIND',332.60],['CODEVASF',800.95],['FACHESF',760.79],
    ['FUSEX',794.01],['PARTICULAR Cartão',400],['PARTICULAR PIX',350],['PLAN ASSISTE',721.48],
    ['POSTAL SAÚDE',761.34],['SAÚDE CAIXA',764.93],['VALE',724.62]
  ]},
  {name:'Uretrocistografia Retrógrada',ref:{hm:70,co:42,filme:12,material:345},conv:[
    ['ASSEC/CEHOP',456.53],['ASSEM',468.65],['CAPESAUDE',440.72],['CASEMBRAPA',467.02],
    ['CASSE',517.31],['CASSIND',326.07],['CODEVASF',511.35],['FACHESF',464.40],
    ['FUSEX',497.62],['PARTICULAR Cartão',400],['PARTICULAR PIX',350],['PLAN ASSISTE',431.88],
    ['POSTAL SAÚDE',464.95],['SAÚDE CAIXA',475.33],['VALE',439.02]
  ]},
  {name:'Uretrocist. Miccional Criança',ref:{hm:95,co:45,filme:12,material:641},conv:[
    ['ASSEM',793.59],['CASEMBRAPA',789.44],['CASSE',879.75],['CASSIND',347.38],
    ['CODEVASF',831.22],['FACHESF',787.78],['PARTICULAR Cartão',800],['PARTICULAR PIX',750],
    ['PLAN ASSISTE',744.18],['SAÚDE CAIXA',799.01],['VALE',755.48]
  ]},
  {name:'Uretrocist. Retrógrada Criança',ref:{hm:95,co:45,filme:12,material:345},conv:[
    ['ASSEM',497.20],['CASEMBRAPA',499.84],['CASSE',546.83],['CASSIND',347.38],
    ['CODEVASF',541.62],['FACHESF',491.39],['PARTICULAR Cartão',800],['PARTICULAR PIX',750],
    ['PLAN ASSISTE',454.58],['SAÚDE CAIXA',509.41],['VALE',469.88]
  ]},
  {name:'Enema Opaco',ref:{hm:99,co:48,filme:22,material:166},conv:[
    ['CASSE',335.55],['PARTICULAR Cartão',350],['PARTICULAR PIX',350]
  ]},
  {name:'RX Esôfago-Estômago-Duodeno',ref:{hm:99,co:39,filme:17,material:59},conv:[
    ['ASSEC/CEHOP',300],['CASSE',214.88],['LIFE EMPRESARIAL',128.74],
    ['PARTICULAR Cartão',350],['PARTICULAR PIX',350]
  ]},
  {name:'Esofagograma',ref:{hm:45,co:25,filme:7,material:30},conv:[
    ['CASSE',106.43],['PARTICULAR Cartão',300],['PARTICULAR PIX',250]
  ]},
  {name:'RX Trânsito Intestinal',ref:{hm:72,co:39,filme:17,material:30},conv:[
    ['ASSEC/CEHOP',300],['CASSE',158.90],['PARTICULAR Cartão',350],
    ['PARTICULAR PIX',350],['PETROBRAS',155.85]
  ]},
  {name:'Fistulografia',ref:{hm:51,co:35,filme:9,material:305},conv:[
    ['FUSEX',400.75],['PARTICULAR Cartão',350],['PARTICULAR PIX',300],
    ['PETROBRAS',377.52],['VALE',362.59]
  ]},
  {name:'Sialografia',ref:{hm:80,co:41,filme:8,material:0},conv:[
    ['FUSEX',129.58],['PARTICULAR Cartão',360],['PARTICULAR PIX',350]
  ]}
];

let cur=0, myChart=null;

const costPlugin={
  id:'costLine',
  afterDraw(chart){
    const tot=getTotal();
    if(!tot) return;
    const {ctx,chartArea:{top,bottom},scales:{x}}=chart;
    const xp=x.getPixelForValue(tot);
    ctx.save();
    ctx.beginPath();
    ctx.setLineDash([6,4]);
    ctx.strokeStyle='#e05a2b';
    ctx.lineWidth=2;
    ctx.moveTo(xp,top);
    ctx.lineTo(xp,bottom);
    ctx.stroke();
    ctx.setLineDash([]);
    ctx.fillStyle='#e05a2b';
    ctx.font='500 10px -apple-system,sans-serif';
    ctx.textAlign='left';
    ctx.fillText('Custo mín.',xp+4,top+14);
    ctx.restore();
  }
};

const btnC=document.getElementById('btns');
procs.forEach((p,i)=>{
  const b=document.createElement('button');
  b.className='pb'+(i===0?' act':'');
  b.textContent=p.name;
  b.onclick=()=>sel(i);
  btnC.appendChild(b);
});

function sel(i){
  cur=i;
  document.querySelectorAll('.pb').forEach((b,idx)=>b.className='pb'+(idx===i?' act':''));
  const p=procs[i];
  document.getElementById('i-mat').value=p.ref.material;
  document.getElementById('i-hm').value=p.ref.hm;
  document.getElementById('i-co').value=p.ref.co;
  document.getElementById('i-ot').value=p.ref.filme;
  document.getElementById('cht-ttl').textContent=p.name+' — valores por convênio';
  const rt=p.ref.hm+p.ref.co+p.ref.filme+p.ref.material;
  document.getElementById('l-ref').innerHTML=
    `HM: R$${p.ref.hm} &nbsp;|&nbsp; CO: R$${p.ref.co} &nbsp;|&nbsp; Filme: R$${p.ref.filme} &nbsp;|&nbsp; Material: R$${p.ref.material}<br>Total no sistema: <strong>R$${rt.toFixed(2)}</strong>`;
  go();
}

function getTotal(){
  return ['i-mat','i-hm','i-co','i-ot'].reduce((s,id)=>s+(parseFloat(document.getElementById(id).value)||0),0);
}

function go(){
  const p=procs[cur];
  const tot=getTotal();
  document.getElementById('l-tot').textContent='R$ '+tot.toFixed(2);
  const sorted=[...p.conv].sort((a,b)=>b[1]-a[1]);
  const labels=sorted.map(([k])=>k);
  const vals=sorted.map(([,v])=>v);
  const colors=vals.map(v=>v>=tot?'#2a8a55':'#c73232');
  const ok=vals.filter(v=>v>=tot).length;
  document.getElementById('l-ok').textContent=ok;
  document.getElementById('l-nk').textContent=vals.length-ok;

  const h=Math.max(220,sorted.length*38+60);
  document.getElementById('cw').style.height=h+'px';

  const ctx=document.getElementById('mc').getContext('2d');
  if(myChart) myChart.destroy();
  myChart=new Chart(ctx,{
    type:'bar',
    plugins:[costPlugin],
    data:{
      labels,
      datasets:[{
        label:'Valor convênio',
        data:vals,
        backgroundColor:colors,
        borderRadius:3,
        indexAxis:'y',
      }]
    },
    options:{
      indexAxis:'y',
      responsive:true,
      maintainAspectRatio:false,
      plugins:{
        legend:{display:false},
        tooltip:{
          callbacks:{
            label:(c)=>{
              const v=c.raw,m=v-tot,pct=tot>0?(m/tot*100).toFixed(1):0;
              return [`R$ ${v.toFixed(2)}`,`Margem: ${m>=0?'+':''}R$${m.toFixed(2)} (${m>=0?'+':''}${pct}%)`];
            }
          }
        }
      },
      scales:{
        x:{ticks:{callback:v=>'R$'+v.toFixed(0),font:{size:11}},grid:{color:'rgba(0,0,0,0.05)'}},
        y:{ticks:{font:{size:11}},grid:{display:false}}
      }
    }
  });

  const tb=document.getElementById('tbody');
  tb.innerHTML='';
  sorted.forEach(([name,val])=>{
    const m=val-tot,pct=tot>0?(m/tot*100):0;
    const mc=m>=0?'#1d6f3a':'#c73232';
    const badge=m<0
      ?'<span class="badge b-no">Inviável</span>'
      :pct<8
        ?'<span class="badge b-wa">Margem estreita</span>'
        :'<span class="badge b-ok">Viável</span>';
    tb.innerHTML+=`<tr>
      <td>${name}</td>
      <td style="font-weight:600;">R$ ${val.toFixed(2)}</td>
      <td style="color:#888;">R$ ${tot.toFixed(2)}</td>
      <td style="color:${mc};font-weight:600;">${m>=0?'+':''}R$ ${Math.abs(m).toFixed(2)}</td>
      <td>${badge}</td>
    </tr>`;
  });
}

sel(0);
</script>
</body>
</html>
