<!doctype html>
<html lang="ko">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>You&Ra 오감 감정 민감성 테스트</title>
<style>
 :root{--bg:#faf7f2;--card:#ffffff;--ink:#1f2421;--muted:#6b6b6b;--brand:#1c4e80;--line:#ececec}
 *{box-sizing:border-box}
 body{margin:0;padding:0;background:var(--bg);color:var(--ink);font-family:system-ui,-apple-system,Segoe UI,Roboto,"Apple SD Gothic Neo","Malgun Gothic",sans-serif}
 .wrap{max-width:900px;margin:28px auto;padding:0 16px}
 .card{background:var(--card);border-radius:16px;box-shadow:0 6px 20px rgba(0,0,0,.06);padding:18px 16px;margin-bottom:16px}
 header.card{display:flex;gap:16px;align-items:center}
 header img{height:70px}
 h1{margin:0 0 4px}
 .muted{color:var(--muted)}
 .q-area{min-height:120px;display:flex;align-items:center;justify-content:center;text-align:center;font-size:18px;padding:10px}
 .likert{display:flex;gap:8px;justify-content:center;flex-wrap:wrap}
 .likert button{border:1px solid #cfd8e3;background:#f6f8fb;border-radius:10px;padding:10px 12px;cursor:pointer;font-weight:700;min-width:64px}
 .likert button.active{background:var(--brand);color:#fff;border-color:var(--brand)}
 .nav{display:flex;gap:8px;margin-top:12px}
 .nav button{flex:1;border:none;background:var(--brand);color:#fff;border-radius:10px;padding:12px;font-weight:700;cursor:pointer}
 .nav button.secondary{background:#eaeaea;color:#1f1f1f}
 .progress{height:8px;background:#f0f3f5;border-radius:999px;overflow:hidden;margin-top:8px}
 .bar{height:8px;background:linear-gradient(90deg,#1c74d8,#00b2a9);width:0%}
 .result{background:var(--card);border-left:6px solid var(--brand);padding:14px;border-radius:10px;margin-top:12px}
 .result-card{display:flex;gap:14px;align-items:flex-start;flex-wrap:wrap;margin-top:12px}
 .result-card img{width:110px;height:110px;object-fit:cover;border-radius:12px}
 .small{font-size:12px;color:var(--muted)}
 footer{color:#9a9a9a;font-size:12px;margin:24px 0 40px;text-align:center}
</style>
</head>
<body>
<div class="wrap">
  <header class="card">
    <img src="logo_youandra.jpg" alt="You&Ra Center">
    <div>
      <h1>오감 감정 민감성 테스트</h1>
      <div class="muted">총 30문항 · Likert 5점 (1 전혀 아님 ~ 5 매우 그럼).<br>문항은 <b>랜덤</b>으로 한 문항씩 제시됩니다.</div>
      <div class="small">© You&Ra • 감정은 흘려보내야 건강해집니다</div>
    </div>
  </header>

  <div id="exam" class="card">
    <div class="q-area" id="qText">로딩 중…</div>
    <div class="likert" id="likert"></div>
    <div class="nav">
      <button id="prevBtn" class="secondary">이전</button>
      <button id="nextBtn">다음</button>
    </div>
    <div style="display:flex;justify-content:space-between;margin-top:8px;">
      <div class="small" id="counter">0 / 30</div>
      <div class="small">검사 중에는 감각 이름을 숨깁니다.</div>
    </div>
    <div class="progress"><div class="bar" id="bar"></div></div>
  </div>

  <div id="out" class="result" style="display:none"></div>

  <div class="card">
    <button id="restart" class="secondary">처음부터 다시</button>
    <button id="exportBtn" class="secondary">CSV 내보내기</button>
  </div>
</div>

<script>
(function(){
  const ITEMS = [
    ["1","주변이 어수선하면, 정리하기 전까지 다른 일에 집중하기 어렵다.","A",false],
    ["2","낮은 색 대비의 문서/화면을 오래 읽으면 눈이 쉽게 피로해진다.","A",false],
    ["3","실내 조명 밝기가 바뀌면, 몸이 그 밝기에 적응하는 데 시간이 걸린다.","A",false],
    ["4","공간의 색채가 달라지면, 내 업무 속도나 오류율도 달라지는 편이다.","A",false],
    ["5","일정 시간 이상 강한 빛에 노출되면 이후 멍하거나 두통이 온다.","A",false],
    ["6","주변 정리가 안 되어도, 해야 할 일만 있으면 시각적 방해를 무시한다.","A",true],
    ["7","두 가지 이상의 말소리가 겹치면 대화 내용을 놓치는 일이 잦다.","B",false],
    ["8","소음 환경에서 집중도가 떨어진다.","B",false],
    ["9","일정 리듬(에어컨, 탁탁 소리 등)이 지속되면 신체가 긴장된다.","B",false],
    ["10","조용한 공간으로 이동하면 회복 시간이 필요하다.","B",false],
    ["11","특정 억양(명령조 등)에 내용보다 톤 자체에 먼저 반응한다.","B",false],
    ["12","작업 몰입 중에는 주변 소음이 커져도 방해받지 않는다.","B",true],
    ["13","옷 라벨/시계줄 같은 압박이 신경 쓰여 오래 착용이 어렵다.","C",false],
    ["14","온도·습도 변화가 크면 감정이 먼저 요동친다.","C",false],
    ["15","낯선 사람이 손/어깨를 툭 치면 몸 반응이 먼저 일어난다.","C",false],
    ["16","긴장 시 어깨·턱·복부 등 특정 부위에 근긴장이 나타난다.","C",false],
    ["17","부드러운 재질을 짧게 만지는 것만으로도 이완된다.","C",false],
    ["18","옷감/침구 재질은 거의 신경 쓰지 않는다.","C",true],
    ["19","향이나 냄새 변화를 가장 먼저 알아차린다.","D",false],
    ["20","새로운 냄새가 나면 출처를 찾을 때까지 집중이 흐트러진다.","D",false],
    ["21","냄새가 사라진 뒤에도 감정 여진이 오래 남는다.","D",false],
    ["22","익숙한 냄새가 나면 상황이 달라도 안전하다고 오판한다.","D",false],
    ["23","같은 향이라도 강도/잔향 차이를 예민하게 구분한다.","D",false],
    ["24","냄새 강도가 커져도 업무 성과에는 거의 변화가 없다.","D",true],
    ["25","식사 시간/규칙이 흐트러지면 집중력이 먼저 떨어진다.","E",false],
    ["26","스트레스 상황에서 특정 음식을 반복적으로 찾는다.","E",false],
    ["27","식사 직후 감정 기복이 줄어드는 체감 시간이 있다.","E",false],
    ["28","예상과 다른 맛이면 행동 계획도 수정한다.","E",false],
    ["29","새로운 맛 기대가 낮으면 소량만 먹고도 포기한다.","E",false],
    ["30","식사 질이나 타이밍이 달라도 하루 감정 곡선은 일정하다.","E",true],
  ];

  const IMGS = { A:"A.jpg",B:"B.jpg",C:"C.jpg",D:"D.jpg",E:"E.jpg" };
  const TEXTS = {
    A:{title:"시각형",feature:["시각 자극에 민감","정돈된 구조·색에서 안정"],routine:["정리·정돈","무채색 환경","자연광 독서/명상","컬러 만다라"]},
    B:{title:"청각형",feature:["소리에 민감","소음은 에너지 고갈"],routine:["백색소음","플레이리스트","조용한 시간","확언 말하기"]},
    C:{title:"촉각형",feature:["촉감·압박감에 민감","부드러운 질감이 안정"],routine:["손 공예","담요·따뜻한 물건","스트레칭+마사지","감정 일기"]},
    D:{title:"후각형",feature:["향에 감정이 소환됨","냄새는 감정 온도계"],routine:["향초·아로마","향기 노트","자연 향 맡기","창작 활동"]},
    E:{title:"미각형",feature:["기분에 따라 입맛 변화","맛·온도·질감이 정서에 영향"],routine:["감정 음식 기록","식사=회복 루틴","따뜻한 음료"]},
  };

  let order=[],idx=0,answers={};

  function shuffle(a){for(let i=a.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[a[i],a[j]]=[a[j],a[i]];}return a;}
  function newRun(){order=shuffle([...ITEMS]);idx=0;answers={};render();}

  function render(){
    const it=order[idx]; if(!it) return;
    document.getElementById("qText").innerText = it[0]+"번. "+it[1];
    const likert=document.getElementById("likert"); likert.innerHTML="";
    for(let v=1;v<=5;v++){
      const b=document.createElement("button");
      b.textContent=v; if(answers[it[0]]==v) b.classList.add("active");
      b.onclick=()=>{answers[it[0]]=v;render();};
      likert.appendChild(b);
    }
    document.getElementById("counter").innerText=(idx+1)+" / "+ITEMS.length;
    document.getElementById("bar").style.width=((idx+1)/ITEMS.length*100)+"%";
  }

  function calc(){
    const scores={A:0,B:0,C:0,D:0,E:0};
    for(const it of ITEMS){
      let v=answers[it[0]]||3;
      if(it[3]) v=6-v;
      scores[it[2]]+=v;
    }
    return scores;
  }

  function show(){
    const s=calc();const out=document.getElementById("out");
    const max=Math.max(...Object.values(s));
    const tops=Object.entries(s).filter(([k,v])=>v===max).map(([k])=>k);
    out.innerHTML="";
    tops.forEach(t=>{
      const d=TEXTS[t];
      const card=document.createElement("div");card.className="result-card";
      card.innerHTML=`<img src="${IMGS[t]}"><div><h3>${d.title}</h3>
      <p><b>특징:</b> ${d.feature.join(" · ")}</p>
      <p><b>회복 루틴:</b><ul><li>${d.routine.join("</li><li>")}</li></ul></p></div>`;
      out.appendChild(card);
    });
    out.style.display="block";
  }

  document.getElementById("nextBtn").onclick=()=>{if(idx<ITEMS.length-1){idx++;render();}else{show();}};
  document.getElementById("prevBtn").onclick=()=>{if(idx>0){idx--;render();}};
  document.getElementById("restart").onclick=()=>{newRun();document.getElementById("out").style.display="none";};
  document.getElementById("exportBtn").onclick=()=>{
    const s=calc();const row=[new Date().toISOString(),s.A,s.B,s.C,s.D,s.E].join(",");
    const csv="time,A,B,C,D,E\n"+row+"\n";
    const blob=new Blob([csv],{type:"text/csv"});const a=document.createElement("a");
    a.href=URL.createObjectURL(blob);a.download="scores.csv";a.click();
  };

  newRun();
})();
</script>
</body>
</html>
