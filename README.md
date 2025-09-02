# Sveglia Evoluta for Home Assistant

An advanced smart alarm system that combines progressive light and Spotify music control with sophisticated snooze functionality, state memorization, and automatic restoration.

## Overview

This package provides a complete intelligent wake-up system that gradually increases light brightness and music volume over a configurable duration. It includes advanced snooze capabilities, device state memorization, and automatic restoration of pre-alarm conditions.

## Prerequisites

### Required Software
- **Home Assistant** with packages support enabled
- **SpotifyPlus Integration** (not the standard Spotify integration)
- **Compatible Devices**: Smart bulbs with brightness control and Spotify-compatible media players

### Configuration Requirements
Add to your `configuration.yaml`:
```yaml
homeassistant:
  packages: !include_dir_named packages
```

### Supported Hardware
- Smart switches/relays for light power control
- Dimmable smart bulbs or lights
- Spotify Connect compatible speakers/devices

## Package Components

### Input Helpers (38 entities)
- **5 Input Booleans**: Control triggers and behavior configurations
- **12 Input Numbers**: Timing parameters, volume/brightness settings, and counters
- **1 Input Select**: Alarm type selection (Light Only/Music Only/Both)
- **8 Input Text**: Device entity and custom script configuration
- **3 Timers**: Main alarm, progress tracking, and snooze management
- **9 Sensors/Binary Sensors**: Status monitoring and configuration validation

### Automations (4)
- **Main Alarm**: Complete alarm cycle management
- **Preparation**: Device testing and pre-alarm state memorization
- **Trigger Reset**: Automatic cleanup of temporary states
- **Notifications**: Visual feedback for state changes

### Scripts (3)
- **Complete Reset**: Full system restoration
- **Automatic Cleanup**: Soft maintenance on restart
- **Customizable Scripts**: User-defined actions for each alarm phase

## Core Features

### Smart Preparation
- Automatic device testing (5-second duration)
- Memorization of original volume and brightness levels
- Configuration validation before use
- Device readiness verification

### Progressive Control
- Gradual volume increase (configurable 1%-100%)
- Progressive brightness enhancement (configurable 1%-100%)
- Customizable number of updates and intervals
- Smooth transitions to prevent jarring wake-ups

### Advanced Snooze System
- Configurable maximum snooze count (1-100)
- Fixed or multiplicative progressive duration
- Selective light/music control during snooze periods
- Intelligent snooze availability tracking

### Intelligent Restoration
- Automatic restoration of pre-alarm values at cycle end
- Separate device control (maintain on/turn off)
- Smooth transitions to avoid sensory shock
- Configurable end-of-alarm behavior

### Real-time Monitoring
- Live alarm progress percentage
- Visual countdown timers
- Real-time device status
- Continuous configuration validation

## Operating Modes

### Light Only Mode
Exclusive lighting control with progressive brightness increase from initial to maximum configured levels.

### Music Only Mode
Spotify volume management with automatic device selection and progressive volume ramping.

### Light and Music Mode
Synchronized coordination of both lighting and audio systems with parallel progression curves.

## Device Configuration

### Required Entity Inputs
- `input_text.sveglia_entita_switch`: Power switch/relay entity
- `input_text.sveglia_entita_luce`: Smart bulb/light entity
- `input_text.sveglia_entita_media_player`: SpotifyPlus entity
- `input_text.sveglia_spotify_device_name`: Spotify Connect device name

### Critical Parameters
- **Spotify URI**: Format `spotify:track:ID` or `spotify:playlist:ID`
- **Total Duration**: Minutes + seconds configuration
- **Volume Range**: Initial (1-50%) to maximum (50-100%)
- **Brightness Range**: Initial (1-30%) to maximum (50-100%)

## Installation

1. Save the package file as `packages/sveglia_evoluta.yaml`
2. Ensure packages are enabled in `configuration.yaml`
3. Restart Home Assistant
4. Configure device entities in the Lovelace card
5. Set desired alarm parameters
6. Test with the preparation function

## Usage Workflow

### Initial Setup
1. Configure device entities in the control card
2. Set timing, volume, and brightness parameters
3. Choose alarm type (Light/Music/Both)
4. Configure snooze preferences

### Operation Cycle
1. **Preparation**: Activate `input_boolean.sveglia_attivazione`
   - Tests devices for 5 seconds
   - Memorizes current volume/brightness levels
   - Validates configuration

2. **Activation**: Trigger `input_boolean.sveglia_trigger_inizio`
   - Only available after successful preparation
   - Starts progressive alarm cycle

3. **Management**: Use snooze or stop as needed
   - Snooze maintains or pauses devices based on configuration
   - Stop immediately ends alarm cycle

4. **Restoration**: Automatic at cycle completion
   - Returns devices to pre-alarm states if configured for shutdown
   - Smooth transitions prevent abrupt changes

## Safety and Reliability Features

### Error Handling
- `continue_on_error: true` for critical actions
- Configurable timeouts for network operations
- Parameter validation before execution
- Graceful fallback on device unavailability

### Data Persistence
- Timers survive Home Assistant restarts
- Configuration states maintained between sessions
- Automatic cleanup of temporary states on reboot
- Restore functionality for interrupted cycles

### Security Controls
- Parameter range validation
- Device availability verification
- Fallback to safe default values
- Protection against infinite loops

## Customization Options

### Custom Scripts
- Begin alarm script execution
- Natural end script execution
- Manual interruption script execution
- User-defined notification systems

### Behavior Configuration
- Maintain lights/music during snooze
- Keep devices on at manual alarm end
- Multiplicative snooze duration increase
- Custom progression curves

### Monitoring and Logging
- Real-time status sensors
- Progress tracking
- Error state reporting
- Historical cycle data

## Troubleshooting

### Common Issues
- **SpotifyPlus not working**: Verify integration installation and device name accuracy
- **Lights not responding**: Check entity configuration and device availability
- **Configuration invalid**: Review parameter ranges and required fields
- **Timer not starting**: Ensure preparation step completed successfully

### Validation Checks
The system includes comprehensive validation that checks:
- Duration parameters > 0
- Volume ranges within acceptable limits
- Brightness values in valid ranges
- Required entities are configured
- Alarm activation completed before start

## Technical Notes

### Performance Considerations
- Update intervals calculated based on total duration and number of updates
- Minimal resource usage during idle periods
- Efficient state management for large snooze counts
- Optimized Spotify API calls

### Integration Dependencies
- Requires SpotifyPlus (not standard Spotify integration)
- Compatible with most smart light platforms
- Timer functionality independent of external services
- Persistent storage for critical state data

This package provides a sophisticated yet reliable wake-up experience with extensive customization options and robust error handling.
