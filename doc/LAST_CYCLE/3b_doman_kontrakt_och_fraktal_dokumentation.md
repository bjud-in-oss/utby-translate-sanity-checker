# Steg 3b: Domän, Kontrakt och Fraktal Dokumentation

### Domän: Web Audio & Gemini Live Translate Protocol

#### 1. AudioWorklet Processor Specification
- **Namn:** `pcm-recorder-processor`
- **Algoritm:**
  - Tar in Float32Array från kanal 0 vid webbläsarens samplingsfrekvens (`sampleRate`, oftast 44 100 eller 48 000 Hz).
  - Resamplar linjärt till 16 000 Hz:
    `const targetSampleRate = 16000;`
    `const ratio = sampleRate / targetSampleRate;`
  - Kvantiserar till Int16:
    `const s = Math.max(-1, Math.min(1, floatVal));`
    `const int16 = s < 0 ? s * 0x8000 : s * 0x7FFF;`
  - Skickar över buffertar av typ `Int16Array` till huvudtråden via `this.port.postMessage(pcmData.buffer, [pcmData.buffer])`.

#### 2. WAV Blob Generator Specification
- Skapar en 44-byte standard RIFF header:
  - `ChunkID`: "RIFF"
  - `ChunkSize`: 36 + SubChunk2Size
  - `Format`: "WAVE"
  - `Subchunk1ID`: "fmt "
  - `Subchunk1Size`: 16 (PCM)
  - `AudioFormat`: 1 (Linear PCM)
  - `NumChannels`: 1 (Mono)
  - `SampleRate`: 16 000
  - `ByteRate`: 16 000 * 1 * 2 = 32 000
  - `BlockAlign`: 2
  - `BitsPerSample`: 16
  - `Subchunk2ID`: "data"
  - `Subchunk2Size`: pcmData.byteLength
- Konkatenerar header och PCM-data till `new Blob([header, pcmData], { type: 'audio/wav' })`.

#### 3. Gemini BidiGenerateContent Protocol Specification
- **Endpoint:** `wss://generativelanguage.googleapis.com/ws/google.ai.generativelanguage.v1alpha.GenerativeService.BidiGenerateContent?key=${apiKey}`
- **Setup:**
  ```json
  {
    "setup": {
      "model": "models/gemini-3.5-live-translate-preview",
      "generationConfig": {
        "responseModalities": ["AUDIO", "TEXT"]
      }
    }
  }
  ```
- **Streaming Chunks:** Paket om 1 600 Int16-samples (3 200 bytes) kodade i standard Base64.
- **Mottagning:**
  - Parser för `serverContent.modelTurn.parts`:
    - `part.text`: Samlas i översättningsrutan.
    - `part.inlineData`: PCM-ljud (oftast 24 000 Hz) samlas och avkodas till en `AudioBuffer` och spelas upp automatiskt när `turnComplete: true` tas emot.
