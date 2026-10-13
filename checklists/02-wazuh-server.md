# Phase 2: Install Wazuh Server

Always use the current quickstart at documentation.wazuh.com so versions are not out of date.

- [ ] Open the Wazuh "Quickstart" page and copy the current all-in-one install command
- [ ] Run it on the Wazuh server VM (takes 10 to 20 minutes)
- [ ] Save the admin password printed at the end in a private password manager (never in the repo)
- [ ] Open `https://<wazuh-server-ip>` from your host or Kali browser (accept the self-signed cert warning)
- [ ] Log in and confirm the dashboard loads
- [ ] Screenshot the empty dashboard: `evidence/screenshots/02-dashboard.png`
- [ ] Snapshot the Wazuh VM as `wazuh-installed`

## Troubleshooting
- [ ] If the install fails, check RAM (needs 4 GB) and internet (NAT adapter)
