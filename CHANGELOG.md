# Changelog

All notable changes to this project will be documented in this file.

## [2.0.0] - 2024-12-19

### Added
- **WSS Support**: Secure WebSocket connections (wss://) for production use
- **New CLI Option**: `--wss` flag to force secure connections regardless of port
- **Auto-detection**: Port 5443 automatically uses WSS protocol
- **Better Error Handling**: Improved connection error messages and debugging
- **Modern Dependencies**: Migrated to libsoup-3.0 for WebSocket handling

### Changed
- **WebSocket Library**: Migrated from `librws` to `libsoup-3.0`
- **Default App**: Changed default application from "WebRTCAppEE" to "Trial"
- **Build System**: Updated CMakeLists.txt to use libsoup-3.0 instead of librws
- **Dependencies**: Added libsoup-3.0-dev and glib-networking requirements

### Removed
- **librws Dependency**: Completely removed librws library and subdirectory
- **Legacy WebSocket Code**: Removed all librws-specific connection handling

### Fixed
- **TLS Support**: Fixed secure WebSocket connections for SSL-enabled AMS
- **Connection Reliability**: Improved connection stability and error recovery
- **Build Warnings**: Fixed compilation warnings and improved code quality
- **VP8 Pipeline**: Fixed video decoder pipeline by removing non-existent `vp8parse` element
- **Codec Mismatch**: Resolved H.264 vs VP8 codec incompatibility
- **Element Creation**: Added comprehensive null checks for GStreamer elements
- **Stream Duration**: Fixed short-lived streams with proper VP8 handling

### Testing
- **Verified**: WSS connections to AntMedia Server (ams-21007.antmedia.cloud:5443)
- **Verified**: VP8 video and OPUS audio streaming for 30+ seconds
- **Verified**: WebRTC signaling with proper ICE candidate exchange
- **Verified**: Echo server connectivity (ws:// and wss://)

### Migration Notes
- **Breaking Change**: Requires libsoup-3.0-dev and glib-networking
- **Default App**: Applications using "WebRTCAppEE" need to specify `--appname WebRTCAppEE`
- **WSS Usage**: Use `--wss` flag or port 5443 for secure connections
- **Rebuild Required**: Full rebuild needed after dependency installation

## [1.0.0] - Previous Version

### Features
- Basic WebRTC support with librws WebSocket library
- Publish, Play, and P2P modes
- File streaming support
- Test video/audio sources
- Multiple stream support

### Limitations
- WS (WebSocket) only, no WSS support
- Limited TLS integration
- Deprecated librws dependency 