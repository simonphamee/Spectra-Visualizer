# Spectra

A real-time audio spectrum visualizer that runs entirely in the browser. Drop an MP3 or plug in a mic — watch sound become light.

Built with vanilla JavaScript, the Web Audio API, and the Canvas 2D API. No frameworks, no build step, no dependencies. One HTML file.

> This project was built using AI-paired development with [Claude](https://claude.ai). I directed the architecture, features, and UX decisions; Claude wrote the implementation. More on that [below](#how-this-was-built).

## Demo

Open `index.html` in any modern browser. Drop an MP3 onto the canvas or click to browse. Hit the mic button to visualize live audio from your environment.

## Features

**Three visualization modes**
- **Bars** — classic frequency spectrum with peak-hold caps, bloom glow on loud bins, and a mirrored reflection beneath the baseline
- **Radial** — frequency bins projected around a circle using trigonometry, with a bass-reactive breathing ring at the center
- **Waveform** — raw time-domain oscilloscope trace drawn as a single glowing polyline

**Live audio input**
- MP3 file upload via drag-and-drop or file picker
- Microphone / line-in capture via `getUserMedia` with browser DSP disabled for a truthful spectrum
- Safe source switching — mic signal is routed to the analyser only, never to speakers, preventing feedback

**Interactive controls**
- FFT size selector (256 / 512 / 1024 / 2048) — change frequency resolution live and watch the trade-off against time resolution
- Four color themes: Amber, Cyberpunk Pink, Matrix Green, Ice Blue
- Custom transport bar with seek, volume, and mute
- Fullscreen presentation mode

**Real-time telemetry**
- Dominant frequency in Hz (bin index × sample rate / FFT size)
- Approximate level in dBFS from mean magnitude
- Hardware sample rate from the AudioContext

## Architecture

Everything lives in one `index.html`, organized into four labeled sections:

```
Section 1 — UI Layout        HTML + CSS, dark instrument-panel theme
Section 2 — Audio Engine      AudioContext, AnalyserNode, source switching
Section 3 — Transport UI      Custom controls synced via requestAnimationFrame
Section 4 — Render Loop        Canvas drawing, mode dispatch, telemetry
```

**Audio pipeline (file mode):**
```
<audio> → MediaElementSourceNode → AnalyserNode → destination (speakers)
```

**Audio pipeline (mic mode):**
```
getUserMedia → MediaStreamSourceNode → AnalyserNode → (nothing — visual only)
```

**Key performance decisions:**
- Zero per-frame allocations — `Uint8Array` buffers are filled in place, gradient is an offscreen 1×256 canvas strip stretched via `drawImage`
- Peak-hold caps use preallocated `Float32Array` state with frame-based pseudo-gravity
- Radial mode precomputes all `cos`/`sin` values into lookup tables at init
- Transport UI updates are change-guarded so DOM writes only happen when values actually move
- `smoothingTimeConstant: 0.85` on the analyser for silky bar motion without flicker
- High-frequency compensation: a quadratic gain ramp (`1 + 1.6·t²`) lifts the naturally weak treble bins so the right side of the display stays alive

## How This Was Built

I built this project through iterative prompting with Claude (Anthropic's AI assistant). I didn't write the code by hand — I described what I wanted at each step, reviewed the output, tested it in the browser, and directed the next feature.

What I brought to the process: the product vision, feature prioritization, UX decisions (the amber VU-meter aesthetic, the tape-deck upload slot, the instrument-panel telemetry), and the iterative build order that kept the app testable at every commit.

What Claude brought: the implementation — Web Audio API wiring, Canvas math, the performance patterns, and the specific engineering solutions like the anti-feedback mic routing and the offscreen gradient strip.

I'm sharing this honestly because I think the collaboration itself is interesting, and because misrepresenting AI-generated code as hand-written helps nobody.

## Tech

HTML · CSS · JavaScript · Web Audio API · Canvas 2D

## Browser Support

Any modern browser with Web Audio API support (Chrome, Firefox, Safari, Edge). Microphone input requires HTTPS or localhost.

## License

MIT
