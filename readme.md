<!doctype html>
<html lang="ko">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>플라스틱 한 번 더 생각하기 — 디자인 프로토타입</title>
<style>
  :root{
    --bg:#f6fbf9;
    --card:#ffffff;
    --accent:#0f7a57;
    --accent-2:#20a085;
    --muted:#6b7785;
    --glass: rgba(255,255,255,0.85);
    --shadow: 0 8px 24px rgba(20,30,40,0.06);
    font-family: "Noto Sans KR", system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
  }
  *{box-sizing:border-box}
  body{margin:0;background:linear-gradient(180deg,var(--bg),#eef7f2);color:#112226;line-height:1.45}
  header{background:linear-gradient(90deg,var(--accent),var(--accent-2));color:white;padding:20px 18px}
  header .wrap{max-width:1100px;margin:0 auto;display:flex;align-items:center;gap:18px;flex-wrap:wrap}
  h1{margin:0;font-size:20px;letter-spacing:-0.5px}
  p.lead{margin:4px 0 0;color:rgba(255,255,255,0.92);font-size:13px}
  main{max-width:1100px;margin:20px auto;padding:16px}
  .grid{display:grid;grid-template-columns:1fr;gap:14px}
  @media(min-width:980px){.grid{grid-template-columns: 1fr 420px}}
  .card{background:var(--card);border-radius:12px;padding:14px;box-shadow:var(--shadow)}
  .section-title{display:flex;align-items:center;justify-content:space-between;gap:10px;margin-bottom:10px}
  .section-title h2{margin:0;font-size:16px}
  .muted{color:var(--muted);font-size:13px}
  /* Icon buttons */
  .icons{display:flex;flex-wrap:wrap;gap:10px}
  .icon-btn{
    width:120px;height:88px;border-radius:10px;background:var(--glass);display:flex;flex-direction:column;align-items:center;justify-content:center;gap:6px;border:1px solid rgba(0,0,0,0.04);cursor:pointer;transition:transform .12s ease, box-shadow .12s;
  }
  .icon-btn:hover{transform:translateY(-6px);box-shadow:0 14px 30px rgba(10,40,30,0.08)}
  .icon-btn.active{outline:3px solid rgba(15,122,87,0.12);transform:translateY(-6px)}
  .icon-label{font-size:13px;color:#0b2b24;font-weight:600}
  .count-badge{background:#fff;border-radius:999px;padding:4px 8px;font-size:12px;border:1px solid rgba(0,0,0,0.05)}
  /* stats */
  .stats{display:flex;gap:10px;align-items:center;flex-wrap:wrap}
  .stat{flex:1;min-width:120px;background:linear-gradient(180deg,#fbfffb,#f0fff7);padding:10px;border-radius:10px;border:1px solid rgba(10,80,50,0.04)}
  .stat strong{display:block;font-size:18px;color:#0f4c3a}
  .chart-wrap{display:flex;flex-direction:column;align-items:center;gap:8px}
  canvas{max-width:100%}
  /* checklist */
  .checklist{display:flex;flex-direction:column;gap:8px}
  .chk{display:flex;align-items:center;gap:8px;background:#f7fffa;padding:8px;border-radius:8px;border:1px solid rgba(10,80,50,0.04)}
  .chk input{width:18px;height:18px}
  .mini {font-size:13px;color:var(--muted)}
  .controls{display:flex;gap:8px;flex-wrap:wrap}
  button.btn{background:var(--accent);color:white;border:none;padding:8px 12px;border-radius:10px;cursor:pointer;font-weight:700}
  button.ghost{background:transparent;border:1px solid rgba(0,0,0,0.06);color:#123;cursor:pointer;padding:8px 10px;border-radius:10px}
  footer{max-width:1100px;margin:18px auto;padding:8px 16px;color:var(--muted);font-size:13px}
  .note{background:#fff9; padding:8px;border-radius:8px;border:1px dashed rgba(10,80,50,0.06)}
  .flex{display:flex;gap:10px;align-items:center}
  .right-col{position:sticky;top:18px}
  .suggest{background:linear-gradient(180deg,#fff,#f3fff6);padding:10px;border-radius:10px;border:1px solid rgba(10,80,50,0.04)}
  .export-area textarea{width:100%;height:120px;border-radius:8px;border:1px solid #eef6f0;padding:10px}
  .small{font-size:12px;color:var(--muted)}
  .reset{background:#fff;border:1px solid rgba(200,0,0,0.06);color:#c33;padding:8px 10px;border-radius:8px}
</style>
</head>
<body>
<header>
  <div class="wrap">
    <div>
      <h1>플라스틱 한 번 더 생각하기</h1>
      <p class="lead">디자인 관점의 인터랙티브 프로토타입 — 오늘 당신의 일회용 습관을 시각화하고 작은 실천을 제안합니다.</p>
    </div>
    <div style="margin-left:auto" class="small">저장: 브라우저(localStorage)</div>
  </div>
</header>

<main>
  <div class="grid">
    <!-- LEFT: main interactions -->
    <section class="card">
      <div class="section-title">
        <h2>오늘의 일회용품 기록하기</h2>
        <div class="muted">아이콘을 눌러 오늘 사용한 수량을 기록하세요</div>
      </div>

      <!-- icons -->
      <div class="icons" id="icons">
        <!-- buttons inserted by JS -->
      </div>

      <!-- stats + chart -->
      <div style="margin-top:14px" class="stats">
        <div class="stat">
          <div class="mini">오늘 총 사용</div>
          <strong id="totalToday">0</strong>
          <div class="small">일회용품 총 개수</div>
        </div>
        <div class="stat chart-wrap">
          <canvas id="pie" width="240" height="240" aria-label="사용 비율 차트"></canvas>
          <div class="small">종류별 사용 비율</div>
        </div>
      </div>

      <!-- suggestion -->
      <div style="margin-top:14px">
        <div class="section-title">
          <h2>오늘의 작은 제안</h2>
          <div class="muted">당신의 기록을 바탕으로 제안합니다</div>
        </div>
        <div class="suggest" id="suggestBox">
          <strong id="suggestTitle">아직 기록이 없어요 — 작은 행동부터 시작해볼까요?</strong>
          <div class="small" id="suggestText">텀블러 챙기기, 일회용 빨대 거절하기 등 간단한 실천을 추천합니다.</div>
        </div>
      </div>

      <!-- checklist -->
      <div style="margin-top:14px">
        <div class="section-title">
          <h2>내가 할 수 있는 행동 체크리스트</h2>
          <div class="muted">체크하면 실천 점수가 올라갑니다</div>
        </div>
        <div class="checklist" id="checklist">
          <label class="chk"><input type="checkbox" data-key="bring_mug"> 텀블러/머그 사용하기</label>
          <label class="chk"><input type="checkbox" data-key="reuse_bag"> 장바구니/에코백 챙기기</label>
          <label class="chk"><input type="checkbox" data-key="refuse_straw"> 빨대 거절하기</label>
        </div>
        <div style="margin-top:10px" class="flex">
          <div class="note"><strong>실천 점수: </strong> <span id="score">0</span> / 3</div>
          <div style="margin-left:auto" class="small muted">체크는 하루 단위로 리셋 가능합니다</div>
        </div>
      </div>

      <!-- controls -->
      <div style="margin-top:14px" class="controls">
        <button id="saveBtn" class="btn">오늘 저장</button>
        <button id="resetDay" class="ghost">오늘 초기화</button>
        <button id="exportBtn" class="ghost">제출용 텍스트 만들기</button>
        <button id="resetAll" class="reset">모든 데이터 삭제</button>
      </div>

      <div style="margin-top:12px" class="small muted">이 프로토타입은 디자인(시각화·UX) 관점으로 제작되었습니다. 데이터는 브라우저 로컬에 저장됩니다.</div>
    </section>

    <!-- RIGHT: sidebar -->
    <aside class="right-col">
      <div class="card">
        <div class="section-title"><h2>요약</h2><div class="muted">당신의 최근 기록</div></div>
        <div style="display:flex;flex-direction:column;gap:8px">
          <div class="mini"><strong>오늘 사용 상세</strong></div>
          <div id="todayDetail" class="small muted">아직 기록이 없습니다.</div>
          <hr/>
          <div class="mini"><strong>누적(저장된 날짜)</strong></div>
          <div id="historySummary" class="small muted">데이터가 없습니다.</div>
        </div>
      </div>

      <div style="margin-top:12px" class="card">
        <div class="section-title"><h2>인포그래픽 예시 문구</h2><div class="muted">디자인 제출용 텍스트</div></div>
        <div class="small">
          <p><strong>메인 카피:</strong><br>
          “나의 하루가 지구에 남긴 흔적 — 작은 선택이 큰 변화를 만듭니다.”</p>
          <p><strong>그래픽 라인:</strong><br>
          생산 → 사용 → 폐기: 각 단계에 아이콘을 배치해 흐름을 보여줍니다.</p>
        </div>
      </div>

      <div style="margin-top:12px" class="card">
        <div class="section-title"><h2>업로드 · 출력</h2><div class="muted">제출 파일 생성</div></div>
        <div class="export-area">
          <textarea id="exportText" readonly placeholder="제출용 텍스트 영역 — '제출용 만들기'를 누르면 생성됩니다."></textarea>
          <div style="margin-top:8px;display:flex;gap:8px">
            <button id="downloadTxt" class="btn">텍스트 다운로드</button>
            <button id="copyTxt" class="ghost">클립보드 복사</button>
          </div>
        </div>
      </div>

    </aside>
  </div>
</main>

<footer>
  <div style="max-width:1100px;margin:0 auto">
    <div class="small muted">출처 표기 예: 통계 데이터는 공개 자료를 참고(예: 환경부 사이트). HTML/CSS/JS 프로토타입 — 디자인 계열 과제 제출용.</div>
  </div>
</footer>

<script>
/* ====== 데이터 구조 & 초기값 ====== */
const ITEMS = [
  {key:'paper_cup', label:'종이컵', icon: renderCupSVG('#0f7a57')},
  {key:'plastic_cup', label:'플라스틱 컵', icon: renderCupSVG('#208b6f')},
  {key:'straw', label:'빨대', icon: renderStrawSVG('#2aa88b')},
  {key:'plastic_bag', label:'비닐봉지', icon: renderBagSVG('#1ca27e')},
  {key:'cutlery', label:'일회용 수저', icon: renderForkSVG('#18a37a')}
];

const STORAGE_KEY = 'design_ecoprototype_v1';
const TODAY_KEY = dateId(new Date());

/* ====== UI 요소 참조 ====== */
const iconsWrap = document.getElementById('icons');
const totalTodayEl = document.getElementById('totalToday');
const pieCanvas = document.getElementById('pie');
const suggestTitle = document.getElementById('suggestTitle');
const suggestText = document.getElementById('suggestText');
const checklist = document.getElementById('checklist');
const scoreEl = document.getElementById('score');
const saveBtn = document.getElementById('saveBtn');
const resetDayBtn = document.getElementById('resetDay');
const resetAllBtn = document.getElementById('resetAll');
const exportBtn = document.getElementById('exportBtn');
const todayDetail = document.getElementById('todayDetail');
const historySummary = document.getElementById('historySummary');
const exportText = document.getElementById('exportText');
const downloadTxt = document.getElementById('downloadTxt');
const copyTxt = document.getElementById('copyTxt');

/* ====== 로컬 상태 ====== */
let state = loadState(); // shape: {days: {dateId: {paper_cup: n,...}}, checks: {dateId: {bring_mug:true,...}} }

/* ====== 초기 렌더링 ====== */
renderIconButtons();
renderToday();
renderChart();
renderSummary();

/* ====== 이벤트 바인딩 ====== */
saveBtn.addEventListener('click', saveToday);
resetDayBtn.addEventListener('click', ()=>{
  if(confirm('오늘의 기록을 초기화하시겠어요?')){
    clearToday();
    renderToday(); renderChart(); renderSummary();
  }
});
resetAllBtn.addEventListener('click', ()=>{
  if(confirm('모든 저장 데이터를 삭제합니다. 계속할까요?')){
    localStorage.removeItem(STORAGE_KEY);
    state = loadState();
    renderToday(); renderChart(); renderSummary(); alert('데이터가 삭제되었습니다.');
  }
});
exportBtn.addEventListener('click', generateExport);
downloadTxt.addEventListener('click', downloadExport);
copyTxt.addEventListener('click', copyExport);

/* 체크리스트 동작 */
checklist.querySelectorAll('input[type=checkbox]').forEach(ch=>{
  ch.addEventListener('change', e=>{
    const key = ch.dataset.key;
    const val = ch.checked;
    const d = TODAY_KEY;
    if(!state.checks) state.checks = {};
    if(!state.checks[d]) state.checks[d] = {};
    state.checks[d][key] = val;
    saveState();
    updateScore();
  });
});

/* ====== 함수 ====== */

function dateId(date){
  const y = date.getFullYear();
  const m = String(date.getMonth()+1).padStart(2,'0');
  const d = String(date.getDate()).padStart(2,'0');
  return `${y}-${m}-${d}`;
}
function loadState(){
  try{
    const raw = localStorage.getItem(STORAGE_KEY);
    if(!raw) return {days:{}, checks:{}};
    return JSON.parse(raw);
  }catch(e){ return {days:{}, checks:{}}; }
}
function saveState(){
  localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
}

function renderIconButtons(){
  iconsWrap.innerHTML = '';
  ITEMS.forEach(it=>{
    const btn = document.createElement('button');
    btn.className = 'icon-btn';
    btn.type = 'button';
    btn.innerHTML = `<div style="width:48px;height:40px">${it.icon}</div>
                     <div class="icon-label">${it.label}</div>
                     <div class="count-badge" data-key="${it.key}">0</div>`;
    btn.addEventListener('click', ()=>{ incrementItem(it.key); });
    iconsWrap.appendChild(btn);
  });
  updateIconCounts();
}

function getTodayCounts(){
  const d = TODAY_KEY;
  if(!state.days) state.days = {};
  if(!state.days[d]) state.days[d] = {};
  return state.days[d];
}

function incrementItem(key){
  const d = TODAY_KEY;
  if(!state.days) state.days = {};
  if(!state.days[d]) state.days[d] = {};
  const obj = state.days[d];
  obj[key] = (obj[key]||0) + 1;
  saveState();
  updateIconCounts();
  renderToday();
  renderChart();
  autoSuggest();
}

function updateIconCounts(){
  const today = getTodayCounts();
  document.querySelectorAll('.count-badge').forEach(b=>{
    const key = b.dataset.key;
    b.textContent = (today[key] || 0);
    const btn = b.closest('.icon-btn');
    if((today[key]||0) > 0) btn.classList.add('active'); else btn.classList.remove('active');
  });
}

function renderToday(){
  const today = getTodayCounts();
  const total = Object.values(today).reduce((s,n)=>s+(Number(n)||0),0);
  totalTodayEl.textContent = total;
  // detail text
  if(total === 0){
    todayDetail.textContent = '아직 기록이 없습니다. 아이콘을 눌러 오늘 사용한 일회용품을 기록해보세요.';
  } else {
    todayDetail.innerHTML = Object.entries(today).map(([k,v])=>{
      const label = ITEMS.find(i=>i.key===k)?.label||k;
      return `<div style="display:flex;justify-content:space-between"><div>${label}</div><div>${v}개</div></div>`;
    }).join('');
  }
  updateScore();
  updateIconCounts();
  autoSuggest();
}

function clearToday(){
  const d = TODAY_KEY;
  if(state.days && state.days[d]) delete state.days[d];
  if(state.checks && state.checks[d]) delete state.checks[d];
  saveState();
}

/* simple pie chart draw */
function renderChart(){
  const ctx = pieCanvas.getContext('2d');
  const w = pieCanvas.width;
  const h = pieCanvas.height;
  ctx.clearRect(0,0,w,h);
  const dataObj = getTodayCounts();
  const entries = ITEMS.map(it=>({k:it.key,v:dataObj[it.key]||0, color: randomColorForKey(it.key)})).filter(e=>e.v>0);
  const total = entries.reduce((s,e)=>s+e.v,0);
  if(total === 0){
    // placeholder
    ctx.fillStyle = '#eefaf4';
    ctx.beginPath(); ctx.arc(w/2,h/2,90,0,Math.PI*2); ctx.fill();
    ctx.fillStyle = '#0f7a57'; ctx.font = 'bold 14px Noto Sans KR';
    ctx.textAlign='center'; ctx.textBaseline='middle';
    ctx.fillText('기록 없음', w/2, h/2);
    return;
  }
  // draw pie
  let start = -Math.PI/2;
  entries.forEach(e=>{
    const angle = (e.v/total) * Math.PI*2;
    ctx.beginPath();
    ctx.moveTo(w/2,h/2);
    ctx.fillStyle = e.color;
    ctx.arc(w/2,h/2,90,start,start+angle);
    ctx.closePath(); ctx.fill();
    start += angle;
  });
  // legend
  let ly = 16;
  ctx.font = '12px Noto Sans KR';
  entries.forEach((e,idx)=>{
    ctx.fillStyle = e.color;
    ctx.fillRect(10, 10 + idx*18, 12, 12);
    ctx.fillStyle = '#123';
    ctx.textAlign='left'; ctx.fillText(`${ITEMS.find(i=>i.key===e.k).label} (${e.v})`, 28, 20 + idx*18);
  });
}

/* small deterministic color mapper */
function randomColorForKey(k){
  const seeds = {
    paper_cup:'#56b38d', plastic_cup:'#3aa07e', straw:'#2fbf9b', plastic_bag:'#1ca27e', cutlery:'#15986f'
  };
  return seeds[k] || '#0f7a57';
}

/* simple suggestion logic */
function autoSuggest(){
  const today = getTodayCounts();
  const total = Object.values(today).reduce((s,n)=>s+(Number(n)||0),0);
  if(total === 0){
    suggestTitle.textContent = '아직 기록이 없어요 — 작은 행동부터 시작해볼까요?';
    suggestText.textContent = '가장 쉬운 것은 텀블러 사용하기, 빨대 거절하기 같은 작은 습관이에요.';
    return;
  }
  // find highest item
  let maxKey = null; let maxVal = -1;
  for(let k in today){
    if(today[k] > maxVal){ maxVal = today[k]; maxKey = k; }
  }
  const label = ITEMS.find(i=>i.key===maxKey)?.label || '항목';
  if(maxVal >= 3){
    suggestTitle.textContent = `${label}이(가) 많이 보이네요`;
    suggestText.textContent = `오늘 ${label}을(를) ${maxVal}번 사용하셨군요. 다음 주에는 해당 항목을 1~2번 줄이는 목표를 세워보면 어떨까요? 예: 텀블러 챙기기, 매장 컵 없이 주문하기.`;
  } else {
    suggestTitle.textContent = `잘 기록하셨어요 — 작은 개선 제안`;
    suggestText.textContent = `${label}은(는) ${maxVal}번 사용되었습니다. 지속적으로 줄이는 것이 중요합니다. 단기 목표: 이번 주에 2번 이하로 줄여보기.`;
  }
}

/* score update */
function updateScore(){
  const d = TODAY_KEY;
  const checks = (state.checks && state.checks[d]) || {};
  const totalChecks = ['bring_mug','reuse_bag','refuse_straw'].reduce((s,k)=> s + (checks[k]?1:0), 0);
  scoreEl.textContent = totalChecks;
}

/* save today's data */
function saveToday(){
  const d = TODAY_KEY;
  // ensure state.days exists
  if(!state.days) state.days = {};
  state.days[d] = getTodayCounts(); // already updated
  saveState();
  alert('오늘 기록이 저장되었습니다.');
  renderSummary();
}

/* generate export text */
function generateExport(){
  const d = TODAY_KEY;
  const today = state.days && state.days[d] ? state.days[d] : {};
  const checks = (state.checks && state.checks[d]) ? state.checks[d] : {};
  const total = Object.values(today).reduce((s,n)=>s+(Number(n)||0),0);
  const parts = [];
  parts.push(`[플라스틱 한 번 더 생각하기] — 제출용 요약`);
  parts.push(`날짜: ${d}`);
  parts.push('');
  parts.push(`오늘 총 사용 일회용품: ${total}개`);
  for(let it of ITEMS){
    parts.push(`- ${it.label}: ${today[it.key]||0}개`);
  }
  parts.push('');
  parts.push('내 실천 체크리스트:');
  parts.push(`- 텀블러 사용: ${checks.bring_mug ? '예' : '아니오'}`);
  parts.push(`- 장바구니 챙기기: ${checks.reuse_bag ? '예' : '아니오'}`);
  parts.push(`- 빨대 거절하기: ${checks.refuse_straw ? '예' : '아니오'}`);
  parts.push('');
  parts.push('디자인 계열 관점으로 본 짧은 소감:');
  parts.push('“시각화는 인식을 바꾸는 첫걸음이다. 오늘 기록을 통해 내가 어떤 소비패턴을 가지는지 알게 되었고, 디자인으로 이를 직관적으로 전달하면 행동 변화를 촉발할 수 있다.”');
  const txt = parts.join('\n');
  exportText.value = txt;
  alert('제출용 텍스트가 생성되었습니다. 아래에서 다운로드하거나 복사하세요.');
}

/* download export as .txt */
function downloadExport(){
  const v = exportText.value || '';
  if(!v){ alert('먼저 "제출용 텍스트 만들기"를 눌러주세요.'); return; }
  const blob = new Blob([v], {type:'text/plain;charset=utf-8'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = `eco_report_${TODAY_KEY}.txt`;
  document.body.appendChild(a); a.click(); a.remove();
  URL.revokeObjectURL(url);
}

/* copy export to clipboard */
function copyExport(){
  const v = exportText.value || '';
  if(!v){ alert('먼저 "제출용 텍스트 만들기"를 눌러주세요.'); return; }
  navigator.clipboard.writeText(v).then(()=>alert('복사되었습니다.'));
}

/* summary rendering (history) */
function renderSummary(){
  // summary of saved days
  const days = (state.days) ? Object.keys(state.days) : [];
  if(days.length === 0){
    historySummary.textContent = '저장된 데이터가 없습니다.';
  } else {
    const lines = days.slice(-7).reverse().map(d=>{
      const tot = Object.values(state.days[d] || {}).reduce((s,n)=>s+(Number(n)||0),0);
      return `${d}: ${tot}개`;
    });
    historySummary.innerHTML = lines.join('<br/>');
  }
}

/* ====== SVG icon renderers (return string) ====== */
function renderCupSVG(color){
  return `<svg width="48" height="40" viewBox="0 0 48 40" fill="none" xmlns="http://www.w3.org/2000/svg">
    <rect x="7" y="6" width="34" height="22" rx="3" fill="${color}" opacity="0.13"/>
    <path d="M10 10h28l-2 12a4 4 0 0 1-4 3H16a4 4 0 0 1-4-3L10 10z" fill="${color}" />
    <rect x="14" y="2" width="20" height="4" rx="2" fill="${color}" opacity="0.9"/>
  </svg>`;
}
function renderStrawSVG(color){
  return `<svg width="48" height="40" viewBox="0 0 48 40" fill="none" xmlns="http://www.w3.org/2000/svg">
    <path d="M6 34c6-10 20-20 34-28" stroke="${color}" stroke-width="4" stroke-linecap="round" stroke-linejoin="round"/>
    <circle cx="6" cy="34" r="3" fill="${color}"/>
  </svg>`;
}
function renderBagSVG(color){
  return `<svg width="48" height="40" viewBox="0 0 48 40" fill="none" xmlns="http://www.w3.org/2000/svg">
    <path d="M10 8h28v22a3 3 0 0 1-3 3H13a3 3 0 0 1-3-3V8z" fill="${color}" opacity="0.12"/>
    <path d="M14 8v-2a4 4 0 0 1 4-4h12a4 4 0 0 1 4 4v2" stroke="${color}" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
    <path d="M16 16h16" stroke="${color}" stroke-width="2" stroke-linecap="round"/>
  </svg>`;
}
function renderForkSVG(color){
  return `<svg width="48" height="40" viewBox="0 0 48 40" fill="none" xmlns="http://www.w3.org/2000/svg">
    <path d="M12 6v12" stroke="${color}" stroke-width="3" stroke-linecap="round"/>
    <path d="M18 6v12" stroke="${color}" stroke-width="3" stroke-linecap="round"/>
    <path d="M24 6v12" stroke="${color}" stroke-width="3" stroke-linecap="round"/>
    <path d="M18 20v10" stroke="${color}" stroke-width="4" stroke-linecap="round"/>
  </svg>`;
}

/* ====== Utility: ensure initial structure exists ====== */
(function ensureInit(){
  if(!state.days) state.days = {};
  if(!state.checks) state.checks = {};
})();
</script>
</body>
</html>
