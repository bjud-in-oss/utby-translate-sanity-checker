# Steg 2d1: Evaluering av Audio Queue & Controls (TCK-003)

### Evaluering
- **Latency & Sömlöshet:** Genom att beräkna `nextPlaybackTime` baserat på `audioBuffer.duration` spelas inkommande paket upp utan gap eller överlappningar.
- **Robusthet vid användaravbrott:** Genom att hålla referenser till schemalagda `AudioBufferSourceNode` kan vi avbryta dem omedelbart med `src.stop()`, vilket förhindrar att oönskat ljud fortsätter spela i bakgrunden.
- **Bakåtkompatibilitet:** Vi sparar fortfarande råa chunks i `state.receivedAudioChunks`, vilket gör att WAV-sammanställningen vid `sc.turnComplete` eller manuell uppspelning med `btn-play-gemini-audio` fortsätter fungera som ett komplement för arkivering.
