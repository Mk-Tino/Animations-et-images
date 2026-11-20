Voici une animation HTML5 complète et interactive du **va-et-vient à 2 interrupteurs A et B**.

La lampe est allumée quand A et B sont dans une position **différente** :
**L = A ⊕ B** (OU exclusif / XOR)

Elle contient les 4 parties demandées, toutes synchronisées :
1. Le circuit électrique animé avec courant qui circule
2. La table de vérité
3. Le chronogramme en temps réel
4. Le logigramme normalisé

Copiez ce code dans un fichier `va-et-vient.html` et ouvrez-le dans votre navigateur. Cliquez sur les interrupteurs, sur les boutons ou sur les entrées du logigramme.

```html
<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Va-et-Vient Interactif - L = A XOR B</title>
<style>
  *{box-sizing:border-box}
  body{margin:0;font-family:Segoe UI,Arial,sans-serif;background:#eef2f7;color:#1f2937}
  header{background:linear-gradient(135deg,#1e3a8a,#2563eb);color:#fff;padding:28px 20px;text-align:center}
  header h1{margin:0 0 8px;font-size:28px}
  header p{margin:0;opacity:.9}
  main{max-width:980px;margin:auto;padding:20px}
  .card{background:#fff;border-radius:16px;padding:22px;margin-bottom:24px;box-shadow:0 4px 18px rgba(0,0,0,.08)}
  h2{color:#1e40af;margin-top:0;border-bottom:2px solid #dbeafe;padding-bottom:8px}
  .svg-wrap{background:#f8fafc;border:1px solid #e5e7eb;border-radius:12px;padding:10px}
  svg{width:100%;height:auto;display:block}
  .wire{stroke:#94a3b8;stroke-width:4;fill:none;stroke-linecap:round}
  .wire.active{stroke:#f97316;stroke-dasharray:12 8;animation:flow .6s linear infinite}
  @keyframes flow{to{stroke-dashoffset:-20}}
  .logic-wire{stroke:#94a3b8;stroke-width:3;fill:none}
  .logic-wire.active{stroke:#f97316;stroke-width:4}
  .switch{cursor:pointer}
  .switch:hover{opacity:.85}
  .controls{display:flex;flex-wrap:wrap;gap:10px;margin:16px 0}
  button{border:none;border-radius:10px;padding:11px 16px;font-weight:700;cursor:pointer;background:#2563eb;color:#fff}
  button:hover{background:#1d4ed8}
  button.secondary{background:#475569}
  button.auto{background:#059669}
  .badges{display:flex;gap:12px;flex-wrap:wrap;align-items:center}
  .badge{background:#eff6ff;border:1px solid #bfdbfe;padding:8px 14px;border-radius:999px;font-weight:700}
  .badge.on{background:#fef3c7;border-color:#f59e0b;color:#92400e}
  table{width:100%;border-collapse:collapse;text-align:center;margin-top:10px}
  th{background:#2563eb;color:#fff;padding:12px}
  td{padding:11px;border:1px solid #e5e7eb;font-size:17px}
  tr.active{background:#fde68a;font-weight:800;outline:2px solid #f59e0b}
  canvas{width:100%;height:auto;background:#fff;border:1px solid #e5e7eb;border-radius:12px}
  code{background:#eff6ff;padding:2px 6px;border-radius:6px}
  footer{text-align:center;color:#6b7280;padding:10px 20px 30px}
</style>
</head>
<body>
<header>
  <h1>💡 Circuit Va-et-Vient Interactif</h1>
  <p>Deux interrupteurs A et B commandent une lampe L &nbsp;•&nbsp; <b>L = A ⊕ B</b> &nbsp;•&nbsp; La lampe est allumée si A ≠ B</p>
</header>

<main>

<!-- 1. CIRCUIT ELECTRIQUE -->
<section class="card">
  <h2>1. Circuit électrique animé</h2>
  <p>Cliquez sur l'<b>interrupteur A</b> ou l'<b>interrupteur B</b> (ou touches <code>A</code> / <code>B</code> du clavier).<br>
  Le courant orange ne circule que si A et B sont sur la même navette : haut/haut ou bas/bas.</p>

  <div class="svg-wrap">
  <svg id="circuitSvg" viewBox="0 0 900 400" xmlns="http://www.w3.org/2000/svg">
    <!-- Source -->
    <rect x="20" y="150" width="80" height="100" rx="10" fill="#fff" stroke="#334155" stroke-width="2"/>
    <text x="60" y="175" text-anchor="middle" font-size="12" font-weight="bold">SOURCE</text>
    <text x="60" y="195" text-anchor="middle" font-size="11">Phase</text>
    <text x="60" y="215" text-anchor="middle" font-size="11">Neutre</text>
    <circle cx="100" cy="175" r="6" fill="#ef4444"/>
    <circle cx="100" cy="225" r="6" fill="#3b82f6"/>

    <!-- Wires -->
    <path id="wirePhase" class="wire" d="M100 175 H170 V190 H250"/>
    <path id="wireTop" class="wire" d="M250 100 H630"/>
    <path id="wireBottom" class="wire" d="M250 270 H630"/>
    <path id="wireLamp" class="wire" d="M630 190 H732"/>
    <path id="wireReturn" class="wire" d="M808 190 V360 H100 V225"/>

    <text x="440" y="88" text-anchor="middle" font-size="13" fill="#475569">Navette 1 (haut)</text>
    <text x="440" y="258" text-anchor="middle" font-size="13" fill="#475569">Navette 2 (bas)</text>
    <text x="135" y="162" font-size="12" fill="#dc2626" font-weight="bold">Phase</text>
    <text x="135" y="218" font-size="12" fill="#2563eb" font-weight="bold">Neutre</text>

    <!-- Contacts A -->
    <circle id="contactATop" cx="250" cy="100" r="8" fill="#fff" stroke="#111" stroke-width="2"/>
    <circle id="contactABottom" cx="250" cy="270" r="8" fill="#fff" stroke="#111" stroke-width="2"/>
    <!-- Contacts B -->
    <circle id="contactBTop" cx="630" cy="100" r="8" fill="#fff" stroke="#111" stroke-width="2"/>
    <circle id="contactBBottom" cx="630" cy="270" r="8" fill="#fff" stroke="#111" stroke-width="2"/>

    <!-- Interrupteur A -->
    <g id="switchA" class="switch">
      <line id="leverA" x1="250" y1="190" x2="250" y2="100" stroke="#111" stroke-width="6" stroke-linecap="round"/>
      <circle id="handleA" cx="250" cy="100" r="10" fill="#ef4444" stroke="#fff" stroke-width="2"/>
      <circle cx="250" cy="190" r="11" fill="#111"/>
      <text x="250" y="315" text-anchor="middle" font-size="16" font-weight="bold">Interrupteur A</text>
      <text id="stateTextA" x="250" y="335" text-anchor="middle" font-size="13" fill="#2563eb">A = 0 (haut)</text>
    </g>

    <!-- Interrupteur B -->
    <g id="switchB" class="switch">
      <line id="leverB" x1="630" y1="190" x2="630" y2="100" stroke="#111" stroke-width="6" stroke-linecap="round"/>
      <circle id="handleB" cx="630" cy="100" r="10" fill="#ef4444" stroke="#fff" stroke-width="2"/>
      <circle cx="630" cy="190" r="11" fill="#111"/>
      <text x="630" y="315" text-anchor="middle" font-size="16" font-weight="bold">Interrupteur B</text>
      <text id="stateTextB" x="630" y="335" text-anchor="middle" font-size="13" fill="#2563eb">B = 0 (haut)</text>
    </g>

    <!-- Lampe -->
    <g id="lampGroup">
      <g id="lampRays" opacity="0" stroke="#f59e0b" stroke-width="3" stroke-linecap="round">
        <line x1="770" y1="135" x2="770" y2="120"/><line x1="725" y1="155" x2="715" y2="145"/>
        <line x1="815" y1="155" x2="825" y2="145"/><line x1="720" y1="190" x2="705" y2="190"/>
        <line x1="820" y1="190" x2="835" y2="190"/>
      </g>
      <circle id="lampCircle" cx="770" cy="190" r="38" fill="#f1f5f9" stroke="#111" stroke-width="3"/>
      <path d="M755 200 L762 180 L769 200 L776 180 L783 200" fill="none" stroke="#111" stroke-width="2"/>
      <text x="770" y="138" text-anchor="middle" font-size="20" font-weight="bold">L</text>
      <text id="lampStateText" x="770" y="255" text-anchor="middle" font-size="13" font-weight="bold" fill="#64748b">ÉTEINTE</text>
    </g>
  </svg>
  </div>

  <div class="controls">
    <button id="btnA">Basculer A</button>
    <button id="btnB">Basculer B</button>
    <button id="btnAuto" class="auto">▶ Démonstration automatique</button>
    <button id="btnReset" class="secondary">↺ Réinitialiser le chronogramme</button>
  </div>

  <div class="badges">
    <span class="badge" id="badgeA">A = 0</span>
    <span class="badge" id="badgeB">B = 0</span>
    <span class="badge" id="badgeL">L = 0</span>
    <span id="equationText"><b>L = 0 ⊕ 0 = 0 → Lampe ÉTEINTE</b></span>
  </div>
</section>

<!-- 2. TABLE DE VERITE -->
<section class="card">
  <h2>2. Table de vérité</h2>
  <p>La ligne surlignée en jaune correspond à l'état actuel. La lampe est à <b>1</b> uniquement quand A et B sont différents.</p>
  <table id="truthTable">
    <thead><tr><th>A</th><th>B</th><th>L = A ⊕ B</th><th>Interprétation</th></tr></thead>
    <tbody>
      <tr data-a="0" data-b="0" class="active"><td>0</td><td>0</td><td>0</td><td>Éteinte</td></tr>
      <tr data-a="0" data-b="1"><td>0</td><td>1</td><td>1</td><td>Allumée</td></tr>
      <tr data-a="1" data-b="0"><td>1</td><td>0</td><td>1</td><td>Allumée</td></tr>
      <tr data-a="1" data-b="1"><td>1</td><td>1</td><td>0</td><td>Éteinte</td></tr>
    </tbody>
  </table>
  <p><code>L = A ⊕ B = (A ∧ ¬B) ∨ (¬A ∧ B)</code></p>
</section>

<!-- 3. CHRONOGRAMME -->
<section class="card">
  <h2>3. Chronogramme</h2>
  <p>Évolution des signaux A, B et L en fonction du temps. Chaque clic sur un interrupteur crée un front montant/descendant.</p>
  <canvas id="chrono" width="900" height="280"></canvas>
</section>

<!-- 4. LOGIGRAMME -->
<section class="card">
  <h2>4. Logigramme (circuit logique normalisé)</h2>
  <p>Cliquez sur les boîtes d'entrée <b>A</b> et <b>B</b> à gauche : elles sont synchronisées avec le circuit électrique. La porte est un <b>OU exclusif (XOR)</b>.</p>
  <div class="svg-wrap">
  <svg id="logicSvg" viewBox="0 0 900 280" xmlns="http://www.w3.org/2000/svg">
    <!-- Entrées -->
    <g id="logicInputA" class="switch">
      <rect x="20" y="40" width="80" height="50" rx="8" fill="#fff" stroke="#111" stroke-width="2"/>
      <text x="60" y="62" text-anchor="middle" font-size="18" font-weight="bold">A</text>
      <text id="logicValA" x="60" y="82" text-anchor="middle" font-size="14" font-weight="bold" fill="#dc2626">0</text>
    </g>
    <g id="logicInputB" class="switch">
      <rect x="20" y="150" width="80" height="50" rx="8" fill="#fff" stroke="#111" stroke-width="2"/>
      <text x="60" y="172" text-anchor="middle" font-size="18" font-weight="bold">B</text>
      <text id="logicValB" x="60" y="192" text-anchor="middle" font-size="14" font-weight="bold" fill="#dc2626">0</text>
    </g>

    <path id="logicWireA" class="logic-wire" d="M100 65 H300 V75 H350"/>
    <path id="logicWireB" class="logic-wire" d="M100 175 H300 V145 H350"/>

    <!-- Porte XOR -->
    <path id="xorGate" d="M350 35 Q400 110 350 185 L485 185 Q545 110 485 35 Z" fill="#fff7ed" stroke="#111" stroke-width="2.5"/>
    <path d="M335 35 Q385 110 335 185" fill="none" stroke="#111" stroke-width="2.5"/>
    <text x="435" y="118" text-anchor="middle" font-size="34" font-weight="bold">⊕</text>
    <text x="435" y="142" text-anchor="middle" font-size="11">OU EXCLUSIF</text>

    <path id="logicWireOut" class="logic-wire" d="M485 110 H634"/>

    <!-- Lampe logique -->
    <g id="logicLamp">
      <g id="logicLampRays" opacity="0" stroke="#f59e0b" stroke-width="3" stroke-linecap="round">
        <line x1="670" y1="62" x2="670" y2="50"/><line x1="632" y1="80" x2="622" y2="72"/><line x1="708" y1="80" x2="718" y2="72"/>
      </g>
      <circle id="logicLampCircle" cx="670" cy="110" r="36" fill="#f1f5f9" stroke="#111" stroke-width="3"/>
      <path d="M656 120 L662 100 L668 120 L674 100 L680 120" fill="none" stroke="#111" stroke-width="2"/>
      <text x="670" y="55" text-anchor="middle" font-size="18" font-weight="bold">L</text>
      <text id="logicValL" x="670" y="168" text-anchor="middle" font-size="16" font-weight="bold" fill="#dc2626">0</text>
    </g>

    <text x="450" y="255" text-anchor="middle" font-size="16">L = A ⊕ B &nbsp;&nbsp;•&nbsp;&nbsp; L = (A ∧ ¬B) ∨ (¬A ∧ B)</text>
  </svg>
  </div>
</section>

</main>
<footer>Animation pédagogique HTML5 / SVG / Canvas — Aucune bibliothèque externe requise</footer>

<script>
let A = 0, B = 0, L = 0;
let history = [{t:0, A:0, B:0, L:0}];
let startTime = Date.now();
let autoInterval = null;

function nowSec(){ return (Date.now() - startTime) / 1000; }
function $(id){ return document.getElementById(id); }

function setState(newA, newB){
  if(newA === A && newB === B) return;
  A = newA; B = newB; L = A ^ B;
  history.push({t: nowSec(), A, B, L});
  updateAll();
}
function toggleA(){ setState(A ^ 1, B); }
function toggleB(){ setState(A, B ^ 1); }

function updateAll(){
  // Boutons et badges
  $('stateAbtn') && ($('stateAbtn').textContent = A + (A===0?' (haut)':' (bas)'));
  $('badgeA').textContent = 'A = ' + A;
  $('badgeB').textContent = 'B = ' + B;
  $('badgeL').textContent = 'L = ' + L;
  $('badgeL').classList.toggle('on', L===1);
  $('equationText').innerHTML = `<b>L = ${A} ⊕ ${B} = ${L} → Lampe ${L?'ALLUMÉE':'ÉTEINTE'}</b>`;

  // Circuit : leviers
  $('leverA').setAttribute('y2', A===0 ? 100 : 270);
  $('handleA').setAttribute('cy', A===0 ? 100 : 270);
  $('leverB').setAttribute('y2', B===0 ? 100 : 270);
  $('handleB').setAttribute('cy', B===0 ? 100 : 270);
  $('stateTextA').textContent = 'A = ' + A + (A===0?' (haut)':' (bas)');
  $('stateTextB').textContent = 'B = ' + B + (B===0?' (haut)':' (bas)');

  // Contacts
  $('contactATop').setAttribute('fill', A===0 ? '#fde68a' : '#fff');
  $('contactABottom').setAttribute('fill', A===1 ? '#fde68a' : '#fff');
  $('contactBTop').setAttribute('fill', B===0 ? '#fde68a' : '#fff');
  $('contactBBottom').setAttribute('fill', B===1 ? '#fde68a' : '#fff');

  // Fils actifs
  $('wireTop').classList.toggle('active', L===1 && A===0 && B===0);
  $('wireBottom').classList.toggle('active', L===1 && A===1 && B===1);
  $('wirePhase').classList.toggle('active', L===1);
  $('wireLamp').classList.toggle('active', L===1);
  $('wireReturn').classList.toggle('active', L===1);

  // Lampe circuit
  $('lampCircle').setAttribute('fill', L ? '#fde047' : '#f1f5f9');
  $('lampRays').setAttribute('opacity', L ? 1 : 0);
  $('lampStateText').textContent = L ? 'ALLUMÉE' : 'ÉTEINTE';
  $('lampStateText').setAttribute('fill', L ? '#d97706' : '#64748b');

  // Table de vérité
  document.querySelectorAll('#truthTable tbody tr').forEach(tr=>{
    tr.classList.toggle('active', parseInt(tr.dataset.a)===A && parseInt(tr.dataset.b)===B);
  });

  // Logigramme
  $('logicValA').textContent = A;
  $('logicValB').textContent = B;
  $('logicValL').textContent = L;
  $('logicWireA').classList.toggle('active', A===1);
  $('logicWireB').classList.toggle('active', B===1);
  $('logicWireOut').classList.toggle('active', L===1);
  $('xorGate').setAttribute('fill', L ? '#fde68a' : '#fff7ed');
  $('logicLampCircle').setAttribute('fill', L ? '#fde047' : '#f1f5f9');
  $('logicLampRays').setAttribute('opacity', L ? 1 : 0);

  drawChronogram();
}

// CHRONOGRAMME
function drawChronogram(){
  const canvas = $('chrono'), ctx = canvas.getContext('2d');
  const W = canvas.width, H = canvas.height;
  ctx.clearRect(0,0,W,H);
  ctx.fillStyle = '#ffffff'; ctx.fillRect(0,0,W,H);

  const left = 55, right = 15, timeW = W - left - right;
  const pxPerSec = 55;
  const elapsed = nowSec();
  const viewDur = timeW / pxPerSec;
  const endT = Math.max(viewDur, elapsed + 0.3);
  const startView = endT - viewDur;
  const xFor = t => left + (t - startView) * pxPerSec;

  // Grille temps
  ctx.strokeStyle = '#e5e7eb'; ctx.lineWidth = 1;
  ctx.fillStyle = '#6b7280'; ctx.font = '11px Arial'; ctx.textAlign = 'center';
  for(let s = Math.ceil(startView); s <= Math.floor(endT); s++){
    const x = xFor(s);
    ctx.beginPath(); ctx.moveTo(x,15); ctx.lineTo(x,H-25); ctx.stroke();
    ctx.fillText(s+'s', x, H-8);
  }

  const lanes = [
    {key:'A', label:'A', y:25},
    {key:'B', label:'B', y:105},
    {key:'L', label:'L', y:185}
  ];

  lanes.forEach((lane, idx)=>{
    ctx.fillStyle = idx%2 ? '#f9fafb' : '#ffffff';
    ctx.fillRect(left, lane.y-8, timeW, 64);
    ctx.strokeStyle = '#d1d5db'; ctx.strokeRect(left, lane.y-8, timeW, 64);
    ctx.fillStyle = lane.key==='L' ? '#b45309' : '#1d4ed8';
    ctx.font = 'bold 16px Arial'; ctx.textAlign = 'center';
    ctx.fillText(lane.label, 28, lane.y+28);
    ctx.font = '10px Arial'; ctx.fillStyle = '#6b7280';
    ctx.fillText('1', left+8, lane.y+8);
    ctx.fillText('0', left+8, lane.y+46);
  });

  lanes.forEach(lane=>{
    const yFor = v => v===1 ? lane.y+2 : lane.y+38;
    ctx.strokeStyle = lane.key==='L' ? '#d97706' : '#2563eb';
    ctx.lineWidth = 3; ctx.lineJoin = 'round';
    ctx.beginPath();
    for(let i=0;i<history.length;i++){
      const t0 = history[i].t;
      const t1 = i+1 < history.length ? history[i+1].t : elapsed;
      const v = history[i][lane.key];
      ctx.moveTo(xFor(t0), yFor(v));
      ctx.lineTo(xFor(t1), yFor(v));
      if(i+1 < history.length){
        ctx.lineTo(xFor(t1), yFor(history[i+1][lane.key]));
      }
    }
    ctx.stroke();
  });

  // Curseur temps réel
  const xc = xFor(elapsed);
  ctx.strokeStyle = '#ef4444'; ctx.lineWidth = 2; ctx.setLineDash([4,4]);
  ctx.beginPath(); ctx.moveTo(xc,15); ctx.lineTo(xc,H-25); ctx.stroke(); ctx.setLineDash([]);
  ctx.fillStyle = '#ef4444'; ctx.font = 'bold 11px Arial'; ctx.textAlign = 'center';
  ctx.fillText('maintenant', xc, 12);
  ctx.fillStyle = '#374151'; ctx.font = '12px Arial'; ctx.textAlign = 'right';
  ctx.fillText('Temps →', W-15, H-8);
}

// Événements
$('switchA').addEventListener('click', toggleA);
$('switchB').addEventListener('click', toggleB);
$('logicInputA').addEventListener('click', toggleA);
$('logicInputB').addEventListener('click', toggleB);
$('btnA').addEventListener('click', toggleA);
$('btnB').addEventListener('click', toggleB);

$('btnReset').addEventListener('click', ()=>{
  startTime = Date.now(); A = 0; B = 0; L = 0;
  history = [{t:0, A:0, B:0, L:0}];
  updateAll();
});

$('btnAuto').addEventListener('click', function(){
  if(autoInterval){
    clearInterval(autoInterval); autoInterval = null;
    this.textContent = '▶ Démonstration automatique';
  } else {
    this.textContent = '⏸ Arrêter la démonstration';
    autoInterval = setInterval(()=>{
      Math.random() < 0.5 ? toggleA() : toggleB();
    }, 1500);
  }
});

document.addEventListener('keydown', e=>{
  if(e.key==='a' || e.key==='A') toggleA();
  if(e.key==='b' || e.key==='B') toggleB();
});

updateAll();
setInterval(drawChronogram, 100);
</script>
</body>
</html>
```

### Comment ça marche ?

* **Circuit :** A et B sont des commutateurs à 2 positions (haut = 0, bas = 1). Le courant ne passe que si les deux leviers sont sur la même navette. C'est pour cela que la lampe s'allume quand A ≠ B.
* **Table de vérité :** la ligne active suit vos clics, ce qui prouve L = 1 seulement pour (0,1) et (1,0).
* **Chronogramme :** il enregistre chaque changement avec son temps. Vous voyez clairement que L monte quand A ou B change et que l'autre est différent.
* **Logigramme :** c'est la traduction logique du même système : une porte **OU exclusif ⊕** dont la sortie pilote la lampe.

Vous pouvez utiliser la démonstration automatique pour générer un chronogramme intéressant à recopier dans un cahier.