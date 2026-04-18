## v1.0.1 Development

**Features**
- Added allowing http access by subnet
- Included all [rfc 1918 local networks](https://www.rfc-editor.org/rfc/rfc1918#:~:text=1996%0A%0A%0A3.-,Private%20Address%20Space,-The%20Internet%20Assigned) into allowed subnets list by default
- Included ability to exclude 192.168.x.x from allowed subnets by setting `include_192_in_allowed_networks:` flag 

**Limitations**

- The core Home Assistant http functionality of automatic banning of failed login attempts does not work

## v1.0.0 [Initial Release](https://github.com/HLFCode/Home-Assistant-http/releases/tag/v2026.04.1) HA v25.04.01 (2026-04-10)

**Features**

- Banning http access by subnet
- Optional logging of attempted access from an IP in a banned subnet
- Optional persistent notification of attempted access from an IP in a banned subnet

**Limitations**

- The core Home Assistant http functionality of automatic banning of failed login attempts does not work

**Prerequisites**

- Home Assistant >=V2026.04.01
