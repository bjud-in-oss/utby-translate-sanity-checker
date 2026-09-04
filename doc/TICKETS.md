# Ärendehantering (Tickets)

## Epik: Sanitetstest för Google Gemini Live Translate API
Ett renodlat, minimalistiskt testverktyg i en enda fristående HTML-fil (med Vanilla JS, Tailwind CDN och inbyggd AudioWorklet) för att bekräfta att ljudström från NDI Virtual Webcam / vMix samplas till 16 kHz 16-bit PCM och förstås/översätts felfritt av Gemini Live Translate API utan mellanhänder.

### Del-tickets
- [x] **TCK-001**: Etablera arkitektur, domänkontrakt och isolerad implementation av Gemini Live Sanity Check i en enda fristående HTML-vy.
  - *Mål*: Konfiguration (enhetsval, VU-mätare, API-nyckel, avstängd DSP), tvåstegsflöde (Inspelning -> Sändning), WebSocket Bidi-protokoll mot `models/gemini-3.5-live-translate-preview`, återkoppling av text och ljud samt PCM-dump för lokal kvalitetslyssning.
  - *Status*: Slutförd (Fas 2)
- [x] **TCK-002**: Korrigera resampling-förhållande till 16 kHz PCM16 samt ta bort utgående turnCompletePayload.
  - *Mål*: Dynamisk beräkning av `ratio = inputSampleRate / 16000` via AudioContext.sampleRate (så 8s vid 48 kHz ger exakt ~128 000 samples i stället för 256 000) samt avlägsna utgående clientContent.turnComplete för att förhindra WebSocket-felkod 1007.
  - *Status*: Slutförd (Fas 2)
