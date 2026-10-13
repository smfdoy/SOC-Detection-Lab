# Phase 3: Deploy Agent on Endpoint

- [ ] Dashboard > Agents > Deploy new agent
- [ ] Choose Linux (DEB), enter the Wazuh server IP, name the agent `lab-endpoint`
- [ ] Copy the generated commands and run them on the endpoint VM
- [ ] Start the agent: `sudo systemctl daemon-reload && sudo systemctl enable --now wazuh-agent`
- [ ] Confirm the agent shows as **Active** in the dashboard
- [ ] Screenshot: `evidence/screenshots/03-agent-active.png`

## Enable real-time file monitoring (needed for Scenario 4)
- [ ] On the endpoint: `sudo mkdir /root/fim-test`
- [ ] Edit `/var/ossec/etc/ossec.conf` and add this on its own line just above `</syscheck>`:
  `<directories realtime="yes">/root/fim-test</directories>`
- [ ] Restart the agent: `sudo systemctl restart wazuh-agent`
- [ ] Save the sanitized snippet to `configs/fim-snippet.xml`
- [ ] Snapshot the endpoint as `agent-ready`
