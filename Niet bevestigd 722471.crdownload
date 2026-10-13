// ---------- CONSTANTS ----------
const KEY = "peloton-manager-v3";
const STATS = ["climb", "sprint", "tt", "stamina"];
const LABEL = { climb: "Climbing", sprint: "Sprint", tt: "Time Trial", stamina: "Stamina" };
const ICON = { climb: "⛰️", sprint: "⚡", tt: "⏱️", stamina: "🔋" };
const FIRST = ["Luca","Jonas","Mathieu","Tiesj","Pascal","Ruben","Oscar","Matteo","Tom","Elias","Pieter","Diego","Noah","Victor","Jasper","Ivan","Marco","Sander","Kasper","Anton","Felix","Hugo","Mikkel","Liam"];
const LAST = ["Van der Berg","Pedersen","Rossi","Dubois","Kowalski","Moreau","García","Janssen","Hansen","Lindqvist","Verhoeven","Bianchi","Novak","Ferreira","Kruger","Walker","De Smet","Ortiz","Lemaire","Visser","Brandt","Costa"];
const AI_TEAMS = ["Nordic Velo","Iberia Cycling","Alpen Racing","Lowlands Elite","Adriatic Force","Bavaria Pulse","Gallic Stars","Andes Wheels","Baltic Storm","Sunrise Pro","Red Dune","Silver Peak","Coastline CC","Ironwood","Tundra Team","Mistral Racing","Aurora Velo","Pacific Rays","Granite Cycling","Harbor Elite","Sierra Pro","Volcano Team","Meridian","Helix Racing"];
const KITS = [
  { name: "Crimson",  a: "#c8102e", b: "#ffffff" },
  { name: "Royal",    a: "#1d4ed8", b: "#fbbf24" },
  { name: "Emerald",  a: "#059669", b: "#ecfdf5" },
  { name: "Violet",   a: "#7c3aed", b: "#f0abfc" },
  { name: "Sunset",   a: "#ea580c", b: "#111827" },
  { name: "Midnight", a: "#0f172a", b: "#38bdf8" },
];
const UCI_TABLE = [100,80,60,50,45,40,36,32,28,24,20,16,12,10,8];
const PRIZE = [2000,1200,800,500,300,200,150,100,80,60];
const TOKEN_PRIZE = [10,6,3];
const GREEN_TABLE = [50,30,20,18,16,14,12,10,8,7,6,5,4,3,2];
const KOM_TABLE = [10,6,4,2,1];
const SEASON_END = [10, 1];
const PROFILE = {
  flat:     { sprint: .5,  stamina: .3,  tt: .1,  climb: .1, noise: 14 },
  sprint:   { sprint: .7,  stamina: .2,  tt: .1,  climb: 0,  noise: 10 },
  cobble:   { sprint: .35, stamina: .35, tt: .15, climb: .15, noise: 16 },
  hill:     { climb: .35, sprint: .2,  stamina: .3, tt: .15, noise: 12 },
  mountain: { climb: .6,  stamina: .3,  tt: .1,  sprint: 0,  noise: 8 },
  itt:      { tt: .7,    stamina: .2,  climb: .1, sprint: 0,  noise: 3 },
};
const TERRAIN_NAME = { flat: "Flat", sprint: "Sprint", cobble: "Cobbles", hill: "Hills", mountain: "Mountain", itt: "Time trial" };
const TERRAIN_ICON = { flat: "🛣️", sprint: "⚡", cobble: "🧱", hill: "⛰️", mountain: "🏔️", itt: "⏱️" };
const JERSEYS = [
  { key: "yellow", label: "Yellow · GC" },
  { key: "green",  label: "Green · Points" },
  { key: "polka",  label: "Polka dot · Mountains" },
  { key: "white",  label: "White · Best young" },
];
const SCHEDULE = [
  { name: "Opening Classic",      weight: 1,   stages: [[2,22,"cobble"]] },
  { name: "Coastal Sprint",       weight: 1,   stages: [[3,8,"sprint"]] },
  { name: "Hill Country Classic", weight: 1.1, stages: [[3,22,"hill"]] },
  { name: "Flemish Cobbles",      weight: 1.2, stages: [[4,5,"cobble"]] },
  { name: "Ardennes Hills",       weight: 1.2, stages: [[4,19,"mountain"]] },
  { name: "Three Peaks Tour",     weight: 1.5, stages: [[5,10,"flat"],[5,11,"hill"],[5,12,"mountain"]] },
  { name: "River Time Trial",     weight: 1,   stages: [[6,2,"itt"]] },
  { name: "Alpine Stage Race",    weight: 1.6, stages: [[6,20,"hill"],[6,21,"mountain"],[6,22,"itt"],[6,23,"mountain"]] },
  { name: "Grand Tour",           weight: 2,   stages: [[7,4,"flat"],[7,5,"sprint"],[7,6,"hill"],[7,7,"itt"],[7,8,"mountain"],[7,9,"mountain"],[7,10,"flat"]] },
  { name: "Sprinters' Gala",      weight: 1.1, stages: [[7,25,"sprint"]] },
  { name: "Autumn Peaks",         weight: 1.2, stages: [[8,15,"mountain"]] },
  { name: "Grand Final Classic",  weight: 1.5, stages: [[9,20,"cobble"]] },
];

