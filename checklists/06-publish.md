# Phase 6: Report and Publish

## Reports
- [ ] Copy `docs/incident-report-template.md` to `docs/incident-01-ssh-brute-force.md` and fill it in
- [ ] Write at least 2 incident reports (3 or more is stronger)
- [ ] Write an executive summary in the README a non-technical reader can follow
- [ ] Fill in Results and What I Learned in the README

## Before you push
- [ ] Search the repo for passwords, tokens, and real personal IPs
- [ ] Confirm the Wazuh admin password is NOT anywhere in the repo
- [ ] No VM images or large files

## On GitHub first
- [ ] Create an empty repo named `soc-detection-lab` (no README, no .gitignore)
- [ ] Add topics: `soc`, `wazuh`, `siem`, `kali-linux`, `mitre-attack`, `homelab`, `cybersecurity`

## Commands (run inside the soc-detection-lab folder)
```
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git init
git add .
git commit -m "Initial commit: SOC detection lab with Wazuh and Kali"
git branch -M main
git remote add origin https://github.com/smfdoy/soc-detection-lab.git
git push -u origin main
```

## After
- [ ] Pin the repo on your GitHub profile
- [ ] Add the link to your resume and LinkedIn Featured section
- [ ] Commit after every phase from now on: `git add . && git commit -m "Complete phase X" && git push`
