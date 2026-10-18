# Lab 01 — Linux Networking Basics (Kali Linux)

> **Project:** [HP t730 — Frigate & IoT Security Lab](https://github.com/Dennyboy21/t730-frigate-iot-lab)  
> **Training platform:** Kali Linux in Oracle VM VirtualBox  
> **Status:** Initial Linux networking practice completed; subsequent network validation documented below  
> **Next lab:** [02 — OPNsense VirtualBox Firewall Lab](02-opnsense-virtualbox-lab.md)

## 1. Purpose

Build confidence with basic Linux network and system-diagnostic commands before setting up and troubleshooting a virtual OPNsense firewall. The skills covered here are foundational for system administration, cybersecurity monitoring, and network troubleshooting.

This is a **personal, isolated virtual lab**. It is not a production network deployment, and this report does not contain credentials, host machine identifiers, or workplace information.

## 2. Lab environment

| Component | Description |
| --- | --- |
| Hypervisor | Oracle VM VirtualBox on a Windows laptop |
| Guest OS | Kali Linux (`Home Cybersecurity Lab` VM) |
| Shell | Kali terminal |
| Exercise focus | Interfaces, routes, connectivity, DNS, listening sockets, memory and storage |
| Recovery point | `Baseline - Linux Networking Exercise Complete` (pre-OPNsense practice snapshot) |

The VM's network configuration has evolved as later labs were completed. IP addressing quoted in **Section 5** reflects the later OPNsense training network, **not necessarily the earlier snapshot**.

## 3. Command practice checklist

The initial practice session used this command checklist. The table explains what each command measures and how to interpret it. **The exact command outputs from the initial session were not retained here**, so no unverified initial results are asserted.

| Command | What it examines | What to look for |
| --- | --- | --- |
| `ip -br address` | Network interfaces and assigned addresses | `UP` state; interface names such as `eth0`; IPv4 address and prefix |
| `ip route` | IPv4 routing table | Default route, outgoing interface, and locally connected networks |
| `ping -c 4 1.1.1.1` | Basic reachability to an external IP | Replies, round-trip time, and packet loss |
| `getent hosts github.com` | DNS/name lookup through the system resolver | At least one returned address; lookup failures suggest DNS problems |
| `ss -tuln` | Listening TCP and UDP sockets | Protocol, local address, and port; local exposure of services |
| `free -h` | Memory utilization | Available and used memory, including buffers/cache |
| `df -h` | Filesystem space | Used/available disk space and filesystem mount points |

### Commands to reproduce

Run these inside the Kali terminal on your own test VM:

```bash
ip -br address
ip route
ping -c 4 1.1.1.1
getent hosts github.com
ss -tuln
free -h
df -h
```

**Interpretation tips**

- An interface may be up yet have no usable IPv4 address. Check both link state and addressing.
- A working ping to an IP address does not establish that DNS works; test name resolution separately.
- `ip route` should show an appropriate default gateway when the VM is intended to reach external networks.
- `ss -tuln` reports local listening sockets, not a list of remote systems or confirmed external exposure.
- `free -h` and `df -h` are quick local resource checks, useful when a VM behaves unexpectedly.

## 4. Basic troubleshooting workflow

When a Linux VM cannot reach a website:

1. **Verify the interface:** `ip -br address` — is the intended adapter up and assigned an address?
2. **Verify the route:** `ip route` — does a default route exist, and does it use the intended interface?
3. **Test the local gateway:** `ping -c 3 <gateway-ip>` — can the VM reach its first hop?
4. **Test external IP reachability:** `ping -c 3 1.1.1.1` — can packets reach an external address?
5. **Test DNS separately:** `getent hosts github.com` — does a hostname resolve?
6. **Inspect services if needed:** `ss -tuln` — is the expected local service listening?

If the VM cannot ping a gateway, investigate the virtual adapter, its VirtualBox network mode, DHCP/static IP configuration, and subnet first. If an IP test works but a hostname fails, investigate DNS instead of changing firewall policies blindly.

> **Important:** Some systems intentionally block ICMP; a failed ping alone is not proof that all TCP/HTTPS connectivity is broken.

## 5. Verified follow-up: Kali attached to the OPNsense lab

During the later **OPNsense VirtualBox Firewall Lab** on October 17, 2026, we captured actual evidence that Kali could diagnose and use a functioning isolated network. These are **later follow-up observations**, not invented outputs for the initial command checklist.

| Test | Observed and verified result |
| --- | --- |
| Kali IPv4 interface (`ip -4 addr`) | `eth0` received `192.168.77.193/24` by DHCP |
| Routing (`ip route`) | Default gateway `192.168.77.1` via `eth0` |
| Gateway ping | `ping -c 3 192.168.77.1`: 3/3 replies, 0% loss |
| DNS + outbound connectivity | `ping -c 3 opnsense.org`: hostname resolved, 3/3 replies, 0% loss |
| HTTPS connectivity | `curl -I --max-time 10 https://example.com`: HTTP/2 `200` |
| Firewall rule test | ICMP to `1.1.1.1` blocked with rule enabled, then resumed after disabling it |

The `192.168.77.0/24` addresses are **disposable VirtualBox lab-only addresses**, retained because they explain how the exercise worked. DHCP addresses may change after a reboot or lease renewal.

Supporting images are included in the separate OPNsense report:

- [Kali DHCP address and route evidence](../../screenshots/laptop-practice/opnsense/04-kali-dhcp-and-route.png)
- [Gateway and hostname connectivity evidence](../../screenshots/laptop-practice/opnsense/05-kali-baseline-connectivity.png)
- [Firewall connectivity restoration evidence](../../screenshots/laptop-practice/opnsense/12-icmp-connectivity-restored.png)

For the detailed installation, firewall policy, logging, and rollback procedure, see [Lab 02 — OPNsense VirtualBox Firewall Lab](02-opnsense-virtualbox-lab.md).

## 6. Skills practiced and lessons learned

- Read Linux network-interface and IPv4 routing information.
- Distinguish link connectivity, local gateway reachability, external IP connectivity, and DNS resolution.
- Understand how DHCP leases and a default gateway allow a client to communicate through a firewall.
- Use basic Linux commands to inspect local network services and resource usage.
- Compare command-line observations to firewall-side logs when troubleshooting policy behavior.
- Protect sensitive information when preparing lab notes for a public repository.

## 7. Documentation and security notes

The original pre-OPNsense terminal output was not archived in this report; the **follow-up evidence above is independently verified from screenshots**. Avoid presenting the checklist as a historical command transcript. If more images from the initial practice are recovered, add only those that can be verified and sanitized.

Before sharing screenshots, remove or crop usernames, personally identifying browser content, physical-network identifiers, credentials, tokens, certificate private material, and unrelated logs. No passwords or configuration backups should be committed to GitHub.

**Next:** Continue to [Lab 02](02-opnsense-virtualbox-lab.md) for OPNsense firewall setup, rule creation, ICMP denial, logging, and connectivity restoration.