// ---------- HELPERS ----------
const $ = s => document.querySelector(s);
const rnd = (a, b) => a + Math.random() * (b - a);
const pick = a => a[Math.floor(Math.random() * a.length)];
const clamp = (v, a, b) => Math.max(a, Math.min(b, v));
const uid = () => Math.random().toString(36).slice(2, 10);
const money = n => "$" + Math.round(n).toLocaleString("en-US");
const ovr = r => Math.round(STATS.reduce((a, s) => a + r.stats[s], 0) / STATS.length);
const value = r => Math.max(500, Math.round((ovr(r) - 30) ** 2 * 4));
const dayOf = (m, d) => Math.round((Date.UTC(S.year, m - 1, d) - Date.UTC(S.year, 0, 1)) / 864e5);
const seasonEnd = () => dayOf(SEASON_END[0], SEASON_END[1]);
const fmtDate = day => new Date(Date.UTC(S.year, 0, 1) + day * 864e5)
  .toLocaleDateString("en-GB", { weekday: "short", day: "numeric", month: "short", timeZone: "UTC" });
const fmtTime = s => {
  s = Math.max(0, Math.round(s));
  return `${Math.floor(s / 3600)}:${String(Math.floor(s % 3600 / 60)).padStart(2, "0")}:${String(s % 60).padStart(2, "0")}`;
};
const fmtGap = s => (s <= 0 ? "+0:00:00" : `+${fmtTime(s)}`);

function toast(msg) {
  const t = document.createElement("div");
  t.className = "toast";
  t.textContent = msg;
  document.body.appendChild(t);
  setTimeout(() => t.remove(), 3000);
}

// ---------- STATE ----------
let S = null;
let view = "dashboard";
let selKit = 0;

function load() {
  try {
    const g = JSON.parse(localStorage.getItem(KEY));
    return g && g.riders && g.events ? g : null;
  } catch { return null; }
}
function save() { if (S) localStorage.setItem(KEY, JSON.stringify(S)); }
function news(msg) { S.news.unshift(msg); S.news = S.news.slice(0, 30); }

// ---------- RIDERS ----------
function makeRider(boost = 0, team = "Free agent", youth = false) {
  const age = youth ? 17 + Math.floor(rnd(0, 5)) : 19 + Math.floor(rnd(0, 16));
  const r = { id: uid(), name: `${pick(FIRST)} ${pick(LAST)}`, team, age, tired: 0, trainedDay: -1, stats: {}, potential: 0, price: 0 };
  STATS.forEach(s => r.stats[s] = Math.round(clamp(rnd(38, 66) + boost, 20, 88)));
  const o = ovr(r);
  r.potential = Math.round(clamp(o + Math.max(0, 28 - age) * 1.1 + rnd(2, 12), o + 1, 96));
  return r;
}
function scouts() {
  return Array.from({ length: 3 }, () => {
    const r = makeRider(rnd(4, 14), "Free agent", true);
    r.price = Math.round(value(r) * .9);
    return r;
  });
}
function listings() {
  return Array.from({ length: 5 }, () => {
    const r = makeRider(rnd(0, 10), pick(AI_TEAMS));
    r.price = Math.round(value(r) * 1.15);
    return r;
  });
}

