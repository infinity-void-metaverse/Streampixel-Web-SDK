📐 1. Configuration
Use the StreamPixelApplication method to initialize the streaming SDK. It returns two objects:

pixelStreaming: Handles core WebRTC communication and event responses.

appStream: Provides video rendering, DOM container, input control, and utility methods.

✅ Example

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
🔄 Key Parameters

Param	Type	Description
AutoConnect	boolean	Connects automatically on load
region	string	Project region (e.g., "Asia-pacific")
resX / resY	number	Desired resolution
afktimeout	number	Time (ms) before idle session is terminated
mouseInput, keyBoardInput, touchInput	boolean	Input options
appId	string	Your projectId
🧩 2. Connection Handlers
You can register WebRTC and video lifecycle events on the appStream object to respond to key connection states.

🔌 Supported Events

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
These are useful for updating UI, showing loaders, or debugging connection status.

🧠 3. Interact with Unreal Application (Emit + Listen)
📥 Receiving Messages from Unreal
To listen for custom messages emitted by the Unreal app:


pixelStreaming.addResponseEventListener("handle_responses", (response) => {
  console.log("Response from UE:", response);
});

Unreal developers must use EmitResponse("handle_responses", payload) on the backend.

📤 Sending Messages to Unreal
Use this to send commands or UI interactions from the frontend to Unreal:


appStream.stream.emitUIInteraction({
  message: { value: '480p (854x480)', type: "setResolution" }
});

This allows frontend-driven control for resolution, quality presets, camera switching, etc.

🔇 4. Audio Control
You can programmatically toggle audio on/off using the internal audio element:


const toggleSound = () => {
  const audioElement = appStream.stream._webRtcController.streamController.audioElement;
  audioElement.play();
  audioElement.muted = !audioElement.muted;
};
This is useful for building mute/unmute buttons or auto-muting behavior.

🧼 5. Disconnect & AFK Timeout
🔌 Manually Disconnect
To manually disconnect the stream:


pixelStreaming.disconnect();
appStream.stream.disconnect();
⏳ AFK Timeout
AFK (Away From Keyboard) behavior is controlled via:

afktimeout: 250 // in milliseconds
If there's no user interaction within this timeout, the session will be disconnected automatically (if enabled in backend).

Tip: Use frontend activity tracking (mousemove, keypress) to reset AFK timers if needed.

📊 6. Display Stream Stats
You can show performance/debug stats using:

appStream.statsPanel.show();
console.log(appStream.statsPanel._statsResult);
This includes WebRTC metrics like bandwidth, latency, and frame rate.

🧩 Recommended Usage Pattern in React

const { appStream, pixelStreaming } = StreamPixelApplication(config);

// Add video
videoRef.current.append(appStream.rootElement);

// Setup handlers
appStream.onWebRtcConnected = () => console.log("Connected");
pixelStreaming.addResponseEventListener("eventName", handleCustomEvent);

// Interact
appStream.stream.emitUIInteraction({ message: { type: 'action', value: 'trigger' } });

// Disconnect when needed
pixelStreaming.disconnect();


