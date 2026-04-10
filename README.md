# Home-Assistant-http

http component for Home Assistant to include banning by subnet which can be installed as a cutsom_component

## Additions to core Home Assistant

The standard method of banning is for HA to log login attempts and ban after a number of failed attempts.

All of this is retained in this custom component.

Lacking in this is the ability to ban bots which try to probe the HA file structure.

HA reports these like this

`
2026-04-06 23:30:06.553 WARNING (MainThread) [homeassistant.http.security_filter] Filtered a potential harmful request from 109.248.170.188 to: /cgi-bin/../../../../../../../../../../bin/sh
`

If you are plagued by these you can add the IP address or a range of IP addresses as a banned network in the normal configuration.yaml

If you want to ban a range of IP addresses to reduce this, list the networks in `banned_networks`. To block `1.2.3.[0-255]` use the entry `1.2.3.0/24`. To block `1.2.[0-255].[0-255]` use `1.2.0.0/16`.

If you edit the banned networks list, you will need to restart Home Assistant for it to take effect

By default, logging failed attempts to the log file and showing as Persistent Notifications are enabled. To disable either of these, use the `log_banned_networks` and `notify_banned_networks` flags and set them to `False`.

Banned IP log entries are `info` messages, so to see them the logger default, or for this integration, must be at `info` or lower. See the [logger](https://www.home-assistant.io/integrations/logger/) integration for more information.

```yaml
logger:
  default: critical
  logs:
    homeassistant.components.http: info
```
Configuration should follow this format

```yaml
# Example configuration.yaml entry
http:
  ip_ban_enabled: true
  login_attempts_threshold: 5
  banned_networks:
    - 111.7.0.0/16
    - 45.0.0.0/8
  log_banned_networks: true
  notify_banned_networks: true

```

All functionality of the core http component is retained.


## Installation

To use copy the http folder to the HA's config/custom_components folder and restart HA

## Risks

Home Assistant will not start if you update HA to a version which breaks this custom component replacement for http.

To avoid being stuck set up smb/samba access to the config folder so you can remove this custom component from the config folder without needing Home Assistant to be running