// ---------- CALENDAR ----------
function makeCalendar() {
  return SCHEDULE.map((e, i) => ({
    id: i, name: e.name, weight: e.weight, done: false,
    stages: e.stages.map(([m, d, t]) => ({ day: dayOf(m, d), terrain: t, done: false, missed: false })),
    field: null, meta: {}, gc: {}, pts: {}, kom: {}, jerseys: null,
  }));
}

function newGame(team, mgr, kit) {
  S = { team, mgr, kit, year: 2026, season: 1, day: 0, coins: 3000, tokens: 25,
        ai: [], riders: [], pool: [], market: [], lineup: [], events: [], uci: {}, news: [] };
  S.day = dayOf(2, 1);
  AI_TEAMS.forEach(t => { for (let i = 0; i < 8; i++) S.ai.push(makeRider(rnd(-2, 10), t)); });
  S.riders = Array.from({ length: 6 }, () => makeRider(rnd(0, 6), team));
  S.lineup = S.riders.slice(0, 4).map(r => r.id);
  S.events = makeCalendar();
  S.pool = scouts();
  S.market = listings();
  news(`Welcome, ${mgr}! Season ${S.year} is about to start. Build ${team} into a UCI powerhouse.`);
  return S;
}

function buildField(ev) {
  const mine = S.lineup
    .filter(id => S.riders.some(r => r.id === id))
    .map(id => ({ id, user: true }));
  const ai = [...S.ai].sort(() => Math.random() - .5).slice(0, 40).map(r => ({ id: r.id, user: false }));
  ev.field = [...mine, ...ai];
}

// Runs one stage. skipUser = true means the player missed it.
function runStage(ev, si, skipUser) {
  if (!ev.field) buildField(ev);
  const st = ev.stages[si], prof = PROFILE[st.terrain];
  const entries = [];
  ev.field.forEach(f => {
    const r = f.user ? S.riders.find(x => x.id === f.id) : S.ai.find(x => x.id === f.id);
    if (!r) return;
    ev.meta[r.id] = { name: r.name, team: f.user ? S.team : r.team, user: f.user, age: r.age };
    entries.push({ r, user: f.user, absent: f.user && skipUser });
  });

  const scored = entries.filter(e => !e.absent).map(e => {
    const base = STATS.reduce((a, s) => a + (prof[s] || 0) * e.r.stats[s], 0);
    return { ...e, perf: base - e.r.tired * 0.25 + rnd(0, prof.noise) };
  }).sort((a, b) => b.perf - a.perf);

  const top = scored.length ? scored[0].perf : 0;
  const times = scored.map(e => ({ ...e, time: (top - e.perf) * 12 }))
    .concat(entries.filter(e => e.absent).map(e => ({ ...e, time: 1800 })))
    .sort((a, b) => a.time - b.time);

  times.forEach(e => ev.gc[e.r.id] = (ev.gc[e.r.id] || 0) + e.time);
  const finishers = times.filter(e => !e.absent);
  finishers.slice(0, 15).forEach((e, i) => ev.pts[e.r.id] = (ev.pts[e.r.id] || 0) + GREEN_TABLE[i]);
  if (st.terrain === "hill" || st.terrain === "mountain")
    finishers.slice(0, 5).forEach((e, i) => ev.kom[e.r.id] = (ev.kom[e.r.id] || 0) + KOM_TABLE[i]);

  scored.forEach(e => { if (e.user) e.r.tired = clamp(e.r.tired + (st.terrain === "itt" ? 12 : 20), 0, 100); });
  st.done = true;

  return times.map((e, i) => ({
    place: i + 1, name: e.r.name, team: ev.meta[e.r.id].team,
    user: e.user, absent: e.absent, time: e.time,
  }));
}

function gcRanking(ev) { return Object.entries(ev.gc).sort((a, b) => a[1] - b[1]); }

function computeJerseys(ev) {
  const ranks = gcRanking(ev);
  const top = obj => { const e = Object.entries(obj).sort((a, b) => b[1] - a[1])[0]; return e ? e[0] : null; };
  return {
    yellow: ranks[0] ? ranks[0][0] : null,
    green: top(ev.pts),
    polka: top(ev.kom),
    white: (ranks.find(([id]) => ev.meta[id] && ev.meta[id].age <= 25) || [])[0] || null,
  };
}

