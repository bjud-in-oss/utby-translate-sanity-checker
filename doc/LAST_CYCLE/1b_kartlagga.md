# Steg 1b: Kartlägga (TCK-003)

### Svar på GROW-frågorna mot koden

1. **Effects & Signal Processing:**
   - I nuläget ackumulerar `handleGeminiServerMessage` inkommande rådata i `state.receivedAudioChunks` och anropar inte `finalizeGeminiAudio()` förrän `sc.turnComplete` är sant. Det innebär att användaren måste vänta tills Gemini pratat färdigt och sessionen är avslutad innan en hel WAV-fil bakas ihop.
   - Ny mekanism:
     - Vi skapar funktionen `queueRealtimeAudioChunk(rawPcmBytes, sampleRate = 24000)`.
     - Int16-data konverteras direkt till Float32Array `[-1.0, 1.0]`.
     - En `AudioBuffer` skapas: `state.audioContext.createBuffer(1, float32.length, sampleRate)`.
     - En `AudioBufferSourceNode` skapas, ansluts till `state.audioContext.destination` och schemaläggs vid `startTime = Math.max(state.audioContext.currentTime, state.nextPlaybackTime)`.
     - Vid första paketet läggs en minimal jitter-buffert (t.ex. 50 ms) till för att garantera sammanhängande ljud.
     - `state.nextPlaybackTime = startTime + audioBuffer.duration`.
     - Källnoden sparas i `state.activeAudioSources` så att den omedelbart kan stoppas om användaren klickar på "Stoppa".
     - Samtidigt sparas `rawPcmBytes` i `state.receivedAudioChunks` i bakgrunden så att användaren fortfarande kan spela upp hela ljudet igen efteråt vid behov.

2. **State & UI:**
   - I `state` tillförs:
     - `nextPlaybackTime: 0`
     - `activeAudioSources: []`
     - `isStreamingAudioPlaying: false`
   - Gränssnittselement:
     - En visuell statusetikett och pulserande indikator: `"Översätter & Spelar upp i realtid..."` aktiveras direkt vid första mottagna `inlineData`.
     - En tydlig knapp `btn-stop-stream` ("Stäng anslutning / Stoppa") placeras bredvid sändningsknappen. Den aktiveras när anslutningen öppnas eller strömning pågår.
     - Vid klick på "Stoppa":
       - Avbryter pågående WebSocket via `ws.close(1000, "Manuell avstängning")`.
       - Stoppar alla schemalagda källor: `state.activeAudioSources.forEach(s => { try { s.stop(); } catch(e){} });`.
       - Tömmer ljudkön och återställer `state.nextPlaybackTime = 0`.
       - Uppdaterar UI till avslutat läge.

```json
{
  "status": "PLANNING",
  "current_domain": "live_audio_sanity_check",
  "next_step": "2a_forandra_utat_vision",
  "ticket_id": "TCK-003",
  "active_skill": "gemini-api",
  "active_vectors": ["State", "Effects", "Contract"]
}
```
