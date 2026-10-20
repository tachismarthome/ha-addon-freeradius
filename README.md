# FreeRADIUS - Home Assistant Add-on

A minimal [FreeRADIUS](https://freeradius.org/) server packaged as a Home
Assistant add-on. Use it for WPA-Enterprise Wi-Fi, 802.1X or any other RADIUS
client. It also works as a replacement for the RADIUS server that is missing
in TP-Link Omada Controller 6 (for example on the OC200): in Omada you only
enter the IP address of Home Assistant and the shared secret.

Besides the shared secret you can create users with their own password and an
optional VLAN directly in the Home Assistant UI.

## Add-ons in this repository

| Add-on | Description |
| --- | --- |
| [FreeRADIUS](freeradius/) | RADIUS server with per-user passwords and VLAN assignment (PEAP-MSCHAPv2) |

## Installation

[![Add this repository to your Home Assistant instance.](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Ftachismarthome%2Fha-addon-freeradius)

Or add it manually:

1. In Home Assistant open **Settings > Add-ons > Add-on Store**.
2. Open the menu (three dots) and choose **Repositories**.
3. Add `https://github.com/tachismarthome/ha-addon-freeradius`.
4. Install **FreeRADIUS**, set a shared secret and your users in the
   **Configuration** tab, then start the add-on.

Detailed setup instructions are in [DOCS.md](freeradius/DOCS.md).

## Contributing

Issues and pull requests are welcome.

## License

The files in this repository are released under the [MIT License](LICENSE).
FreeRADIUS itself is a separate project licensed under the GPL-2.0.