function addUci(id, pts, m) {
  const e = S.uci[id] || { name: m.name, team: m.team, user: m.user, pts: 0 };
  e.pts += pts; e.team = m.team;
  S.uci[id] = e;
}

function finishEvent(ev) {
  if (ev.done) return;
  ev.done = true;
  const ranks = gcRanking(ev);
  if (!ranks.length) return;
  const mult = ev.weight * (ev.stages.length > 1 ? 2 : 1);
  ranks.slice(0, 15).forEach(([id], i) => addUci(id, Math.round(UCI_TABLE[i] * mult), ev.meta[id]));

  const isMine = id => ev.meta[id] && ev.meta[id].user && S.riders.some(r => r.id === id);
  ranks.slice(0, 10).forEach(([id], i) => {
    if (!isMine(id)) return;
    S.coins += Math.round(PRIZE[i] * ev.weight);
    if (i < 3) S.tokens += TOKEN_PRIZE[i];
  });

  ev.jerseys = computeJerseys(ev);
  Object.values(ev.jerseys).forEach(id => {
    if (id && isMine(id)) { S.coins += 500; S.tokens += 5; }
  });

  const w = ev.meta[ranks[0][0]];
  news(`🏆 ${ev.name} is over. Winner: ${w.name} (${w.team}).`);
}

// ---------- DAYS & SEASON ----------
function nextDay() {
  if (S.day >= seasonEnd()) return seasonEndModal();

  // Stages that were due today and not raced are missed
  S.events.forEach(ev => {
    if (ev.done) return;
    ev.stages.forEach((st, si) => {
      if (!st.done && st.day === S.day) {
        st.missed = true;
        runStage(ev, si, true);
        news(`You missed ${ev.name}${ev.stages.length > 1 ? ` (stage ${si + 1})` : ""}.`);
      }
    });
    if (ev.stages.every(s => s.done)) finishEvent(ev);
  });

  S.day++;
  S.riders.forEach(r => r.tired = clamp(r.tired - 12, 0, 100));
  S.tokens += 1;

  if (S.day % 7 === 0) {
    const income = 150 + S.riders.length * 15;
    S.coins += income;
    S.market = listings();
    news(`Sponsors paid ${money(income)}. New transfer listings are in.`);
  }
  const due = S.events.filter(e => !e.done && e.stages.some(s => !s.done && s.day === S.day));
  if (due.length) news(`🏁 Race day: ${due.map(e => e.name).join(", ")}`);
}

function newSeason() {
  S.season++;
  S.year++;
  S.day = dayOf(2, 1);
  S.events = makeCalendar();
  S.uci = {};
  S.riders.forEach(r => {
    r.age++; r.tired = 0; r.trainedDay = -1;
    if (r.age >= 30) r.potential = Math.max(ovr(r), r.potential - 2);
  });
  S.lineup = S.lineup.filter(id => S.riders.some(r => r.id === id));
  S.pool = scouts();
  S.market = listings();
  news(`🎉 Season ${S.season} begins in ${S.year}!`);
}

// ---------- TEAM RANKING ----------
function teamTable() {
  const t = {};
  [...AI_TEAMS, S.team].forEach(n => t[n] = 0);
  Object.values(S.uci).forEach(e => t[e.team] = (t[e.team] || 0) + e.pts);
  return Object.entries(t).sort((a, b) => b[1] - a[1]);
}
function uciRank() {
  const i = teamTable().findIndex(([n]) => n === S.team);
  return i < 0 ? "-" : i + 1;
}
function teamPts() { return (teamTable().find(([n]) => n === S.team) || [0, 0])[1]; }

