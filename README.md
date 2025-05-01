# HA-to-PVoutput
![image](https://github.com/user-attachments/assets/d9447d8c-bd72-45d8-b931-5f638e516f5c)
PVOutput is an online platform for monitoring and sharing solar power generation data. It allows users to upload their solar system data, track performance, and compare it with others worldwide. The platform supports various integrations with solar inverters, energy management systems, and third-party tools for data automation and visualization.

**To be able to upload from Home Assistant to PVoutput just put this code into your configuration.yaml and restart.**

1. Open an account at https://pvoutput.org
2. Go to settings and write down your API Key and Secret
3. Go to your configuration.yaml and add the code provided
3. Make sure you add your API KEY and SECRET
4. Edit the sensor names in the bottom line under "payload" to your sensor names
5. Restart Home Assistant
6. Create an automation to upload frequently

It will upload your data for:

- Production
- PV Power
- Outdoor Temperature
- Grid Voltage


Add this code to your configuration.yaml
```
#############################################
# PVoutput | Upload to https://pvoutput.org #
#############################################
rest_command:
  pvoutput_upload:
    url: https://pvoutput.org/service/r2/addstatus.jsp
    method: post
    content_type: "application/x-www-form-urlencoded"
    headers:
       X-Pvoutput-Apikey: KEY
       X-Pvoutput-SystemId: SECRET
    payload: "d={{now().strftime('%Y%m%d')}}&t={{now().strftime('%H:%M')}}&v1={{(states('sensor.inverter_today_production')|float*1000)}}&v5={{(states('sensor.outdoor_temperature'))|round(0)}}&v6={{(states('sensor.inverter_grid_l1_voltage'))|round(0)}}&v2={{(states('sensor.inverter_pv_power'))|round(0)}}"
``` 

Automation:
- This YAML code uploads your data every 10 minutes
 - If you wish to upload a lower interval edit to max. 5 minutes
```
alias: PVOutput Uploader
description: Uploads values to PVOutput
triggers:
  - minutes: /10
    trigger: time_pattern
conditions: []
actions:
  - data: {}
    action: rest_command.pvoutput_upload
mode: single
```

Hope this helps someone.
