# DNA del sito Prof. Sciarra — Analisi completa e specifiche per il portale generatore

> **Scopo di questo documento:** servire da specifica tecnica unica per il nuovo repository.
> Contiene: estratto di tutti i pattern, stili, componenti e scelte di design del sito attuale,
> più la lista di ciò che andrà costruito da zero per il portale multi-insegnante.

---

## 1. Architettura del sito attuale

```
prof-sciarra-website/
├── index.html                    ← Home page: tracce → classi → lezioni
├── afm/
│   ├── terza/  (7 lezioni: bit, CPU, RAM, HDD, supporti ottici, client-server, introduzione)
│   └── quarta/ (2 lezioni: foglio di calcolo, funzione SE)
├── sia/
│   ├── terza/  (8 lezioni: stessa base AFM + flowlab)
│   ├── quarta/ (4 lezioni: classi Java, costruttori, lab, getter-setter)
│   └── quinta/ (9 lezioni: TCP/IP, OSI, Bluetooth, P2P, JSON, ERP/CRM, SQL, ER, intranet)
├── ai/                          ← Competenza trasversale
│   ├── sito_web_con_AI.html     ← Prompt engineering (6 step)
│   └── come-funziona-un-llm.html ← Lezione interattiva avanzata
├── probabilita/                 ← Competenza trasversale
│   └── probabilita-condizionata.html
└── universita/                  ← Approfondimenti universitari
    ├── p2p-vs-client-server.html
    └── bit-parita-crc-hamming.html
```

### Regola di naming dei file

- Minuscolo, trattini, nessuno spazio
- Pattern: `argomento-breve.html` (es. `classi-java.html`, `probabilita-condizionata.html`)
- Lezioni numerate quando formano una sequenza: `0_introduzione_informatica.html`, `1_bit_e_successori.html`

---

## 2. Home page (index.html)

### Struttura

```
<header>          ← Avatar "AS" + nome + chip materie
<main>
  <p.intro>       ← Testo di benvenuto
  <section.track id="afm">    ← Una section per argomento
    <div.track-header>        ← Badge colorato + titolo + descrizione
    <div.classes>             ← Grid di card
      <div.class-card afm>    ← Una card per classe
        <div.class-year>      ← Cerchio anno + nome classe
        <ul.class-subjects>   ← Lista link alle lezioni
```

### Colori dei track (argomenti)

| Track     | Badge CSS       | Colore        | Cerchio | Bordo card |
|-----------|-----------------|---------------|---------|------------|
| AFM       | `.track-badge.afm` | `#ff6b9d` (rosa) | `.year-circle.afm` | `.class-card.afm::before` |
| SIA       | `.track-badge.sia` | `#7cffb2` (verde) | `.year-circle.sia` | `.class-card.sia::before` |
| AI        | `.track-badge.ai`  | `#b388ff` (viola) | `.year-circle.ai`  | `.class-card.ai::before`  |
| UNI       | `.track-badge.uni` | `#ffc857` (giallo) | `.year-circle.uni` | `.class-card.uni::before` |
| Probabilità | `.track-badge.pro` | `#ffa14e` (arancio) | `.year-circle.pro` | `.class-card.pro::before` |

Ogni badge: `background: rgba(colore, 0.15)`, `color: var(--colore)`, `border: 1px solid rgba(colore, 0.4)`.

### CSS variabili globali (index.html)

```css
:root {
  --bg: #0b1020;
  --bg-2: #0f1530;
  --panel: #141b3a;
  --panel-2: #1a2247;
  --text: #e6ecff;
  --muted: #9aa6c7;
  --accent: #6ee7ff;
  --accent-2: #b388ff;
  --afm: #ff6b9d;
  --sia: #7cffb2;
  --ai: #b388ff;
  --uni: #ffc857;
  --prob: #ffa14e;
  --shadow: 0 10px 40px rgba(0,0,0,0.3);
}
```

### Caratteristiche della home

- **Background**: `radial-gradient(1200px 800px at 80% -10%, #1b2350 0%, #0b1020 60%)`
- **Grid overlay**: `linear-gradient(rgba(110,231,255,0.05) 1px, transparent 1px)` con `background-size: 40px 40px`, posizionato su `body::before`
- **Card layout**: `grid-template-columns: repeat(auto-fit, minmax(280px, 1fr))`
- **Animazioni**: `fadeInUp` con ritardi scalari (0.1s, 0.2s, 0.3s)
- **Card hover**: `translateY(-4px)` + glow sul bordo
- **Link lezioni**: sfondo `rgba(255,255,255,0.03)` con hover `translateX(4px)` + glow accent