// ---------- ACTIONS ----------
const actions = {
  lineup(id) {
    if (S.lineup.includes(id)) S.lineup = S.lineup.filter(x => x !== id);
    else if (S.lineup.length >= 8) toast("Your race team is full (max 8 riders).");
    else S.lineup.push(id);
  },

  sell(id) {
    const r = S.riders.find(x => x.id === id);
    if (!r) return;
    if (S.riders.length <= 3) return toast("You need at least 3 riders in your squad.");
    const p = Math.round(value(r) * .75);
    S.coins += p;
    S.riders = S.riders.filter(x => x !== r);
    S.lineup = S.lineup.filter(x => x !== id);
    news(`Sold ${r.name} for ${money(p)}.`);
  },

  buy(id) {
    const r = S.market.find(x => x.id === id);
    if (!r) return;
    if (S.riders.length >= 10) return toast("Squad is full (max 10 riders).");
    if (S.coins < r.price) return toast(`You need ${money(r.price)} to sign ${r.name}.`);
    S.coins -= r.price;
    S.market = S.market.filter(x => x !== r);
    r.team = S.team;
    S.riders.push(r);
    news(`Signed ${r.name} for ${money(r.price)}.`);
  },

  scout() {
    if (S.tokens < 5) return toast("Not enough tokens. Scouting costs 5.");
    S.tokens -= 5;
    S.pool = scouts();
    news("Scouts found 3 new prospects.");
  },

  sign(id) {
    const r = S.pool.find(x => x.id === id);
    if (!r) return;
    if (S.riders.length >= 10) return toast("Squad is full (max 10 riders).");
    if (S.coins < r.price) return toast(`You need ${money(r.price)} to sign ${r.name}.`);
    S.coins -= r.price;
    S.pool = S.pool.filter(x => x !== r);
    r.team = S.team;
    S.riders.push(r);
    news(`Signed ${r.name} from scouting.`);
  },

  train(id, stat) {
    const r = S.riders.find(x => x.id === id);
    if (!r) return;
    if (r.trainedDay === S.day) return toast(`${r.name} already trained today.`);
    if (S.tokens < 3) return toast("Not enough tokens. Training costs 3.");
    if (r.tired >= 80) return toast(`${r.name} is exhausted. Let them recover first.`);
    S.tokens -= 3;
    r.trainedDay = S.day;
    r.tired = clamp(r.tired + 10, 0, 100);
    const room = (r.potential - r.stats[stat]) / Math.max(1, r.potential);
    const chance = clamp(0.3 + room * 1.5 - (r.age > 29 ? 0.1 : 0), 0.05, 0.9);
    if (r.stats[stat] < r.potential && Math.random() < chance) {
      r.stats[stat] = Math.min(r.potential, r.stats[stat] + (Math.random() < .2 ? 2 : 1));
      news(`${r.name} improved ${LABEL[stat]} to ${r.stats[stat]}.`);
    } else {
      news(`${r.name}'s ${LABEL[stat]} session had no effect.`);
    }
  },

  stage(evI, si) {
    const ev = S.events[+evI];
    const st = ev && ev.stages[+si];
    if (!st) return toast("Race not found.");
    if (st.done) return toast("You already raced this stage.");
    if (st.day !== S.day) return toast(`This stage is on ${fmtDate(st.day)}. You can only race on that day.`);

    // Auto-pick a team if none was chosen
    if (!S.lineup.length) {
      S.lineup = [...S.riders]
        .sort((a, b) => ovr(b) - ovr(a))
        .slice(0, 4)
        .map(r => r.id);
      toast("No race team selected. Your best 4 riders were picked.");
    }

    const ranked = runStage(ev, +si, false);
    if (ev.stages.every(s => s.done)) finishEvent(ev);
    showModal(stageModal(ev, +si, ranked));
    news(`${ev.name}${ev.stages.length > 1 ? ` stage ${+si + 1}` : ""}: ${ranked[0].name} wins.`);
  },
};

// ---------- VIEWS ----------
function riderCard(r, extra = "") {
  return `<div class="card rider">
    <div class="card-head">
      <div><h3>${r.name}</h3><div class="meta">${r.team} · Age ${r.age}</div></div>
      <span class="ovr">${ovr(r)}</span>
    </div>
    <div class="meta">Potential ${r.potential} · Tiredness ${r.tired}%</div>
    ${STATS.map(s => `<div class="stat"><span>${ICON[s]} ${LABEL[s]}</span>
      <div class="bar"><i style="width:${r.stats[s]}%"></i></div><b>${r.stats[s]}</b></div>`).join("")}
    ${extra}
  </div>`;
}

