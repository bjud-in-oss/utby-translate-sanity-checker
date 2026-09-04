# Steg 1a: Orientera (TCK-002)

### Vektor-inriktade GROW-frågor för TCK-002

1. **Effects & Signal Processing (Ljudhastighet och Resampling):**
   - Hur säkerställer vi att AudioWorklet-processorn dynamiskt beräknar samplingsförhållandet `ratio = inputSampleRate / 16000` baserat på den faktiska `AudioContext.sampleRate` (t.ex. 48 000 Hz eller 44 100 Hz)?
   - Hur garanterar vi att en 8-sekunders inspelning vid 48 kHz resulterar i exakt ~128 000 samples (16 000 samples/sekund) i stället för 256 000 samples, vilket eliminerar den förvrängda, halverade uppspelningshastigheten?

2. **Contract & WebSocket Protocol (Avlägsnande av manuell turnComplete):**
   - Varför orsakar utgående anrop med `ws.send(JSON.stringify({ clientContent: { turns: [], turnComplete: true } }))` felkod 1007 (`Request contains an invalid argument`) i Gemini Live BidiGenerateContent?
   - Hur upprätthåller vi full mottagning och asynkron tolkning genom att lämna WebSocket-anslutningen öppen efter sista PCM-chunken, samtidigt som alla inkommande händelselyssnare (inklusive `sc.turnComplete` och `finalizeGeminiAudio()`) förblir fullt aktiva?