---

## 3. Famiglie di stili delle lezioni

Il sito ha **quattro famiglie** distinte. Per il portale generatore, la più riutilizzabile è la **Famiglia A** (Standard Dark), che copre ~70% delle lezioni.

### Famiglia A — Standard Dark (la più importante per il portale)

**File di riferimento:** `ai/sito_web_con_AI.html`, `probabilita/probabilita-condizionata.html`, `sia/quarta/classi-java.html`, `sia/quinta/tcp-ip-stack.html`, `sia/quinta/pila-osi.html`, `sia/quinta/json.html`

#### CSS (tutte le variabili da `:root`)

```css
:root {
  --bg: #0b1020;                  /* sfondo principale */
  --bg-2: #0f1530;                /* sfondo secondario */
  --panel: #141b3a;               /* pannelli */
  --panel-2: #1a2247;             /* pannelli alternativi */
  --text: #e6ecff;                /* testo principale */
  --muted: #9aa6c7;               /* testo secondario */
  --accent: #6ee7ff;              /* colore primario (può cambiare per materia) */
  --accent-2: #b388ff;            /* colore secondario per gradienti */
  --good: #7cffb2;                /* verde positivo/successo */
  --warn: #ffc857;                /* giallo warning */
  --bad: #ff6b9d;                 /* rosso/rosa errore */
  --teal: #6ee7ff;                /* ciano (alcune lezioni lo usano) */
}
```

**Background standard:**
```css
background: radial-gradient(1200px 800px at 80% -10%, #1b2350 0%, var(--bg) 60%) var(--bg);
```

**Grid overlay (body::before):**
```css
background-image:
  linear-gradient(rgba(110, 231, 255, 0.05) 1px, transparent 1px),
  linear-gradient(90deg, rgba(110, 231, 255, 0.05) 1px, transparent 1px);
background-size: 40px 40px;
```

Nei file che cambiano accent (es. Probabilità), il colore del grid overlay cambia coerentemente (`rgba(255, 161, 78, 0.04)`).

#### Tipografia

```css
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
font-family: "SF Mono", Menlo, Consolas, monospace;  /* per codice */
```

#### Struttura HTML standard

```html
<!-- Bottone home flottante (fisso, in basso a destra, z-index altissimo) -->
<a href="../index.html" style="position:fixed;right:16px;bottom:16px;z-index:2147483647;
   display:inline-flex;align-items:center;gap:8px;padding:11px 18px;border-radius:999px;
   background:#0b1020;color:#fff;font:600 14px/1 ...;text-decoration:none;
   border:1px solid rgba(255,255,255,.25);box-shadow:0 6px 20px rgba(0,0,0,.35);">← Home</a>

<!-- Breadcrumb -->
<div class="topbar">
  <div class="breadcrumb">
    <a href="../index.html">Home</a> ·
    <a href="../index.html#track-id">Nome Track</a> ·
    <strong>Nome Lezione</strong>
  </div>
</div>

<!-- Header -->
<header>
  <span class="tag">BADGE TESTO</span>
  <h1>Titolo con gradiente</h1>
  <p>Sottotitolo / descrizione</p>
</header>

<!-- Navigazione interna (opzionale ma consigliata) -->
<nav class="toc">
  <!-- link pill-shaped alle ancore della pagina -->
  <a href="#sezione1">1 · Sezione</a>
  <a href="#sezione2">2 · Sezione</a>
</nav>

<main>
  <section class="block" id="sezione1">
    <div class="card">
      <span class="kick">// 01 · etichetta</span>
      <h2>Titolo sezione</h2>
      <p class="lead">Sottotitolo</p>
      <!-- contenuto -->
    </div>
  </section>

  <!-- ... altre sezioni ... -->
</main>

<footer>
  <p>Materia · Competenza · Prof. Andrea Sciarra</p>
  <p><a href="../index.html">← Torna alla home</a></p>
</footer>
```

---

### Famiglia B — Lezioni a tema chiaro (AFM quarta)

**File di riferimento:** `afm/quarta/funzione_se.html`, `afm/quarta/foglio-di-calcolo.html`

