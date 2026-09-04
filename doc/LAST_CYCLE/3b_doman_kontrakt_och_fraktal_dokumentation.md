# Steg 3b: Domän, Kontrakt och Fraktal Dokumentation (TCK-003)

### Kontrakt för Real-Time Streaming Audio Queue
- **Input:** `rawPcmBytes` (Uint8Array, 16-bit Mono Little-Endian PCM vid 24 000 Hz från Gemini Live Translate).
- **Transformation:** Konvertering till `Float32Array` och generering av `AudioBuffer`.
- **Schemaläggning:**
  - `startTime = Math.max(state.audioContext.currentTime, state.nextPlaybackTime)`
  - Buffertens längd beräknas och adderas: `state.nextPlaybackTime = startTime + audioBuffer.duration`.
- **Avbrott:**
  - `state.activeAudioSources.forEach(s => s.stop())`
  - `state.webSocket.close()`
