# ⚡ Recovery Score

**A data-driven recovery scoring system that compares your daily HRV, Resting Heart Rate, Total Sleep, and Deep Sleep against a personalized 14-day rolling baseline to determine training readiness.**

[![Live Demo](https://img.shields.io/badge/Live-Demo-00FF88?style=for-the-badge&logo=github)](https://tameradawi.github.io/recovery-score)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](LICENSE)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)

---

## Quick Start

**Want to use it right now?** Three steps:

1. **Install both Shortcuts** on your iPhone (links below)
2. **Connect them** (one-time setup, takes 30 seconds)
3. **Run** — your recovery score appears automatically

| Download | Link |
|----------|------|
| **Recovery Score Shortcut** | [Install from iCloud](https://www.icloud.com/shortcuts/63c813f3e7f44505b1cd0e2076679eec) |
| **Sleep Analysis Shortcut** (required) | [Install from iCloud](https://www.icloud.com/shortcuts/ab67a110e9c043538ff484c6ab66b6d1) |
| **Web Dashboard** | [Open App](https://tameradawi.github.io/recovery-score) |

### Setup Guide

**Step 1 — Install both Shortcuts**

Tap both links above on your iPhone. When prompted, tap "Add Shortcut" for each one. You should now see both shortcuts in your Shortcuts app.

**Step 2 — Connect the Sleep Shortcut to Recovery Score**

This is the important part. The Recovery Score shortcut needs to know which shortcut fetches your sleep data:

1. Open the **Shortcuts** app
2. Find **Recovery Score** and tap the **three dots** (⋯) to edit it
3. Scroll down until you find the **"Run Shortcut"** action
4. Tap on the shortcut name inside that action — it may say "Shortcut" or show a blank
5. Select the **Sleep Analysis** shortcut (the other one you just installed)
6. Tap **Done** to save

**Step 3 — Grant Health Permissions**

The first time you run the shortcut, your iPhone will ask for permission to access Health data. Tap **Allow** for all requests (Heart Rate Variability, Resting Heart Rate, and Sleep).

**Step 4 — Run It**

Tap the Recovery Score shortcut. It will:
1. Pull your HRV and RHR from Apple Health
2. Run the Sleep Analysis shortcut to get your sleep hours and deep sleep
3. Compute your 14-day baselines
4. Open Safari with your personalized recovery dashboard

**Step 5 (Optional) — Automate It**

Make it run every morning without touching your phone:

1. Go to the **Automation** tab in Shortcuts
2. Tap **+** then **Time of Day**
3. Set to **7:00 AM** (or whenever you wake up), repeat **Daily**
4. Choose **Run Shortcut** and select **Recovery Score**
5. Turn OFF **Ask Before Running**

Done. Every morning you wake up to your recovery score.

### Requirements

- iPhone with **Apple Watch** paired
- iOS 16 or later
- Both shortcuts installed and connected (see setup above)
- Apple Watch worn during sleep for sleep data

---

## The Problem

Athletes and active people need to know when to push and when to recover. Commercial solutions like Whoop and Oura cost $30+/month and use proprietary, black-box algorithms. I wanted a transparent, free, and fully customizable alternative built on data I already own from my Apple Watch.

## The Model

```
Recovery Score = HRV vs baseline x 40%  +  RHR vs baseline x 30%  +  Sleep quality x 30%
```

### Component Breakdown

**HRV — Heart Rate Variability (40% weight)**

Today's HRV compared to your rolling 14-day average. HRV reflects parasympathetic nervous system recovery. This gets the highest weight because the data shows high-HRV days produce approximately 10% more training volume than low-HRV days. Scoring is linear: 30ms above baseline scores near 100, at baseline scores ~65, 30ms below scores near 0.

**RHR — Resting Heart Rate (30% weight)**

Today's RHR compared to your 14-day average. Lower than baseline means well-recovered; higher means your body is handling accumulated stress. An RHR 5+ bpm above baseline is a reliable warning sign. Uses an inverse linear scale: 10bpm below baseline scores near 100.

**Sleep Quality (30% weight)**

Combines total sleep hours and deep sleep minutes. The target baseline is 8 hours of total sleep. Deep sleep above 45 minutes receives a bonus; above 70 minutes scores near maximum. Within this component, total hours are weighted at 60% and deep sleep at 40%. Short sleep or low deep sleep pulls the score down significantly.

### Training Zones

| Score | Zone | Action |
|-------|------|--------|
| **60+** | 🟢 GO | Train as planned. Push for progression. |
| **40-59** | 🟡 CAUTION | Train but back off intensity 5-10%. Skip PR attempts. |
| **< 40** | 🔴 REST | Replace the session with a walk or full rest. |

### Why a 14-Day Rolling Baseline?

Population averages are misleading — a "good" HRV for one person is completely different from another. By comparing against your own 14-day rolling average, the model adapts to your unique physiology. It detects relative changes that actually matter for recovery, eliminating the need for age, gender, or fitness-level normalization tables.

## Architecture

```
┌──────────────┐     ┌─────────────────┐     ┌──────────────────────────┐
│  Apple Watch  │────>│  Apple Health    │────>│  iOS Shortcuts           │
│  (sensors)    │     │  (data store)   │     │                          │
└──────────────┘     └─────────────────┘     │  Recovery Score Shortcut │
                                              │    │                      │
                                              │    v                      │
                                              │  Sleep Analysis Shortcut │
                                              │    │                      │
                                              └────┼──────────────────────┘
                                                   │
                                                   v  (URL with 6-8 parameters)
                                              ┌──────────────────┐
                                              │  Recovery Score  │
                                              │  Web App         │
                                              │  (GitHub Pages)  │
                                              └──────────────────┘
```

**Data flow:** Apple Watch sensors collect biometric data continuously. Apple Health stores it. The Recovery Score Shortcut queries Health for HRV and RHR (today + 14-day baselines), then calls the Sleep Analysis Shortcut which parses sleep stage data (Core, REM, Deep) and returns a formatted report. Recovery Score extracts total sleep and deep sleep minutes via regex, constructs a URL with all parameters, and opens the web app. The web app runs the scoring algorithm, renders the dashboard, and saves the entry to localStorage for history tracking.

## Features

- **Composite Recovery Score** — Weighted algorithm combining HRV, RHR, and sleep quality into a single 0-100 score
- **Visual Dashboard** — Animated score ring with zone classification, component breakdown, and score bars
- **4 Metric Cards** — HRV, RHR, Sleep Hours, and Deep Sleep with today's value, component score, and 14-day baseline comparison
- **7-Day History** — Visual bar chart showing recent scores with zone coloring
- **History Tracking** — localStorage-based daily log storing up to 90 days of entries
- **Apple Shortcuts Integration** — Fully automated two-shortcut data pipeline via URL parameters
- **Smart Sleep Detection** — Auto-converts sleep values whether received in seconds, minutes, or hours
- **Baseline Fallback** — Uses URL-provided baselines when available, falls back to stored history baselines when not
- **Manual Input** — Full input form for entering values directly with instant score computation
- **Model Documentation** — Built-in explainer page covering the science behind each component
- **Mobile-First Design** — Optimized for iPhone viewport, dark theme, bottom tab navigation
- **Zero Dependencies** — Pure HTML, CSS, and JavaScript. No frameworks, no build tools, no npm

## URL Parameters

The web app accepts health data via URL query strings:

| Parameter | Description | Example | Required |
|-----------|-------------|---------|----------|
| `hrv` | Today's average HRV (ms) | 48.5 | Yes |
| `rhr` | Today's resting heart rate (bpm) | 55 | Yes |
| `sleep` | Total sleep (auto-detects sec/min/hrs) | 602 | Yes |
| `deep` | Deep sleep (minutes) | 77 | No |
| `bhrv` | 14-day average HRV (ms) | 42.3 | No* |
| `brhr` | 14-day average RHR (bpm) | 58.1 | No* |
| `bslp` | 14-day average sleep (auto-detects) | 480 | No* |
| `bdeep` | 14-day average deep sleep (minutes) | 48 | No* |

*Baselines fall back to stored history if not provided in URL.

Example URL:
```
https://tameradawi.github.io/recovery-score/?hrv=48&rhr=55&sleep=602&deep=77&bhrv=42&brhr=58
```

## Scoring Algorithm Detail

### HRV Score (0-100)
```javascript
// Linear scale: +30ms above baseline = 100, at baseline = 65, -30ms below = 0
score = 65 + (diff / 30) * 35
// where diff = todayHRV - baselineHRV
```

### RHR Score (0-100)
```javascript
// Inverse linear: 10bpm below baseline = 100, at baseline = 65, +10bpm above = 0
score = 65 + (diff / 10) * 35
// where diff = baselineRHR - todayRHR (positive when today is lower = good)
```

### Sleep Score (0-100)
```javascript
// Hours component (60% of sleep score):
//   9+ hrs = 90, 8+ = 75, 7+ = 60, 6+ = 40, 5+ = 25, <5 = 10
//   Bonus/penalty vs baseline ratio

// Deep sleep component (40% of sleep score):
//   70+ min = 100, 55+ = 80, 45+ = 65, 30+ = 45, 20+ = 30, <20 = 15
//   Bonus/penalty vs baseline ratio

sleepScore = hoursScore * 0.6 + deepScore * 0.4
```

### Composite
```javascript
composite = hrvScore * 0.4 + rhrScore * 0.3 + sleepScore * 0.3
// Zones: 60+ = GO | 40-59 = CAUTION | <40 = REST
```

## Technical Decisions

| Decision | Reasoning |
|----------|-----------|
| **No framework** | The entire app is ~600 lines. React/Vue would add complexity without benefit for a single-page scoring tool. |
| **Single HTML file** | Simplifies deployment to any static host. One file to audit, one file to deploy. |
| **Two-shortcut architecture** | Separates health data queries (simple) from sleep stage parsing (complex). Modular, easier to debug. |
| **Regex for sleep extraction** | The sleep shortcut returns formatted text. Regex pattern matching (`Sleep: (\d+) min`) is the most reliable extraction method after testing alternatives (Split Text, Dictionary). |
| **localStorage** | No backend needed. All data stays on the user's device. Privacy by default, zero server costs. |
| **URL parameter API** | Enables integration with Apple Shortcuts without any server, authentication, or API layer. |
| **CSS-only animations** | Hardware-accelerated transitions via transform and opacity. No animation library needed. |
| **Auto-detection for sleep** | Sleep data arrives in different units depending on the source. Auto-converting (>1440 = seconds, >24 = minutes, else hours) eliminates user errors. |
| **Baseline fallback** | URL baselines take priority, stored history baselines ensure the app works with partial data. |

## Project Structure

```
recovery-score/
├── index.html    <- Entire application (HTML + CSS + JS)
├── README.md     <- Documentation
└── LICENSE       <- MIT License
```

## Hosting / Deployment

```bash
# Clone
git clone https://github.com/tameradawi/recovery-score.git

# Deploy via GitHub Pages:
# Settings -> Pages -> Source: main -> / (root) -> Save

# Live at:
# https://tameradawi.github.io/recovery-score
```

No server. No npm install. No build step. One HTML file.

## Future Roadmap

- [ ] Progressive Web App with offline support via Service Worker
- [ ] CSV export for training log integration
- [ ] Trend analysis with 7-day and 30-day moving averages
- [ ] Training load integration (RPE x duration) for load:recovery ratio
- [ ] Correlation analysis — which metrics best predict individual performance
- [ ] Native watchOS complication showing the score on your wrist

## References

- Plews, D.J. et al. (2013). "Training Adaptation and Heart Rate Variability in Elite Endurance Athletes"
- Buchheit, M. (2014). "Monitoring Training Status with HR Measures: Do All Roads Lead to Rome?"
- Bellenger, C.R. et al. (2016). "Monitoring Athletic Training Status Through Autonomic Heart Rate Regulation"

## License

MIT — use it, modify it, learn from it.

---

**Built by Tamer Adawi** · [LinkedIn](https://linkedin.com/in/tameradawi) · [GitHub](https://github.com/tameradawi)
