# HAFireIndexRating

Home Assistant configuration to calculate and graph the **Fire Danger Index** based on local weather data from an Ecowitt weather station.

## Overview

This package implements the **McArthur Forest Fire Danger Index (FFDI)**, the standard fire danger rating system used by the Country Fire Authority (CFA) and fire services across Australia.

The Fire Danger Index combines:
- 🌡️ **Temperature** - Higher temperatures dry out vegetation
- 💧 **Humidity** - Lower humidity increases fire risk  
- 💨 **Wind Speed** - Wind fans flames and spreads fire
- 🏜️ **Drought Factor** - User-configurable measure of vegetation dryness (1-10)

### Fire Danger Rating Levels

| FDI Range | Rating | Color |
|-----------|--------|-------|
| 0-11 | Low-Moderate | 🟢 Green |
| 12-24 | Moderate | 🟢 Green |
| 25-31 | High | 🟡 Yellow |
| 32-49 | Very High | 🟠 Orange |
| 50-74 | Severe | 🟠 Orange |
| 75-99 | Extreme | 🔴 Red |
| 100+ | Catastrophic | ⚫ Black |

## Features

- ✅ **Automatic Calculation** - Fire Danger Index calculated every 15 minutes
- ✅ **Ecowitt Integration** - Works with Ecowitt weather station sensors
- ✅ **Dashboard Graphs** - Visual graphs showing current and historical FDI
- ✅ **3-Day History** - Historical data retained for 3 days for review
- ✅ **Configurable Alerts** - Set threshold for fire danger notifications
- ✅ **Daily Monitoring** - Runs from 1:00 AM to 11:45 PM
- ✅ **Single Package** - All configuration in one file for easy management

## Installation

### Prerequisites

- Home Assistant installed and running
- Ecowitt weather station configured in Home Assistant
- Know your Ecowitt sensor entity IDs (temperature, humidity, wind speed)

### Step 1: Download the Configuration Files

Download the following files to your Home Assistant config directory:

- `fire_danger_index.yaml` - Main configuration (sensors, automations, inputs)
- `fire_danger_dashboard.yaml` - Lovelace dashboard cards

### Step 2: Add Package to Configuration

Add the following to your `configuration.yaml`:

```yaml
homeassistant:
  packages:
    fire_danger_index: !include fire_danger_index.yaml
```

### Step 3: Restart Home Assistant

Restart Home Assistant to load the new configuration:

1. Go to **Settings** → **System** → **Restart**
2. Or use the command line: `ha core restart`

### Step 4: Configure Your Sensors

After restart, configure your Ecowitt sensor entity IDs:

1. Go to **Settings** → **Devices & Services** → **Helpers**
2. Find and edit these helpers:
   - **Temperature Sensor Entity ID** - Set to your Ecowitt temperature sensor (e.g., `sensor.gw1000_outdoor_temperature`)
   - **Humidity Sensor Entity ID** - Set to your Ecowitt humidity sensor (e.g., `sensor.gw1000_humidity`)
   - **Wind Speed Sensor Entity ID** - Set to your Ecowitt wind speed sensor (e.g., `sensor.gw1000_wind_speed`)

**Common Ecowitt Entity Patterns:**
- `sensor.ecowitt_temperature` / `sensor.gw1000_outdoor_temperature`
- `sensor.ecowitt_humidity` / `sensor.gw1000_humidity`
- `sensor.ecowitt_wind_speed` / `sensor.gw1000_wind_speed`

### Step 5: Add Dashboard Cards

#### Option A: Add to Existing Dashboard (Recommended)

1. Go to your dashboard
2. Click **Edit** (pencil icon)
3. Click **Add Card** → **Manual**
4. Copy card configurations from `fire_danger_dashboard.yaml`

#### Option B: Create Dedicated Dashboard

Add to your `configuration.yaml`:

```yaml
lovelace:
  mode: yaml
  dashboards:
    fire-danger:
      mode: yaml
      title: Fire Danger
      icon: mdi:fire-alert
      show_in_sidebar: true
      filename: fire_danger_dashboard.yaml
```

## Configuration

### Adjustable Settings

All settings are accessible from the dashboard:

| Setting | Description | Default |
|---------|-------------|---------|
| Alert Threshold | FDI level that triggers notifications | 50 |
| Drought Factor | Vegetation dryness (1=wet, 10=very dry) | 5 |
| Notifications Enabled | Enable/disable push notifications | Off |

### Drought Factor Guide

| Value | Condition |
|-------|-----------|
| 1-2 | Recent significant rainfall |
| 3-4 | Soil moisture adequate |
| 5-6 | Normal/slightly dry conditions |
| 7-8 | Extended dry period |
| 9-10 | Severe drought conditions |

## Notifications

⚠️ **Note:** Push notifications are **disabled by default** as mentioned in the requirements.

To enable notifications when configured:

1. Set up your preferred notification service in Home Assistant
2. Edit `fire_danger_index.yaml` and uncomment the notification service call
3. Update the service name to match your notification platform
4. Toggle **Enable Notifications** on the dashboard

## How It Works

### Fire Danger Index Calculation

The McArthur FFDI formula:

```
FFDI = 2 × exp(-0.45 + 0.987×ln(DF) - 0.0345×H + 0.0338×T + 0.0234×U)
```

Where:
- **DF** = Drought Factor (1-10)
- **H** = Relative Humidity (%)
- **T** = Temperature (°C)
- **U** = Wind Speed (km/h)

### Automation Schedule

- **Update Frequency:** Every 15 minutes
- **Active Period:** 1:00 AM to 11:45 PM daily
- **Data Retention:** 3 days (automatically purged)

## Dashboard Views

### Main View: Fire Danger Index
- Current FDI gauge display
- Today's FDI graph
- 3-day historical graph
- Current weather conditions
- Settings controls

### History View: Fire History
- Separate graphs for each day
- 3-day statistics (min/max/mean)
- Complete 3-day timeline

## Troubleshooting

### Sensor Shows "Unavailable"

1. Verify your Ecowitt sensor entity IDs are correct
2. Check that your weather station is online
3. Ensure sensor values are within valid ranges:
   - Temperature: -50°C to 60°C
   - Humidity: 0% to 100%
   - Wind Speed: 0+ km/h

### Graph Not Showing Data

1. Ensure recorder is configured (included in package)
2. Wait 15 minutes for first data point
3. Check Home Assistant logs for errors

### Finding Your Ecowitt Entity IDs

1. Go to **Developer Tools** → **States**
2. Filter by "ecowitt" or "gw1000"
3. Note the entity IDs for temperature, humidity, and wind speed

## Files

| File | Description |
|------|-------------|
| `fire_danger_index.yaml` | Main configuration package |
| `fire_danger_dashboard.yaml` | Lovelace dashboard cards |
| `README.md` | This documentation |

## License

This project is open source and available under the MIT License.

## Credits

- Fire Danger Index formula based on the McArthur Mark 5 Forest Fire Danger Index
- Developed for use with the Country Fire Authority (CFA) fire danger rating system
- Designed for integration with Ecowitt weather stations via Home Assistant
