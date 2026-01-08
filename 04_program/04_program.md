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

##　セル結合
<tr>
  <td class="time">11:30–13:00</td>
  <td colspan="3"><span class="badge">Break</span> Lunch</td>
</tr>

---

## 2) 縦方向のセル結合（rowspan）— “同じセッションが複数枠にまたがる” も可能
<td rowspan="2"> ... </td>
を使います。ポイントは **rowspan を置いた列の、次行の同じ列<td>を省略する** ことです。

例：IPE Hall の Keynote が 09:00–11:30（2行）にまたがるケース

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
      <td class="time">09:00–09:50</td>
      <td rowspan="2"><span class="badge">Keynote</span> Keynote Lecture</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td class="time">10:00–11:30</td>
      <!-- ここは IPE Hall 列を“書かない” (rowspan中) -->
      <td><span class="badge">Symposium</span> Symposium 1</td>
      <td><span class="badge">Oral</span> Oral Session 1</td>
    </tr>
  </tbody>
</table>

---
