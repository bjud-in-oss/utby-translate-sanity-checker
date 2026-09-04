# Steg 2c1: Vektoranalys - Effects & Contract (TCK-002)

### 1. Effects: AudioWorklet Resampling-beräkning
I `createInlineWorkletBlob()`:
```javascript
class PCMRecorderProcessor extends AudioWorkletProcessor {
  constructor(options) {
    super();
    const inputSampleRate = (options && options.processorOptions && options.processorOptions.inputSampleRate) || sampleRate || 48000;
    this.ratio = inputSampleRate / 16000;
    this.sourceIndex = 0;
    this.lastSample = 0;
  }

  process(inputs, outputs, parameters) {
    const input = inputs[0];
    if (!input || !input[0] || input[0].length === 0) return true;
    const inputChannel = input[0];
    const step = this.ratio > 0 ? this.ratio : 1;

    const outputSamples = [];
    while (this.sourceIndex < inputChannel.length) {
      const idx = Math.floor(this.sourceIndex);
      const frac = this.sourceIndex - idx;
      const s0 = idx < inputChannel.length ? inputChannel[idx] : this.lastSample;
      const s1 = (idx + 1) < inputChannel.length ? inputChannel[idx + 1] : s0;
      const interpolated = s0 + frac * (s1 - s0);

      const clamped = Math.max(-1, Math.min(1, interpolated));
      const int16 = clamped < 0 ? clamped * 0x8000 : clamped * 0x7FFF;
      outputSamples.push(Math.round(int16));

      this.sourceIndex += step;
    }
    this.sourceIndex -= inputChannel.length;
    this.lastSample = inputChannel[inputChannel.length - 1];

    if (outputSamples.length > 0) {
      const pcm16 = new Int16Array(outputSamples);
      this.port.postMessage(pcm16.buffer, [pcm16.buffer]);
    }
    return true;
  }
}
```
Och i anropet vid start av inspelning:
```javascript
const workletNode = new AudioWorkletNode(state.audioContext, 'pcm-recorder-processor', {
  processorOptions: {
    inputSampleRate: state.audioContext.sampleRate
  }
});
```

### 2. Contract: Avlägsnande av clientContent.turnComplete
I `streamPcmChunks(ws)`:
Efter att loopen med PCM-chunks har slutförts:
- Ta bort:
  ```javascript
  if (ws.readyState === WebSocket.OPEN) {
    const turnCompletePayload = {
      clientContent: {
        turns: [],
        turnComplete: true
      }
    };
    ws.send(JSON.stringify(turnCompletePayload));
  }
  ```
- Ersätt med:
  ```javascript
  log(`Alla ${totalChunks} PCM-chunks har strömmats till Gemini Live. WebSocket förblir öppen och väntar på modellsvar...`, 'success');
  sendBtnText.textContent = 'Väntar på Geminis svar...';
  streamingLabel.textContent = 'Sändning klar • Väntar på respons';
  ```
