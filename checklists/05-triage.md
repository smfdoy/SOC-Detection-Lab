# Phase 5: Detect and Triage

For each scenario, do all of these:

- [ ] Dashboard > Threat Hunting (or Security events), filter by agent `lab-endpoint` and the attack time window
- [ ] Find the matching alert(s) and record: rule ID, rule description, severity level, timestamp
- [ ] Open the alert and read the raw log (`full_log`) to see the evidence
- [ ] Screenshot the alert: `evidence/screenshots/05-scenarioX.png`
- [ ] Note the MITRE ATT&CK tag Wazuh assigned (if any)
- [ ] Decide: true positive, false positive, or missed detection
- [ ] Add a row to `docs/detection-log.md`

## If nothing showed up
- [ ] Check the agent is Active and the clock time is correct on both VMs
- [ ] Widen the time filter
- [ ] Mark it as a **missed detection** in the log. This is a valid finding. Write why it likely happened (for example, port scans need network monitoring such as Suricata)

## Stretch goal
- [ ] Install Suricata on the endpoint and re-run the Nmap scan to close the Scenario 2 gap
