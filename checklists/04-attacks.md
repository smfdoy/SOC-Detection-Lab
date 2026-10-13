# Phase 4: Run Attack Scenarios

Rules: only attack the lab endpoint. Write the start time of each attack in `docs/detection-log.md`.

## Scenario 1: SSH brute force (T1110)
- [ ] On Kali, make a small wordlist: `printf "password\n123456\nadmin\nletmein\nqwerty\n" > passwords.txt`
- [ ] Run: `hydra -l labuser -P passwords.txt ssh://<endpoint-ip> -t 4`
- [ ] Note the time and result

## Scenario 2: Port scan (T1046)
- [ ] Run: `nmap -sS -sV <endpoint-ip>`
- [ ] Note the time and open ports found

## Scenario 3: New account (T1136.001)
- [ ] On the endpoint, simulate an attacker persisting: `sudo adduser backdoor-test`
- [ ] Note the time

## Scenario 4: File integrity (T1565.001)
- [ ] On the endpoint: `sudo bash -c 'echo test > /root/fim-test/notes.txt'`
- [ ] Then modify it: `sudo bash -c 'echo changed >> /root/fim-test/notes.txt'`
- [ ] Note the time

## Scenario 5: Sudo use (T1548.003)
- [ ] Log in as `labuser`, run: `sudo whoami` and `sudo cat /etc/shadow`
- [ ] Note the time

## Clean up
- [ ] Remove the test user: `sudo deluser --remove-home backdoor-test`
