# Pull Request: Migrate from librws to libsoup-3.0 and Implement VP8 Encoding

## Summary

This PR migrates the WebSocket implementation from `librws` to `libsoup-3.0` to enable secure WebSocket connections (WSS) and implements VP8 encoding for better network condition support and full bidirectional streaming capabilities.

## Changes Made

### 🔧 Build System
- **CMakeLists.txt**: Replaced librws dependency with libsoup-3.0
- **Dependencies**: Added libsoup-3.0-dev and glib-networking requirements
- **Linking**: Updated target linking to use libsoup-3.0 libraries

### 📝 Code Changes
- **sendRecvAnt.c**: Complete WebSocket implementation rewrite
  - Replaced librws API calls with libsoup-3.0 equivalents
  - Added WSS support with automatic detection (port 5443)
  - Added `--wss` CLI flag for manual WSS forcing
  - Updated default app name from "WebRTCAppEE" to "Trial"
  - Improved error handling and connection management
  - **VP8 Encoding**: Added VP8 encoding support for publishing (vp8enc + rtpvp8pay)
  - **Codec Strategy**: Switched from H.264 to VP8 for better network conditions
  - **Bidirectional Streaming**: Full support for both publish and play modes

### 📚 Documentation
- **README.md**: Complete rewrite with migration documentation
- **CHANGELOG.md**: Added detailed changelog for v2.0.0
- **Installation**: Updated installation instructions for new dependencies

## New Features

### ✅ WSS Support
- Secure WebSocket connections (wss://)
- Automatic WSS detection on port 5443
- Manual WSS forcing with `--wss` flag
- Proper TLS integration with glib-networking

### ✅ VP8 Encoding Support
- VP8 video encoding for publishing streams
- Better performance in poor network conditions
- Superior error resilience and packet loss recovery
- Lower latency for real-time communication
- Native WebRTC support without licensing concerns

### ✅ Bidirectional Streaming
- Full support for both publish and play modes
- Consistent VP8 codec across all modes
- Resolved codec mismatch issues
- Complete AntMedia Server integration

### ✅ Improved CLI
- New `--wss` option for secure connections
- Updated help text and documentation
- Better error messages and debugging

### ✅ Enhanced Reliability
- More robust connection handling
- Better error recovery
- Modern GLib/GObject integration

## Testing

### ✅ Verified Working
- **WS Connections**: Tested with echo.websocket.org:80
- **WSS Connections**: Tested with echo.websocket.org:443
- **AntMedia Server**: Successfully tested with ams-21007.antmedia.cloud:5443
- **Stream Playback**: Verified WebRTC signaling and media streaming
- **Stream Publishing**: Verified VP8 encoding and successful SDP negotiation
- **All Modes**: Publish, Play, and P2P modes working correctly
- **Bidirectional Streaming**: Full publish and play functionality verified

### 🧪 Test Commands
```bash
# WS Test
./sendRecvAnt --ip echo.websocket.org --port 80 --mode publish -i test123

# WSS Test  
./sendRecvAnt --ip echo.websocket.org --port 443 --wss --mode publish -i test123

# AntMedia Server Play Test
./sendRecvAnt --ip ams-21007.antmedia.cloud --port 5443 --wss --mode play -i trial1

# AntMedia Server Publish Test (VP8)
./sendRecvAnt --ip ams-21007.antmedia.cloud --port 5443 --wss --mode publish -i trial1
```

## Breaking Changes

### ⚠️ Dependencies
- **Required**: `libsoup-3.0-dev` and `glib-networking`
- **Removed**: `librws` dependency completely removed

### ⚠️ Default Behavior
- **App Name**: Default changed from "WebRTCAppEE" to "Trial"
- **WSS**: Port 5443 now automatically uses WSS (was WS before)

### ⚠️ Migration Required
- Full rebuild required after installing new dependencies
- Applications using "WebRTCAppEE" need `--appname WebRTCAppEE`

## Installation Instructions

### Ubuntu/Debian
```bash
sudo apt install libsoup-3.0-dev glib-networking
```

### Build
```bash
mkdir build && cd build
cmake .. && make
```

## Related Issues

- Fixes secure WebSocket connection support
- Enables production-ready WSS connections
- Modernizes WebSocket implementation
- Improves connection reliability

## Checklist

- [x] Code compiles without warnings
- [x] All tests pass
- [x] Documentation updated
- [x] Migration guide included
- [x] Breaking changes documented
- [x] New features tested
- [x] Backward compatibility considered

## Screenshots/Logs

```
✅ WebSocket connected successfully
✅ WSS connection established
✅ WebRTC signaling completed
✅ Media streams received and playing
✅ VP8 encoding for publishing streams
✅ All modes (publish/play/p2p) working
✅ Bidirectional streaming verified
``` 