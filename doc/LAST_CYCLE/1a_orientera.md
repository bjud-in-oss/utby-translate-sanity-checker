# Steg 1a: Orientera (TCK-003)

### Vektor-inriktade GROW-frågor för TCK-003

1. **Effects & Signal Processing (Streaming Audio Queue):**
   - Hur designar vi en kontinuerlig Web Audio schemaläggare med `AudioBufferSourceNode` och `AudioContext.currentTime` så att inkommande PCM-paket (24 000 Hz Mono Int16) spelas upp omedelbart i realtid utan hack, pauser eller klick?
   - Hur kopplar vi loss kravet på att vänta på `sc.turnComplete` eller en helhetskonvertering via `finalizeGeminiAudio()` för att påbörja hörbar återkoppling?

2. **State & UI (Indikator & Avbrottskontroll):**
   - Vilka tillståndsvariabler krävs i `state` för att hålla reda på `nextPlaybackTime`, aktiva `AudioBufferSourceNode`-instanser och streamingstatus?
   - Hur presenterar vi omedelbart för användaren ("Översätter & Spelar upp i realtid...") så fort det första ljudpaketet dimper ner?
   - Hur implementerar vi knappen "Stäng anslutning / Stoppa" för att avbryta pågående sändning, stänga WebSocket-anslutningen och omedelbart tysta eventuellt köat ljud?
