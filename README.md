# ⚡ Recovery Score

**A data-driven recovery scoring system that uses HRV, Resting Heart Rate, and Sleep data against a personalized 14-day rolling baseline to determine daily training readiness.**

[![Live Demo](https://img.shields.io/badge/Live-Demo-00FF88?style=for-the-badge&logo=github)](github.com/TamerAdawi/recovery-score)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)

---

## The Problem

Athletes and active people need to know **when to push and when to recover**. Commercial solutions (Whoop, Oura) cost $30+/month and use proprietary algorithms. I wanted a transparent, free, and customizable alternative that runs on data I already own from my Apple Watch.

## The Model

```
Recovery Score = HRV vs baseline (40%) + RHR vs baseline (30%) + Sleep quality (30%)
```

Each component is scored **0–100** against a personal **14-day rolling average**:

| Component | Weight | Logic |
|-----------|--------|-------|
| **HRV** (Heart Rate Variability) | 40% | Higher is better. `ratio = today / baseline`. Above baseline = recovery signal. |
| **RHR** (Resting Heart Rate) | 30% | Lower is better. `ratio = baseline / today`. Below baseline = recovery signal. |
| **Sleep** | 30% | 7–9h optimal (base 60), 6–7h moderate (base 40), <6h poor (base 20). Bonuses for baseline consistency and 7.5h+. |

### Zones

| Score | Zone | Meaning |
|-------|------|---------|
| **60+** | 🟢 GO | Fully recovered. Train at full intensity. |
| **40–59** | 🟡 CAUTION | Moderate intensity recommended. |
| **< 40** | 🔴 REST | Prioritize recovery. Light movement only. |

### Why a 14-Day Rolling Baseline?

Population averages are misleading — a "good" HRV for one person is completely different from another. By comparing against **your own** 14-day rolling average, the model:
- Adapts to your unique physiology
- Detects **relative changes** that actually matter
- Eliminates the need for age/gender/fitness normalization tables

## Features

- **Recovery Dashboard** — Animated score ring with zone classification and component breakdown
- **Manual Input** — Enter daily values with instant score computation
- **Apple Shortcuts Integration** — Auto-fill via URL parameters from Apple Health queries
- **History Tracking** — LocalStorage-based daily log with visual bar chart
- **Model Explainer** — Built-in documentation explaining the science
- **Mobile-First Design** — Designed for iPhone, works everywhere
- **Zero Dependencies** — Pure HTML/CSS/JS, no frameworks, no build step

## Live Demo

**[→ Open the app](github.com/TamerAdawi/recovery-score)**

Try it with sample data:
```
https://TamerAdawi.github.io/recovery-score/?hrv=48&rhr=55&sleep=7.5&bhrv=42&brhr=58&bsleep=7.0
```

## Apple Shortcuts Integration

The app accepts health data via URL parameters, enabling a fully automated pipeline:

```
Apple Watch → Health → Shortcuts → This App
```

### URL Parameters

| Param | Description | Example |
|-------|-------------|---------|
| `hrv` | Today's HRV (ms) | 48 |
| `rhr` | Today's Resting HR (bpm) | 55 |
| `sleep` | Today's sleep (hours) | 7.5 |
| `bhrv` | 14-day avg HRV | 42 |
| `brhr` | 14-day avg RHR | 58 |
| `bsleep` | 14-day avg sleep | 7.0 |

### Shortcut Setup

1. Create a Shortcut that queries Apple Health for HRV, RHR, and Sleep
2. Compute 14-day baselines using "Calculate Statistics → Average"
3. Open the URL with all 6 parameters
4. Set as a daily automation at 7:00 AM

The app auto-computes the score, saves to history, and shows the dashboard.

## Setup & Deployment

### Option 1: GitHub Pages (Recommended)

```bash
# 1. Fork or clone this repo
git clone github.com/TamerAdawi/recovery-score.git

# 2. That's it. Enable GitHub Pages:
#    Settings → Pages → Source: main → / (root) → Save

# 3. Your app is live at:
#    github.com/TamerAdawi/recovery-score
```

### Option 2: Local

```bash
# Just open the file
open index.html
```

No server needed. No `npm install`. No build step.

## Project Structure

```
recovery-score/
├── index.html          ← Entire app (HTML + CSS + JS in one file)
├── README.md           ← This file
└── LICENSE             ← MIT License
```

Single-file architecture is intentional — it makes deployment trivial and keeps the project focused on the algorithm and UI, not tooling.

## Technical Decisions

| Decision | Reasoning |
|----------|-----------|
| **No framework** | The app is ~800 lines total. React/Vue would add complexity without benefit. |
| **Single HTML file** | Simplifies deployment (GitHub Pages, any static host) and makes the code easy to audit. |
| **LocalStorage** | No backend needed. Data stays on the user's device. Privacy by default. |
| **CSS animations only** | No animation libraries. Hardware-accelerated transitions via `transform` and `opacity`. |
| **URL parameter API** | Enables integration with Apple Shortcuts without any server or auth layer. |

## Future Improvements

- [ ] Service Worker for offline support (PWA)
- [ ] Export history as CSV
- [ ] Trend analysis with 7-day moving average visualization
- [ ] Integration with Health Auto Export for fully automated data pipeline
- [ ] watchOS complication via native Swift app

## References

- Plews, D.J. et al. (2013). "Training Adaptation and Heart Rate Variability in Elite Endurance Athletes"
- Buchheit, M. (2014). "Monitoring training status with HR measures"
- Bellenger, C.R. et al. (2016). "Monitoring Athletic Training Status Through Autonomic Heart Rate Regulation"

## License

MIT — use it, modify it, learn from it.

---

**Built by Tamer Adawi** · [LinkedIn](https://www.linkedin.com/in/tamer-adawi-36a6a91a6/) · [GitHub](https://github.com/TamerAdawi)