- **Sfondo chiaro**: `--paper: #F4F8F4`, `--card: #FFFFFF`
- **Accent verde Excel**: `--green: #107C41`
- **Font diverso**: `'Archivo', 'IBM Plex Sans'`
- Stessa struttura a card ma adattata per argomenti "da ufficio"
- Non ha breadcrumb né floating home button

### Famiglia C — Lezioni ultra-interattive

**File di riferimento:** `come-funziona-un-llm.html`

- **Sfondo**: `#0a0f1e` con grid a 46px
- **Canvas animato** sullo sfondo (particelle + connessioni)
- **Nav fissa** con backdrop-filter blur
- **Font display**: `'Syne'`, `'Instrument Sans'`, `'JetBrains Mono'`
- **Scroll reveal** via IntersectionObserver
- **Eroina a due colonne** con terminale animato (typewriter + barre probabilità)
- **Ticker bar** scrolling infinito
- **Statistiche animate** (counter)
- Ogni modulo JS è isolato in una funzione con try/catch

### Famiglia D — Temi specializzati (Bluetooth, Flowlab)

**Bluetooth** (`sia/quinta/bluetooth.html`):
- Accent blu `#0082FC`, font `'Unbounded'`, `'Space Grotesk'`
- Design "tech" con icone grandi, timeline visiva

**Flowlab** (`sia/terza/flowlab.html`):
- Canvas chiaro con griglia, tool ispirati a Flowgorithm
- Accent verde `#2FA36B`

---

## 4. Catalogo componenti riutilizzabili

### 4.1 Breadcrumb (`div.topbar > div.breadcrumb`)

```html
<div class="topbar">
  <div class="breadcrumb">
    <a href="../index.html">Home</a> ·
    <a href="../index.html#track">Track</a> ·
    <strong>Lezione</strong>
  </div>
</div>
```

CSS: `max-width: 980px`, padding `20px 24px 0`, colore muted, link accent.

### 4.2 Header con gradiente

```html
<header>
  <span class="tag">BADGE</span>
  <h1>Titolo</h1>
  <p>Sottotitolo</p>
</header>
```

- Tag: `display:inline-block`, bordo arrotondato, background semitrasparente accent
- h1: `background: linear-gradient(90deg, var(--accent), var(--accent-2))` con `-webkit-background-clip: text` per effetto testo sfumato
- Dimensione: `clamp(1.8rem, 4.2vw, 2.7rem)`

### 4.3 Navigazione interna (`nav.toc`)

```html
<nav class="toc">
  <a href="#sezione1">1 · Sezione</a>
  <a href="#sezione2">2 · Sezione</a>
</nav>
```

- Flex-wrap, gap 8px
- Link: pill-shaped (`border-radius: 999px`), bordo sottile, hover accent

### 4.4 Sezione card (`section.block > div.card`)

```css
.card {
  background: linear-gradient(180deg, rgba(20,27,58,0.7), rgba(15,21,48,0.7));
  border: 1px solid rgba(255,255,255,0.08);
  border-radius: 16px;
  padding: 26px 28px;
  margin-bottom: 22px;
  box-shadow: 0 10px 40px rgba(0,0,0,0.3);
  position: relative;
  overflow: hidden;
}
.card::before {
  content: "";
  position: absolute; top: 0; left: 0; bottom: 0;
  width: 4px;
  background: var(--accent);   /* barra colorata a sinistra */
}
```

### 4.5 Etichetta sezione (`.kick`)

```css
.kick {
  display: inline-block;
  font-size: 0.75rem;
  letter-spacing: 0.16em;
  text-transform: uppercase;
  color: var(--accent);
  margin-bottom: 8px;
  font-weight: 700;
}
```

Esempio: `// 01 · etichetta`

### 4.6 Box formula

```html
<div class="formula-box">
  <span class="name">Nome formula</span>
  P(A | B) = <span class="frac orange"><span class="num">P(A ∩ B)</span><span class="den">P(B)</span></span>
</div>
```

```css
.formula-box {
  background: #060914;
  border: 1px solid rgba(accent,0.3);
  border-radius: 12px;
  padding: 18px 22px;
  text-align: center;
  font-size: 1.25rem;
}
```

La frazione è realizzata con flexbox colonna + `border-top` sul denominatore:
```css
.frac { display:inline-flex; flex-direction:column; align-items:center; }
.frac .den { border-top:2px solid currentColor; padding:3px 8px 0; }
```

### 4.7 Box comparazione (good vs bad)

