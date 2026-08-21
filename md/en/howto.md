# How-To Guide

[简体中文](../howto.md)

Task-oriented walkthroughs. The exhaustive feature list lives in [Implemented Features](./function.md); this page covers what people actually do most.

- [Rotate IP and auto-update DNS](#rotate-ip-and-auto-update-dns)
- [Rotate automatically when an IP goes dark](#rotate-automatically-when-an-ip-goes-dark)
- [Auto-shutdown on traffic overage](#auto-shutdown-on-traffic-overage)
- [Bring stopped instances back up](#bring-stopped-instances-back-up)
- [A1 free tier audit](#a1-free-tier-audit)
- [Domain and certificate expiry monitoring](#domain-and-certificate-expiry-monitoring)
- [Give each account its own outbound IP](#give-each-account-its-own-outbound-ip)
- [Rescue an instance you cannot SSH into](#rescue-an-instance-you-cannot-ssh-into)
- [Run one command across many hosts](#run-one-command-across-many-hosts)
- [Import existing cloud instances as sessions](#import-existing-cloud-instances-as-sessions)

---

## Rotate IP and auto-update DNS

The most common operation after an IP gets blocked: swap the IP and repoint the domain in one go, which amounts to DDNS.

**Prerequisite** — Cloudflare credentials under Settings → Config File Settings. Either form works:

| Method | Fields | Where to get it |
|------|--------|---------|
| API Token (recommended) | `cf_api_token` | Create in the Cloudflare dashboard with Zone→DNS→Edit and Zone→Zone→Read |
| Global API Key | `cf_email` + `cf_account_key` | My Profile → API Tokens → API Keys → Global API Key |

With both present, the token wins. The token is scoped; the Global API Key controls the entire account. Use the token unless you cannot.

**Bot** — `/oracle` → "2. IP Management":

- Rotate the IP
- Point the current IP at a Cloudflare-hosted domain
- Rotate the IP and update the DNS records bound to it in the same step (pseudo-DDNS)
- Unbind — clear every domain currently pointing at this IP
- Delete the current IP

**Web** — Cloud Management → instance card → network actions: rotate IPv4, rotate IPv6, attach extra IPv4, attach IPv6, attach a reserved IP. DNS records are edited directly under the Cloudflare tab.

---

## Rotate automatically when an IP goes dark

`/oracle` → "16. Auto IP Rotation Monitor".

Settings:

- Which IP to watch
- Whether to test through a proxy (without one, reachability is judged from the client machine)
- Netflix non-original content check
- IP range constraint — keep rotating until the new IP falls inside the range you specify

On trigger it rotates the IP and updates domain bindings per your rules.

---

## Auto-shutdown on traffic overage

OCI's free allowance is 10240 GB (10 TB) per month; past that you are billed by usage. Waking up to a few dozen dollars of overage after someone hammers your bandwidth is a common accident.

Cloud Management → Cloud Monitoring → "+ Add Traffic Guard":

1. Pick the account to watch
2. Set the monthly threshold. **Use 9000, not 10240** — Oracle's billing data lags 4–24 hours, so a threshold at the ceiling arrives too late
3. Choose "Notify only" or "Auto shutdown"

If you choose auto shutdown, know this:

- It fires immediately with no second confirmation, and stops instances across **every region** on that account (SOFTSTOP)
- If you start them again and are still over the threshold, they get stopped again within the hour
- Leave them off too long and Oracle may reclaim capacity quota for contended shapes like A1, meaning you have to win the capacity race all over again

The usage figure shown in the rule comes from the same billing API and is not real-time.

---

## Bring stopped instances back up

Cloud Management → Cloud Monitoring → Uptime Guard:

| Toggle | Behavior |
|------|------|
| Stop notifications | Checks every 8 minutes and sends a Telegram message on unexpected shutdown |
| Auto-start | Starts stopped instances back up |

Accounts shut down by Traffic Guard are excluded for the rest of the month — otherwise they would restart and keep burning bandwidth.

The bot equivalents are "9. Instance Status Monitoring" and "10. Auto-start on Failure". Same configuration, either side.

---

## A1 free tier audit

Oracle's ARM free allowance is per account, and accounts over it can have resources reclaimed. Cloud Management → A1 Audit checks and downscales in bulk.

1. **Audit** — scans A1.Flex usage across accounts in parallel and flags each as over-allowance, compliant, or query-failed
2. **Downscale** — three granularities: per account (splits the target evenly across that account's instances), batch (select every downscalable account at once), or per instance
3. The default target is 2 OCPU / 12 GB and can be changed

Hard rules:

- Downscale only, never upscale, and **never deletes an instance automatically**
- It reuses the capacity-race resize: out-of-capacity errors retry until they succeed, results arrive over Telegram, and the task can be cancelled from the task list
- Running instances reboot during the resize
- The target OCPU count cannot be lower than the instance count, or it will not divide — downscale per instance or delete a few first
- Deletion is per instance with explicit confirmation. There is no bulk delete

Free users can audit the current account only; Lightning users can audit and downscale every account at once.

---

## Domain and certificate expiry monitoring

Cloud Management → Domain Monitoring. Every day at 04:25 it checks domain registration expiry and SSL certificate expiry, then warns over Telegram as the dates approach.

Add domains by hand, or use "Import from Cloudflare" to pull in your hosted zones in one shot (duplicates are skipped; if you hit the cap it tells you what was left out).

Warning tiers are 30, 14, 7, and 1 day plus expired. Each tier fires once, so it will not nag daily.

Worth knowing:

- Enter the **registrable domain** (`example.com`), not a subdomain. Subdomains have no registration record, so domain expiry shows "unknown" — certificate checks still work
- A failed check keeps the previous values and marks them unknown rather than overwriting good data
- Search handles internationalized domains and converts to punycode automatically
- Adding, importing, and enabling reminders require Lightning; disabling, deleting, viewing, and "Check now" do not

---

## Give each account its own outbound IP

Several Oracle accounts hitting the API from one client all share the same egress IP. Route specific accounts through their own proxy instead, per profile.

**Web** — Settings → Config File Settings → open the profile → "API Outbound Proxy" at the bottom of the panel. Enter the proxy address, plus username and password if it needs auth, and save.

To disable it you must click "Remove Proxy". Clearing the fields and saving does not count.

The profile list on the overview page has an outbound-proxy column, so you can see at a glance which accounts are covered and jump straight to the form.

**Bot** — `/oproxy`.

Accounts with a proxy configured use it for every request; there is no silent fallback to a direct connection on failure. A malformed address fails loudly instead of quietly exposing your real IP.

---

## Rescue an instance you cannot SSH into

SSH refuses, you firewalled yourself out, or the system will not boot — use the serial console.

Cloud Management → instance → Serial Console. The client generates a temporary key pair, establishes the connection, and gives you a terminal in the browser.

If the system itself is broken, the built-in Netboot.xyz flow detects the UEFI/GRUB menu and boots a rescue environment, pausing for confirmation before anything destructive.

Keys expire after 30 minutes and stale connections are cleaned up on a timer.

---

## Run one command across many hosts

Web SSH terminal → Batch Commands. Select the hosts, type the command, send once.

Results are grouped per host in a workbench, and you can keep issuing commands to the same selection without reselecting.

For a one-off on the client server itself, the bot's `/command` is quicker.

---

## Import existing cloud instances as sessions

No need to type IPs one by one. Host panel → Cloud Host Sync, covering OCI, AWS EC2, AWS Lightsail, GCP, Azure, DigitalOcean, SolusVM, and VirtFusion.

What syncs is the connection info; usernames and keys still need configuring once. Store the key under SSH Key Management and every session can pick it.

Each cloud's instance cards also carry an "SSH" button that jumps straight into a terminal — handy for one-time connections.
