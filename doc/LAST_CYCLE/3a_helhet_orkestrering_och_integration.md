# Steg 3a: Helhet, Orkestrering och Integration (TCK-002)

### Orkestreringsflöde
1. **AudioContext initiering:**
   - `inputSampleRate = state.audioContext.sampleRate` loggas explicit till diagnostikfönstret.
   - `AudioWorkletNode` skapas med `processorOptions: { inputSampleRate }`.
2. **Resampling och buffring (Steg 1):**
   - AudioWorklet läser input-kanalen, tillämpar `step = inputSampleRate / 16000` och kvantiserar linjärt interpolerade punkter till Int16 Little-Endian.
   - Vid 8 sekunders inspelning vid 48 kHz erhålls exakt 128 000 samples.
3. **WebSocket strömning och svarslyssning (Steg 2 & 3):**
   - 100 ms PCM-chunks skickas sekventiellt via `realtimeInput`.
   - När alla chunks skickats lämnas anslutningen öppen utan utgående `turnCompletePayload`.
   - Serverns asynkrona svar tas emot i `ws.onmessage` och triggar `finalizeGeminiAudio()` vid `sc.turnComplete`.