```html
<div class="compare">
  <div class="box bad">
    <h4>❌ Vago</h4>
    <pre><code>...</code></pre>
  </div>
  <div class="box good">
    <h4>✅ Specifico</h4>
    <pre><code>...</code></pre>
  </div>
</div>
```

```css
.compare { display:grid; grid-template-columns: 1fr 1fr; gap: 14px; }
.compare .bad { background: rgba(bad,0.08); border: 1px solid rgba(bad,0.3); }
.compare .good { background: rgba(good,0.08); border: 1px solid rgba(good,0.3); }
```

### 4.8 Callout / Tip

```html
<div class="tip">
  💡 <b>Titolo:</b> testo descrittivo.
</div>
<div class="tip warn">
  ⚠️ <b>Attenzione:</b> avviso importante.
</div>
```

```css
.tip {
  background: rgba(accent,0.08);
  border: 1px solid rgba(accent,0.3);
  border-radius: 12px;
  padding: 14px 18px;
}
.tip.warn {
  background: rgba(warn,0.08);
  border-color: rgba(warn,0.3);
}
```

### 4.9 Blocchi di codice

```html
<pre><code>console.log("hello");</code></pre>
```

```css
pre {
  background: #060914;
  border: 1px solid rgba(255,255,255,0.08);
  border-radius: 12px;
  padding: 16px 18px;
  overflow-x: auto;
  font-family: "SF Mono", Menlo, Consolas, monospace;
  font-size: 0.88rem;
}
code {
  background: rgba(accent, 0.1);
  color: var(--accent);
  padding: 2px 6px;
  border-radius: 4px;
}
```

### 4.10 Checklist

```html
<ul class="checklist">
  <li>Elemento con ✓</li>
</ul>
```

```css
ul.checklist { list-style:none; }
ul.checklist li { padding-left:26px; position:relative; }
ul.checklist li::before { content:"✓"; position:absolute; left:0; color:var(--good); font-weight:800; }
```

### 4.11 Quiz interattivo

```html
<div class="quiz-card" id="quizCard">
  <div class="quiz-top">
    <span id="quizProg">Domanda 1 di N</span>
    <span id="quizScore">Punti: 0</span>
  </div>
  <h3 id="quizQ"></h3>
  <div class="quiz-opts" id="quizOpts"></div>
  <div class="quiz-why" id="quizWhy" hidden></div>
  <div class="quiz-foot">
    <button class="btn" id="quizNext" hidden>Avanti →</button>
  </div>
</div>
<div class="quiz-end" id="quizEnd" hidden>
  <div class="score" id="endScore">0/N</div>
  <p id="endMsg"></p>
  <button class="btn" id="quizRestart">↻ Ricomincia</button>
</div>
```

JS: array di oggetti `{q, o:[], a, w}`, logica a stato (`qi, pts, answered`), opzioni con classi `.ok` / `.ko` al click.

### 4.12 Pulsanti

```css
.btn {
  font-family: inherit; font-weight: 700; font-size: 0.9rem;
  color: #201205;
  background: var(--accent); border: 1px solid var(--accent);
  border-radius: 10px; padding: 10px 20px; cursor: pointer;
}
.btn:hover { background: var(--accent-2); transform: translateY(-1px); }
```

### 4.13 Due griglie responsive

```css
.grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
@media (max-width: 720px) { .grid2 { grid-template-columns: 1fr; } }
```

### 4.14 Slider interattivo con output

```html
<div class="slider-row">
  <label>🏷️ <b>Nome</b> — descrizione</label>
  <input type="range" id="slider" min="0" max="100" step="1" value="50">
  <output id="sliderOut">50%</output>
</div>
```

### 4.15 Derivazione passo-passo (`.deriv`)

```html
<div class="deriv">
  <div class="ds">
    <strong>Passo 1 — Titolo.</strong><br>
    Testo esplicativo.<br>
    <span class="eq">formula centrata</span>
  </div>
</div>
```

Le `ds` usano `counter-increment` CSS per la numerazione automatica, con cerchio arancione a sinistra.

### 4.16 Barra impilata (orizzontale)

```html
<div class="bar-stack">
  <div style="width:33%;background:#7cffb2"></div>
  <div style="width:20%;background:#ff6b9d"></div>
</div>
```

### 4.17 Tabelle dati con highlighting

```html
<table class="tab">
  <thead><tr><th></th><th>Col1</th><th>Col2</th><th>Totale</th></tr></thead>
  <tbody>
    <tr><th>Riga1</th><td data-r="R1" data-c="C1">val</td>...</tr>
  </tbody>
</table>
```

