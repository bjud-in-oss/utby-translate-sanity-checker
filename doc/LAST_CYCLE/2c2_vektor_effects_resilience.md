# Steg 2c2: Vektoranalys - Effects & Resilience

### 1. Effects (Sidoeffekter och Hårdvaruintegration)
- **AudioWorklet via Blob:**
  - Vi skapar en inbäddad `Blob` med MIME-typen `application/javascript` direkt i HTML-koden.
  - Workleten tar emot stereoljud eller monoljud från vald mikrofondriver (NDI Virtual Webcam / vMix / fysiskt ljudkort), konverterar från hårdvarans samplingsfrekvens (t.ex. 48 000 Hz eller 44 100 Hz) till målet 16 000 Hz med linjär interpolation, klipper amplituden och kvantiserar till 16-bit signed integer (-32768 till +32767).
- **Web Audio Signal Chain:**
  ```
  MediaStreamSource -> AnalyserNode -> AudioWorkletNode -> Destination (avstängd/mute för att undvika medhörning)
  ```
- **AnalyserNode & VU-meter:**
  - Använder `getByteFrequencyData` eller `getByteTimeDomainData` på en canvas för att rita en snabb, responsiv analog-liknande VU-mätare med topp-indikator och dB-skala.
- **WebSocket Streaming:**
  - WebSocket skickar Base64-kodade 16 kHz PCM-paket var 100:e millisekund (`setInterval` eller asynkron tidsstyrd loop).

### 2. Resilience (Tålighet och Felsäkerhet)
- **DSP-kontroll:** Om webbläsaren eller ljudkortet har problem med att tvinga fram parametrarna loggas och visas de faktiska applicerade `MediaTrackSettings`.
- **Felmeddelanden:** Alla WebSocket-fel (`onerror`, `onclose` med felkod och orsak) fångas upp och visas i en särskild diagnostikruta.
- **Ljudåtergivning:** Ljud från modellen (ofta 24 kHz PCM) packas ihop till en spelbar audio-buffer eller WAV för säker återgivning utan att krascha ljudkontexten.
- **Minneshantering:** När ny inspelning startas rensas tidigare ljudbuffertar och gamla Blob-URL:er frigörs via `URL.revokeObjectURL`.
