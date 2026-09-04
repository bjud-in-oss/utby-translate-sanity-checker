# Steg 3a: Helhet, Orkestrering och Integration

### Systemarkitektur och Komponentflöde
Applikationen struktureras som en självständig applikation i `index.html`. Den kan serveras via den inbyggda webbservern och laddas direkt i webbläsaren.

```
+-------------------------------------------------------------------------+
|                    Gemini Live Audio Sanity Check                       |
+-------------------------------------------------------------------------+
| [1. Konfiguration & Enhet]                                              |
|  - Val av ljudenhet (NDI Virtual Webcam / vMix / default)               |
|  - Gemini API Key input (lokal, sparas i localStorage)                 |
|  - DSP Status: echoCancellation=false, noiseSuppression=false, agc=false|
|  - Live VU-meter (Canvas-baserad med peak- och RMS-skala)               |
+-------------------------------------------------------------------------+
| [2. Inspelning (Steg 1)]                                                |
|  - Knapp: "Starta inspelning (5-10s)" / "Stoppa"                        |
|  - AudioWorklet fångar rå PCM (resamplar till 16kHz Int16)               |
|  - Bytes- och tidsindikator                                            |
|  - Mute under inspelning                                                |
+-------------------------------------------------------------------------+
| [3. Sändning & Översättning (Steg 2)]                                   |
|  - Knapp: "Skicka till Gemini Live Translate API"                       |
|  - WebSocket: wss://generativelanguage.googleapis.com/...               |
|  - Modell: models/gemini-3.5-live-translate-preview                     |
|  - Strömmar ~100 ms PCM chunks (1600 samples)                           |
+-------------------------------------------------------------------------+
| [4. Resultat & Diagnostik (Steg 3)]                                     |
|  - Vad Gemini hörde (Input transcript)                                  |
|  - Översatt text (Gemini översättning)                                  |
|  - Översatt ljud från Gemini (Automatisk uppspelning)                   |
|  - Egen ljud-dump (Spela upp lokal 16kHz PCM som WAV)                   |
|  - Live WebSocket- och felsökningslogg                                  |
+-------------------------------------------------------------------------+
```
