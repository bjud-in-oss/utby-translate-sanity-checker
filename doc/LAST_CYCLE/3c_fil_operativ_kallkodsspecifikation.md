# Steg 3c: Fil-operativ Källkodsspecifikation

### Förändringsplan för källkodsfiler (förberedelse inför Fas 2)

#### 1. Målfil: `index.html`
- Ersätts med en komplett, självständig och ren HTML5-fil.
- Innehåller:
  - Tailwind CSS via officiell CDN för snabb, oberoende och elegant rendering.
  - Inline AudioWorklet script som skapas via `URL.createObjectURL(new Blob([...], { type: 'application/javascript' }))`.
  - Vanilla JS applikationslogik:
    - `setupDeviceEnumeration()`: Listar mikrofoner (`audioinput`), filtrerar och uppdaterar select-menyn.
    - `initAudioStream(deviceId)`: Öppnar stream med `echoCancellation: false`, `noiseSuppression: false`, `autoGainControl: false`.
    - `renderVUMeter()`: Ritar kontinuerligt RMS och Peak på canvas.
    - `startRecording()`: Skapar AudioWorkletNode, startar timer, buffrar `Int16Array`.
    - `stopRecording()`: Stoppar worklet, sammanfogar chunks till en minnesbuffert, genererar WAV Blob för "Egen ljud-dump".
    - `sendToGeminiLive()`: Initierar WebSocket mot `models/gemini-3.5-live-translate-preview`, skickar setup och strömmar 100 ms PCM chunks med Base64-kodning, hanterar serverContent.
    - `playGeminiAudio()`: Återger mottaget PCM-ljud efter mottagande.
    - `playOwnPcmAudio()`: Spelar upp den insamlade WAV-filen.

#### 2. Källkodsegenskaper & BORTTAGEN_PROP
BORTTAGEN_PROP: Inga

BESLUT: GODKÄND
