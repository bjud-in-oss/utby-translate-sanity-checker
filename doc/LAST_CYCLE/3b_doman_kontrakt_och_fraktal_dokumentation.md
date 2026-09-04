# Steg 3b: Domän, Kontrakt och Fraktal Dokumentation (TCK-002)

### 1. Resampling-kontrakt (PCMRecorderProcessor)
- `input`: Float32Array (webbläsarens hårdvarusamplingsfrekvens, t.ex. 48 000 Hz)
- `processorOptions.inputSampleRate`: tal (standard 48 000)
- `targetSampleRate`: 16 000
- `ratio`: `inputSampleRate / targetSampleRate`
- `output`: Int16Array vid exakt 16 000 Hz Mono Little-Endian

### 2. Gemini Live BidiGenerateContent Protokollkontrakt
- **Setup:** Skickas vid `ws.onopen`.
- **Media Ingestion:** Strömmar chunks om 1 600 samples (3 200 bytes) med mimeType `audio/pcm;rate=16000`.
- **Slut på uppladdning:** Inget utgående `turnCompletePayload`. Anslutningen hålls öppen.
- **Respons:** `serverContent.modelTurn.parts` (text och audio) ackumuleras och spelas upp när `serverContent.turnComplete: true` anländer från servern.