function jerseyBlock(ev) {
  const j = computeJerseys(ev);
  return `<div class="jerseys">${JERSEYS.map(J => {
    const id = j[J.key], m = id && ev.meta[id];
    return `<div class="jersey ${J.key}"><span>${J.label}</span>${m ? m.name : "—"}</div>`;
  }).join("")}</div>`;
}

function stageButton(ev, i, st, si, isStage) {
  if (st.done) return `<em>✔ Done</em>`;
  if (st.missed) return `<em class="muted">Missed</em>`;
  if (st.day === S.day)
    return `<button class="small primary" data-act="stage:${i}:${si}">🏁 Race now</button>`;
  if (st.day < S.day) return `<em class="muted">Missed</em>`;
  return `<button class="small" disabled title="Only on race day">Opens ${fmtDate(st.day)}</button>`;
}

function eventCard(ev, i) {
  const isStage = ev.stages.length > 1;
  const started = Object.keys(ev.gc).length > 0;
  const top = gcRanking(ev).slice(0, 5);
  const dates = isStage
    ? `${fmtDate(ev.stages[0].day)} → ${fmtDate(ev.stages[ev.stages.length - 1].day)}`
    : fmtDate(ev.stages[0].day);
  return `<div class="card event ${ev.done ? "done" : ""}">
    <div class="card-head">
      <div>
        <h3>${ev.name}</h3>
        <div class="meta">${isStage ? `Stage race · ${ev.stages.length} stages` : "One-day race"} · ${dates}</div>
      </div>
      <span class="badge">UCI ×${ev.weight}</span>
    </div>
    ${ev.stages.map((st, si) => {
      const today = st.day === S.day && !st.done;
      return `<div class="stage ${st.done ? "done" : ""} ${st.missed ? "missed" : ""} ${today ? "today" : ""}">
        <span>${TERRAIN_ICON[st.terrain]} ${isStage ? `Stage ${si + 1} · ` : ""}${TERRAIN_NAME[st.terrain]}</span>
        <small>${fmtDate(st.day)}</small>
        ${stageButton(ev, i, st, si, isStage)}
      </div>`;
    }).join("")}
    ${started ? `<div class="gc">
      <h4>${ev.done ? "Final" : "Live"} classification</h4>
      ${top.map(([id, t], k) => `<div class="gc-row">
        <span>${k + 1}. ${ev.meta[id].name} <small>${ev.meta[id].team}</small></span>
        <b>${k === 0 ? fmtTime(t) : fmtGap(t - top[0][1])}</b></div>`).join("")}
      ${jerseyBlock(ev)}
    </div>` : ""}
  </div>`;
}

