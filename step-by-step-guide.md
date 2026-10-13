# Step-by-Step Guide (Beginner Friendly)

Follow the parts in order. Do not skip the "Check" lines. If a check fails, fix it before moving on.
Time needed: about 6 to 10 hours total. Split it over several days.

## Progress Tracker
- [ ] Part 1: Install VirtualBox
- [ ] Part 2: Make the private lab network
- [ ] Part 3: Build the Wazuh server VM
- [ ] Part 4: Build the endpoint VM
- [ ] Part 5: Set up Kali
- [ ] Part 6: Take snapshots
- [ ] Part 7: Install Wazuh
- [ ] Part 8: Connect the endpoint to Wazuh
- [ ] Part 9: Run the attacks
- [ ] Part 10: Find the alerts
- [ ] Part 11: Write incident reports
- [ ] Part 12: Put it on GitHub

Tick each box in your own copy of this file as you go, then commit the file so your progress shows on GitHub.

## Words you will see
| Word | Simple meaning |
|---|---|
| VM (virtual machine) | A computer running inside your computer |
| VirtualBox | Free program that runs VMs |
| SIEM | A tool that collects logs and raises alerts (Wazuh is ours) |
| Agent | Small program on a computer that sends its logs to the SIEM |
| Endpoint | A computer being monitored |
| Host-only network | A private network only your VMs can see |
| Alert | A warning the SIEM creates when logs match a rule |
| MITRE ATT&CK | A public list of attacker techniques with ID numbers |

## What you need
- A computer with 16 GB RAM (8 GB can work if you only run 2 VMs at once), 80 GB free disk, and virtualization turned on in BIOS
- Internet
- A free GitHub account (github.com)

---

## Part 1: Install VirtualBox
- [ ] Go to virtualbox.org and click **Downloads**.
- [ ] Download the version for your computer (Windows, Mac, or Linux) and install it with the default options.
- [ ] Also download and install the **Extension Pack** only if the site tells you it is needed for your setup. Most people can skip it.

- [ ] **Check:** VirtualBox opens and shows an empty machine list.

## Part 2: Make the private lab network
- [ ] In VirtualBox, open **Tools > Network** (or **File > Tools > Network Manager** in older versions).
- [ ] Open the **Host-only Networks** tab and click **Create**.
- [ ] Leave the DHCP server turned on. Click **Apply**.

- [ ] **Check:** You see one host-only network listed (usually named vboxnet0 or similar).

## Part 3: Build the Wazuh server VM
- [ ] Download **Ubuntu Server 22.04 LTS** ISO from ubuntu.com/download/server.
- [ ] In VirtualBox click **New**. Name it `wazuh-server`. Pick the ISO. Tick **Skip Unattended Installation**.
- [ ] Give it **4096 MB** RAM, **2** CPUs, and **50 GB** disk.
- [ ] Before starting, open **Settings > Network**:
   - Adapter 1: **NAT**
   - Adapter 2: tick Enable, choose **Host-only Adapter**, pick your network from Part 2
- [ ] Start the VM and run the installer:
   - Language: English. Keyboard: your layout.
   - Network: accept the defaults (you should see two interfaces with addresses)
   - Storage: use the entire disk, accept defaults
   - Profile: pick a name, server name `wazuh-server`, a username, and a password you will remember
   - **Skip** OpenSSH for this VM (you can add it later). Skip extra snaps.
- [ ] When it says install complete, choose **Reboot**. If it asks to remove the install medium, press Enter.
- [ ] Log in with your username and password.
- [ ] Update it: `sudo apt update && sudo apt full-upgrade -y`
- [ ] Find its IP addresses: `ip a` . Write down the one that starts with the same numbers as your host-only network (often `192.168.56.x`).

- [ ] **Check:** `ping -c 3 google.com` works (NAT adapter) and you wrote down the host-only IP.

## Part 4: Build the endpoint VM
Repeat Part 3 with these differences:
- Name: `lab-endpoint`
- **2048 MB** RAM, **1** CPU, **20 GB** disk
- In the installer, **tick Install OpenSSH server**
- Server name: `lab-endpoint`

If you used Ubuntu Desktop instead of Server, OpenSSH is not installed. Run `sudo apt install openssh-server -y` and `sudo systemctl enable --now ssh`, then confirm with `sudo ss -tlnp | grep :22`.

After you log in:
- [ ] Update: `sudo apt update && sudo apt full-upgrade -y`
- [ ] Create a test user: `sudo adduser labuser` (choose a simple lab-only password)
- [ ] Get its IP: `ip a` and write down the host-only IP.

- [ ] **Check:** You have a host-only IP for the endpoint.

