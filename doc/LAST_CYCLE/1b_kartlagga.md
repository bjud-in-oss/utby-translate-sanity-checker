# Steg 1b: Kartlägga (TCK-002)

### Svar på GROW-frågorna mot koden

1. **Effects & Signal Processing:**
   - I `index.html` användes tidigare globala `sampleRate` inne i AudioWorklet utan att explicit binda den till `state.audioContext.sampleRate` eller tillhandahålla en dynamisk `ratio`-beräkning via nodens `processorOptions`.
   - Lösning:
     - Vi hämtar `inputSampleRate = state.audioContext.sampleRate` (t.ex. 48 000 Hz).
     - Vi skickar med `{ processorOptions: { inputSampleRate } }` vid instantiering av `AudioWorkletNode`.
     - I AudioWorklet-processorn beräknas:
       `const inputSampleRate = (options && options.processorOptions && options.processorOptions.inputSampleRate) || sampleRate || 48000;`
       `this.ratio = inputSampleRate / 16000;`
     - Steglängden för sampling i processorn sätts till `const step = this.ratio;`.
     - För en 8-sekunders inspelning vid 48 kHz blir antalet inlästa samples 384 000. Med `step = 3.0` (48000 / 16000) produceras exakt 384 000 / 3 = 128 000 samples, vilket motsvarar exakt 8,0 sekunder vid 16 000 Hz.

2. **Contract & WebSocket Protocol:**
   - I `streamPcmChunks(ws)` i `index.html` finns följande rader som triggar krasch med felkod 1007:
     ```javascript
     if (ws.readyState === WebSocket.OPEN) {
       const turnCompletePayload = {
         clientContent: {
           turns: [],
           turnComplete: true
         }
       };
       ws.send(JSON.stringify(turnCompletePayload));
     }
     ```
   - I Gemini Multimodal Live API förväntar sig servern att kontinuerliga media-chunks strömmas i `realtimeInput`. Ett tomt `clientContent` med tomma turns anses vara ett ogiltigt argument i BidiGenerateContent-sessionen.
   - Åtgärd: Ta helt bort detta utgående `ws.send()`-anrop. Lämna WebSocket-anslutningen öppen.
   - Servern skickar själv `serverContent.turnComplete: true` när den har transkriberat och genererat översättningen. Vår inkommande hanterare i `ws.onmessage` förblir 100 % intakt och anropar `finalizeGeminiAudio()`.

```json
{
  "status": "PLANNING",
  "current_domain": "live_audio_sanity_check",
  "next_step": "2a_forandra_utat_vision",
  "ticket_id": "TCK-002",
  "active_skill": "gemini-api",
  "active_vectors": ["Contract", "Effects"]
}
```
