# CivicVoice — Bengaluru Civic Complaint Assistant

A Generative AI web application that converts an informal citizen report of a civic
problem — typed, **spoken**, or supported by a **photo** — into a **formal municipal
complaint** addressed to the correct Bengaluru civic body (BBMP / BWSSB / BESCOM /
Traffic Police), generated simultaneously in **English, Kannada, and Hindi**, with
**audio readout**.

> Same pattern as an "FIR documentation simplifier": raw unstructured text in →
> structured official document out, powered by an LLM.

## Problem it solves

Filing a civic complaint in Bengaluru usually requires writing a formal letter in
English, knowing which department is responsible, and describing the issue precisely.
Many citizens — especially those more comfortable in Kannada or Hindi, or unable to
type — never file complaints because of this friction. CivicVoice removes it: speak or
type in any of the three languages, and get a ready-to-submit formal complaint.

## Generative AI features

| Modality | Feature | Technology |
|----------|---------|------------|
| Text | Raw description → structured formal complaint (category, department, severity, letter) | Gemini Flash with JSON schema output |
| Language | One-click switch between English / ಕನ್ನಡ / हिंदी versions | Gemini multilingual generation (all 3 in one call) |
| Image | Attach a photo of the issue as evidence; the model describes it and factors it into severity | Gemini vision (multimodal input) |
| Audio in | Dictate the complaint by voice in en-IN / kn-IN / hi-IN | Web Speech API (SpeechRecognition) |
| Audio out | "Read aloud" the generated complaint in the selected language | Web Speech API (speechSynthesis) |

## Setup

```bash
npm install
copy .env.example .env    # then paste your key into .env
npm run dev
```

Get a free Gemini API key at https://aistudio.google.com/apikey (no credit card).

**Demo mode:** if no API key is configured, the app shows a realistic sample response
so the full UI can be demoed offline.

## Architecture

```
Browser (React + Vite)
 ├─ ComplaintForm  — text input, mic (SpeechRecognition), photo upload (base64)
 ├─ src/api/gemini.js — one structured-output call to Gemini Flash:
 │     system prompt (civic-domain knowledge of BBMP/BWSSB/BESCOM)
 │     + user text + optional inline image
 │     → JSON: {category, department, severity, location,
 │              title/summary/complaint/next_steps × {en, kn, hi}}
 └─ ResultCard — language tabs, TTS readout, copy / download .txt
```

Note: for a production deployment the API key should be moved behind a small server
proxy (as done with the `api/` folder pattern in the bengaluru-twin project); calling
Gemini directly from the browser is acceptable for a course demo.

## Tech stack

- React 18 + Vite
- Google Gemini API (`gemini-flash-latest`, structured JSON output, vision)
- Web Speech API (voice input + text-to-speech, no key required)
- No backend required for the demo