## Part 5: Set up Kali
- [ ] Go to kali.org > **Get Kali** > **Virtual Machines** > **VirtualBox**.
- [ ] Download the 64-bit file and the **SHA256** value shown on the page.
- [ ] Verify the download. On Windows PowerShell: `Get-FileHash <filename> -Algorithm SHA256`. On Mac/Linux: `shasum -a 256 <filename>`. The result must match the site.
- [ ] Extract the file (use 7-Zip if needed). Double-click the `.vbox` file to add it to VirtualBox.
- [ ] Open **Settings > Network** on the Kali VM:
   - Adapter 1: **NAT**
   - Adapter 2: **Host-only Adapter** (same network)
- [ ] Start Kali and log in (default is `kali` / `kali`).
- [ ] Change the password right away: open a terminal and run `passwd`.
- [ ] Update: `sudo apt update && sudo apt full-upgrade -y`
- [ ] Get its IP: `ip a`.

- [ ] **Check:** Kali can reach the endpoint: `ping -c 3 <endpoint-ip>`.

If the host-only adapter has no IP on Ubuntu, run `ip a` to find its name (like `enp0s8`), then `sudo dhclient enp0s8`.

## Part 6: Take snapshots
For each of the 3 VMs: click the VM, open **Snapshots**, click **Take**, name it `clean-baseline`.
This lets you undo mistakes.

- [ ] **Check:** All 3 VMs show a snapshot.

## Part 7: Install Wazuh on the server VM
- [ ] Open a browser on your host and go to **documentation.wazuh.com**. Search for **Quickstart**.
- [ ] Copy the current install command shown there. It is usually a `curl` command followed by `sudo bash ./wazuh-install.sh -a`. Use the one on the site, not an old one from a video.
- [ ] Paste and run it on the `wazuh-server` VM. Wait 10 to 20 minutes.
- [ ] At the end it prints a **username and password** for the dashboard. Copy them into a private notes app or password manager. **Never put them in GitHub.**
- [ ] On your host or Kali browser, go to `https://<wazuh-server-ip>`. Click through the browser warning (the certificate is self-signed).
- [ ] Log in.
- [ ] Take a screenshot of the dashboard and save it to `evidence/screenshots/02-dashboard.png`.
- [ ] Run `df -h /` and confirm the disk is not near 100% full
- [ ] Take a VM snapshot named `wazuh-installed`.

- [ ] **Check:** The Wazuh dashboard loads and you can log in.

## Part 8: Connect the endpoint to Wazuh
- [ ] In the Wazuh dashboard, open the menu and go to **Agents > Deploy new agent**.
- [ ] Choose: **Linux**, **DEB amd64**. Enter the Wazuh server IP. Name the agent `lab-endpoint`.
- [ ] Copy the commands it shows and run them on the endpoint VM.
- [ ] Start it:
  ```
  sudo systemctl daemon-reload
   sudo systemctl enable --now wazuh-agent
   ```
- [ ] Wait one minute, then refresh **Agents** in the dashboard.
- [ ] Screenshot to `evidence/screenshots/03-agent-active.png`.

- [ ] **Check:** `lab-endpoint` shows as **Active**.

### Turn on real-time file monitoring
- [ ] On the endpoint: `sudo mkdir /root/fim-test`
- [ ] Open the config: `sudo nano /var/ossec/etc/ossec.conf`
- [ ] Find the `</syscheck>` line (use Ctrl+W to search). Add this line on its own line **just above** `</syscheck>`, not after it:
   `<directories realtime="yes">/root/fim-test</directories>`
- [ ] Check the line sits above `</syscheck>` and has no space after the `>`
- [ ] Save (Ctrl+O, Enter) and exit (Ctrl+X).
- [ ] Restart: `sudo systemctl restart wazuh-agent`
- [ ] Snapshot the endpoint as `agent-ready`.

## Part 9: Run the attacks
Write down the time before each attack (you will need it to find alerts). Only attack your lab endpoint.

### Attack 1: SSH brute force
On **Kali**:
- [ ] `printf "password\n123456\nadmin\nletmein\nqwerty\n" > passwords.txt`
- [ ] `hydra -l labuser -P passwords.txt ssh://<endpoint-ip> -t 4`
- [ ] Note the time. It will fail because none of the passwords are right. That is what you want.

### Attack 2: Port scan
- [ ] On **Kali**, run: `nmap -sS -sV <endpoint-ip>`
- [ ] Note the time and which ports it found

### Attack 3: New account (attacker persistence)
- [ ] On the **endpoint**, run: `sudo adduser backdoor-test`
- [ ] Note the time

### Attack 4: File change
- [ ] On the **endpoint**, run:
  ```
  sudo bash -c 'echo test > /root/fim-test/notes.txt'
  sudo bash -c 'echo changed >> /root/fim-test/notes.txt'
  ```
- [ ] Note the time

