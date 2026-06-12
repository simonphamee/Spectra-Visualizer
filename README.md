# Spectra

A real-time audio spectrum visualizer that runs entirely in the browser. Drop an MP3, watch it come alive.

Built with vanilla JavaScript, the Web Audio API (`AnalyserNode` FFT), and the Canvas 2D API. No frameworks, no build step, no dependencies.

## How it works

1. Open `index.html` in any modern browser
2. Drop an MP3 file onto the canvas (or click to browse)
3. Audio plays through your speakers while 180 frequency bins are drawn at 60fps

## Under the hood

- **Audio pipeline:** `<audio>` element → `MediaElementSourceNode` → `AnalyserNode` (FFT 512) → speakers
- **Render loop:** `requestAnimationFrame` reads a `Uint8Array(256)` buffer every frame — zero per-frame allocations
- **HF compensation:** quadratic gain ramp balances the natural high-frequency roll-off so treble bins stay visible
- **Gradient rendering:** a 1×256 offscreen canvas strip is stretched per-bar via `drawImage`, avoiding per-frame gradient creation

## Tech

HTML · CSS · JavaScript · Web Audio API · Canvas 2D

## License

MIT
