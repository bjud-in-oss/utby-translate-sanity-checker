# Steg 1b: Kartlägga

### Svar på GROW-frågorna mot kodbasen och domänen

1. **State & Contract:**
   - Den nuvarande kodbasen är en ren React/Vite-mall med ett tomt `App.tsx` och standard `index.html`.
   - Användaren begär specifikt att verktyget byggs som en **minimal, fristående Web Audio-applikation i en enda HTML-fil (HTML, Vanilla JS och Tailwind CDN)**.
   - Vi implementerar en ren, deterministisk tillståndsmaskin:
     - `UNCONFIGURED` (saknar API-nyckel eller enhetsval)
     - `READY` (enhet vald och API-nyckel angiven, realtids-VU aktiv)
     - `RECORDING` (fångar 16 kHz 16-bit PCM till en dynamisk TypedArray-buffert, 5–10 s)
     - `RECORDED` (inspelning stoppad, buffert låst, klar för sändning eller lokal provlyssning)
     - `SENDING` (öppnar WebSocket, skickar setup-meddelande och därefter 100 ms PCM-paket)
     - `WAITING_RESPONSE` (väntar på transkribering, översättning och returljud)
     - `DONE` (visar källtranskript, översättning och spelar upp översatt ljud)
     - `ERROR` (visar utförlig diagnostik och felorsak)

2. **Effects & I/O:**
   - Web Audio API konfigureras med ett `AudioContext` och en inline `AudioWorkletProcessor` (skapad via en data/blob-URL).
   - `navigator.mediaDevices.getUserMedia` anropas med:
     ```javascript
     {
       audio: {
         deviceId: selectedDeviceId ? { exact: selectedDeviceId } : undefined,
         echoCancellation: false,
         noiseSuppression: false,
         autoGainControl: false,
         channelCount: 1,
         sampleRate: 16000
       }
     }
     ```
   - Workleten nedsamplar eller konverterar inkommande Float32-samples till Int16 PCM och skickar tillbaka dem till huvudtråden.
   - En canvas- eller CSS-baserad VU-mätare kopplas via en `AnalyserNode` för att i realtid bekräfta att NDI/vMix skickar ljudsignal.
   - WebSocket ansluts direkt mot Gemini Multimodal Live API:
     `wss://generativelanguage.googleapis.com/ws/google.ai.generativelanguage.v1alpha.GenerativeService.BidiGenerateContent?key=${apiKey}`

3. **Resilience & Feedback:**
   - PCM-bufferten sparas även lokalt. En funktion skapar ett standard 44-byte RIFF WAV-huvud och en `Blob` så användaren med ett klick kan lyssna på den råa inspelningen ("Egen ljud-dump").
   - Detaljerad statuslogg visas på skärmen för varje skickat paket, WebSocket-händelse och inkommande svarsfragment.

```json
{
  "status": "PLANNING",
  "current_domain": "live_audio_sanity_check",
  "next_step": "2a_forandra_utat_vision",
  "ticket_id": "TCK-001",
  "active_skill": "gemini-api",
  "active_vectors": ["State", "Contract", "Effects", "Resilience"]
}
```