### Attack 5: Sudo use
- [ ] If needed, add `labuser` to sudo from your main account: `sudo usermod -aG sudo labuser`
- [ ] On the **endpoint**, log in as `labuser` (`su - labuser`), then run:
  ```
  sudo whoami
  sudo cat /etc/shadow
  ```
- [ ] Note the time

### Clean up
- [ ] Remove the test user: `sudo deluser --remove-home backdoor-test`

- [ ] **Check:** You have 5 timestamps written down.

## Part 10: Find the alerts
Do this for each attack.
- [ ] In Wazuh, open **Threat Hunting** (or **Security events**).
- [ ] Set the time range to cover your attack time. Filter by agent `lab-endpoint`.
- [ ] Look for alerts near your timestamp. Click one to open it.
- [ ] Write down: **rule ID**, **description**, **level**, **time**, and any **MITRE** tag.
- [ ] Read the `full_log` field. This is the raw evidence.
- [ ] Screenshot to `evidence/screenshots/05-scenarioX.png` (replace X with the number).
- [ ] Decide the verdict:
   - **True positive**: real attack activity correctly flagged
   - **False positive**: alert with no real problem
   - **Missed detection**: you did the attack and saw no alert
- [ ] Fill in one row in `docs/detection-log.md`.

If you see nothing: check the agent is Active, widen the time range, and check both VMs have the right clock. A missed detection is a valid result. Write why it may have happened (for example, port scans need network monitoring like Suricata).

- [ ] **Check:** All 5 rows in the detection log are filled in.

## Part 11: Write incident reports
- [ ] Copy `docs/incident-report-template.md` and rename it `docs/incident-01-ssh-brute-force.md`.
- [ ] Fill in each section. Keep the summary to 2 or 3 plain sentences.
- [ ] Add your screenshot and the key log line as evidence.
- [ ] Write one for at least 2 scenarios. The SSH brute force and new account ones are the easiest.

- [ ] **Check:** At least 2 finished reports in `docs/`.

## Part 12: Put it on GitHub

### Easiest method (no commands)
- [ ] Log in to github.com. Click **+ > New repository**.
- [ ] Name it `soc-detection-lab`. Set it to **Public**. Do not add a README. Click **Create repository**.
- [ ] Click **uploading an existing file**. Drag your whole project folder contents in and click **Commit changes**.
   (The web page only accepts files, so drag the contents, and check the folders came across.)

### Command method (better to learn)
- [ ] Install Git from git-scm.com (Windows/Mac) or `sudo apt install git` (Linux/Kali).
- [ ] Open a terminal in your project folder and set your name and email once:
   ```
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```
- [ ] Turn the folder into a repo and save your first version:
   ```
   git init
   git add .
   git commit -m "Initial commit: SOC detection lab"
   git branch -M main
   ```
- [ ] Connect to GitHub and upload:
   ```
   git remote add origin https://github.com/smfdoy/soc-detection-lab.git
   git push -u origin main
   ```
- [ ] GitHub does not accept your account password here. When asked to log in, use a **Personal Access Token** (GitHub > Settings > Developer settings > Personal access tokens) as the password, or install the GitHub CLI and run `gh auth login`.

### Before you push, check
- [ ] No passwords, tokens, or the Wazuh admin login anywhere in the files
- [ ] No VM files or large files
- [ ] Screenshots do not show personal info

### After you push
- [ ] Add topics on the repo page: `soc`, `wazuh`, `siem`, `kali-linux`, `mitre-attack`, `homelab`.
- [ ] Pin the repo on your profile.
- [ ] Add the link to your resume and LinkedIn.
- [ ] After every future change: `git add .` then `git commit -m "what changed"` then `git push`.

- [ ] **Check:** Your repo page shows the README with the tables and folders.

---

## If you get stuck
| Problem | Try this |
|---|---|
| VM will not start | Confirm virtualization is on in BIOS |
| No IP on host-only adapter | `sudo dhclient <interface-name>` |
| Wazuh install fails | Check 4 GB RAM and that the NAT adapter has internet |
| "Failed to start Wazuh manager" | Run `df -h /`. If Use% is near 100%, free space or grow the disk. Then `sudo systemctl restart wazuh-manager` |
| `Could not get lock /var/lib/apt/lists/lock` | Another update is running (common on first boot). Wait 5 to 15 minutes, check with `ps aux \| grep -i apt`, then rerun. Do not delete the lock file |
| Hydra says "Connection refused" / Nmap shows all ports closed | SSH is not running on the endpoint. Install and start `openssh-server` (see Part 4) |
| Kernel panic, "No working init found" | Install did not finish or boot order is wrong. Redo the install fully |
| `vmwgfx` graphics errors on boot | Harmless. To hide: VM Settings > Display > Graphics Controller > VBoxSVGA |
| Agent not Active | Check the server IP you entered, then `sudo systemctl status wazuh-agent` |
| No alerts appear | Widen the time range, check VM clocks, record it as a missed detection |