### 4.18 Griglia interattiva (dadi 6×6)

```css
.dice-grid { display:grid; grid-template-columns: repeat(6,1fr); gap: 5px; }
.dcell { aspect-ratio:1; background: var(--panel-2); border-radius:8px; }
.dcell.inA { background: rgba(accent,0.55); }    /* evidenziato */
.dcell.dim { opacity:0.16; }                      /* oscurato */
```

---

## 5. Pattern JavaScript riutilizzabili

### 5.1 Struttura modulare

```js
/* utility */
const $=s=>document.querySelector(s), $$=s=>document.querySelectorAll(s);

/* moduli isolati */
function initModuloX(){ /* ... */ }
function initModuloY(){ /* ... */ }

/* avvio con protezione errori */
[initModuloX, initModuloY].forEach(fn=>{
  try { fn(); } catch(err) { console.warn('Modulo '+fn.name+' in errore:', err); }
});
```

### 5.2 Quiz engine

- Array `quiz` con `{q, o:[], a (indice risposta corretta), w (spiegazione)}`
- Variabili di stato: `qi` (indice), `pts` (punteggio), `answered` (flag)
- `renderQ()` costruisce i pulsanti e attacca i listener
- Al click: disabilita tutte le opzioni, colora `.ok` / `.ko`, mostra spiegazione
- `#quizNext` passa alla domanda successiva o mostra `#quizEnd`

### 5.3 Reveal on scroll (solo lezione LLM)

```js
const io = new IntersectionObserver(entries => entries.forEach(e => {
  if (e.isIntersecting) { e.target.classList.add('in'); io.unobserve(e.target); }
}), { threshold: 0.03, rootMargin: '0px 0px -5% 0px' });
```

### 5.4 Counter animato (solo lezione LLM)

```js
function step(now) {
  const p = Math.min(1, (now - t0) / 1400);
  const v = Math.round(target * (1 - Math.pow(1 - p, 3)));  // easing cubico
  el.textContent = v.toLocaleString('it-IT') + suffix;
  if (p < 1) requestAnimationFrame(step);
}
```

### 5.5 Typewriter (solo lezione LLM)

Loop asincrono con `await sleep(ms)` tra ogni carattere. Demo eroe con sequenze predefinite.

---

## 6. Colori per materia (mappatura)

Per il generatore, ogni disciplina avrà un accent personalizzato. Ecco la logica attuale e come estenderla:

| Disciplina        | Accent      | Nome variabile    |
|-------------------|-------------|-------------------|
| Informatica/SIA   | `#6ee7ff`   | cyan              |
| AFM / Excel       | `#107C41`   | verde scuro       |
| AI / Prompt       | `#6ee7ff`   | cyan              |
| Probabilità       | `#ffa14e`   | arancio           |
| Università        | `#ffc857`   | giallo            |
| Sistemi e Reti    | `#0082FC`   | blu Bluetooth     |
| Matematica pura   | (da definire) `#8b5cf6` viola, o mantiene arancio |
| Fisica            | (da definire) `#ef4444` rosso |
| Lettere / Storia  | (da definire) `#d4a574` marrone chiaro |
| Scienze / Chimica | (da definire) `#10b981` verde smeraldo |
| Inglese / Lingue  | (da definire) `#f472b6` rosa |
| Disegno / Arte    | (da definire) `#e879f9` fucsia |

---

## 7. Cosa va costruito da zero per il portale

### 7.1 Sistema di autenticazione
- Registrazione/login insegnanti
- Profilo: nome, materia, indirizzo, classi
- Ruolo: free vs premium

### 7.2 Database dei programmi ministeriali
- **Formato**: JSON strutturato
- **Campi per ogni argomento**:
  ```json
  {
    "indirizzo": "Liceo Scientifico",
    "anno": 3,
    "disciplina": "Fisica",
    "macroarea": "Meccanica",
    "argomento": "Le leggi di Newton",
    "parole_chiave": ["forza", "massa", "accelerazione", "inerzia"],
    "difficolta": "media",
    "prerequisiti": ["algebra vettoriale", "unità di misura"],
    "ore_stimate": 6
  }
  ```
- **Indirizzi prioritari**: SIA, Liceo Scientifico (da recuperare da te)
- Da costruire: uno script di parsing/normalizzazione, una struttura modulare per aggiungere altri indirizzi in futuro

