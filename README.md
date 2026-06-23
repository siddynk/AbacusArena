[README.md](https://github.com/user-attachments/files/29233933/README.md)
<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0a0a1a,50:1c1c42,100:281c00&height=200&section=header&text=🧮%20Abacus%20Arena&fontSize=48&fontColor=fbbf24&animation=twinkling&fontAlignY=38&desc=Real-Time%20Mental%20Math%20Quiz%20Platform&descAlignY=58&descSize=18&descColor=c9a227" width="100%"/>

<br/>

[![Live Demo](https://img.shields.io/badge/▶%20LIVE%20DEMO-smartkidabacus.netlify.app-fbbf24?style=for-the-badge&logo=netlify&logoColor=black)](https://smartkidabacus.netlify.app/)
[![GitHub](https://img.shields.io/badge/Source-siddynk-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/siddynk/abacus-arena)
[![Built With](https://img.shields.io/badge/Built%20With-Vanilla%20JS-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://github.com/siddynk/abacus-arena)
[![Database](https://img.shields.io/badge/Database-Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)](https://supabase.com)

<br/>

> **A fully deployed, real-time math quiz platform built for actual students.**
> No React. No frameworks. No backend server. Just one HTML file, vanilla JavaScript, and a real database.

</div>

---

## 🎯 What Is This?

Abacus Arena is a **live, production-grade quiz platform** I built from scratch for my abacus training students because no existing tool fit what I needed.

It runs entirely in a **single HTML file** — no build step, no Node.js, no framework — yet supports real-time data sync, live admin controls, animated themes, and timed sessions across multiple difficulty levels.

This is not a tutorial clone. **Real students use this.**

---

## ✨ Features

<table>
<tr>
<td width="50%">

### 🎮 Student Experience
- 🌌 **2 selectable themes** — Cosmic & Saffron with animated floating doodles
- 📱 **Mobile-first** — built for phones, not laptops
- ⏱ **Timed questions** — 10s (1-2 digit) / 20s (3 digit / mix)
- 💡 **Per-question hints** showing the exact abacus formula to use
- 🎯 **Score & accuracy** tracked after every session
- 🔊 **Listening Abacus mode** — questions dictated via Speech Synthesis API

</td>
<td width="50%">

### 🏛 Admin Controls
- 🔐 **Password-protected** admin dashboard
- 🔴 **Kill Switch** — disables the test for ALL students instantly, even mid-question
- 📊 **Live leaderboard** — scores, accuracy, digit mode, timestamp
- ✉️ **Custom lock message** — set what students see when test is disabled
- 🗑 **Clear all records** with one click
- 📡 **Live watcher** — polls every 4s, students get locked out in real time

</td>
</tr>
</table>

---

## 📚 Formula Modules

| Module | Description | Rule Used |
|---|---|---|
| 🔵 **Big B** | Complements of 10 | `+6 → +10, −4` on ones place |
| 🟣 **Small Sis** | Complements of 5 | `+4 → +5, −1` upper bead |
| 🟠 **Combo** | Mixed Big B & Small Sis | Both rules apply |
| 🟢 **Listening Abacus** | Audio-dictated questions | Mental tracking only |

### Digit Modes

| Mode | Timer | Questions | Format |
|---|---|---|---|
| 1 Digit | 10s | 20 | `7 + 6 − 3 + 8 − 5` |
| 2 Digit | 10s | 20 | `28 + 15 − 14 + 27` |
| 3 Digit | 20s | 13 | `280 + 150 − 140 + 270` |
| 2 & 3 Mix | 20s | 13 | `75 + 16 + 130 + 18 + 160` |

> Each session draws from a **shuffled bank of 20 unique questions** — no repeats within a session.

---

## 🛠 Tech Stack

<div align="center">

| Layer | Technology |
|---|---|
| **Frontend** | HTML5 · CSS3 · Vanilla JavaScript (zero frameworks) |
| **Database** | Supabase (PostgreSQL) |
| **Realtime Sync** | Supabase REST API + client-side polling |
| **Voice** | Web Speech Synthesis API |
| **Hosting** | Netlify |
| **Auth** | Client-side password gate + URL param (`?admin=true`) |

</div>

---

## 🏗 Architecture

```
┌─────────────────────────────────────────────────────┐
│                  index.html (single file)            │
│                                                      │
│  ┌─────────────┐        ┌──────────────────────┐    │
│  │   Student   │        │   Admin Dashboard    │    │
│  │   Flow      │        │   (?admin=true)      │    │
│  │             │        │                      │    │
│  │ Theme Pick  │        │  Kill Switch ──────────────────► Supabase
│  │ Name Entry  │        │  Leaderboard  ◄────────────────── app_settings
│  │ Dashboard   │        │  Clear Records         │    │
│  │ Quiz Game   │        └──────────────────────┘    │
│  │ Results     │                                     │
│  └──────┬──────┘                                     │
│         │                                            │
│         ▼                                            │
│  Kill Watcher (polls every 4s) ────────────────────────────► Supabase
│  Score Submit ──────────────────────────────────────────────► abacus_scores
└─────────────────────────────────────────────────────┘
```

---

## 🚀 Run It Yourself

**1. Clone the repo**
```bash
git clone https://github.com/siddynk/abacus-arena.git
cd abacus-arena
```

**2. Set up Supabase**

Create a free project at [supabase.com](https://supabase.com), then run this in the SQL Editor:

```sql
-- Scores table
create table abacus_scores (
  id uuid primary key default gen_random_uuid(),
  student_name text,
  topic text,
  digit_mode text,
  score int,
  total_questions int,
  accuracy_rate int,
  created_at timestamptz default now()
);

-- App settings (kill switch)
create table app_settings (
  key text primary key,
  value text
);

insert into app_settings (key, value) values
  ('kill_switch', 'off'),
  ('kill_message', 'The test is currently not active.');

-- Enable RLS
alter table abacus_scores enable row level security;
alter table app_settings enable row level security;

create policy "Allow all" on abacus_scores for all using (true) with check (true);
create policy "Allow all" on app_settings for all using (true) with check (true);
```

**3. Add your credentials**

In `index.html`, update these three lines:
```js
const SUPABASE_URL   = 'YOUR_SUPABASE_URL';
const SUPABASE_KEY   = 'YOUR_SUPABASE_ANON_KEY';
const ADMIN_PASSWORD = 'YOUR_ADMIN_PASSWORD';
```

**4. Open in browser**
```
Just open index.html — no server needed.
```

**5. Deploy (optional)**

Drag and drop the folder into [netlify.com/drop](https://app.netlify.com/drop) — live in 30 seconds.

---

## 🔐 Admin Access

| URL | Access |
|---|---|
| `yoursite.com/` | Student view |
| `yoursite.com/?admin=true` | Admin password gate |

> **Admin always bypasses the kill switch** — you can never lock yourself out.

---

## 🐛 Interesting Bug I Fixed in Production

The kill switch button wasn't working despite Supabase being connected. After an hour of debugging in DevTools — querying the database live from the browser console, checking RLS policies, calling functions directly — I found it:

```js
// The bug — one word
"INACTIVE".includes("ACTIVE") // → true 😭

// The fix
st.textContent.trim().startsWith("ACTIVE") // → correctly false when INACTIVE
```

One substring. One hour. One lesson about never using `.includes()` for state checks.

---

## 📁 Project Structure

```
abacus-arena/
└── index.html        ← The entire application (HTML + CSS + JS)
```

Yes, really. The whole thing.

---

## 👨‍💻 Built By

<div align="center">

**Sid Nayak** — 4th Year B.E. Information Science @ Sapthagiri College of Engineering

[![LinkedIn](https://img.shields.io/badge/LinkedIn-nayak--sid-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/nayak-sid)
[![GitHub](https://img.shields.io/badge/GitHub-siddynk-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/siddynk)
[![LeetCode](https://img.shields.io/badge/LeetCode-sidspams-FFA116?style=for-the-badge&logo=leetcode&logoColor=black)](https://leetcode.com/u/sidspams/)

</div>

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:281c00,50:1c1c42,100:0a0a1a&height=100&section=footer" width="100%"/>

*Built because the tool I needed didn't exist. So I built it.*

</div>
