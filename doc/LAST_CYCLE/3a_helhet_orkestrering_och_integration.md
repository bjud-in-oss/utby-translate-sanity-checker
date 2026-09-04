# Steg 3a: Helhet, Orkestrering och Integration (TCK-003)

### Detaljerat händelseflöde
1. **Användaren klickar på "Skicka testsekvens till Gemini Live Translate":**
   - Knappen avaktiveras och byter text till "Strömmar...".
   - Stoppknappen `btn-stop-stream` aktiveras och visas tydligt med röd/mörkgrå varning.
   - `state.nextPlaybackTime = 0` nollställs.
2. **Inkommande ljudpaket i `ws.onmessage`:**
   - Varje `part.inlineData` skickas omedelbart till `queueRealtimeAudioChunk(rawPcmBytes, 24000)`.
   - Ljudet spelas upp kontinuerligt i realtid genom schemalagda `AudioBufferSourceNode`.
   - Statusraden för Geminis ljud visar en pulserande grön status: `"Översätter & Spelar upp i realtid..."`.
3. **Avbrott / Stopp:**
   - Om användaren klickar på "Stäng anslutning / Stoppa", avbryts anslutningen direkt, alla ljudkällor tystas och UI återställs.
