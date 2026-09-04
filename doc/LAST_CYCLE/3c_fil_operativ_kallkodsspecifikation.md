# Steg 3c: Fil-operativ Källkodsspecifikation (TCK-003)

### Källkodsändringar för index.html

#### 1. Tillstånd i `state`
```javascript
nextPlaybackTime: 0,
activeAudioSources: [],
```

#### 2. Tillägg av UI-knapp för stopp i HTML
I sektion 2 intill sändningsknappen:
```html
<div class="pt-2 flex items-center gap-2">
  <button id="btn-send-gemini" type="button" disabled class="flex-1 py-3 px-4 rounded-xl bg-sky-600 hover:bg-sky-500 disabled:opacity-40 disabled:hover:bg-sky-600 text-white font-medium text-sm transition shadow-lg shadow-sky-950/50 flex items-center justify-center gap-2 active:scale-[0.99]">
    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 5l7 7m0 0l-7 7m7-7H3" /></svg>
    <span id="send-btn-text">Skicka testsekvens till Gemini Live Translate</span>
  </button>
  <button id="btn-stop-stream" type="button" disabled class="py-3 px-4 rounded-xl bg-slate-800 hover:bg-red-950/80 hover:text-red-300 disabled:opacity-30 text-slate-400 font-medium text-sm transition border border-slate-700 hover:border-red-500/50 flex items-center justify-center gap-2">
    <svg class="w-4 h-4 text-red-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
    <span>Stäng anslutning / Stoppa</span>
  </button>
</div>
```

#### 3. Real-Time Streaming Audio Queue i JS
```javascript
function queueRealtimeAudioChunk(rawPcmBytes, sampleRate = 24000) {
  if (!state.audioContext) return;
  if (state.audioContext.state === 'suspended') {
    state.audioContext.resume();
  }

  const int16 = new Int16Array(rawPcmBytes.buffer, rawPcmBytes.byteOffset, rawPcmBytes.byteLength / 2);
  const float32 = new Float32Array(int16.length);
  for (let i = 0; i < int16.length; i++) {
    const s = int16[i];
    float32[i] = s < 0 ? s / 0x8000 : s / 0x7FFF;
  }

  const audioBuffer = state.audioContext.createBuffer(1, float32.length, sampleRate);
  audioBuffer.getChannelData(0).set(float32);

  const source = state.audioContext.createBufferSource();
  source.buffer = audioBuffer;
  source.connect(state.audioContext.destination);

  const now = state.audioContext.currentTime;
  if (state.nextPlaybackTime < now) {
    state.nextPlaybackTime = now + 0.05;
  }

  source.start(state.nextPlaybackTime);
  state.nextPlaybackTime += audioBuffer.duration;

  state.activeAudioSources.push(source);
  source.onended = () => {
    const idx = state.activeAudioSources.indexOf(source);
    if (idx !== -1) state.activeAudioSources.splice(idx, 1);
    if (state.activeAudioSources.length === 0 && !state.isStreaming) {
      geminiAudioStatus.textContent = 'Realtidsuppspelning klar';
    }
  };

  geminiAudioStatus.innerHTML = `<span class="text-emerald-400 font-semibold animate-pulse">● Översätter &amp; Spelar upp i realtid...</span>`;
}
```

#### 4. Uppdatering av `handleGeminiServerMessage`
Vid inkommande `part.inlineData`:
Anropa omedelbart `queueRealtimeAudioChunk(rawPcmBytes, 24000)`.
Kravet att vänta på `sc.turnComplete` eller `finalizeGeminiAudio()` för att påbörja uppspelning tas bort.

BORTTAGEN_PROP: wait_for_turn_complete_to_play_audio

BESLUT: GODKÄND
