# OPNsense Web GUI MFA Hardening and HTTPS Troubleshooting

**Period:** October 24, 2026  
**System:** OPNsense 26.7 virtual firewall on Proxmox  
**Outcome:** Web GUI TOTP enforced and validated for two administrator accounts.

## Objective

Implement time-based one-time password (TOTP) MFA for the firewall's web administration portal without disrupting the established lab segmentation policy.

## MFA implementation

1. Defined a local TOTP authentication server in **System → Access → Servers**.
2. Enrolled TOTP seeds for the existing `root` administrator and a separate `labadmin` administrator.
3. Rotated the `root` TOTP seed after the initial seed was accidentally shown in a screenshot; the exposed seed is **not** the currently enrolled seed.
4. Confirmed both accounts could authenticate in **System → Access → Tester** against the TOTP server.
5. Set **System → Settings → Administration → Authentication → Server** to **`Local-TOTP-MFA` only**; saved, reloaded the page, and confirmed the selection persisted.
6. Tested an independent browser login with password only and with TOTP+password.

For this configured local TOTP workflow, the current six-digit code is entered **immediately before** the user's password in the single password field, with no separator. Do not publish example real tokens or password material.

## Acceptance criteria

| Security control | Test | Result |
|---|---|---|
| Secondary administrator | Password-only login after persisted server selection | REJECTED |
| Secondary administrator | TOTP code + password via fresh Web GUI login | ACCEPTED |
| Root administrator | TOTP code + password via fresh Web GUI login | ACCEPTED |
| Authenticator and firewall | Clock verification via UTC checks | No apparent clock discrepancy |
| Web GUI configuration | Authentication server after save/reload | `Local-TOTP-MFA` retained |

**Scope:** Password-only rejection was explicitly verified for the secondary administrator. This does not claim a separate password-only rejection test for every account.

## Failure investigation: MFA configured but not enforced

**Symptom:** TOTP tests passed within the Authentication Tester, yet a password-only Web GUI login still succeeded and a code+password Web GUI attempt failed.

**Cause found:** The Administration page's **Server** setting reverted to or remained **Nothing selected**. An unset selection allowed the default local-database authentication path.

**Correction:** Explicitly select `Local-TOTP-MFA`, save, reload, and confirm the selection is still present. Only after verifying persistence did the fresh-session password-only test fail and the TOTP+password test succeed.

**Lesson learned:** Passing a backend authentication test does not establish that the actual Web GUI is enforcing MFA. Test both **negative** and **positive** login paths after saving and reloading settings.

## Failure investigation: temporary HTTPS management timeout

During the configuration period, HTTPS access from the Debian lab client temporarily timed out, even though ICMP to the firewall LAN address continued to work.

| Diagnostic | Observation | What it established |
|---|---|---|
| ICMP from Debian | 3/3 replies | Basic lab-LAN reachability |
| TCP 443 listener | `lighttpd` listening on IPv4 and IPv6 | Listener process existed |
| Loopback HTTPS | HTTP 403 for local HEAD request | Web service processed a local request; 403 alone did not establish failure |
| Interface capture | Incoming SYNs observed on LAN vNIC | Client traffic reached the firewall interface |
| PF log metadata | `pass in` recorded for an HTTPS SYN | Logged rule decision was pass, not block |
| SYN-cookie check | Mode `never`, inactive | Ruled out SYN-cookie-always hypothesis |
| Route lookup | Return route to lab client via LAN vNIC | Ordinary route was correct |
| PF state inspection | Web GUI connection observed in `TIME_WAIT` | At least one TCP session advanced beyond SYN |
| Final verbose HTTPS request | TLS 1.3 negotiation and HTTP/2 **200 OK** | HTTPS service was reachable again |

The original timeout's precise cause **was not established**. Do not describe it as a confirmed firewall bug or claim that MFA caused it. No blanket firewall disablement or factory reset was needed.

### Read-only investigation commands

From the OPNsense shell (replace placeholders with local test addresses where applicable):

```sh
sockstat -l | grep ':443'
curl -k -I --connect-timeout 5 --max-time 10 https://127.0.0.1
route -n get <LAB_CLIENT_IP>
pfctl -vsi | grep -i -A3 syncookie
pfctl -ss | grep <LAB_CLIENT_IP>
tcpdump -nei pflog0 'host <LAB_CLIENT_IP> and tcp port 443'
```

From Debian:

```sh
ping -c 3 <FIREWALL_LAN_IP>
curl -vk --connect-timeout 5 --max-time 15 https://<FIREWALL_LAN_IP>/ -o /dev/null
```

**Security note:** `curl -vk` exposes HTTP headers and can include live session cookies. Use it only when necessary, and redact all session identifiers, credentials, OTP seeds, QR codes and device identifiers before publishing evidence. `-k` skips certificate verification and was used only for controlled troubleshooting of the lab's self-signed certificate.

## Remaining controls

- Export a current configuration backup and verify encryption/restoreability in private storage.
- Consider issuing a trusted local management certificate and removing the need for TLS bypass during routine checks.
- Review account privileges, session timeouts, logs, and host patching periodically.
- Verify each new authentication or firewall change with both allowed and denied test cases.
