# Frigate Telegram Notification Blueprint

Home Assistant Blueprint for sending advanced Telegram notifications from Frigate events.

## Features

- 📸 **Snapshot with bounding boxes** - Shows detection boxes around objects
- 🎥 **Event video** - Complete recording quoted to the snapshot
- 👤 🚗 **Custom emojis** - Person (👤) and Car (🚗)
- 🕐 **Timestamps** - Time display on both messages
- 🌓 **Smart notification modes** - 6 operating modes including day/night filters
- 🎯 **Advanced filters** - Zones, objects, presence

## Notification Modes

Choose when to receive notifications:

- **Disabled** - No notifications
- **Away only** - Only when you're not home
- **Night only** - Only after sunset (ignores presence)
- **Day only** - Only during daytime (ignores presence)
- **Away + Night** - When away during day OR always at night
- **Always active** - 24/7 notifications

## Installation

### 1. Import Blueprint

**Settings** → **Automations & Scenes** → **Blueprints** → **Import Blueprint**

Use this URL:
```
https://github.com/kayaman78/homeassistant-frigate-telegram-enhanced/blob/main/frigate_telegram_enhanced.yaml
```

### 2. Create Automation

**Settings** → **Automations & Scenes** → **Create Automation** → **Use Blueprint**

Required fields:
- **Frigate Camera**: camera name from Frigate config (e.g., `front_door`)
- **Target**: your Telegram `chat_id`
- **Notification Mode**: select your preferred mode

## Configuration

### Required parameters:
- **Frigate Camera**: camera name from Frigate config
- **Target**: Telegram chat_id
- **Notification Mode**: when to send notifications

### Optional parameters:
- **Base URL**: external Home Assistant URL (for remote access)
- **Zone Filter**: filter by specific zones
- **Trigger Objects**: filter by objects (person, car, etc.)
- **Presence Filter**: entity to check home/away status

## Example Notifications

```
👤 Person detected
📹 Front Door
🕐 14:30:15
[photo with bounding box]

  ↳ 📹 Front Door - Event Recording
    🕐 14:30:28
    [video]
```

## Requirements

- Home Assistant 2024+
- Frigate 0.17+ integrated in Home Assistant
- Configured Telegram Bot
- Active Frigate recording for bounding boxes

## Notes

**Night/Day detection** uses sun elevation (below horizon = night). This is automatic based on your Home Assistant location settings.

**Presence filter** requires a presence entity (like device_tracker or person entity) configured in the optional parameters.

## Version

Blueprint compatible with Home Assistant 2024+ and Frigate 0.16+