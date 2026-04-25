# Home-Assistant-http

Custom component for Home Assistant to augment the core http component functionality.

This component adds the ability to ban groups of IP addresses by subnet or to ban everything but allow specific subnets access

# Notice

I've just found out the HA core functionality of banning failed login attempts DOES NOT WORK - setting the threshold config flag does nothing.

## Additions to core Home Assistant

The core http component checks for potentially harmful requests and can ban login credential failures

The checks are retained in this custom component except the login failure banning does not currently work

Lacking in this core functionality is the ability to ban bots which try to probe the HA file structure.

HA reports these like this

`
2026-04-06 23:30:06.553 WARNING (MainThread) [homeassistant.http.security_filter] Filtered a potential harmful request from 109.248.170.188 to: /cgi-bin/../../../../../../../../../../bin/sh
`

If you are plagued by these you can add the IP address or a range of IP addresses as a banned network in the normal configuration.yaml

If you want to ban a range of IP addresses to reduce this, list the networks in `banned_networks`. To block `1.2.3.[0-255]` use the entry `1.2.3.0/24`. To block `1.2.[0-255].[0-255]` use `1.2.0.0/16`.

It will not let you block the Home Assistant Supervisor (usually on network 10.x.x.x)

If you edit the banned networks list, you will need to restart Home Assistant for it to take effect

By default, logging failed attempts to the log file and showing as Persistent Notifications are enabled. To disable either of these, use the `log_banned_networks` and `notify_banned_networks` flags and set them to `False`.

Banned IP log entries are `info` messages, so to see them the logger default, or for this integration, must be at `info` or lower. See the [logger](https://www.home-assistant.io/integrations/logger/) integration for more information.

```yaml
logger:
  default: critical
  logs:
    homeassistant.components.http: info
```
Configuration using selective networks to be banned should follow this format

```yaml
# Example configuration.yaml entry
http:
  ip_ban_enabled: True # optional as defaults to True
  login_attempts_threshold: 5
  banned_networks: # Make sure the format is right, e.g. /16 must end in 0.0/16
    - 111.7.0.0/16
    - 45.0.0.0/8
  log_banned_networks: True # optional as defaults to True
  notify_banned_networks: True # optional as defaults to True
  include_192_in_allowed_networks: True # optional as defaults to True

```

The opposite approach of banning the entire internet but allow specific subnets use this format

```yaml
# Example configuration.yaml entry
http:
  ip_ban_enabled: True # optional as defaults to True
  login_attempts_threshold: 5
  allowed_networks: # Make sure the format is right, e.g. /16 must end in 0.0/16
    - 84.63.0.0/16 #Vodafone broadband
    - 84.64.0.0/13 #Vodafone broadband
    - 31.80.0.0/12 # EE Mobile
    - 86.128.0.0/11 #BT broadband
    - 86.160.0.0/12 #BT broadband
  banned_networks: # Make sure the format is right, e.g. /16 must end in 0.0/16
    - 0.0.0.0/1 # half of the internet
    - 128.0.0.0/1 # the other half...
  log_banned_networks: True # optional as defaults to True
  notify_banned_networks: True # optional as defaults to True
  include_192_in_allowed_networks: True # optional as defaults to True

```

It is impossible to ban the [rfc 1918](https://www.rfc-editor.org/rfc/rfc1918#:~:text=1996%0A%0A%0A3.-,Private%20Address%20Space,-The%20Internet%20Assigned) local subnets `10.0.0.0/8` and `172.16.0.0/12`.

`192.168.0.0/16` is also not able to be banned by default unless you set the `include_192_in_allowed_networks` flag to False (for testing purposes)

All functionality of the core http component is retained except the banning of login failures.


## Installation

To use copy the http folder to the HA's config/custom_components folder and restart HA.

After a successful start, edit the `configuration.yaml` to include the new features.

## Risks

Home Assistant will not start if you update HA to a version which breaks this custom component replacement for http.

To avoid being stuck set up smb/samba access to the config folder so you can remove this custom component from the config folder without needing Home Assistant to be running.

If you use the allowed_networks list and ban the entire internet you could lock youself out if your local access is not via the default `192.168.0.0/16` network or you set the `include_192_in_allowed_networks` flag to False so make sure you always have an allowed network you can access HA through

## Home Assistant Versions

Tested with 2026.4.0 - > 2026.4.4
