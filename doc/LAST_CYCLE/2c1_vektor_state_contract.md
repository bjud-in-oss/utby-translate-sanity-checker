# Steg 2c1: Vektoranalys - State & Contract

### 1. State (Tillståndsmodell)
Applikationen styrs av ett centralt reaktivt tillståndsobjekt i Vanilla JS:
```javascript
const state = {
  apiKey: '',
  selectedDeviceId: '',
  audioDevices: [],
  isListeningToMic: false,
  isRecording: false,
  recordingSeconds: 0,
  recordedChunks: [],      // Array av Int16Array
  totalRecordedBytes: 0,
  recordedWavUrl: null,
  isStreaming: false,
  streamingProgress: 0,
  geminiTranscript: '',
  geminiTranslation: '',
  geminiAudioChunks: [],   // Mottaget ljud från modellen
  isGeminiAudioPlaying: false,
  logs: []
};
```

### 2. Contract (Datakontrakt)
- **PCM Ljudkontrakt:**
  - Samplingsfrekvens: 16 000 Hz
  - Kanaler: 1 (Mono)
  - Bitdjup: 16-bit Signed Integer (Little-Endian)
  - Chunk-storlek för sändning: ~100 ms = 1 600 samples = 3 200 bytes per paket.
- **WAV Export Kontrakt:**
  - Standard 44-byte RIFF/WAVE header genereras direkt från minnesbufferten så att ljudet kan laddas i en vanlig `<audio>` tagg för lokal kvalitetslyssning.
- **Gemini WebSocket Kontrakt:**
  - Setup Payload:
    ```json
    {
      "setup": {
        "model": "models/gemini-3.5-live-translate-preview",
        "generationConfig": {
          "responseModalities": ["AUDIO", "TEXT"],
          "speechConfig": {
            "voiceConfig": {
              "prebuiltVoiceConfig": {
                "voiceName": "Puck"
              }
            }
          }
        }
      }
    }
    ```
  - Realtime Input Chunks:
    ```json
    {
      "realtimeInput": {
        "mediaChunks": [
          {
            "mimeType": "audio/pcm;rate=16000",
            "data": "<base64_encoded_pcm>"
          }
        ]
      }
    }
    ```
  - Avslutande signal (`turnComplete`):
    ```json
    {
      "clientContent": {
        "turns": [],
        "turnComplete": true
      }
    }
    ```
