# Changelog

## 0.3.1

- Fix: forward the VLAN assignment of the inner PEAP tunnel to the final
  Access-Accept (`use_tunneled_reply = yes`), so WPA-Enterprise clients
  actually land in their assigned VLAN
- Fix: only change `default_eap_type` where intended (PEAP at the top level,
  MSCHAPv2 inside the `peap` block); the previous patch changed every occurrence

## 0.3.0

- Add a user list (username, password, optional VLAN) to the add-on options
- Add `client_network`, `server_name` and `debug` options
- Generate EAP certificates automatically and store them in `/data/certs`
- Copy the CA certificate to `/share/freeradius/ca.pem`
- Use PEAP as the default EAP type
- Validate the configuration before starting the server
- Refuse to start with the default shared secret
- Add `freeradius-eap`, `jq` and `openssl` to the image
- Drop the deprecated `armv7` architecture

## 0.2.2

- Minimal FreeRADIUS server with a configurable shared secret