const views = {
  dashboard() {
    const todayEv = S.events.filter(e => !e.done && e.stages.some(s => s.day === S.day && !s.done));
    const nextP = S.events
      .flatMap(ev => ev.stages.filter(s => !s.done && !s.missed && s.day > S.day).map(s => ({ ev, s })))
      .sort((a, b) => a.s.day - b.s.day)[0];
    const raceBox = todayEv.length
      ? `<div class="card hero-card"><h3>🏁 Race day!</h3>
           ${todayEv.map(e => `<p>${e.name}</p>`).join("")}
           <button class="primary" data-view="races">Go to race calendar</button></div>`
      : `<div class="card"><h3>Next race</h3>
           ${nextP ? `<p><b>${nextP.ev.name}</b> — ${fmtDate(nextP.s.day)}</p>
                      <p class="hint">in ${nextP.s.day - S.day} day(s)</p>`
                   : "<p>No more races this season.</p>"}</div>`;
    return `<h2>Welcome, ${S.mgr}</h2>
      <div class="grid3">
        ${raceBox}
        <div class="card"><h3>Your squad</h3>
          <p class="big-num">${S.riders.length}<small> / 10 riders</small></p>
          <p class="hint">Race team: ${S.lineup.length}/8</p></div>
        <div class="card"><h3>Team UCI points</h3>
          <p class="big-num">${teamPts()}</p>
          <p class="hint">Ranked #${uciRank()}</p></div>
      </div>
      <h3 class="sec">Latest news</h3>
      <ul class="news">${S.news.slice(0, 12).map(n => `<li>${n}</li>`).join("")}</ul>`;
  },

  squad() {
    return `<h2>Squad <small>${S.riders.length}/10</small></h2>
      <p class="hint">Pick up to 8 riders for your race team. Race team: <b>${S.lineup.length}/8</b></p>
      <div class="grid">${S.riders.map(r => riderCard(r, `<div class="row">
        <button class="small ${S.lineup.includes(r.id) ? "on" : ""}" data-act="lineup:${r.id}">
          ${S.lineup.includes(r.id) ? "✓ In race team" : "+ Race team"}</button>
        <button class="small danger" data-act="sell:${r.id}">Sell ${money(Math.round(value(r) * .75))}</button>
      </div>`)).join("") || `<p class="hint">No riders. Sign some from Scouting.</p>`}</div>`;
  },

  training() {
    return `<h2>Training camp</h2>
      <p class="hint">Each rider trains once per day. Sessions cost 3 tokens and add tiredness. You have <b>${S.tokens}</b> tokens.</p>
      <div class="grid">${S.riders.map(r => riderCard(r, `
        <div class="train">${STATS.map(s => `<button class="small" data-act="train:${r.id}:${s}"
          ${r.trainedDay === S.day || r.tired >= 80 ? "disabled" : ""}>${ICON[s]} ${LABEL[s]}</button>`).join("")}</div>
        <p class="hint">${r.trainedDay === S.day ? "Trained today ✔" : r.tired >= 80 ? "Too tired, rest first" : "Ready to train"}</p>`)).join("")}</div>`;
  },

  scouting() {
    return `<h2>Scouting</h2>
      <p class="hint">Young prospects with high potential. A new batch costs 5 tokens.</p>
      <button class="primary" data-act="scout">🔭 Scout new batch (5 tokens)</button>
      <div class="grid">${S.pool.map(r => riderCard(r, `<div class="row">
        <span class="price">${money(r.price)}</span>
        <button class="small" data-act="sign:${r.id}">Sign</button></div>`)).join("")}</div>`;
  },

  transfers() {
    return `<h2>Transfer market</h2>
      <p class="hint">Listings refresh every week. Coins: <b>${money(S.coins)}</b> · Squad ${S.riders.length}/10</p>
      <div class="grid">${S.market.map(r => riderCard(r, `<div class="row">
        <span class="price">${money(r.price)}</span>
        <button class="small" data-act="buy:${r.id}">Buy</button></div>`)).join("")}</div>`;
  },

  races() {
    return `<h2>Race calendar · ${S.year}</h2>
      <p class="hint">Races can only be ridden on their day. Today is <b>${fmtDate(S.day)}</b>. Use Next day to move on.</p>
      <div class="cal">${S.events.map((ev, i) => eventCard(ev, i)).join("")}</div>`;
  },

  standings() {
    const riders = Object.entries(S.uci).map(([id, e]) => ({ id, ...e })).sort((a, b) => b.pts - a.pts).slice(0, 25);
    const teams = teamTable().slice(0, 15);
    return `<h2>UCI World Ranking · ${S.year}</h2>
      <div class="two">
        <div class="card"><h3>Riders</h3><table>
          ${riders.map((r, i) => `<tr class="${r.user ? "mine" : ""}"><td>${i + 1}</td><td>${r.name}</td>
            <td class="muted">${r.team}</td><td><b>${r.pts}</b></td></tr>`).join("") || "<tr><td>No points yet. Go race!</td></tr>"}
        </table></div>
        <div class="card"><h3>Teams</h3><table>
          ${teams.map(([n, p], i) => `<tr class="${n === S.team ? "mine" : ""}"><td>${i + 1}</td><td>${n}</td>
            <td><b>${p}</b></td></tr>`).join("")}
        </table></div>
      </div>`;
  },
};

// ---------- MODALS ----------
function showModal(html) {
  $("#modal-body").innerHTML = html;
  $("#modal").classList.remove("hidden");
}
function closeModal() { $("#modal").classList.add("hidden"); }

