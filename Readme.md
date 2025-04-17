# 📡 StreamPixel SDK Integration Guide

Welcome to the official StreamPixel SDK guide. This SDK helps you integrate Unreal Engine applications into your frontend via WebRTC and provides control over video rendering, input handling, and backend communication.

---

## 📐 1. Configuration

Use the `StreamPixelApplication()` method to initialize the SDK. It returns two objects:

- **`pixelStreaming`**: Handles core WebRTC communication and event responses.
- **`appStream`**: Provides video rendering, DOM container, input control, and utility methods.

### ✅ Example

```js
const { appStream, pixelStreaming } = StreamPixelApplication({
  AutoPlayVideo: true,
  region: "Asia-pacific",
  StartVideoMuted: true,
  AutoConnect: true,
  useMic: false,
  appId: "6744fb98094f3b905b08a4fa",
  afktimeout: 250,
  touchInput: true,
  mouseInput: true,
  gamepadInput: true,
  resolution: true,
  hoverMouse: true,
  xrInput: false,
  keyBoardInput: true,
  fakeMouseWithTouches: false,
  resX: 1920,
  resY: 1080
});
```

### 🔄 Key Parameters

| Param              | Type     | Description                                      |
|--------------------|----------|--------------------------------------------------|
| `AutoConnect`      | boolean  | Connects automatically on load                  |
| `region`           | string   | Project region (e.g., `"Asia-pacific"`)          |
| `resX` / `resY`    | number   | Desired resolution                               |
| `afktimeout`       | number   | Idle timeout in milliseconds                    |
| `mouseInput`       | boolean  | Enable mouse input                              |
| `keyBoardInput`    | boolean  | Enable keyboard input                           |
| `touchInput`       | boolean  | Enable touch input                              |
| `appId`            | string   | Your project ID                                 |

---

## 🧩 2. Connection Handlers

Register connection lifecycle events to update your UI and monitor the connection status.

```js
appStream.onVideoInitialized = () => {
  console.log("VIDEO INITIALIZED");
};

appStream.onWebRtcSdp = () => {
  console.log("SDP sent");
};

appStream.onWebRtcConnecting = () => {
  console.log("Connecting...");
};

appStream.onWebRtcConnected = () => {
  console.log("Connected!");
};

appStream.onConnectAction = () => {
  console.log("Connect Action Triggered");
};
```

---

## 🧠 3. Interact with Unreal Application (Emit + Listen)

### 📥 Receiving Messages from Unreal

```js
pixelStreaming.addResponseEventListener("handle_responses", (response) => {
  console.log("Response from UE:", response);
});
```

> Unreal developers must use `EmitResponse("handle_responses", payload)` on the backend.

### 📤 Sending Messages to Unreal

```js
appStream.stream.emitUIInteraction({
  message: { value: '480p (854x480)', type: "setResolution" }
});
```

Supports control for resolution, quality presets, camera switching, etc.

---

## 🔇 4. Audio Control

Use this snippet to toggle sound on/off:

```js
const toggleSound = () => {
  const audioElement = appStream.stream._webRtcController.streamController.audioElement;
  audioElement.play();
  audioElement.muted = !audioElement.muted;
};
```

---

## 🧼 5. Disconnect & AFK Timeout

### 🔌 Manually Disconnect

```js
pixelStreaming.disconnect();
appStream.stream.disconnect();
```

### ⏳ AFK Timeout

If no user interaction occurs within the `afktimeout` duration, the session will disconnect (if backend supports it).

> 💡 Tip: Track frontend activity (e.g., `mousemove`, `keydown`) to reset AFK timeout if needed.

---

## 📊 6. Display Stream Stats

```js
appStream.statsPanel.show();
console.log(appStream.statsPanel._statsResult);
```

Displays WebRTC debug metrics like frame rate, bandwidth, and latency.

---

## 🧩 Recommended Usage Pattern in React

```js
const { appStream, pixelStreaming } = StreamPixelApplication(config);

// Mount video element
videoRef.current.append(appStream.rootElement);

// Setup lifecycle handlers
appStream.onWebRtcConnected = () => console.log("Connected");

// Listen for events from Unreal
pixelStreaming.addResponseEventListener("eventName", handleCustomEvent);

// Send interaction to Unreal
appStream.stream.emitUIInteraction({
  message: { type: 'action', value: 'trigger' }
});

// Cleanup
pixelStreaming.disconnect();
```

---

## 🛠️ Need Help?

For integration support, documentation, and updates, contact the StreamPixel team or visit our [official support page](https://streampixel.io).

---