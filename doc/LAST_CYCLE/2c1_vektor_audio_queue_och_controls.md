# Steg 2c1: Vektoranalys - Audio Queue, State & Controls (TCK-003)

### 1. Web Audio Real-Time Streaming Queue
```javascript
function queueRealtimeAudioChunk(rawPcmBytes, sampleRate = 24000) {
  if (!state.audioContext) return;
  if (state.audioContext.state === 'suspended') {
    state.audioContext.resume();
  }

  // Konvertera Int16 Little-Endian till Float32 [-1.0, 1.0]
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
    state.nextPlaybackTime = now + 0.05; // 50 ms jitter buffert
  }

  source.start(state.nextPlaybackTime);
  state.nextPlaybackTime += audioBuffer.duration;

  state.activeAudioSources.push(source);
  source.onended = () => {
    const idx = state.activeAudioSources.indexOf(source);
    if (idx !== -1) state.activeAudioSources.splice(idx, 1);
    if (state.activeAudioSources.length === 0 && !state.isStreaming) {
      geminiAudioStatus.textContent = 'Realtidsuppspelning slutförd';
    }
  };

  // Uppdatera visuell indikator
  geminiAudioStatus.innerHTML = `<span class="text-emerald-400 font-semibold animate-pulse">● Översätter &amp; Spelar upp i realtid...</span>`;
}
```

### 2. Stoppa-kontroll (Avbryt & Tysta)
```javascript
function stopStreamingAndAudio() {
  log('Användaren stoppade strömning och ljud manuellt.', 'warn');
  
  if (state.webSocket) {
    try {
      state.webSocket.close(1000, 'Stoppad av användaren');
    } catch (e) {}
    state.webSocket = null;
  }

  // Tysta och rensa alla aktiva ljudkällor
  if (state.activeAudioSources) {
    state.activeAudioSources.forEach(src => {
      try { src.stop(); } catch (e) {}
    });
    state.activeAudioSources = [];
  }
  state.nextPlaybackTime = 0;

  geminiAudioStatus.textContent = 'Avbruten av användaren';
  resetStreamingUI();
}
```