### 7.3 Generatore di template

È il cuore del portale. Prende in input:
- Disciplina + indirizzo → determina accent e tono
- Argomento (dal programma) → titolo, struttura, tipologia di widget
- Stile scelto (standard dark, interattivo, chiaro)

Produce:
- **Output base (free)**: titoli delle sezioni, struttura card vuota, breadcrumb, header
- **Output completo (premium)**: HTML compilato con paragrafi, definizioni, box, quiz

**Tre template iniziali:**

| Template | Per | Tratti distintivi |
|---|---|---|
| **Standard** | Lezioni teoriche | Card + kick + formula box + tip + quiz |
| **Step-by-step** | Guide / prompt / procedure | Step numerati + compare boxes |
| **Interattivo** | Matematica / fisica / coding | Slider + griglie + grafici + input live |

### 7.4 Sistema di pagamento
- Generazioni gratuite mensili (es. 3)
- Pacchetti premium (es. 10, 50, illimitate)
- Integrazione con Stripe o PayPal
- Webhook per attivazione account premium

### 7.5 Interfaccia utente del portale

```
/                          ← Landing page (spiega cos'è, per chi è)
/login                     ← Accesso
/register                  ← Registrazione
/dashboard                 ← Pannello controllo: materie, generazioni, credito
/generate                  ← Pagina di generazione (wizard a step)
/preview/:id               ← Anteprima della lezione generata
/account                   ← Profilo, pagamenti, storico
```

### 7.6 API interna (se il generatore è lato server)

Se il generatore usa un backend (Node, Python, etc.), serviranno endpoint:

```
POST /api/generate          ← Genera una lezione (richiede autenticazione)
GET  /api/programs/:track   ← Restituisce il programma di un indirizzo
GET  /api/templates         ← Restituisce i template disponibili
GET  /api/user/credits      ← Crediti residui
```

---

## 8. Decisioni da prendere prima di scrivere codice

1. **Stack tecnologico**: puro frontend (HTML generato lato client) o backend? Consiglio: backend (Node/Express o Python/Flask) per gestire auth, pagamenti e generazione lato server. Il frontend resta statico come ora.

2. **Hosting**: il portale può stare su Vercel/Render (backend serverless) + Supabase (auth e DB), oppure su un VPS tradizionale. I costi sono bassi all'inizio.

3. **Generazione contenuti — DECISO**: useremo l'AI via API per generare i testi delle lezioni. L'insegnante descrive l'argomento che gli serve, il sistema chiama un LLM (es. DeepSeek) e produce HTML già compilato. L'insegnante può poi modificare il risultato con ulteriori richieste in linguaggio naturale («accorcia questa sezione», «aggiungi un esempio sul calcolo combinatorio», «cambia il tono, è troppo formale»). Ogni richiesta di modifica consuma crediti.
   - **Modello consigliato**: DeepSeek 4.1 Flash (o equivalente economico). Con 10-20€ di credito e pochi utenti iniziali, si coprono mesi di utilizzo.
   - **Mitigazione allucinazioni**: il prompt di sistema vincola l'AI ai soli contenuti del programma ministeriale. Le definizioni e i quiz vengono generati con l'istruzione esplicita di attenersi ai fatti noti. L'insegnante ha sempre l'ultima parola con l'editor manuale.
   - **Rate limiting**: ogni utente ha un limite di richieste AI al mese (es. 5 gratis, 20 col piano base, illimitate col premium). Ogni "genera lezione" e ogni "modifica con AI" scala dal contatore.

4. **Multilingua?**: solo italiano, o prevedi altre lingue? Inizia solo italiano.

5. **Dominio**: `prof-sciarra-website.github.io` per il sito attuale, un dominio separato per il portale (es. `generatore-lezioni.it` o `docenti.dev`) oppure un altro repo GitHub Pages?

6. **Stili CSS delle singole pagine**: l'insegnante può scegliere tra più varianti CSS. Le pagine interne (lezioni) possono ereditare lo stesso stile della home oppure averne uno diverso, a scelta dell'utente. Da valutare se offrire 3-4 "temi" predefiniti (Standard Dark, Chiaro, Tech/Bluetooth, Minimal) o permettere la personalizzazione del colore accent.

## 7.7 Flusso utente completo (journey)

Questa sezione descrive l'esperienza dell'insegnante dall'arrivo sul portale al sito pubblicato.

### Fase 1 — Onboarding e creazione del sito

