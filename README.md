# Streampixel Web SDK

Stream Unreal Engine content directly into any web browser via WebRTC.

## Installation

```bash
npm install git+https://github.com/infinity-void-metaverse/Streampixel-Web-SDK.git#latest
```

## Quick Start

```js
import { StreamPixelApplication } from 'streampixelsdk';

const { appStream, pixelStreaming, queueHandler, UIControl, reconnectStream } =
  await StreamPixelApplication({
    appId: 'YOUR_PROJECT_ID', // From Streampixel dashboard
    AutoConnect: true,
  });
```

That's it. The SDK resolves signaling servers, TURN credentials, and UE instance routing automatically from your `appId`.

## Configuration

All settings below are **optional** — they default to your Streampixel dashboard configuration.

```js
await StreamPixelApplication({
  appId: 'YOUR_PROJECT_ID',       // Required

  // Connection
  AutoConnect: true,
  streamerId: 'instance-id',      // Target a specific streamer
  sfuHost: 'false',               // SFU host mode
  sfuPlayer: 'false',             // SFU viewer mode
  forceTurn: true,                // Force TURN relay

  // Codec
  primaryCodec: 'AV1',            // 'AV1' | 'H264' | 'VP9' | 'VP8'
  fallBackCodec: 'H264',

  // Resolution
  maxStreamQuality: '1080p (1920x1080)',
  startResolution: '1080p (1920x1080)',
  startResolutionMobile: '480p (854x480)',
  startResolutionTab: '720p (1280x720)',
  resolutionMode: 'Fixed Resolution Mode',

  // Bitrate / Quality
  minBitrate: 1,
  maxBitrate: 100,
  minQP: 20,
  maxQP: -1,

  // Input
  mouseInput: true,
  keyBoardInput: true,
  touchInput: true,
  hoverMouse: true,
  gamepadInput: true,
  xrInput: true,
  fakeMouseWithTouches: false,

  // Audio / Camera
  useMic: true,
  useCamera: true,

  // UI
  showResolution: true,

  // AFK
  afktimeout: 120,
});
```

## Return Values

| Object | Description |
|--------|-------------|
| `appStream` | Application wrapper — DOM root, video lifecycle, overlays |
| `pixelStreaming` | Core SDK — events, connect/disconnect, input control |
| `queueHandler` | Register callback for queue position updates |
| `UIControl` | Toggle audio, change resolution, toggle hover mouse, get stats |
| `reconnectStream` | Emits state events during auto-reconnect |

## Events

```js
// WebRTC lifecycle
pixelStreaming.addEventListener('webRtcAutoConnect', () => {});
pixelStreaming.addEventListener('webRtcConnecting', () => {});
pixelStreaming.addEventListener('webRtcSdp', () => {});
pixelStreaming.addEventListener('webRtcConnected', () => {});
pixelStreaming.addEventListener('streamLoading', () => {});
pixelStreaming.addEventListener('playStream', () => {});
pixelStreaming.addEventListener('videoInitialized', () => {});
pixelStreaming.addEventListener('webRtcFailed', () => {});
pixelStreaming.addEventListener('webRtcDisconnected', () => {});

// AFK
pixelStreaming.addEventListener('afkWarningActivate', (e) => {});
pixelStreaming.addEventListener('afkWarningUpdate', (e) => {});
pixelStreaming.addEventListener('afkWarningDeactivate', () => {});
pixelStreaming.addEventListener('afkTimedOut', () => {});

// Stats
pixelStreaming.addEventListener('statsReceived', (e) => {});

// Reconnection
reconnectStream.on('state', (data) => {
  // data.status: 'connecting' | 'reconnecting' | 'retrying' | 'connected' | 'disconnected' | 'failed'
});

// Queue
queueHandler((msg) => {
  // msg.position, msg.message
});

// UE responses
pixelStreaming.addResponseEventListener('handle_responses', (response) => {});
```

## Sending Commands to UE

```js
// Custom JSON payload
appStream.stream.emitUIInteraction({ type: 'action', value: 'data' });

// Console command
pixelStreaming.emitConsoleCommand('stat fps');
```

## UIControl Methods

| Method | Description |
|--------|-------------|
| `toggleAudio()` | Toggle stream audio on/off |
| `handleResMax('1920x1080')` | Set max resolution |
| `toggleHoveringMouse(true)` | Enable/disable hover events |
| `getStreamStats()` | Get current stream statistics |
| `getResolution()` | Get available resolution options |

## Voice & Text Chat

```js
import { StreamPixelVoiceChat } from 'streampixelsdk';

const chat = new StreamPixelVoiceChat(roomName, userName, voiceChat, avatar, micStart);
await chat.join();

chat.onMessage((msg) => {});
chat.onParticipantUpdate((participants) => {});
chat.sendMessage('Hello');
chat.toggleMic();
chat.leave();
```

## Example Application

See the full working example with loading screen, custom UI, dev tools, and more:

**[Streampixel SDK Example](https://github.com/infinity-void-metaverse/Streampixel-SDK-Example)**

## License

Proprietary — see your Streampixel license agreement for terms.
