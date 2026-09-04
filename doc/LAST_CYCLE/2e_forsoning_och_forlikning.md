# Steg 2e: Försoning och Förlikning

### Sammanvägning av krav och begränsningar
- **Enhetskrav:** Single-file HTML (Vanilla JS + Tailwind CDN + inbäddad AudioWorklet) för maximal portabilitet och sanitetstestning utan lokala beroenden.
- **Domänkrav:** Google Gemini Live Translate API (`models/gemini-3.5-live-translate-preview`), 16 000 Hz 16-bit Little-Endian Mono PCM, ~100 ms chunk streaming.
- **Feedbackkrav:** VU-mätare, "Egen ljud-dump" (WAV-uppspelning av den insamlade datan), transkriberings- och översättningskort samt automatisk uppspelning av Geminis returljud.
- **Arkitektonisk renhet:** Inga kompromisser med DSP (alla filter strikt avstängda). Ingen medhörning under inspelning.

MÄTTNAD: JA