function stageModal(ev, si, ranked) {
  const st = ev.stages[si];
  const lead = ranked[0];
  const row = x => `<tr class="${x.user ? "mine" : ""}">
    <td>${x.place}</td><td>${x.name}</td><td class="muted">${x.team}</td>
    <td>${x.absent ? "DNS" : x.place === 1 ? fmtTime(x.time) : fmtGap(x.time - lead.time)}</td></tr>`;
  const top10 = ranked.slice(0, 10).map(row).join("");
  const mine = ranked.filter(x => x.user && !x.absent && x.place > 10).map(row).join("");
  return `<div class="modal-head">
      <span>${TERRAIN_ICON[st.terrain]}</span>
      <div><h2>${ev.name}</h2>
        <small>${ev.stages.length > 1 ? `Stage ${si + 1} of ${ev.stages.length} · ` : ""}${TERRAIN_NAME[st.terrain]} · ${fmtDate(st.day)}</small></div>
    </div>
    <p class="winner">🏆 Stage winner: <b>${lead.name}</b> (${lead.team})</p>
    <table class="res">${top10}${mine}</table>
    <h3>${ev.done ? "🎉 Final classification" : "Jerseys so far"}</h3>
    ${jerseyBlock(ev)}
    <div class="row"><button class="primary" data-act="close">Close</button></div>`;
}

function seasonEndModal() {
  showModal(`<h2>🏁 Season ${S.season} complete!</h2>
    <p>Your team finished <b>#${uciRank()}</b> in the UCI team ranking with <b>${teamPts()}</b> points.</p>
    <p class="hint">Your riders will age by a year and the next season's calendar starts on 1 February.</p>
    <button class="primary big" data-act="newSeason">Start season ${S.season + 1}</button>`);
}

// ---------- RENDER ----------
function render() {
  if (!S) return;
  const k = KITS[S.kit];
  const logo = $("#logo");
  logo.style.background = `linear-gradient(135deg, ${k.a}, ${k.b})`;
  logo.textContent = S.team.split(" ").map(w => w[0]).join("").slice(0, 3).toUpperCase();
  $("#team-title").textContent = S.team;
  $("#mgr-title").textContent = `Manager ${S.mgr} · Season ${S.season}`;
  $("#date").textContent = fmtDate(S.day);
  $("#coins").textContent = money(S.coins);
  $("#tokens").textContent = S.tokens;
  $("#uci-rank").textContent = uciRank();
  document.querySelectorAll("#nav [data-view]").forEach(b => b.classList.toggle("active", b.dataset.view === view));
  $("#app").innerHTML = views[view]();
  save();
}

function start() {
  $("#start").classList.add("hidden");
  $("#game").classList.remove("hidden");
  render();
}

function buildKits() {
  $("#kits").innerHTML = KITS.map((k, i) =>
    `<button class="kit ${i === selKit ? "sel" : ""}" data-kit="${i}" title="${k.name}"
      style="background:linear-gradient(135deg, ${k.a} 50%, ${k.b} 50%)"></button>`).join("");
}

// ---------- EVENTS ----------
$("#kits").addEventListener("click", e => {
  const b = e.target.closest("[data-kit]");
  if (!b) return;
  selKit = +b.dataset.kit;
  buildKits();
});

$("#new-btn").onclick = () => {
  const team = $("#team-name").value.trim() || "My Team";
  const mgr = $("#mgr-name").value.trim() || "Manager";
  if (load() && !confirm("Start a new career? Your current save will be deleted.")) return;
  newGame(team, mgr, selKit);
  start();
};

$("#continue-btn").onclick = () => {
  S = load();
  if (S) start();
};

document.addEventListener("click", e => {
  if (e.target.id === "modal") return closeModal();

  const v = e.target.closest("[data-view]");
  if (v) { view = v.dataset.view; return render(); }

  const b = e.target.closest("[data-act]");
  if (!b || b.disabled) return;
  const [name, ...args] = b.dataset.act.split(":");

  switch (name) {
    case "next":
      nextDay();
      break;
    case "close":
      closeModal();
      break;
    case "newSeason":
      newSeason();
      closeModal();
      break;
    case "reset":
      if (confirm("Delete this career for good?")) { localStorage.removeItem(KEY); location.reload(); }
      return;
    default:
      if (actions[name]) actions[name](...args);
  }
  render();
});

// ---------- BOOT ----------
buildKits();
const saved = load();
if (saved) {
  $("#continue-btn").classList.remove("hidden");
  $("#team-name").value = saved.team;
  $("#mgr-name").value = saved.mgr;
  selKit = saved.kit;
  buildKits();
}