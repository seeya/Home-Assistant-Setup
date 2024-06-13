# Start

Clone this project and rename it to `homeassistant`. If you don't want to rename it, update the volume paths in `docker-compose.yml`.

```
cd /home/pi
git clone https://github.com/seeya/Home-Assistant-Setup.git
mv Home-Assistant-Setup homeassistant
```

# Example .env

This is the bare minimum `.env` which should exists

```
HA_URL=http://homeassistant:8123
TUNNEL_TOKEN=
TZ=Asia/Singapore
```

# Zigbee2MQTT

On the first run with `sudo docker-compose up`, the following error will appear.

```
zigbee2mqtt    | Zigbee2MQTT:error 2023-12-27 06:57:59: MQTT error: connect ECONNREFUSED 127.0.0.1:1883
```

New configuration files will also be created in `/home/pi/homeassistant/zigbee2mqtt/configuration.yml`.
Edit the config and set the server from `mqtt://localhost` to `mqtt://mosquitto`.

```yaml
homeassistant: true
frontend: true
permit_join: true
available: true
mqtt:
  base_topic: zigbee2mqtt
  server: mqtt://mosquitto
  user: xxxx
  password: xxxx
serial:
  # adapter: ezsp # uncomment this if you're using SONOFF ZBDongle-E
  port: /dev/ttyACM0
...
```

# Cloudflared tunnel

Create a tunnel using Cloudflare Zero Trust and add the following environment variable into your `.env` file.
This will allow you to access your HomeAssistant securely over the internet.
If you've issues accessing through the public domain over Cloudflare, check homeassistant logs. It should show some IP range which is blocked.
Replace the IP with the trust_proxies.

```
TUNNEL_TOKEN=
```

Next, we need to edit HomeAssistant configuration to allow forward proxies.

Edit the file `/home/pi/homeassistant/config/configuration.yml` adding the `http` section.

```
# Loads default set of integrations. Do not remove.
default_config:

# Load frontend themes from the themes folder
frontend:
  themes: !include_dir_merge_named themes

automation: !include automations.yaml
script: !include scripts.yaml
scene: !include scenes.yaml

http:
  use_x_forwarded_for: true
  trusted_proxies:
    - 172.20.0.0/16
    - 0.0.0.0/24
```

# eWeLink

This is for our Heater switch. Clone the project, build the docker image and add the following into our environment file `.env`.

```
git clone https://github.com/CoolKit-Technologies/ha-addon.git
cd ha-addon/eWeLink_Smart_Home/
docker build . -t ewelink_smart_home
```

Add the following to our environment file. The value of `SUPERVISOR_TOKEN` can be created in HomeAssistant Profile's Long-live access tokens. 

```
HA_URL=http://homeassistant:8123
SUPERVISOR_TOKEN=
```
