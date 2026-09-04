# Steg 1a: Orientera

### Vektor-inriktade GROW-frågor för TCK-001

1. **State & Contract (Tillstånd och Dataprotokoll):**
   - Hur modellerar vi tillståndsmaskinen för ett strikt sekventiellt tvåstegsflöde (`IDLE` -> `RECORDING` -> `RECORDED` -> `STREAMING_AND_WAITING` -> `COMPLETED` / `ERROR`) så att mikrofonupptagning och Gemini-svar aldrig överlappar eller orsakar rundgång?
   - Hur garanterar vi att ljuddata samplas och buntas till exakt 16 000 Hz, mono, 16-bit PCM Little-Endian och strömmas i paket om ca 100 ms (1 600 samples = 3 200 bytes per chunk)?

2. **Effects & I/O (Web Audio, Enheter och WebSocket):**
   - Hur säkerställer vi fullständig avstängning av webbläsarens inbyggda DSP (`echoCancellation: false`, `noiseSuppression: false`, `autoGainControl: false`) vid hämtning av mediastream från valfri ljudenhet (t.ex. NDI Virtual Webcam / vMix)?
   - Hur etableras och hanteras den direkta WebSocket-uppkopplingen mot `wss://generativelanguage.googleapis.com/ws/google.ai.generativelanguage.v1alpha.GenerativeService.BidiGenerateContent?key=...` för modellen `models/gemini-3.5-live-translate-preview`?

3. **Resilience & Feedback (Självläkning, Kontroll och Egen-lyssning):**
   - Hur möjliggör vi omedelbar kvalitetsverifiering av den faktiskt insamlade PCM-datan (eget ljudspår omvandlat till WAV Blob) innan man drar slutsats om API-fel?
   - Hur hanterar gränssnittet WebSocket-felkoder, avbrutna sessioner eller utebliven mikrofonbehörighet på ett pedagogiskt och självförklarande sätt?