```
Insegnante arriva → /register → inserisce:
  - Nome e cognome
  - Tipo di scuola (Liceo, Istituto Tecnico, Professionale...)
  - Indirizzo specifico (es. Liceo Scientifico, SIA, AFM...)
  - Classi che insegna, con materie per ogni classe
    Esempio:
      3ª Liceo Scientifico → Fisica, Matematica
      4ª Liceo Scientifico → Fisica
      5ª Liceo Scientifico → Fisica, Matematica
  - Sceglie un tema CSS per il sito (3-4 varianti)
```

A questo punto il sistema **genera la home page** (`index.html`) usando il template identico a quello del sito Prof. Sciarra, ma popolato con i dati dell'insegnante:

- Avatar (iniziali)
- Nome e sottotitolo
- Chip delle materie
- Track per ogni classe (badge colorato, titolo, anno)
- Card per ogni classe con la lista materie
- I link alle lezioni sono inizialmente vuoti o puntano a placeholder

L'insegnante vede subito **un'anteprima live** della sua home e può rigenerarla se vuole cambiare qualcosa.

### Fase 2 — Lezioni pre-confezionate (offerte da noi)

Sotto ogni materia di ogni classe, il sistema mostra automaticamente **2-3 lezioni già pronte** che noi abbiamo preparato per quell'indirizzo e quell'anno. Sono il "contenuto seed": danno valore immediato e mostrano all'insegnante il livello di qualità che può aspettarsi.

```
4ª SIA · Informatica
  📚 [Nostra] Le classi in Java: progetto e istanze          ← già pronta
  📚 [Nostra] I costruttori in Java                           ← già pronta
  📚 [Nostra] Getter & Setter in Java — incapsulamento        ← già pronta
  ➕ Crea una nuova lezione...                                ← tasto per generare
```

Queste lezioni pre-confezionate sono **gratuite** e servono da:
- Dimostrazione della qualità
- Fiducia iniziale («non parto da zero»)
- Gancio per far venire voglia di generarne di personalizzate

### Fase 3 — Generazione di una nuova lezione

L'insegnante clicca **«Crea nuova lezione»**. Si apre un'interfaccia con:

1. **Selezione dell'argomento dal programma ministeriale** (dropdown o ricerca)
   - Il sistema mostra gli argomenti previsti per quella materia/classe/indirizzo
   - L'insegnante può anche digitare un argomento libero

2. **Descrizione di ciò che serve** (campo testo libero)
   - "Voglio una lezione sui principi della dinamica, con esempi pratici di forze"
   - "Mi serve una spiegazione del teorema di Bayes con un esempio sui test medici"
   - "Preparami una lezione sulla Prima Guerra Mondiale, cause e conseguenze"

3. **Scelta del tipo di lezione**
   - Standard (card + paragrafi + quiz)
   - Step-by-step (adatta per procedure / guide)
   - Interattiva (con widget, slider, grafici)

4. **Scelta dello stile CSS** (se diverso da quello della home)

5. Clicca **«Genera»** → il backend chiama l'AI con:
   - Prompt di sistema vincolato al programma ministeriale
   - Istruzioni precise sul formato HTML atteso (struttura card, componenti, stili)
   - Il contesto dell'insegnante (materia, classe, tono)

La lezione viene generata e mostrata in **anteprima**. L'insegnante la vede esattamente come apparirà ai suoi studenti.

### Fase 4 — Modifica interattiva con l'AI

Una volta generata la lezione, l'insegnante può modificarla in due modi:

**A) Editor manuale** (gratuito, sempre disponibile):
- Modifica diretta del testo, titoli, paragrafi
- Riordino delle sezioni
- Aggiunta/rimozione di componenti (tip, formula, quiz)
- Cambio colori

**B) Modifica con AI** (consuma 1 credito a richiesta):
L'insegnante scrive in linguaggio naturale cosa vuole cambiare:
- «Accorcia la sezione 2, è troppo lunga»
- «Aggiungi un esempio sul pendolo dopo il paragrafo sulle forze»
- «Rendi il tono più informale, sembra un manuale universitario»
- «Traduci la lezione in inglese»
- «Aggiungi 3 domande al quiz finale»
- «Sostituisci l'esempio delle carte con uno sui dadi»

Il sistema invia all'AI il contenuto attuale della lezione + la richiesta di modifica, e restituisce la lezione aggiornata. L'insegnante può iterare quante volte vuole (finché ha crediti).

