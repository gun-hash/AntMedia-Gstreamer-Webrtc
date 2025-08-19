# GStreamer AntMedia WebRTC Connection

This program enables WebRTC connections between GStreamer and AntMedia Server (AMS). It supports both publishing streams to AMS and playing streams from AMS using WebRTC protocol.

## Features

- **WebRTC Support**: Full WebRTC signaling with SDP offer/answer exchange
- **Multiple Modes**: Publish, Play, and P2P (peer-to-peer) modes
- **Secure Connections**: Support for both WS (WebSocket) and WSS (WebSocket Secure) connections
- **Real-time Streaming**: Live audio/video streaming with low latency
- **Multiple Streams**: Support for playing multiple streams simultaneously
- **File Streaming**: Stream local video files to AMS
- **Test Sources**: Built-in test video/audio sources for testing

## Migration Notice

**v2.0 Update**: This version includes a major migration from `librws` to `libsoup-3.0` for WebSocket connections. This change provides:

- ✅ **WSS Support**: Secure WebSocket connections (wss://)
- ✅ **Better TLS Integration**: Native GLib/GObject integration
- ✅ **Improved Reliability**: More robust connection handling
- ✅ **Modern Dependencies**: Uses current libsoup-3.0 instead of deprecated librws

## Installation

### Prerequisites

#### Ubuntu/Debian:
```bash
# Install GStreamer and dependencies
sudo apt update
sudo apt install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev \
                 libgstreamer-plugins-bad1.0-dev gstreamer1.0-plugins-base \
                 gstreamer1.0-plugins-good gstreamer1.0-plugins-bad \
                 gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-doc \
                 gstreamer1.0-tools gstreamer1.0-x gstreamer1.0-alsa \
                 gstreamer1.0-gl gstreamer1.0-gtk3 gstreamer1.0-qt5 \
                 libjson-glib-dev gstreamer1.0-nice gstreamer1.0-pulseaudio

# Install libsoup-3.0 (required for WebSocket connections)
sudo apt install libsoup-3.0-dev

# Install glib-networking (required for WSS/TLS support)
sudo apt install glib-networking
```

#### Windows:
1. Download and install GStreamer from https://gstreamer.freedesktop.org/download/#windows
2. Install both `gstreamer-1.0` and `gstreamer-devel` packages
3. Install libsoup-3.0 development libraries

#### macOS:
1. Download and install GStreamer from https://gstreamer.freedesktop.org/download/#macos
2. Install both `gstreamer-1.0` and `gstreamer-devel` packages
3. Install libsoup-3.0: `brew install libsoup`
4. Add to PATH: `export PKG_CONFIG_PATH="/Library/Frameworks/GStreamer.framework/Versions/Current/lib/pkgconfig${PKG_CONFIG_PATH:+:$PKG_CONFIG_PATH}"`

## Building

```bash
mkdir build
cd build
cmake ..
make
```

## Usage 

### Command Line Options

```bash
sendRecvAnt [OPTIONS] - GStreamer AntMedia WebRTC Publish and Play

Options:
  -h, --help          Show help options
  -s, --ip            IP address of AntMedia server
  -p, --port          AntMedia server port (default: 5080)
  -f, --filename      Specify file path to stream (MP4 files supported)
  -m, --mode          Mode: publish, play, or p2p (default: publish)
  -a, --appname       Application name (default: Trial)
  -t, --token         Authentication token for publishing
  --wss               Force WSS (secure) connection
  -i, --streamids     Stream IDs (can specify multiple: -i stream1 -i stream2)
```

### Connection Types

#### WS (WebSocket) - Default
- **Port**: 5080 (default)
- **URL**: `ws://server:5080/Trial/websocket`
- **Use case**: Local development, internal networks

#### WSS (WebSocket Secure) - Recommended
- **Port**: 5443 (auto-detected) or use `--wss` flag
- **URL**: `wss://server:5443/Trial/websocket`
- **Use case**: Production, public networks, SSL-enabled AMS

## Examples

### Playing a Stream from AntMedia Server

```bash
# Play stream "trial1" from AMS (auto-detects WSS on port 5443)
./sendRecvAnt --ip ams-21007.antmedia.cloud --port 5443 --wss --mode play -i trial1

# Play stream "stream1" from AMS (WS on port 5080)
./sendRecvAnt --ip your-ams-server.com --port 5080 --mode play -i stream1

# Play multiple streams simultaneously
./sendRecvAnt --ip your-ams-server.com --port 5443 --wss --mode play -i stream1 -i stream2 -i stream3
```

### Publishing a Stream to AntMedia Server

```bash
# Publish test video/audio to AMS
./sendRecvAnt --ip your-ams-server.com --port 5443 --wss --mode publish -i mystream

# Publish from a video file
./sendRecvAnt --ip your-ams-server.com --port 5443 --wss --mode publish -i filestream -f /path/to/video.mp4

# Publish with authentication token
./sendRecvAnt --ip your-ams-server.com --port 5443 --wss --mode publish -i mystream -t your-token
```

### Peer-to-Peer Mode

```bash
# First peer
./sendRecvAnt --ip your-ams-server.com --port 5443 --wss --mode p2p -i p2pstream

# Second peer (same stream ID)
./sendRecvAnt --ip your-ams-server.com --port 5443 --wss --mode p2p -i p2pstream
```

### Using Different Applications

```bash
# Connect to "WebRTCAppEE" application instead of "Trial"
./sendRecvAnt --ip your-ams-server.com --port 5443 --wss --appname WebRTCAppEE --mode play -i stream1
```

### Testing with Echo Servers

```bash
# Test WS connection (basic functionality)
timeout 10 ./sendRecvAnt --ip echo.websocket.org --port 80 --mode publish -i test123

# Test WSS connection (secure functionality)  
timeout 10 ./sendRecvAnt --ip echo.websocket.org --port 443 --wss --mode publish -i test123

# Test with timeout for debugging
timeout 30 ./sendRecvAnt --ip ams-21007.antmedia.cloud --port 5443 --wss --mode play -i trial1
```

## Test Results

### Verified Working Features
- ✅ **WSS connections** to AntMedia Server (ams-21007.antmedia.cloud:5443)
- ✅ **VP8 video and OPUS audio** streaming
- ✅ **WebRTC signaling** with ICE candidates
- ✅ **Long-duration streaming** (30+ seconds sustained)
- ✅ **Multiple stream support**
- ✅ **Echo server testing** (ws:// and wss://)

### Expected Output
```
start ams-21007.antmedia.cloud  5443 websocket connected
received text message: {"streamId":"trial1","definition":"play_started","command":"notification"}
--------------------------audio stream recived ----------------------------------
Successfully linked audioconvert stream
--------------------------video stream recived ----------------------------------
Successfully linked videoconvert stream
received text message: {"videoBitrate":1500000,"streamId":"trial1","definition":"bitrateMeasurement","command":"notification","audioBitrate":96000}
```

### Technical Improvements

#### Fixed Issues
- ✅ **VP8 decoder pipeline**: Removed non-existent `vp8parse` element
- ✅ **Codec mismatch resolved**: Changed from H.264 to VP8 for video streams
- ✅ **Error handling**: Added comprehensive null checks for GStreamer elements
- ✅ **Connection reliability**: Improved with libsoup-3.0 migration
- ✅ **Build warnings**: Fixed implicit declaration warnings

#### Performance Enhancements
- ✅ **Native TLS integration**: Better SSL/WSS support
- ✅ **Robust pipeline**: Proper error checking for element creation and linking
- ✅ **Memory management**: Improved resource cleanup

## WebRTC Protocol Details

The application implements full WebRTC signaling:

1. **WebSocket Connection**: Establishes connection to AMS
2. **SDP Exchange**: Offers and answers for media negotiation
3. **ICE Candidates**: Network traversal for NAT/firewall handling
4. **Media Streaming**: Real-time audio/video using VP8/OPUS codecs

### Supported Codecs
- **Video**: VP8 (WebRTC standard)
- **Audio**: OPUS (WebRTC standard)
- **Container**: MP4 (for file input)

## Troubleshooting

### Connection Issues
- **WSS not working**: Ensure `glib-networking` is installed
- **Port 5443**: Automatically uses WSS, or use `--wss` flag
- **Certificate errors**: Check AMS SSL configuration

### Build Issues
- **libsoup-3.0 not found**: Install `libsoup-3.0-dev`
- **GStreamer plugins missing**: Install additional GStreamer packages

### Runtime Issues
- **No video/audio**: Check GStreamer sink configuration
- **Stream not found**: Verify stream ID exists on AMS
- **Authentication failed**: Check token validity
- **VP8 decoder errors**: Ensure `gstreamer1.0-plugins-good` is installed
- **Short stream duration**: Fixed with proper VP8 pipeline (removed `vp8parse`)
- **Element creation failures**: Check for missing GStreamer plugins
- **Pipeline linking errors**: Verify codec compatibility (VP8/OPUS)

## Migration from Previous Versions

If you're upgrading from a version using `librws`:

1. **Dependencies**: Install `libsoup-3.0-dev` and `glib-networking`
2. **Build**: Rebuild with new CMake configuration
3. **WSS Support**: Use `--wss` flag or port 5443 for secure connections
4. **App Name**: Default app changed from "WebRTCAppEE" to "Trial"

## Contributing

This project welcomes contributions! Please ensure:

1. Code follows existing style
2. New features include tests
3. Documentation is updated
4. Migration notes are included for breaking changes

## License

[Add your license information here]

## Acknowledgments

- AntMedia Server team for WebRTC implementation
- GStreamer community for multimedia framework
- GNOME project for libsoup-3.0
