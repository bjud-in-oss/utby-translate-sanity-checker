# Steg 2a: Förändra Utåt - Vision (TCK-002)

### Målbild för användarens ljudflöde
1. **Kristallklar PCM-provlyssning:**
   - När användaren spelar in 8 sekunder från sin NDI/vMix-ljudkälla samlas exakt 128 000 samples (16 000 Hz Mono Int16).
   - Vid klick på "Spela upp PCM" (eller nedladdning av WAV) spelas ljudet upp i perfekt, naturlig hastighet och tonhöjd utan förvrängning, sludder eller mörkt muller.
2. **Felfri WebSocket-sändning utan felkod 1007:**
   - När de sista PCM-paketen har strömmats förblir WebSocket-anslutningen lugnt öppen utan att skicka otillåtna styrpaket.
   - Gemini Live Translate API tar emot ljudströmmen, bearbetar den och returnerar svarsströmmar med text och 24 kHz ljud utan avbrott.
   - Den grafiska designen, Tailwind-layouten och alla diagnostikpaneler förblir exakt som tidigare.
