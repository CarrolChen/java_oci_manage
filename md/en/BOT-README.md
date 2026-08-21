# Telegram Bot Command Reference

[简体中文](../BOT-README.md)

---

## Table of Contents

- [1. Bot Command Menu](#1-bot-command-menu)
- [2. Oracle Keyboard Menu](#2-oracle-keyboard-menu)

---

## 1. Bot Command Menu

### Cloud Operations

| Command | Description |
|---------|-------------|
| `/oracle` | Open the Oracle Cloud keyboard — launching instances and everything else in section 2 |
| `/aws` | Open the AWS keyboard, covering EC2 instances and Lightsail |
| `/gcp` | Open the GCP keyboard for Compute Engine instance management |
| `/azure` | Open the Azure keyboard for launching and managing VMs |
| `/do` | Open the DigitalOcean Droplet menu: listing, details, power actions, traffic queries |
| `/solusvm` | Manage VPS instances from providers running the SolusVM panel |
| `/virtfusion` | Open the VirtFusion menu: instances grouped by vendor, plus basic power actions |
| `/domain` | Cloudflare domain management |

### Account Management

| Command | Description |
|---------|-------------|
| `/me` | View your Lightning subscription details |
| `/bindclient` | Bind and activate a client |
| `/raninfo` | Generate or reset random account credentials |
| `/flash` | Compare standard and Lightning privileges |
| `/migrate` | Move Lightning privileges to the account you are logged in as. Usage: `/migrate old_username old_password` |

After a fresh client install, copy the `/bindclient username password` line from the activation banner on the client's web page and send it to the bot. That completes activation.

Save whatever `/raninfo` gives you. If your Telegram account is ever banned, those credentials are how you rebind.

### Payment & Activation

| Command | Description |
|---------|-------------|
| `/qrcode` | QR payment for donations or Lightning privileges. Payment activates Lightning automatically |
| `/codeflash` | Claim Lightning using a transaction order number. Usage: `/codeflash order_number` |
| `/getflash` | Activate Lightning with an activation code. Usage: `/getflash code` |

If you paid without filling in the payment note, the automatic activation has nothing to match on — claim it manually with `/codeflash` and your order number.

### Configuration

| Command | Description |
|---------|-------------|
| `/oci` | Upload an Oracle or Azure configuration to the current client |
| `/pubkey` | Add an SSH public key for use when launching instances |
| `/uploadapi` | Add another API configuration to manage |
| `/oproxy` | Configure an outbound HTTP proxy for a client profile |

`/oci` takes configuration text only. For security the bot never receives your private key file: upload the `.pem` to your own client server first, then put that path in `key_file=`. (The web panel can take the config text and the key file together — see [Installation & Configuration](./install.md).)

`/oproxy` routes a profile's API requests through a proxy instead of the client's own IP, which keeps several accounts on one client from sharing an egress address. The same setting is available in the web panel under the profile editor.

### Others

| Command | Description |
|---------|-------------|
| `/command` | Run a shell command on the current client's server |
| `/clearlock` | Clear the lockout caused by too many failed password attempts |
| `/qb` | Report an unreceived payment, or discuss advertising |
| `/qs` | Support line for Lightning users |

> Commands marked as admin-only are not for regular users.

---

## 2. Oracle Keyboard Menu

Send `/oracle` to open the keyboard. The numbering below matches the buttons.

### Instance Operations

| # | Feature | Description |
|---|---------|-------------|
| 1 | **Boot (ARM)** | Launch instances on the current account with a custom OS and shape |
| 3 | **Boot Suspended** | Bring a suspended instance back up without deleting the disk and rebuilding |
| 5 | **Scale Up/Down** | Adjust CPU and memory on an existing Oracle instance |
| 6 | **Instance Mgmt** | Terminate, attach IPv6, attach extra IPv4 (several IPv4 on one instance), smart reserved-IP attach (checks for unused reserved IPs and whether the VNIC already has a public IP), toggle Oracle's own usage monitoring on or off, force power-cycle, rename, delete all IPv6, reset the OS to its initial image |
| 17 | **Quick Boot** | Save a launch configuration, then run several configurations across several profiles, or across several clients, at once |

Shape names in option 1 are Oracle's internal ones — the keyboard links to a reference, and anything starting with `aarch` is ARM. Delay is in seconds. Launching takes a second confirmation: after you finish picking parameters, press the confirm button on the keyboard again or nothing is queued.

For option 17, selecting multiple profiles *and* multiple clients runs the multi-client mode only.

Full walkthrough: [Oracle Instance Launch Guide](./boot-oracle.md).

### IP & Networking

| # | Feature | Description |
|---|---------|-------------|
| 2 | **IP Management** | Rotate the IP; point the current IP at a Cloudflare-hosted domain; rotate the IP and update the DNS records bound to it in the same step (pseudo-DDNS); unbind, which clears every domain pointing at the current IP; delete the current IP |
| 4 | **IPv6 Management** | Rotate and manage IPv6 addresses on the current account |
| 11 | **Open Ports** | Open every port on the account's cloud panel and clear its firewall rules — this is the Oracle console's security list, not the firewall inside the instance |
| 16 | **Monitor (Auto IP)** | Watch a specific IP and rotate it automatically when it fails, then rebind domains |

Anything touching DNS in option 2 needs Cloudflare credentials in the config file first — either `cf_api_token` (scoped, recommended) or `cf_email` plus `cf_account_key`. See [Installation & Configuration](./install.md#cloudflare-configuration-optional).

Option 16 also lets you choose whether to test through a proxy (without one, reachability is judged from the client machine), run a Netflix non-original content check, and constrain results to a given IP range.

### Disk Management

| # | Feature | Description |
|---|---------|-------------|
| 7 | **Disk Management** | Delete a boot volume, grow it (Oracle does not allow shrinking), detach and attach it, or max out VPU performance for faster IO |

### Account & Configuration

| # | Feature | Description |
|---|---------|-------------|
| 8 | **Cloud Account Mgmt** | Add an admin user and upload its API key, reset the current user's password, batch-query user emails, delete users |
| 13 | **Profile Management** | Switch between the profiles configured on this client, or delete ones you no longer need |
| 14 | **Client Management** | Switch between your clients if you run the script on several machines, or delete ones you no longer need |
| 26 | **Clear All 2FA** | Remove every bound two-factor device |
| 27 | **Disable Banned** | Check all clients for banned accounts and comment out their configuration |
| 28 | **Delete Current API** | Permanently delete the API key |

Option 26 is the way out of a lost 2FA device or lost secret. The API in use needs the matching permissions, or it has to belong to the user being cleared.

Option 27 comments configuration out rather than deleting it, so nothing is lost.

Option 28 is irreversible. Once the key is deleted you have to generate a new one from the Oracle console — and if you have lost access to the account's email and this was your only API key, deleting it means losing the account.

### Monitoring & Notifications

| # | Feature | Description |
|---|---------|-------------|
| 9 | **Status Monitor Alert** | Periodically check every instance and send a Telegram message on anomalies |
| 10 | **Status Monitor Auto-Start** | Periodically check every instance and try to start it again on anomalies |
| 29 | **Daily Report** | Scheduled push of cost and traffic usage |

Options 9 and 10 share their configuration with Uptime Guard in the web panel's Cloud Monitoring tab — change either side and the other follows.

### Queries

| # | Feature | Description |
|---|---------|-------------|
| 12 | **Workflow Errors** | The last 3 errors returned by Oracle workflows, nothing older |
| 15 | **Health Check** | Batch-check every profile on every client |
| 18 | **Subscription Info** | Free subscription end date and amount for the current account |
| 19 | **3-Month Traffic** | Traffic used by the current account over the last 3 months |
| 20 | **Query Quota** | Instance, network, and storage quotas |
| 21 | **Running Tasks** | Tasks currently running across all clients, such as launches and upgrades |
| 22 | **Client Load** | Load on the current client's server |
| 25 | **3-Month Costs** | Billed items and amounts over the last 3 months for paid accounts. Multi-region accounts must be queried from the main account |
| 30 | **Batch Query Emails** | Emails for every profile on the current client |
| 34 | **Latest Logs** | The most recent chunk of the client's log output |

### System Operations

| # | Feature | Description |
|---|---------|-------------|
| 23 | **Occupy 25% Memory** | Top memory usage up to 25%. Already at 25% or above, it does nothing |
| 24 | **Clear Cache & Memory** | Drop the system memory cache and release the 25% occupation |
| 31 | **Restart** | Restart the current client |
| 32 | **Upgrade Client** | Check all clients and upgrade them. An upgrade interrupts everything in progress |
| 33 | **Auto Expand Sub-regions** | Find sub-region subscriptions in the configuration file and add them automatically |
