# Web Cloud Management Panel Guide

[简体中文](../cloud.md)

The R-Bot client includes a full-featured Web cloud management panel. Manage Oracle Cloud, AWS, GCP, Azure, DigitalOcean, SolusVM, VirtFusion, and Cloudflare DNS resources directly from your browser — fully aligned with the Telegram bot's capabilities.

---

## Access

After starting the client, visit:

```
https://YOUR_IP:9527
```

Log in and access the cloud management workbench via the view switcher from the host dashboard.

> First-time users need to configure cloud platform Profiles in the top bar "Settings" or via the `client_config` file.

---

## Overview Dashboard

The home page displays overview information for the current Profile:

| Card | Content |
|------|---------|
| **Cost** | Last 3 months spending details |
| **Traffic** | Last 3 months traffic usage |
| **Subscription** | Subscription type, payment, validity |
| **Quota** | Instance, network, storage quota usage |

Switch between Profiles to view different account information.

---

## Oracle Cloud Management

### Instance Management

| Action | Description |
|--------|-------------|
| View Instance List | Display all instances and status with boot volume info merged inline |
| Create Instance | Create a new instance with custom specs, image, and network |
| Quick Config | One-click AMD Micro 1C/1G or ARM A1 2C/12G preset that also fills image, disk, count, delay, and selects a public key |
| Create from Boot Volume | Boot an existing detached boot volume with its system and data intact; one instance at a time, and only in the volume's availability domain |
| Force ARM Boot | Improve ARM instance creation success rate for trial accounts (briefly uses paid features, use at your own risk) |
| Quick Boot | Quickly create an instance from saved configurations |
| Start / Stop / Reboot | Basic instance operations |
| Terminate | Delete instance (optionally preserve boot volume) |
| Reset Image | Reinstall to initial OS image |
| Scale Up/Down | Adjust CPU and memory specs |
| Rename | Change instance display name |
| Repair | Attempt to repair abnormal instance |

![Create Instance](../screenshots/cloud-create.jpg)

### Network / IP Management

| Action | Description |
|--------|-------------|
| View VNICs | Display NICs, private IPs, public IPs |
| Change IPv4 | Change public IPv4 address |
| Change IPv6 | Change IPv6 address |
| Delete IP | Remove public IP or IPv6 |
| Attach IPv4 | Add additional IPv4 to instance |
| Attach IPv6 | Create new IPv6 address |
| Attach Reserved IP | Attach a reserved public IP |

### Volume Management

| Action | Description |
|--------|-------------|
| View Volumes | Display all block storage volumes |
| Resize | Increase volume size (cannot shrink) |
| Adjust VPU | Modify performance units for IO boost |
| Batch VPU | Max out VPU for all volumes at once |
| Detach | Detach volume from instance |
| Delete | Permanently delete block storage volume |

### A1 Config Audit / Downscale

A dedicated workbench for the OCI ARM (A1.Flex) always-free quota — one-click bring over-provisioned accounts back within the free tier.

| Action | Description |
|--------|-------------|
| Config Audit | Scan every account's current-region A1.Flex usage in parallel against the free-tier cap (2 OCPU / 12 GB), flagging Over quota / Within quota / Query failed |
| Account Downscale | Popup with a customizable target spec (recommended 2 OCPU / 12 GB), evenly split across the account's instances |
| Batch Downscale | Select all downscalable accounts and submit in bulk |
| Per-Instance Downscale | Downscale a single instance with the recommended value pre-filled |
| Downscale Method | Reuses preemptive resize (auto-retry on capacity contention until success + Telegram notify + cancelable in task list); downscale-only, never auto-deletes instances |
| Delete Instance | Per-instance double confirmation; no blind bulk delete |
| Scope | Non-Lightning users audit the current account only; Lightning users can audit / downscale all accounts at once |

### User Management

| Action | Description |
|--------|-------------|
| List Users | Display all users in the tenancy |
| Create User | Add a new admin user |
| Update Email | Change user email address |
| Reset Password | Reset user login password (system-generated strong password supported) |
| Clear MFA | One-click clear all multi-factor authentication factors |
| Change Tenant Name | Modify tenancy display name |
| Delete User | Remove specified user |
| Password Policy | View identity domain password policy (expiration, complexity, etc.) |

### Object Storage (OSS)

| Action | Description |
|--------|-------------|
| Browse Buckets | List all storage buckets |
| Browse Objects | Prefix filtering and paginated browsing |
| Upload Files | Streaming upload with path traversal prevention |
| Download Files | Streaming download |
| Delete Objects | Delete individual files |
| Create Folders | Create directory structures |

