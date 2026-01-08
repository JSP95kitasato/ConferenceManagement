![ヘッダー](./01_picture/header.jpg)
# プログラム編成

## 方法1（おすすめ）：Markdown表で作る（軽量・コピペ最強）

### Day 1 — 2026-03-21 (Sat)
| Time | Session | Venue | Notes |
|---:|---|---|---|
| 08:50–09:00 | Opening Ceremony | IPE Hall |  |
| 09:00–09:50 | Keynote Lecture | IPE Hall |  |
| 10:00–11:30 | Symposium 1 | Room A |  |
| 11:30–13:00 | Lunch |  |  |
| 13:00–14:30 | Oral Session 1 | Room A |  |
| 14:40–16:10 | BPA (Best Presentation Award) | Room A |  |
| 16:20–17:10 | General Assembly | IPE Hall |  |
| 18:00–20:00 | Banquet | Cafeteria |  |

---

## 方法2：HTML（複数会場の“横並びタイムテーブル”に強い）
※ GitHub Pages ならだいたいそのまま動きます（Markdown内HTML可）

<style>
.tt { width: 100%; border-collapse: collapse; font-size: 14px; }
.tt th, .tt td { border: 1px solid #ddd; padding: 8px; vertical-align: top; }
.tt th { background: #f6f6f6; }
.tt .time { white-space: nowrap; width: 110px; font-weight: 600; }
.tt .room { width: 33%; }
.badge { display:inline-block; padding:2px 6px; border-radius: 6px; background:#e8f4ff; }
</style>

<h3>Day 1 — 2026-03-21 (Sat)</h3>
<table class="tt">
  <thead>
    <tr>
      <th class="time">Time</th>
      <th class="room">IPE Hall</th>
      <th class="room">Room A</th>
      <th class="room">Room B</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="time">08:50–09:00</td>
      <td><span class="badge">Opening</span> Opening Ceremony</td>
      <td></td><td></td>
    </tr>
    <tr>
      <td class="time">09:00–09:50</td>
      <td><span class="badge">Keynote</span> Keynote Lecture</td>
      <td></td><td></td>
    </tr>
    <tr>
      <td class="time">10:00–11:30</td>
      <td></td>
      <td><span class="badge">Symposium</span> Symposium 1</td>
      <td><span class="badge">Oral</span> Oral Session 1</td>
    </tr>
    <tr>
      <td class="time">11:30–13:00</td>
      <td colspan="3"><span class="badge">Break</span> Lunch</td>
    </tr>
    <tr>
      <td class="time">13:00–14:30</td>
      <td></td>
      <td><span class="badge">Oral</span> Oral Session 2</td>
      <td><span class="badge">Poster</span> Poster Setup</td>
    </tr>
  </tbody>
</table>

---

## 方法3：CSV→自動生成（規模が大きい時に最強）
<style>
  .tt-wrap { max-width: 1100px; margin: 0 auto; }
  .tt-h1 { margin: 12px 0 8px; }
  .tt-note { color:#666; font-size: 0.95em; margin: 0 0 16px; }
  .tt-day { margin: 28px 0 12px; }
  table.tt { width: 100%; border-collapse: collapse; font-size: 14px; }
  .tt th, .tt td { border: 1px solid #ddd; padding: 8px; vertical-align: top; }
  .tt th { background: #f6f6f6; }
  .tt .time { white-space: nowrap; width: 120px; font-weight: 600; }
  .tt .cell { min-width: 160px; }
  .tt .session { margin: 0 0 10px; padding: 8px; border-radius: 8px; background: #f8f8f8; }
  .tt .type { font-weight: 700; margin-right: 6px; }
  .tt .title { font-weight: 650; }
  .tt .meta { color:#555; margin-top: 4px; font-size: 0.95em; }
  .tt .meta div { margin-top: 2px; }
  .tt a { text-decoration: none; }
  .tt a:hover { text-decoration: underline; }
</style>

<div class="tt-wrap">
  <h1 class="tt-h1">Timetable</h1>
  <div class="tt-note">
    Data source: <code>95thJSPtaimtable.csv</code>（編集はCSVのみ。ページは自動反映）
  </div>

  <div id="timetable-root">Loading…</div>
</div>

<script>
/** Minimal CSV parser (supports quoted cells) */
function parseCSV(text) {
  const rows = [];
  let i = 0, field = "", row = [], inQuotes = false;

  while (i < text.length) {
    const c = text[i];

    if (inQuotes) {
      if (c === '"' && text[i + 1] === '"') { field += '"'; i += 2; continue; }
      if (c === '"') { inQuotes = false; i++; continue; }
      field += c; i++; continue;
    } else {
      if (c === '"') { inQuotes = true; i++; continue; }
      if (c === ',') { row.push(field); field = ""; i++; continue; }
      if (c === '\n') { row.push(field); rows.push(row); field = ""; row = []; i++; continue; }
      if (c === '\r') { i++; continue; }
      field += c; i++; continue;
    }
  }
  if (field.length || row.length) { row.push(field); rows.push(row); }
  return rows;
}

function toMinutes(hhmm) {
  const [h, m] = hhmm.split(":").map(Number);
  return h * 60 + m;
}

function uniq(arr) { return Array.from(new Set(arr)); }

function escapeHTML(s) {
  return (s ?? "").toString()
    .replaceAll("&","&amp;")
    .replaceAll("<","&lt;")
    .replaceAll(">","&gt;");
}

async function main() {
  const root = document.getElementById("timetable-root");

  const res = await fetch("./95thJSPtaimtable.csv", { cache: "no-store" });
  if (!res.ok) {
    root.innerHTML = `<div style="color:#b00;">Failed to load CSV: ${res.status}</div>`;
    return;
  }

  const csvText = await res.text();
  const data = parseCSV(csvText.trim());
  if (data.length < 2) {
    root.innerHTML = `<div style="color:#b00;">CSV has no data rows.</div>`;
    return;
  }

  const header = data[0].map(h => h.trim());
  const rows = data.slice(1).map(r => {
    const obj = {};
    header.forEach((h, idx) => obj[h] = (r[idx] ?? "").trim());
    return obj;
  });

  // Group by date
  const dates = uniq(rows.map(r => r.date)).sort();
  let html = "";

  for (const date of dates) {
    const dayRows = rows.filter(r => r.date === date);

    const dayLabel = (dayRows.find(r => r.day_label)?.day_label) || date;
    const rooms = uniq(dayRows.map(r => r.room).filter(x => x && x !== "ALL")).sort();

    // time slots (start-end pairs)
    const slots = uniq(dayRows.map(r => `${r.start}-${r.end}`))
      .sort((a,b) => toMinutes(a.split("-")[0]) - toMinutes(b.split("-")[0]));

    html += `<h2 class="tt-day">${escapeHTML(dayLabel)} <span style="color:#666;font-weight:500;">(${escapeHTML(date)})</span></h2>`;
    html += `<table class="tt"><thead><tr><th class="time">Time</th>`;

    for (const room of rooms) html += `<th>${escapeHTML(room)}</th>`;
    html += `</tr></thead><tbody>`;

    for (const slot of slots) {
      const [start, end] = slot.split("-");
      const slotRows = dayRows.filter(r => r.start === start && r.end === end);

      // If there is an ALL row at this slot -> colspan row
      const allRows = slotRows.filter(r => r.room === "ALL");
      if (allRows.length) {
        const blocks = allRows.map(r => renderSession(r)).join("");
        html += `<tr><td class="time">${escapeHTML(start)}–${escapeHTML(end)}</td>`;
        html += `<td class="cell" colspan="${rooms.length}">${blocks}</td></tr>`;
        continue;
      }

      html += `<tr><td class="time">${escapeHTML(start)}–${escapeHTML(end)}</td>`;

      for (const room of rooms) {
        const cellRows = slotRows.filter(r => r.room === room);
        const blocks = cellRows.length ? cellRows.map(r => renderSession(r)).join("") : "";
        html += `<td class="cell">${blocks}</td>`;
      }
      html += `</tr>`;
    }

    html += `</tbody></table>`;
  }

  root.innerHTML = html;

  function renderSession(r) {
    const type = escapeHTML(r.session_type);
    const title = escapeHTML(r.title);
    const speakers = escapeHTML(r.speakers);
    const notes = escapeHTML(r.notes);
    const link = (r.link ?? "").trim();

    const titleHTML = link
      ? `<a href="${escapeHTML(link)}" target="_blank" rel="noopener"><span class="title">${title}</span></a>`
      : `<span class="title">${title}</span>`;

    const metaBits = [];
    if (speakers) metaBits.push(`<div><b>Speakers:</b> ${speakers}</div>`);
    if (notes) metaBits.push(`<div><b>Notes:</b> ${notes}</div>`);

    return `
      <div class="session">
        <div><span class="type">${type}</span>${titleHTML}</div>
        ${metaBits.length ? `<div class="meta">${metaBits.join("")}</div>` : ``}
      </div>
    `;
  }
}

main().catch(err => {
  const root = document.getElementById("timetable-root");
  root.innerHTML = `<pre style="color:#b00;">${String(err)}</pre>`;
});
</script>