# Steg 2a: Förändra Utåt - Vision

### Målbild för användaren
Användaren arbetar i en krävande streaming- och broadcastmiljö (vMix / NDI Virtual Webcam) och behöver med 100 % säkerhet utesluta eller bekräfta om ljudkällan och PCM-konverteringen accepteras av Google Gemini Live Translate API.

### Gränssnittets arkitektur
- **En enda fristående HTML-fil**: Kan öppnas direkt i valfri modern webbläsare utan byggsteg, eller serveras via dev-servern.
- **Konfigurationszon**:
  - Enhetsväljare med dynamisk lista av tillgängliga ljudingångar.
  - Tydlig visuell VU-mätare (med grön/gul/röd toppindikering och decibel/amplitud) som omedelbart rör sig när NDI/vMix skickar ljud.
  - API-nyckelfält med möjlighet att dölja/visa nyckeln.
  - Indikator för DSP-status: "DSP avstängd (Ren råsignal från NDI)".
- **Steg 1: Inspelningszon**:
  - Stor, distinkt knapp: "Spela in testsekvens" med sekundräknare (0–10 s) och realtidsindikator för samplade bytes i bufferten.
  - Ingen ljudåterkoppling under inspelning för att eliminera ekon och feedbackloopar.
- **Steg 2: Sändnings- och Översättningszon**:
  - Knapp för "Skicka till Gemini Live Translate".
  - Visuell sändningsmätare (visar hur de ~100 ms PCM-paketen strömmas via WebSocket).
- **Steg 3: Resultat- och Diagnostikzon**:
  - Kort för **Originaltranskript (Vad Gemini hörde)**.
  - Kort för **Översättning (Geminis textrespons)**.
  - Spelare för **Översatt ljud från Gemini** (spelas upp automatiskt efter mottagande).
  - Knapp och spelare för **Egen ljud-dump (Insamlad PCM som WAV)**.
  - Diagnostisk live-logg för WebSocket-meddelanden.
