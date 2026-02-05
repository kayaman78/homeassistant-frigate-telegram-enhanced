# Frigate Telegram Notification Blueprint

> **Version 1.0** - Advanced Telegram notifications for Frigate with smart modes, and post-event actions

Home Assistant Blueprint for sending feature-rich Telegram notifications from Frigate events.

## ✨ Features

### 📱 Rich Notifications
- **Snapshot with bounding boxes** - Detection boxes around objects
- **Event video** - Complete recording
- **Custom emojis** - 👤 Person, 🚗 Car, 🚨 Motion
- **Timestamps** - Time display on both messages

### 🌓 Smart Notification Modes (7 modes)
- **Disabled** - No notifications
- **Away only** - Only when nobody is home (multi-person support)
- **Night only** - Only after sunset
- **Day only** - Only during daytime  
- **Away + Night** - Smart combo for home security
- **After hours** - Perfect for offices (outside business hours + weekends + holidays)
- **Always active** - 24/7 notifications

### 🎯 Advanced Filters
- **Multi-person presence** - Supports multiple family members
- **Zone filtering** - Specific zones only
- **Object filtering** - Person, car, or custom objects
- **Workday integration** - Automatic weekend/holiday detection

### 🔧 Automation & Actions
- **Post-event actions** - Trigger lights, alarms, or any Home Assistant action
- **Customizable work hours** - Define your business schedule
- **Send only screenshot** - Toggle to send only screenshot

## 📥 Installation

### Prerequisites

**Required:**
- Home Assistant 2024+
- Frigate 0.15+ integrated in Home Assistant
- Configured Telegram Bot

**Optional (for specific features):**
- Workday integration (for "After hours" mode)
- Person entities (for "Away only" mode)

### Setup Workday Sensor (Optional)

Required only if using "After hours" mode for office cameras.

Add to your `configuration.yaml`:
```yaml
binary_sensor:
  - platform: workday
    country: IT  # Change to your country code (US, GB, DE, FR, etc.)
```

Restart Home Assistant. The sensor `binary_sensor.workday_sensor` will be created automatically.

### Import Blueprint

**Settings** → **Automations & Scenes** → **Blueprints** → **Import Blueprint**

Use this URL:
```
https://github.com/kayaman78/homeassistant-frigate-telegram-enhanced/blob/main/frigate_telegram_enhanced.yaml
```

Or click: [![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://github.com/kayaman78/homeassistant-frigate-telegram-enhanced/blob/main/frigate_telegram_enhanced.yaml)

### Create Automation

**Settings** → **Automations & Scenes** → **Create Automation** → **Use Blueprint**

**Required fields:**
- **Frigate Camera**: camera name from Frigate config (e.g., `front_door`)
- **Target**: your Telegram `chat_id`
- **Notification Mode**: select your preferred mode
- **Base URL**: your local Home Assistant address (e.g., http://192.168.1.10:8123) remembre to remove final / on address

## ⚙️ Configuration

### Required Parameters
| Parameter | Description |
|-----------|-------------|
| **Frigate Camera** | Camera name from your Frigate configuration |
| **Target** | Telegram chat_id (numeric) |
| **Notification Mode** | When to send notifications |

### Optional Parameters
| Parameter | Description | Used by mode |
|-----------|-------------|--------------|
| **Zone Filter** | Enable zone-based filtering | All modes |
| **Trigger Zones** | List of zones to monitor | When zone filter enabled |
| **Trigger Objects** | List of objects (person, car, etc.) | All modes |
| **Presence Filter** | Person/device_tracker entities (multi-select) | Away only, Away + Night |
| **Work Start Time** | Business hours start (HH:MM) | After hours |
| **Work End Time** | Business hours end (HH:MM) | After hours |
| **Workday Sensor** | Workday binary sensor | After hours |
| **Post-Event Action** | Actions to run after detection | All modes |

## 📋 Notification Modes Explained

### 🏠 Home Modes

**Away only**
- Sends notifications only when ALL selected people are away from home
- Perfect for: Family home security
- Requires: Presence entities (person or device_tracker)

**Away + Night**  
- During day: notifications only when away
- During night: always send notifications (even if someone is home)
- Perfect for: Enhanced night security at home

**Night only / Day only**
- Based on sun elevation (automatic)
- Ignores presence completely
- Perfect for: Perimeter monitoring, specific time-based alerts

### 🏢 Office Mode

**After hours**
- **Sends notifications:**
  - ✅ Outside work hours (default: before 7:00 and after 19:00)
  - ✅ All day on weekends
  - ✅ All day on public holidays
- **No notifications:**
  - ❌ During work hours on workdays (default: 7:00-19:00)
- Perfect for: Office security when building should be empty
- Requires: Workday sensor + work hours configuration

## 📸 Example Notifications

### Message 1 (Snapshot)
```
👤 Person detected
📹 Front Door
🕐 14:30:15
[photo with bounding box]
[🏠 Home Assistant] [📹 Frigate]  ← buttons (if configured)
```

### Message 2 (Video)
```
  ↳ 📹 Front Door - Event Recording
    🕐 14:30:28
    [video clip]
```

## 🔌 Post-Event Actions

Trigger any Home Assistant action when an object is detected.

### Example: Turn on lights when person detected
```yaml
post_event_action:
  - service: light.turn_on
    target:
      entity_id: light.entrance
    data:
      brightness: 50
```

### Example: Sound alarm
```yaml
post_event_action:
  - service: switch.turn_on
    target:
      entity_id: switch.siren
  - delay: 5
  - service: switch.turn_off
    target:
      entity_id: switch.siren
```

### Example: Send additional notification
```yaml
post_event_action:
  - service: notify.mobile_app_phone
    data:
      title: "Security Alert"
      message: "Motion detected at {{ camera_name }}"
```

## 🔍 Tips & Tricks

### Multi-person presence
Select multiple person entities in "Presence Filter" - notifications are sent only when **ALL** are away.

### Workday sensor customization
```yaml
binary_sensor:
  - platform: workday
    country: IT
    workdays: [mon, tue, wed, thu, fri]
    excludes: [sat, sun, holiday]
    add_holidays:
      - '2024-12-26'  # Custom holiday
```

### Object detection emoji
Currently supports:
- `person` → 👤
- `car` → 🚗  
- Others → 🚨

Want more? Edit the `emoji` template in the blueprint!

## 📝 Notes

**Night/Day detection** uses sun elevation (below horizon = night). Automatic based on Home Assistant location.

**Presence filter** accepts multiple entities. Notifications sent only when ALL are away.

**After hours mode** requires workday integration. Sensor automatically handles weekends and holidays.

**Work hours** use HH:MM format (e.g., "07:00" not "7:00").

**Bounding boxes** require active Frigate recording to work properly.

## 🐛 Troubleshooting

**No bounding boxes on snapshots?**
- Check Frigate recording is enabled
- Verify snapshot URL includes `?bbox=1` parameter

**After hours not working?**
- Verify workday sensor exists: `binary_sensor.workday_sensor`
- Check sensor state in Developer Tools → States

**Presence not working?**
- Ensure person entities are properly configured
- Check entity states in Developer Tools → States

## 📜 License

MIT License - Free to use, modify, and distribute

## 🙏 Credits

Enhanced and maintained by the community. Originally based on NdR91's Frigate notification blueprint.

## 📌 Version

**v1.0.0** - January 2026

**Features:** 26+ features including smart modes, multi-person presence, office hours, post-event actions, and interactive buttons.
