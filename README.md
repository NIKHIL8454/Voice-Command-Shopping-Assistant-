# Voice Cart — Voice Command Shopping Assistant

A voice-based shopping list manager with smart suggestions. Built as a single
self-contained web app — no build step, no external dependencies, no backend.

**Live app:** open `index.html` directly in a browser (Chrome recommended for
full voice support), or host the file on any static host (GitHub Pages,
Netlify, Firebase Hosting).

## Approach (write-up)

Voice Cart is a single-file, dependency-free web app built around one
pipeline: the browser's Web Speech API turns speech into text, a rule-based
parser (`parseCommand`) extracts intent (add / remove / search / undo /
budget / recipe), quantity, unit, item name, and price filters from loose
phrases, and that structured result drives list state and re-rendering. I
chose a rule-based parser over an external NLP/ML service to keep the app
free, fast, offline-friendly, and easy to debug within the time box — regex
and keyword matching handle the required phrasings ("I need 2 kg of apples",
"find toothpaste under $5") reliably across English, Hindi, and Spanish.
Categorization, substitutes, recipes, and seasonal picks are plain data
dictionaries, so extending the catalog is a data edit, not a code change.
The UI leans into a receipt/aisle-sign visual metaphor — a live mic
waveform drawn from the real microphone stream via the Web Audio API, items
that "print" onto a receipt, and spoken confirmations via SpeechSynthesis —
so the interface feels like a physical shopping trip rather than a form.
Production-quality touches: try/catch around recognition and audio access,
specific error states, a visible "Understanding…" loading state, an
activity log with an undo stack, and toast/confetti feedback. A text-input
fallback and clickable example commands mean every feature is testable
without a working microphone.

## Features

- **Voice input** — Web Speech API, English / Hindi / Spanish toggle, live
  transcript, keyboard shortcut (`V`), floating mic button on scroll
- **Live audio waveform** — a real mic-reactive visualizer built on the Web
  Audio API's `AnalyserNode`, not a decorative animation
- **Spoken replies** — the assistant talks back via `SpeechSynthesis`
  ("Added milk to your list"), with a mute toggle
- **NLP-lite parsing** — intent, quantity, unit, item, price-limit, budget,
  and recipe extraction from natural phrases, in three languages
- **Smart suggestions** — running-low reminders, seasonal picks, substitute
  ideas (e.g. milk → almond milk), and one-tap recipe quick-add ("add
  ingredients for pasta night" adds five items at once)
- **List management** — add / remove / merge quantities, per-item quantity
  steppers, check items off as you shop (with a completion progress ring and
  a confetti celebration), category breakdown bar, voice **undo**
- **Budget tracker** — set a budget by voice ("set budget to 500"), see a
  live spend gauge that shifts from green to amber to red
- **Voice activity log** — a running feed of recognized commands with
  success/fail indicators
- **Voice-activated search** — demo catalog, filterable by price
- **UX** — loading state while a command is processed, toast confirmations,
  graceful fallback when the mic or Speech API is unavailable, mobile-first
  responsive layout

## Tech stack

Plain HTML, CSS, and JavaScript. Web Speech API (`SpeechRecognition`) for
voice input, `SpeechSynthesis` for spoken replies, and the Web Audio API
(`AnalyserNode`) for the live waveform visualizer. Google Fonts loaded via
CDN link. No frameworks, no npm packages, no build tooling.

## Run locally

No install step required.

```
git clone <this-repo-url>
cd voice-cart
open index.html      # or just double-click the file
```

For microphone access to work reliably, serve it over `http://localhost`
instead of `file://` — any static server works, e.g.:

```
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deploy

Any static host works since there's no server code:

- **GitHub Pages**: Settings → Pages → deploy from the `main` branch
- **Netlify**: drag-and-drop the folder onto netlify.com/drop
- **Firebase Hosting**: `firebase init hosting` → `firebase deploy`

## Project structure

```
voice-cart/
├── index.html   # entire app: markup, styles, and logic
└── README.md
```

No `node_modules`, build artifacts, or environment files — there's nothing
to install and nothing sensitive to exclude.

## Notes / limitations

- Voice recognition and the live waveform depend on browser support for the
  Web Speech / Web Audio APIs (best in Chrome/Edge; not available in
  Firefox or Safari). The text-input fallback exercises the same parsing
  and list logic, minus the mic-reactive visuals.
- The product catalog, purchase history, recipes, and seasonal list are
  small demo datasets, not live inventory or a real database.
- Multilingual support currently covers English, Hindi, and Spanish
  keywords.
- Recipe quick-add and the undo stack are session-only (in-memory), by
  design — no browser storage is used, so refreshing the page resets the
  list intentionally.