### Profile Management

| Action | Description |
|--------|-------------|
| List Profiles | Display all configured OCI Profiles, marking which accounts have an API outbound proxy |
| Switch Profile | Switch to a different cloud account |
| Copy to New Region | Clone an existing Profile into another region; credentials are copied server-side and only the region is replaced |
| API Outbound Proxy | Configure an outbound proxy per Profile so different accounts send API requests from different egress IPs |
| Delete Profile | Remove unwanted Profiles |

### Email Delivery

| Action | Description |
|--------|-------------|
| One-Click Setup | Enter domain and sender address to automatically create email domain, DKIM signing, DNS records, sender registration, and SMTP credentials |
| DNS Mode | Supports Cloudflare auto-config and manual mode; manual mode pauses to display DNS records for user confirmation |
| Setup Progress | Async execution with real-time progress bar, resumable after leaving the page |
| Domain Management | View email domain list, DKIM verification status, and lifecycle state |
| DKIM Info | View DKIM CNAME record details for manual DNS configuration reference |
| DKIM Repair | Auto-reconfigure DNS CNAME records via Cloudflare when DKIM verification fails (async with progress tracking) |
| Sender Management | View senders under a specific domain and their status |
| Add Sender | Add a new sender address to an existing email domain |
| SMTP Config | View SMTP connection info (server/port/username) |
| Regenerate Credentials | Regenerate SMTP password (shown once, save it securely) |
| Test Send | Send test email via OCI HTTP API with custom recipient/subject/body |
| Delete Domain | Cascade delete email domain with associated DKIM and sender resources |

### Serial Console

Connect to an OCI instance's serial console via Console Connection — useful for emergency maintenance when SSH is unavailable.

| Action | Description |
|--------|-------------|
| Connect Console | Auto-generate temporary RSA key pair and establish SSH-over-SSH tunnel |
| WebSocket Terminal | Real-time serial terminal interaction in browser |
| Netboot.xyz Automation | Auto-detect UEFI/GRUB menus and boot into Netboot.xyz rescue system |
| Connection Management | Auto-reuse existing connections, 30-minute key TTL, scheduled cleanup of expired connections |
| User Confirmation | Semi-automatic confirmation workflow before dangerous operations |

---

## AWS Management

### EC2 Instance Management

| Action | Description |
|--------|-------------|
| View Instance List | Display all EC2 instances with real-time status |
| Create Instance | Select AMI image, instance type, SSH key; async creation with progress tracking |
| Start / Stop / Reboot | Basic instance power operations |
| Terminate | Permanently delete EC2 instance |

### Lightsail Instance Management

| Action | Description |
|--------|-------------|
| View Instance List | Show Lightsail instances with public IP, bundle, blueprint, spec, region, and creation time |
| Start / Stop / Reboot | Basic power operations |
| Delete Instance | Delete a Lightsail instance (dangerous action, requires confirmation) |
| Traffic This Month | View inbound / outbound / total traffic and the bundle allowance; sourced from monitoring metrics, not billing usage |

### Lightsail Network / IP Management

| Action | Description |
|--------|-------------|
| Static IP Management | Allocate and attach a static IP, detach, release |
| Change Static IP | Allocate a new static IP, switch the attachment, and release the old one |
| Change Dynamic IP | Stop then start to try to obtain a new dynamic public IP when no static IP is attached |
| Firewall Ports | View and save public port rules; supports single ports and port ranges |

### Network Management

| Action | Description |
|--------|-------------|
| VPC Management | View and manage VPC resources |
| Security Groups | Manage security group configurations |

### Statistics & Monitoring

| Action | Description |
|--------|-------------|
| Cost Statistics | AWS Cost Explorer integration for spending details |
| Usage Monitoring | CloudWatch / Lightsail metrics queries |
| Quota Query | View resource quota usage |

---

## GCP Management

### Compute Engine Instance Management

| Action | Description |
|--------|-------------|
| View Instance List | Aggregated view across all zones with status |
| Create Instance | Select zone, machine type, image, disk size; free tier hints included |
| Start / Stop / Reset | Basic instance power operations |
| Delete Instance | Async deletion with Telegram notification on completion |
| Change IP | Release old external IP and allocate a new one |

### Statistics & Overview

| Action | Description |
|--------|-------------|
| Overview | Instance count stats, zone distribution, e2-micro free tier count |
| Traffic Query | Last 3 months sent/received traffic breakdown |

---

## Cloudflare DNS Management

