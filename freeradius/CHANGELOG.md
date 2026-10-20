# Changelog

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