### Fase 5 — Pubblicazione

Quando è soddisfatto, l'insegnante clicca **«Pubblica»**. Il sistema:

1. Salva la lezione nel suo "sito"
2. Aggiorna la home page con il nuovo link
3. Fornisce un'anteprima live
4. Opzionalmente: esporta tutto come ZIP (HTML + CSS + asset) da caricare sul proprio hosting, oppure pubblica direttamente su un sottodominio fornito da noi (es. `mario-rossi.docenti.dev`)

### Riepilogo dei crediti / consumo AI

| Azione | Crediti |
|---|---|
| Registrazione + creazione sito | 0 (gratis) |
| Lezioni pre-confezionate | 0 (gratis, sempre) |
| Generare una nuova lezione con AI | 1 credito |
| Modificare una lezione con AI | 1 credito |
| Rigenerare la home dopo modifiche | 0 (gratis) |
| Modifica manuale (editor testo) | 0 (gratis, sempre) |

**Piani ipotetici:**
- **Free**: 5 crediti/mese (es. 2 lezioni + 3 modifiche)
- **Base** (9,99€/mese): 30 crediti/mese
- **Premium** (19,99€/mese): crediti illimitati + dominio personalizzato

**Costo API stimato (DeepSeek 4.1 Flash):**
- Ogni generazione di lezione: ~50-80K token totali (input + output) → ~0,01-0,02€
- Ogni modifica: ~15-30K token → ~0,003-0,006€
- Con 20€ di credito API: ~1000-2000 generazioni, sufficienti per i primi mesi con pochi utenti

### Vantaggi di questo flusso

- **Nessuna curva di apprendimento**: l'insegnante non vede mai codice, solo linguaggio naturale
- **Valore immediato**: le lezioni pre-confezionate dimostrano subito il prodotto
- **Flessibilità totale**: l'AI genera, l'umano corregge e approva
- **Monetizzazione naturale**: il piano free fa provare, i limiti spingono all'upgrade
- **Costi contenuti**: DeepSeek è economico, i margini sono buoni anche col piano base
- **Personalizzazione senza vincoli**: ogni insegnante può ottenere esattamente il contenuto che gli serve, nel tono e nello stile che preferisce

---

## 9. Cosa NON toccare del sito attuale

- **index.html** resta la home del sito didattico, con le sue tracce
- **Tutte le lezioni esistenti** restano invariate
- **Struttura delle cartelle** esistente non cambia
- Il portale vive in un **repository completamente separato**, con una sua identità visiva (può ispirarsi agli stili, ma è un prodotto diverso)
- L'unico "ponte" è questo documento `.md`, che fa da specifica

---

## 10. Checklist per l'avvio del nuovo repository

- [ ] Creare il repo `portale-generatore-lezioni` (o nome scelto)
- [ ] Copiare questo `.md` nella root del nuovo repo come `SPECS_PORTALE.md`
- [ ] Ottenere i programmi di Liceo Scientifico e SIA (li stai recuperando tu)
- [ ] Scegliere lo stack (backend Node/Express + Supabase consigliato)
- [ ] Implementare il wizard di onboarding (registrazione + raccolta dati insegnante)
- [ ] Implementare il generatore della home page (template index.html popolato coi dati)
- [ ] Implementare il primo template di lezione (Standard Dark) come proof-of-concept
- [ ] Integrare DeepSeek API per la generazione testi
- [ ] Popolare il DB con i primi 2 indirizzi
- [ ] Preparare 2-3 lezioni pre-confezionate per ogni indirizzo/materia/classe
- [ ] Implementare l'editor con modifica AI (prompt «cambia X in Y»)
- [ ] Testare la generazione + modifica di 5 lezioni campione
- [ ] Aggiungere auth e sistema crediti/rate limiting
- [ ] Pubblicare e testare con 2-3 insegnanti reali
- [ ] Iterare in base al feedback
- [ ] Aggiungere pagamento (Stripe)
- [ ] Aggiungere altri indirizzi ministeriali
- [ ] Opzionale: export ZIP del sito, dominio personalizzato per utenti premium

---

> **Versione:** 2.0 — 20 ottobre 2026
> **Ultima modifica:** aggiunto il flusso utente completo (sezione 7.7) con onboarding, generazione sito, lezioni pre-confezionate, modifica AI e sistema crediti.
> **Prossimo passo:** tu recuperi i programmi ministeriali, io apro il nuovo repo con questa specifica come punto di partenza.