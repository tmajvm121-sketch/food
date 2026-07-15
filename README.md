
{"id":1268870149,"name":"food","owner":{"login":"tmajvm121-sketch","type":"User","id":293485938},"default_branch":"main","visibility":"public","archived":false,"disabled":false,"fork":false,"permissions":{"admin":true,"maintain":true,"push":true,"triage":true,"pull":true},"size":9,"description":"식단구성","language":"HTML","pushed_at":"2026-06-14T03:22:35Z","topics":[]}
main
{"id":1268838746,"name":"new","owner":{"login":"tmajvm121-sketch","type":"User","id":293485938},"default_branch":"main","visibility":"public","archived":false,"disabled":false,"fork":false,"permissions":{"admin":true,"maintain":true,"push":true,"triage":true,"pull":true},"size":14,"description":null,"language":"HTML","pushed_at":"2026-06-14T02:01:38Z","topics":[]}
main
<!DOCTYPE html>

<html lang="ko">

<head>

<meta charset="UTF-8">

<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>식단 밸런스 체커</title>

<style>

  :root{

    --bg:#f5f7f2; --card:#ffffff; --ink:#26332a; --muted:#6b7a6f;

    --leaf:#3e7c4f; --leaf-soft:#e3efe6;

    --low:#3b7dd8; --ok:#3e7c4f; --high:#d8722c;

    --line:#d7e0d6;

  }

  *{box-sizing:border-box; margin:0; padding:0;}

  body{font-family:"Apple SD Gothic Neo","Malgun Gothic","맑은 고딕",sans-serif;

       background:var(--bg); color:var(--ink); padding:20px; font-size:18px;}

  .wrap{max-width:860px; margin:0 auto;}

  header{display:flex; align-items:baseline; gap:12px; flex-wrap:wrap; margin-bottom:6px;}

  h1{font-size:30px; letter-spacing:-0.5px;}

  h1 .dot{color:var(--leaf);}

  .sub{color:var(--muted); font-size:15px;}

  .banner{background:#fff6d9; border:1.5px solid #e5c85a; border-radius:10px;

          padding:10px 14px; font-size:15px; margin:10px 0 16px; display:none;}

  .card{background:var(--card); border:1.5px solid var(--line); border-radius:16px;

        padding:20px; margin-bottom:16px;}

  .card h2{font-size:19px; margin-bottom:14px; color:var(--leaf);}

  .gender{display:flex; gap:10px; margin-bottom:18px;}

  .gender button{flex:1; padding:14px; font-size:19px; border-radius:12px;

     border:2px solid var(--line); background:#fff; cursor:pointer; font-weight:700; color:var(--muted);}

  .gender button.on{border-color:var(--leaf); background:var(--leaf-soft); color:var(--leaf);}

  .grid{display:grid; grid-template-columns:repeat(auto-fit,minmax(230px,1fr)); gap:12px;}

  .field{border:1.5px solid var(--line); border-radius:12px; padding:12px 14px; background:#fbfdfa;}

  .field label{display:block; font-weight:700; font-size:16px; margin-bottom:8px;}

  .field input{width:100%; font-size:24px; padding:10px; border:1.5px solid var(--line);

     border-radius:10px; text-align:center; background:#fff;}

  .field input:focus{outline:3px solid var(--leaf-soft); border-color:var(--leaf);}

  .actions{display:flex; gap:10px; margin-top:18px;}

  .btn{flex:1; padding:16px; font-size:20px; font-weight:800; border-radius:12px;

       border:none; cursor:pointer;}

  .btn.main{background:var(--leaf); color:#fff;}

  .btn.ghost{background:#fff; border:2px solid var(--line); color:var(--muted);}

  /* 결과 */

  .row{margin-bottom:16px;}

  .row .top{display:flex; justify-content:space-between; align-items:baseline; margin-bottom:6px;}

  .row .name{font-weight:800; font-size:17px;}

  .row .verdict{font-size:15px; font-weight:800; padding:3px 10px; border-radius:999px;}

  .v-low{background:#e3edfb; color:var(--low);}

  .v-ok{background:var(--leaf-soft); color:var(--ok);}

  .v-high{background:#fbe9db; color:var(--high);}

  .track{position:relative; height:26px; background:#eef2ec; border-radius:8px; overflow:hidden;}

  .bar{position:absolute; left:0; top:0; bottom:0; border-radius:8px 0 0 8px;}

  .bar.low{background:var(--low);} .bar.ok{background:var(--ok);} .bar.high{background:var(--high);}

  .goal{position:absolute; top:-4px; bottom:-4px; width:3px; background:var(--ink); opacity:.55;}

  .row .note{font-size:15px; color:var(--muted); margin-top:5px;}

  .legend{font-size:14px; color:var(--muted); margin-top:6px;}

  footer{font-size:13px; color:var(--muted); text-align:center; margin:14px 0;}

  @media (prefers-reduced-motion: no-preference){ .bar{transition:width .5s ease;} }

</style>

</head>

<body>

<div class="wrap">

  <header>

    <h1>식단 밸런스 체커<span class="dot">.</span></h1>

    <span class="sub">나의 1일 평균 섭취 횟수를 입력하면 권장 기준과 비교해 줍니다</span>

  </header>



  <div class="banner" id="banner">

    ⚠ 교사 안내: 권장 섭취 횟수 기준값을 식사구성안 원문으로 확인·수정한 뒤,

    파일 안 <b>CONFIRMED</b> 값을 <b>true</b>로 바꾸면 이 안내가 사라집니다.

  </div>



  <div class="card">

    <h2>1. 나의 정보와 섭취 횟수 입력</h2>

    <div class="gender">

      <button type="button" id="btnM" onclick="setGender('male')">남학생</button>

      <button type="button" id="btnF" onclick="setGender('female')">여학생</button>

    </div>

    <div class="grid" id="inputs"></div>

    <div class="actions">

      <button class="btn main" type="button" onclick="check()">균형 확인하기</button>

      <button class="btn ghost" type="button" onclick="resetAll()">다시 입력</button>

    </div>

  </div>



  <div class="card" id="resultCard" style="display:none">

    <h2>2. 결과 — 권장 기준(세로선)과 비교해 보세요</h2>

    <div id="rows"></div>

    <div class="legend">파랑=부족(권장 미만) · 초록=적정 · 주황=과잉(권장 초과) · 세로선=권장 섭취 횟수</div>

  </div>



  <footer>입력값은 저장·전송되지 않으며 이 화면 안에서만 계산됩니다. · 권장 기준: 식사구성안(교사 설정값)</footer>

</div>



<script>

/* =========================================================

   ★ 교사 설정 구역 ★

   아래 권장 섭취 횟수는 임시 예시값입니다.

   반드시 식사구성안(2020 한국인 영양소 섭취기준 활용 자료)의

   청소년 권장 식사 패턴 원문 값으로 수정한 뒤 사용하세요.

   수정을 마치면 CONFIRMED 를 true 로 바꿔 주세요.

   ========================================================= */

const CONFIRMED = true;   // ← 기준값 확인 후 true 로 변경

const STANDARDS = {

  male:   { "곡류":3.5, "고기·생선·달걀·콩류":5.5, "채소류":8, "과일류":4, "우유·유제품류":2, "유지·당류":8 },

  female: { "곡류":3, "고기·생선·달걀·콩류":3.5, "채소류":7, "과일류":2, "우유·유제품류":2, "유지·당류":6 }

};

/* ========================================================= */



const GROUPS = Object.keys(STANDARDS.male);

let gender = null;



document.getElementById('banner').style.display = CONFIRMED ? 'none' : 'block';



const box = document.getElementById('inputs');

GROUPS.forEach((g,i)=>{

  const d = document.createElement('div');

  d.className = 'field';

  d.innerHTML = `<label for="g${i}">${g}</label>

    <input id="g${i}" type="number" min="0" max="20" step="0.5" inputmode="decimal" placeholder="회">`;

  box.appendChild(d);

});



function setGender(g){

  gender = g;

  document.getElementById('btnM').classList.toggle('on', g==='male');

  document.getElementById('btnF').classList.toggle('on', g==='female');

}



function comment(g, diff){

  if (diff < 0)  return `${g}이(가) 권장보다 부족해요. 어느 끼니에 더할 수 있을지 생각해 보세요.`;

  if (diff > 0)  return `${g}이(가) 권장보다 많아요. 줄일 수 있는 간식·메뉴를 찾아보세요.`;

  return `${g}은(는) 적정 범위예요. 지금처럼 유지해 보세요.`;

}



function check(){

  if(!gender){ alert('남학생/여학생을 먼저 선택해 주세요.'); return; }

  const std = STANDARDS[gender];

  const rows = document.getElementById('rows');

  rows.innerHTML = '';

  let anyInput = false;



  GROUPS.forEach((g,i)=>{

    const raw = document.getElementById('g'+i).value;

    if(raw === '') return;

    anyInput = true;

    const val = Math.max(0, parseFloat(raw) || 0);

    const goal = std[g];

    const maxScale = Math.max(goal*1.6, val*1.1, 1);

    const barPct  = Math.min(100, val/maxScale*100);

    const goalPct = Math.min(100, goal/maxScale*100);



    let state='ok', label='적정', diff=0;

    if(val < goal){ state='low'; label='부족'; diff=-1; }

    else if(val > goal){ state='high'; label='과잉'; diff=1; }



    const row = document.createElement('div');

    row.className='row';

    row.innerHTML = `

      <div class="top">

        <span class="name">${g}</span>

        <span class="verdict v-${state}">${label} · 나 ${val}회 / 권장 ${goal}회</span>

      </div>

      <div class="track">

        <div class="bar ${state}" style="width:0%"></div>

        <div class="goal" style="left:${goalPct}%"></div>

      </div>

      <div class="note">${comment(g, diff)}</div>`;

    rows.appendChild(row);

    requestAnimationFrame(()=>{ row.querySelector('.bar').style.width = barPct+'%'; });

  });



  if(!anyInput){ alert('섭취 횟수를 한 칸 이상 입력해 주세요.'); return; }

  document.getElementById('resultCard').style.display='block';

  document.getElementById('resultCard').scrollIntoView({behavior:'smooth'});

}



function resetAll(){

  GROUPS.forEach((g,i)=>document.getElementById('g'+i).value='');

  document.getElementById('rows').innerHTML='';

  document.getElementById('resultCard').style.display='none';

}

</script>

</body>

</html>

