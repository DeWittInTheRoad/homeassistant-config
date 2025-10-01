config/
├─ configuration.yaml        # Main entrypoint (includes everything else)
├─ automations.yaml          # Automations (with IDs, aliases, and descriptions)
├─ scenes.yaml               # Scenes
├─ scripts.yaml              # Scripts
├─ groups.yaml               # Custom groups of entities
├─ notify.yaml               # Notification services (mobile, Pushover, etc.)
├─ sensors.yaml              # Sensors (MQTT, templates, etc.)
├─ template.yaml             # Template entities (binary sensors, sensors)
├─ input_boolean.yaml        # Virtual toggles
├─ input_select.yaml         # Dropdowns (presence states, modes)
├─ integrations/             # Integration-specific configs
│   ├─ recorder.yaml          # Database recorder (MariaDB/MySQL)
│   ├─ tts.yaml               # Text-to-speech (Google, Azure, etc.)
│   ├─ frontend.yaml          # Lovelace / UI tweaks
│   ├─ nest.yaml              # Nest integration
│   ├─ mqtt.yaml              # (optional) External MQTT broker
│   ├─ github.yaml            # (optional) GitHub/HACS token
│   ├─ media.yaml             # (optional) Spotify, Hue, Sonos
│   ├─ cloud.yaml             # (optional) Nabu Casa cloud
└─ secrets.yaml              # Sensitive values (API keys, DB, location, etc.)
