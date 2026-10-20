# FreeRADIUS

A minimal FreeRADIUS server for Home Assistant OS. Users, passwords and
VLANs are managed directly in the add-on configuration. It works with any
RADIUS client, for example access points with WPA-Enterprise, switches with
802.1X or routers with VPN authentication.

## Configuration

| Option | Description |
| --- | --- |
| `shared_secret` | Secret shared between the RADIUS clients and this server (often called "master password"). This is **not** a user password. The add-on refuses to start while it still has the default value `changeme`. |
| `client_network` | IPv4 address or CIDR network that may talk to the server, e.g. `192.168.1.0/24`. `0.0.0.0/0` allows everything. See the note below. |
| `server_name` | Name written into the server certificate. Clients can check it when they validate the server. Default: `freeradius.local`. |
| `debug` | Run FreeRADIUS in debug mode (`radiusd -X`). The log then contains credentials, so only use it while troubleshooting. |
| `users` | List of users, see below. |

Each entry in `users` has:

| Field | Description |
| --- | --- |
| `username` | Login name. Must be unique. |
| `password` | Password of the user. |
| `vlan` | Optional VLAN ID (1-4094) sent to the RADIUS client after a successful login. Without it the user is accepted without a VLAN assignment. |

Usernames, passwords and the shared secret must not contain the character
sequences `%{` or `${` or line breaks. Entries that do are skipped (users) or
rejected (secret). Avoid `%` and `$` in passwords where possible.

### Example (YAML mode)

```yaml
shared_secret: "a-long-random-secret"
client_network: "192.168.10.0/24"
server_name: "freeradius.local"
debug: false
users:
  - username: alice
    password: "correct horse battery staple"
    vlan: 20
  - username: bob
    password: "another secret"
```

### Which clients must be allowed?

`client_network` must contain the addresses of every device that sends RADIUS
requests. For Wi-Fi that is usually each **access point**, for wired 802.1X
the switches, for VPN authentication the router. It is not necessarily the
management controller.

## Client devices (WPA-Enterprise)

The add-on uses PEAP with MSCHAPv2.

- EAP method: `PEAP`, phase 2 authentication: `MSCHAPv2`
- Identity: the `username` from the add-on configuration
- Password: the matching `password`
- CA certificate: `ca.pem` from the add-on (see below). Install it on the
  client and enable server validation.
- Domain / server name: the value of `server_name`

## Example: TP-Link Omada (OC200 / Omada SDN)

Omada Controller 6 no longer includes a built-in RADIUS server. Use this
add-on instead:

1. Start the add-on and note the IP address of your Home Assistant host.
2. Put the addresses of your access points into `client_network`.
3. In the Omada controller create a **RADIUS profile** with the IP address of
   your Home Assistant host, authentication port `1812`, accounting port
   `1813` (optional) and the `shared_secret` as password.
4. Create or edit a WLAN: security mode **WPA-Enterprise**, select the
   RADIUS profile.
5. To use the VLAN of each user, enable the option for RADIUS-assigned VLANs
   in the WLAN settings. The exact name depends on the controller version.

## Persistent data

| Location | Content |
| --- | --- |
| `/data/certs` | Certificate authority, server certificate and DH parameters. Created at first start and kept across restarts, updates and rebuilds. |
| `/share/freeradius/ca.pem` | Copy of the CA certificate (public part only) for installing on clients. Reachable via the `share` folder of Home Assistant. |

All other files (`clients.conf`, user list) are regenerated from the add-on
options on every start.

The server certificate is renewed automatically about 30 days before it
expires. It is also reissued when `server_name` changes. The CA stays the same
in both cases, so clients do not have to trust a new CA.

## Testing

The test tool `radtest` is included. Run it from a machine whose IP address
is allowed in `client_network`:

```
radtest alice "correct horse battery staple" HOME_ASSISTANT_IP 0 SHARED_SECRET
```

`radtest` uses plain PAP, which is fine for a quick check. A successful login
ends in `Received Access-Accept`. If the user has a VLAN, the answer contains
`Tunnel-Private-Group-Id`.

## Troubleshooting

Enable `debug`, restart the add-on and watch the log.

| Message / symptom | Likely cause |
| --- | --- |
| `Ignoring request ... from unknown client` | The source address is not covered by `client_network`. |
| `Shared secret is incorrect` | The secret configured on the client differs from `shared_secret`. |
| `Login incorrect` | Wrong username or password, or the entry was skipped (check the start of the log). |
| Login works but no VLAN is applied | VLAN assignment not enabled on the client, VLAN missing on the network, or no `vlan` set for the user. |
| Clients reject the server | CA not installed on the client or `server_name` does not match the configured domain. |

The add-on sets `use_tunneled_reply = yes` in the PEAP configuration, so the
VLAN of a user is part of the final Access-Accept. If a client still lands in
the wrong VLAN, look for `Tunnel-Private-Group-Id` in the last
`Sent Access-Accept` of the debug log and check the VLAN assignment option on
the client side (for Omada: "Enable VLAN Assignment for Wireless Network" in
the RADIUS profile).

## Security notes

- Passwords are stored in plain text in the add-on options, as required by
  MSCHAPv2.
- Restrict `client_network` to your management network whenever possible.
- Do not expose ports 1812/1813 to the internet.

## Limitations

- Static user list managed in the add-on options.
- Authentication methods: PEAP-MSCHAPv2 and PAP.
- Single shared secret for all clients.
