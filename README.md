# ☀️ Home Assistant → PVOutput

<img width="2679" height="475" alt="PVOUTPUT" src="https://github.com/user-attachments/assets/79d81fd3-2084-4785-a097-d8171d4a0488" />


---
This guide explains how to upload your Home Assistant solar data to PVOutput.

PVOutput is an online platform for monitoring and sharing solar power generation data. It allows users to upload their solar system data, track performance, and compare it with other systems worldwide.

---

## 💡 Setup Guide

1. Create an account at https://pvoutput.org
2. Go to **Settings** and copy your:
   - API Key
   - System ID
3. Open your `configuration.yaml`
4. Add the REST command below
5. Replace:
   - `YOUR_API_KEY`
   - `YOUR_SYSTEM_ID`
6. Edit the sensor names inside `payload` to match your own sensors
7. Restart Home Assistant
8. Create the automation to upload data periodically

---

## ⬆️ Uploaded values

This configuration uploads:

- ☀️ Daily production
- ⚡ Current PV power
- 🌡️ Outdoor temperature
- 🔌 Grid voltage

---

## 🔧 Configuration

Add this to your `configuration.yaml`:

```yaml
#############################################
# PVOutput Upload
#############################################

rest_command:
  pvoutput_upload:
    url: "https://pvoutput.org/service/r2/addstatus.jsp"
    method: POST
    content_type: "application/x-www-form-urlencoded"
    headers:
      X-Pvoutput-Apikey: YOUR_API_KEY
      X-Pvoutput-SystemId: YOUR_SYSTEM_ID
    payload: >
      d={{ now().strftime('%Y%m%d') }}&
      t={{ now().strftime('%H:%M') }}&
      v1={{ (states('sensor.inverter_today_production') | float * 1000) | round(0) }}&
      v2={{ states('sensor.inverter_pv_power') | float | round(0) }}&
      v5={{ states('sensor.outdoor_temperature') | float | round(0) }}&
      v6={{ states('sensor.inverter_grid_l1_voltage') | float | round(0) }}
```

### Sensor Mapping

| PVOutput Field | Example Sensor |
|---------------|---------------|
| `v1` Daily Generation (Wh) | `sensor.inverter_today_production` |
| `v2` Power Generation (W) | `sensor.inverter_pv_power` |
| `v5` Temperature (°C) | `sensor.outdoor_temperature` |
| `v6` Voltage (V) | `sensor.inverter_grid_l1_voltage` |

---

## ⚙️ Automation

This Home Assistant Automation uploads data every **10 minutes**.

PVOutput supports a minimum upload interval of **5 minutes**, so do not set it lower.

```yaml
alias: PVOutput Uploader
description: Upload values to PVOutput

trigger:
  - platform: time_pattern
    minutes: "/10"

condition: []

action:
  - service: rest_command.pvoutput_upload

mode: single
```

---

## ☀️ PVOutput → Home Assistant

If you want to import PVOutput data back into Home Assistant and create sensors, install the official integration:

[![Add Integration](https://img.shields.io/badge/Add%20Integration-Home%20Assistant-41BDF0?style=for-the-badge&logo=home-assistant)](https://my.home-assistant.io/redirect/config_flow_start/?domain=pvoutput)

---

## ✅ Result

Your Home Assistant solar data will now be uploaded automatically to PVOutput.

If this helped you, feel free to ⭐ the repository.