| Action | Description |
|--------|-------------|
| List Zones | Display all Cloudflare-managed domains |
| View Records | View all DNS records for a domain |
| Add Record | Create A/AAAA/CNAME and other DNS records |
| Edit Record | Modify existing DNS records |
| Delete Record | Remove specified DNS record |

---

## DigitalOcean Management

### Droplet Management

| Action | Description |
|--------|-------------|
| View Droplet List | Display all Droplets with status, IPs, and specs |
| Create Droplet | Select image, region, size (5 types), SSH key; supports bulk creation and cloud-init scripts |
| Power On / Off / Reboot | Basic power operations |

### Reserved IP Management

| Action | Description |
|--------|-------------|
| View Reserved IPs | Display all reserved IPs with assignment status |
| Allocate Reserved IP | Allocate a new reserved IP to a Droplet |
| Assign / Unassign | Bind or unbind a reserved IP from a Droplet |
| Release | Delete a reserved IP |

### Monitoring & Billing

| Action | Description |
|--------|-------------|
| Bandwidth Monitoring | Per-instance current-period traffic on cards; approximated by daily segment integration (DO has no official traffic API) |
| Billing Overview | Account balance and month-to-date charges |

---

## Azure Management

| Action | Description |
|--------|-------------|
| List VMs | Display all virtual machines and status |
| Create VM | Create a new virtual machine |
| Delete VM | Delete a virtual machine |
| Restart VM | Restart a virtual machine |
| Change IP | Change public IP address |
| Resource Usage | View quota usage |

---

## SolusVM Management

| Action | Description |
|--------|-------------|
| List Nodes | List all VPS nodes |
| Node Dashboard | View VPS status details |
| Boot / Shutdown / Reboot | VPS power operations |

---

## VirtFusion Management

| Action | Description |
|--------|-------------|
| Vendor Grouping | Group instance cards by configured vendor alias |
| List Instances | Display state, IPv4/IPv6, CPU, memory, disk, and creation time |
| Traffic Panel | Show usage and percentage for the current billing period |
| Quick SSH | One-click jump to Web SSH for running instances |
| Start / Stop / Restart / Power Off | Common power actions |
| Rename | Rename an instance directly from the card |
| Reset Password | Return a new system password (shown once) |
| Config Error Handling | Show direct reconfiguration hints when token or panel access fails |

---

## Cloud Monitoring

The "Cloud Monitoring" tab in the workbench. Two groups of rules, sharing one configuration with the bot's instance monitoring — change either side and the other follows.

### Traffic Guard (per account)

The OCI free allowance is 10240 GB per month; past that you are billed by usage. Set a monthly threshold per account.

| Item | Description |
|------|-------------|
| Watched Account | Configured per Profile |
| Threshold | In GB. Billing data lags 4–24 hours, so 9000 leaves room to act — a threshold at the ceiling arrives too late |
| Notify Only | Telegram message, instances untouched |
| Auto Shutdown | Immediately stops **every region's** instances on that account (SOFTSTOP). Fires without a second confirmation |
| Current Usage | Sourced from the Oracle billing API; not a real-time figure |

Before choosing auto shutdown: if you start the instances again while still over the threshold, they are stopped again within the hour. And if they stay off too long, Oracle may reclaim capacity quota for contended shapes like A1, meaning you have to win the capacity race again.

### Uptime Guard (all accounts)

| Toggle | Description |
|--------|-------------|
| Stop Notifications | Checks every 8 minutes and sends a Telegram message on unexpected shutdown |
| Auto-Start | Starts stopped instances back up. Accounts shut down by Traffic Guard are excluded for the rest of the month so bandwidth does not keep burning |

---

## Domain Monitoring

The "Domain Monitoring" tab in the workbench. A daily job checks domain registration expiry and SSL certificate expiry, warning over Telegram as the dates approach.

| Action | Description |
|--------|-------------|
| Add Domain | Add a single domain manually |
| Import from Cloudflare | Bulk-import hosted zones; duplicates are skipped, and anything left out by the cap is reported |
| Reminder Toggle | Enable or disable alerts per domain |
| Check Now | Probe immediately instead of waiting for the daily run |
| Search | Handles internationalized domains, converting to punycode automatically |
| Delete | Stop monitoring the domain |

Alert tiers are 30 / 14 / 7 / 1 days plus expired, deduplicated per tier so each one fires once.

Enter the registrable domain (`example.com`) rather than a subdomain — subdomains have no registration record, so domain expiry shows "unknown" while certificate checks still work. A failed check keeps the previous values and marks them unknown instead of overwriting good data.

