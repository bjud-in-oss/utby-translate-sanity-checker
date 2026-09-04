# Steg 2b: Evaluera Yttre Anpassning

### Analys av användarupplevelse och kontextuella restriktioner
1. **Sanity Check-syftet:**
   - Eftersom syftet är att felsöka en ljudkedja och isolera fel mellan NDI/vMix, AudioWorklet och Gemini, måste gränssnittet vara extremt transparent.
   - Det måste finnas en omedelbar "sanity check" på *båda sidor* av sändningen:
     a) Hörde datorn mikrofonen korrekt? -> VU-mätare + "Egen ljud-dump" (WAV-uppspelning av den exakta insamlade PCM-bufferten).
     b) Accepterade Gemini strömmen och vad gavs i svar? -> WebSocket logg, textrespons och automatisk audio playback.

2. **DSP & NDI-känslighet:**
   - Vissa virtuella ljudkablar och NDI-webcam-drivrutiner ger ibland distorsion eller tystnad om webbläsarens echoCancellation eller AGC är aktiverad. Att hårdkoda dessa till `false` är avgörande.
   - AudioContext måste hantera eventuell webbläsarestriktion ("Autoplay policy / User Gesture") genom att starta AudioContext först vid ett användarklick.

3. **Validering mot krav:**
   - Kravet på "All JavaScript, AudioWorklet (via Blob/Inline) och HTML ska ligga i en och samma fil" respekteras till 100 %.
