<div align="center">

# SaafHawa 🌫️ → ✅
### صاف ہوا

**Air-quality apps show a number. SaafHawa tells you what to do.**

![Backend](https://img.shields.io/badge/backend-FastAPI-009688)a
![AI](https://img.shields.io/badge/AI-Groq%20·%20Llama%203.3%2070B-F4A23B)
![Bilingual](https://img.shields.io/badge/EN%20+%20اردو-bilingual-16B6D4)
![Pakistan](https://img.shields.io/badge/built%20for-Pakistan-0F7268)
![Hackathon](https://img.shields.io/badge/AI%20for%20Civic%20Innovation-2026-1668C7)

</div>

SaafHawa is an AI layer on top of public air-quality data. Instead of showing a
number, it asks who's in your home and turns today's local AQI into specific,
plain-language action — in **English and Urdu** — plus a chat that answers
everyday questions like *"is it safe to walk to school this morning?"*

Built for caregivers in air-sensitive households across **Lahore, Karachi & Islamabad** —
families with young children, elderly members, pregnant members, or people with
pollen, asthma, heart/lung conditions.

---

## 💡 Inspiration
 
Pakistan's cities sit near the top of every "most polluted air" list in the world,
and the people who pay the price are the ones least equipped to read the data —
parents deciding on a school run, an elderly relative breathing through a smoggy
morning, a pregnant woman, a delivery rider working outdoors all day, anyone with
asthma or pollen allergy.
 
Public AQI data already exists. The gap isn't measurement — it's **interpretation**.
Today the answer to *"is the air safe for my child right now?"* is a three-digit
number and a color, in English, with no idea what to actually do. SaafHawa closes
that last mile.
 
> **Themes:** Technology for Civic Good · Open Data & Access to Information
> **Hackathon:** AI for Civic Innovation 2026 — Code for Pakistan × FAST NUCES Islamabad × Grey Software × Scrimba

---

## ✨ Features

- 📍 **Live AQI** for Lahore · Karachi · Islamabad with honest US-EPA categories
- 👨‍👩‍👧 **Household profiles** — child, elderly, pregnant, allergies, heart/lung, outdoor worker — each reshapes the advice
- 🤖 **Bilingual AI action plan** — one-line verdict + 3–4 concrete steps + the why
- 🌐 **English and Urdu** by default, rendered in Nastaliq
- 💬 **Ask-anything chat** — grounded in your air level and household
- 🛟 **Offline-safe** — graceful bilingual fallback so guidance always loads
- 🇵🇰 **Pakistan-only data** — a 3-layer guard rejects foreign stations (no cross-border leaks)

## 🏗️ Architecture

```
Browser (3 pages)  →  FastAPI backend  →  WAQI air data  ┐
                          /aqi /plan /ask                 ├→ SQLite cache (PK-only)
                                 ↓                         ┘
                       Groq · Llama 3.3 70B  →  bilingual verdict JSON (EN + اردو)
```

Three endpoints, one frozen JSON contract (`backend/models.py`), and a
deterministic fallback at every hop so the demo never blanks.
 
Full diagrams: [`assets/architecture.png`](assets/architecture.png) · [`assets/workflow.png`](assets/workflow.png)
 

## 🚀 Quick start

> Tested on Python 3.10 – 3.13 (Windows, macOS, Linux).

### 1. Backend
 
```powershell
cd backend
python -m venv .venv
.venv\Scripts\Activate.ps1                # macOS/Linux: source .venv/bin/activate
python -m pip install -r requirements.txt
copy .env.example .env                    # paste tokens (optional)
python -m uvicorn main:app --reload --port 8000
```

Open **http://localhost:8000/docs** for live, clickable API docs.

### 2. Frontend
 
In a **second** terminal:
 
```powershell
cd frontend
python -m http.server 9000
```
 
Open **http://localhost:9000** → Home page. Click **Launch the app →** for the tool.
 
### Free tokens (no card needed)
 
| Service | Where | Used for |
|---|---|---|
| WAQI / aqicn | https://aqicn.org/data-platform/token/ | real Pakistani station AQI |
| Groq (Llama 3.3 70B) | https://console.groq.com/keys | bilingual AI interpretation |
 
> No tokens? **It still runs.** Both the API and the frontend use a deterministic
> bilingual fallback so guidance is shown even with zero internet.
 
## 📡 API

| Method | Path | Body | Returns |
|---|---|---|---|
| `GET` | `/aqi?area=lahore` | – | `AqiResponse` |
| `POST` | `/plan` | `{ area, household }` | `PlanResponse` |
| `POST` | `/ask` | `{ area, household, question }` | `AskResponse` |
 
**Areas** (Pakistan only): `lahore` · `karachi` · `islamabad`
 
**Household flags** (all optional, default `false`):
`child` · `elderly` · `pregnant` · `allergies` · `heart_lung` · `outdoor_worker`
 
**Try it**
```bash
curl -X POST http://localhost:8000/plan ^
  -H "Content-Type: application/json" ^
  -d "{\"area\":\"lahore\",\"household\":{\"child\":true,\"allergies\":true}}"
```
 
<details>
<summary>Sample response</summary>
```json
{
  "area": "lahore",
  "aqi": 168,
  "category": "Unhealthy",
  "verdict_en": "Keep your child indoors until noon.",
  "verdict_ur": "بچے کو دوپہر تک گھر کے اندر رکھیں۔",
  "steps_en": ["Close windows during peak hours", "Mask the school run",
               "Rinse off pollen after coming inside"],
  "steps_ur": ["صبح و شام کھڑکیاں بند رکھیں", "اسکول جاتے وقت ماسک پہنائیں",
               "اندر آنے کے بعد چہرہ دھوئیں"],
  "why_en": "Sensitive members react to fine particles well before healthy adults feel anything.",
  "why_ur": "باریک ذرات صحت مند بالغ سے پہلے حساس افراد کو متاثر کرتے ہیں۔",
  "source": "ai"
}
```
 
</details>
---

## 🧠 The AI Integration

 
[Groq's Llama 3.3 70B](https://console.groq.com/) (OpenAI-compatible API)
interprets the AQI in context of a specific household and emits a strict
bilingual verdict JSON. Why AI fits here: turning a number into safe,
*personalized*, *plain-language* advice — in two languages, including natural
Urdu — is a reasoning + NLG problem that rules can't cover at the needed
nuance and fluency. If the LLM is unreachable, a deterministic rule-based plan
still returns useful bilingual guidance — so the service degrades gracefully
and never goes blank.
 
---

## 📁 Project structure

```
saafhawa/
├── backend/                # FastAPI service
│   ├── main.py             #   /aqi · /plan · /ask
│   ├── aqi.py              #   WAQI + Pakistan-only 3-layer guard
│   ├── llm.py              #   Groq Llama 3.3 70B + deterministic fallback
│   ├── cache.py            #   SQLite TTL + last-good store
│   ├── config.py           #   env / tokens / supported areas
│   ├── models.py           #   🔒 frozen JSON contract (Pydantic)
│   ├── requirements.txt
│   └── .env.example
│
├── frontend/               # Static multi-page UI
│   ├── index.html          #   Home (landing)
│   ├── app.html            #   The tool itself
│   ├── about.html          #   Mission · team · partners
│   ├── styles.css          #   Mint paper · teal · Nastaliq theme
│   ├── shared.js           #   Nav + footer + team/partner renderers
│   ├── app.js              #   Tool state + API calls + offline fallback
│   ├── team.js             #   ★ EDIT team + partners here
│   └── config.js           #   ★ EDIT API_BASE here
│
├── assets/                 # Architecture diagrams, thumbnails, deck source
├── SaafHawa_Pitch.pptx     # 7-slide pitch deck (speaker notes included)
└── README.md
```
 
---
 
## 🗺️ Next Steps

Live low-cost sensor integration · WhatsApp / SMS delivery · next-day forecast ·
more cities · additional local languages.

---

## 🙏 Acknowledgements
 
SaafHawa exists because of the open work of many others. Sincere thanks to:
 
- **[Code for Pakistan](https://codeforpakistan.org/)** — for organizing the AI
  for Civic Innovation Hackathon 2026 and championing civic tech across the country.
- **[FAST NUCES Islamabad](https://www.nu.edu.pk/)** — for hosting the hackathon
  and providing the space and community.
- **[Grey Software](https://greysoftware.org/)** and **[Scrimba](https://scrimba.com/)**
  — for partnering with Code for Pakistan and supporting builders.
- **The [WAQI / aqicn](https://aqicn.org/) team** — for making global air-quality
  data freely accessible. SaafHawa is built directly on top of their open API.
- **[Groq](https://groq.com/) and the Llama team at Meta** — for the
  Llama 3.3 70B model and a generous free tier that made the AI layer possible
  at zero cost.
- **[FastAPI](https://fastapi.tiangolo.com/), [Pydantic](https://docs.pydantic.dev/), [Uvicorn](https://www.uvicorn.org/)**
  — the Python toolchain holding the backend together.
- **The [Noto Nastaliq Urdu](https://fonts.google.com/noto/specimen/Noto+Nastaliq+Urdu)
  team at Google** — for the typeface that lets Urdu render the way it should.
- **The U.S. State Department Air Quality Program** — whose embassy air monitors
  in Lahore, Karachi and Islamabad provide the ground-truth readings SaafHawa
  reads through WAQI.
- **The Pakistani caregivers, parents, and people living with allergies and
  asthma** — whose daily decisions made the problem visible. SaafHawa is for you.

Built with care during the AI for Civic Innovation Hackathon 2026.

---

## 📄 License

MIT — see [LICENSE](LICENSE).

---

<div align="center">

**SaafHawa · صاف ہوا**
Made with ❤️ for the people who feel the air first.

</div>