Permissions: adding, importing, and enabling reminders require Lightning; disabling, deleting, viewing, and "Check now" do not.

---

## Settings

### Instance Monitoring

| Feature | Description |
|---------|-------------|
| Monitor Alerts | Periodic instance status monitoring with Telegram notifications |
| Auto-Start | Auto-start instances when anomalies detected |
| Daily Report | Scheduled push of cost and traffic reports |
| Health Check | Batch check all Profile account status |
| Boot Notification Account Type | Boot notifications mark the account as upgraded / regular (determined by OCI subscription paymentModel; left unmarked if uncertain) |

### ACME Certificates

Configure Let's Encrypt auto-certificates in the Settings page. See [Web SSH Guide — SSL Certificates](./webssh.md#ssl-certificate-configuration) for details.

### Cloud Platform Configuration

Upload, edit, and manage cloud platform API configurations directly from the web interface across all 7 clouds (OCI/AWS/GCP/Azure/DO/SolusVM/VirtFusion) — no need to manually edit the `client_config` file.

| Feature | Description |
|---------|-------------|
| OCI Config Upload | Paste API config text + upload PEM key file, key_file path auto-configured |
| AWS Config Upload | Paste Access Key ID / Secret Access Key configuration |
| GCP Config Upload | Upload Service Account JSON key file, credentials auto-extracted |
| DO Config Upload | Paste DigitalOcean API Token |
| Azure Config Upload | Paste appId/password/tenant configuration |
| SolusVM Config Upload | Paste API URL and key configuration |
| VirtFusion Config Upload | Paste host/token/preset or use preset vendors for quick filling |
| Merge Mode | Duplicate Profile names are skipped with a warning, new Profiles are appended |
| Online Profile Editing | Configured Profiles are shown as a chip list; click to edit fields inline — no more SSH-ing in to edit files |
| Copy Profile to New Region | OCI / AWS Profiles can be cloned into another region in one click; only the region changes, and AWS availability-zone forms are normalized automatically |
| API Outbound Proxy | Configured per account at the bottom of the profile editor. Disabling requires "Remove Proxy" — clearing the fields and saving does not count |
| Delete Single Profile | Delete a specific Profile and clean up dangling default-Profile references |
| Secret Masking | Secrets / tokens are masked in the UI; plaintext is never sent to the browser |
| AWS Region Fix | Real-time hint and one-click fix when a region is mistyped as an availability zone (e.g. ap-southeast-1a) |
| Cloudflare Config | Edit Cloudflare credentials online. Both API Token (needs only Zone→DNS→Edit and Zone→Zone→Read) and Global API Key are supported; the token wins when both are present |
| Network Config | Edit local address, URL name, and startup mode online |
| Hot Reload | Configuration hot-reloads immediately on save; can also be refreshed manually — no client restart needed |

---

## Themes

The web interface supports 8 themes, each with both light and dark modes:

| Theme | Style |
|-------|-------|
| Classic | Default blue, clean and professional |
| Sakura | Pink tones, ACG / anime style |
| Cyber | Neon cyan-blue, techy |
| Ink | Golden antique, Chinese traditional |
| Aurora | Teal-purple gradient, northern lights |
| Stellar | Deep purple tones, starry sky |
| Abyss | Deep ocean blue-green, bioluminescent |
| Sunset | Warm orange-coral, cozy |

Switch themes using the selector in the top bar. Toggle the light/dark mode button to switch between light and dark modes.

---

## Cloud Instance Quick SSH

All cloud platforms (OCI / AWS EC2 / AWS Lightsail / GCP / Azure / DO / SolusVM / VirtFusion) provide an "SSH" button on instance cards. Click to jump directly to the terminal and connect to the instance — no manual connection setup needed. Each card also provides a one-click IP copy button.

---

## Multi-Cloud Health Check

A single panel summarizes total instance count, running/stopped status, and DNS info across all cloud platforms — a one-screen overview of multi-cloud asset health.

---

## Workbench Experience

- **Priority-Ordered Tabs** — Cloud platform tabs are arranged by frequency of use (OCI, AWS, Azure, GCP, DO, Cloudflare, SolusVM, VirtFusion)
- **Form-Based Config Upload** — Configuration upload switched to form inputs, no more manual formatting
- **Regrouped Action Buttons** — Instance action buttons grouped by usage frequency

---

## Online Support

Click the floating button in the bottom-right corner to open the built-in chat window for direct support communication. Image messages are supported. The icon supports **auto-hide** and **free dragging** to avoid blocking the operation area.
