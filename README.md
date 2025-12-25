# VEX TM Manager Tools

A comprehensive automation and control system for VEX Robotics tournaments, providing real-time integration with Tournament Manager, automated AV control, and live event management.

## Overview

VEX TM Manager Tools is a Flask-based web application that automates and orchestrates tournament production elements by integrating with the VEX Tournament Manager API. The system provides real-time control of:

- **Video switching** (ATEM video switchers)
- **Lighting control** (ZerOS lighting systems)
- **Audio management** (Spotify integration)
- **Match scheduling and notifications**
- **Field state management**
- **Live room monitoring with team notifications**

## Features

- **Real-time Event Processing**: WebSocket integration with VEX TM API for live event streaming
- **Multi-field Management**: Track and control state for multiple competition fields simultaneously
- **Automated AV Control**: Trigger coordinated lighting, video, and audio cues based on match events
- **Web-based Control Interface**: 
  - Administrator dashboard for system configuration
  - Field control and monitoring
  - Manual override controls for all AV systems
  - User management and authentication
- **Public Room Pages**: 
  - No-login access for teams and spectators
  - Live YouTube stream embeds
  - Automated match notifications for assigned teams
- **Match Scheduling**: Automatic fetching and processing of tournament schedules with lead-time notifications
- **Flexible Configuration**: JSON-based configuration for easy customization of mappings and presets
- **Pause Controls**: Granular pause/resume for video, audio, and lighting automation

## Architecture

The application runs five concurrent threads:

1. **Flask Web Server** - User interface and API endpoints
2. **Event Processor** - Consumes and processes events from the queue
3. **Schedule Fetcher** - Retrieves and updates match schedules
4. **Match Scheduler** - Enqueues time-based match events
5. **WebSocket Connector** - Subscribes to VEX TM API and forwards events

All components communicate via a central event queue and share state through JSON files in the `storage/` directory.

For detailed architecture information, see [ARCHITECTURE.md](docs/ARCHITECTURE.md).

## Requirements

- Python 3.12+
- Docker and Docker Compose (for containerized deployment)
- VEX Tournament Manager API credentials
- (Optional) ATEM video switcher
- (Optional) ZerOS lighting console
- (Optional) Spotify Premium account

## Installation

### Docker Deployment (Recommended)

1. Clone the repository:
```bash
git clone https://github.com/vmd1/vex-tm-tools.git
cd vex-tm-tools
```

2. Configure your environment:
   - Edit `storage/config.json` with your VEX TM API credentials and device settings
   - See [Configuration](#configuration) section below

3. Build and run with Docker Compose:
```bash
docker compose up -d
```

The application will be available at `http://localhost:5000`

### Local Development

1. Install Python dependencies:
```bash
pip install -r requirements.txt
```

2. Configure the application (see [Configuration](#configuration))

3. Run the application:
```bash
python main.py
```

## Configuration

The system uses JSON files in the `storage/` directory for configuration:

- **`config.json`**: Main configuration file
  - VEX TM API credentials and endpoints
  - Device IP addresses and settings (ATEM, ZerOS, Spotify)
  - Field-to-camera mappings
  - Room definitions with YouTube stream URLs
  - Pause state for automation categories

- **`actions.json`**: Event-to-action mappings
  - Defines what AV actions trigger on specific events or field state changes
  - Supports lighting presets, camera switches, and audio cues

- **`presets.json`**: Named presets for quick access to common configurations

### VEX TM API Setup

Add your credentials to `storage/config.json`:
```json
{
  "vex_tm_api": {
    "client_id": "your_client_id",
    "client_secret": "your_client_secret",
    "api_key": "your_api_key",
    "base_url": "http://your-tm-server:8080"
  }
}
```

## Usage

### Web Interface

Navigate to `http://localhost:5000` (or your configured host) to access:

- **Dashboard** (`/`): Overview of field states and system status
- **Controls** (`/controls`): Manual control of all AV systems
- **Configuration** (`/config`): Edit system configuration
- **Room Management** (`/rooms`): Configure rooms, streams, and team assignments
- **User Management** (`/users`): Manage user accounts (admin only)
- **Logs** (`/logs`): View system logs

### Public Room Pages

Teams and spectators can access room pages without login:
- Navigate to `/room`
- Enter room number
- View live stream and receive match notifications

### Manual Controls

The control interface allows operators to:
- Trigger lighting presets
- Switch cameras on ATEM
- Control Spotify playback
- Send custom notifications to rooms
- Manually advance field states

### Pause/Resume Automation

Use the Pause page (`/pause`) to temporarily disable automation for:
- Video switching
- Lighting changes  
- Audio playback

This is useful during setup, testing, or when manual control is needed.

## Tools

The `tools/` directory contains utility scripts:

- **`add_user.py`**: Create new user accounts
- **`get_field_sets.py`**: Fetch field configuration from TM API
- **`get_spotify_devices.py`**: List available Spotify devices
- **`simulate_event.py`**: Send test events for development/testing

Run tools from the project root:
```bash
python -m tools.add_user
```

## Development

### Project Structure

```
├── main.py              # Application entry point
├── server.py            # Flask server and routes
├── userManager.py       # User authentication
├── models/              # Data models (Event, FieldState, Config, etc.)
├── modules/             # Device controllers and processors
│   ├── event_processor.py
│   ├── match_scheduler.py
│   ├── tm_manager/      # VEX TM API integration
│   ├── audio/spotify/   # Spotify controller
│   ├── video/atem/      # ATEM controller
│   └── vfx/zeros/       # ZerOS lighting controller
├── templates/           # HTML templates
├── static/              # CSS and client-side assets
├── storage/             # Runtime configuration and state
└── docs/                # Documentation
```

### Adding New Device Controllers

1. Create a new module under the appropriate category in `modules/`
2. Implement the controller interface
3. Add action mappings to `storage/actions.json`
4. Update `storage/config.json` with device settings

### Running Tests

```bash
# Run event simulation
python -m tools.simulate_event
```

## Deployment

The project includes Docker support for production deployment:

- `Dockerfile`: Application container definition
- `compose.yml`: Docker Compose configuration

For deployment with Cloudflare Tunnel (recommended for secure remote access), see [docs/RUN_PLAN.md](docs/RUN_PLAN.md).

## API Documentation

For details on the VEX Tournament Manager API, see [docs/VEX_API_DOCS.md](docs/VEX_API_DOCS.md).

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## License

Copyright © 2025. All rights reserved.

## Support

For issues, questions, or feature requests, please open an issue on GitHub.

## Acknowledgments

Built for VEX Robotics competitions to enhance the production quality and automation of tournament events.
