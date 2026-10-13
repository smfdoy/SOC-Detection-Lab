# Phase 1: Setup VMs and Network

## Host prep
- [ ] Host has 16 GB RAM and 80 GB free disk
- [ ] Virtualization enabled in BIOS (VT-x / AMD-V)
- [ ] VirtualBox installed

## Network
- [ ] Create a Host-only network in VirtualBox (Tools > Network)
- [ ] Each VM gets Adapter 1 = NAT, Adapter 2 = Host-only

## VMs
- [ ] Wazuh server VM: Ubuntu Server 22.04, 2 CPU, 4 GB RAM, 50 GB disk
- [ ] Endpoint VM: Ubuntu, 1 CPU, 2 GB RAM, 20 GB disk
- [ ] Kali VM: official VirtualBox image, checksum verified, default password changed
- [ ] Update all three: `sudo apt update && sudo apt full-upgrade -y`
- [ ] Give each VM a static host-only IP or note its DHCP address
- [ ] Install OpenSSH on the endpoint: `sudo apt install openssh-server -y`
- [ ] Create a test user on the endpoint: `sudo adduser labuser`
- [ ] Snapshot all three VMs as `clean-baseline`

## Verify
- [ ] Kali can ping the endpoint over the host-only network
- [ ] Endpoint can ping the Wazuh server
- [ ] Record IPs in `docs/detection-log.md`
