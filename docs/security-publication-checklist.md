# Public Repository Evidence and Sanitization Checklist

This repository is public. Keep operational evidence useful without publishing credentials or secrets.

## Before adding any screenshot or log

- [ ] Crop away unrelated browser tabs, bookmarks, emails, filenames, and account identifiers.
- [ ] Redact session cookies, authorization tokens, API keys, OTP seeds, TOTP QR codes, recovery codes, passwords, and private keys **before upload**.
- [ ] Redact network identifiers where they reveal private topology: host IPs, MAC addresses, public IPs, client identifiers, SSIDs and device serial numbers.
- [ ] Remove personally identifying account names and email addresses not needed for the learning objective.
- [ ] Prefer short, manually transcribed **sanitized** log excerpts and generic RFC1918 placeholders where possible.
- [ ] Review image corners, browser chrome, terminal scrollback and embedded tooltips; sensitive content may be outside the obvious focal point.
- [ ] Confirm no encrypted or unencrypted firewall configuration XML, Proxmox archives, passwords or private backup material are included.
- [ ] Check the published file itself, not just the local edited preview.

## If a secret is accidentally exposed

1. Consider the exposed token/seed compromised, even if the screenshot is later edited.
2. Rotate or revoke it in the original service.
3. Replace the corresponding authenticator enrollment or credentials.
4. Remove public copies and review repository history; editing a file alone does not erase earlier Git commits.
5. Verify the new credential, document the remediation without publishing its replacement.

## Lab evidence policy

- Keep raw OPNsense packet captures and unredacted configuration exports in private storage.
- Publish only generalized test outcome tables or screenshots sanitized from the originals.
- Do not publish real HTTP verbose-output headers containing session cookies.
- Date evidence, state what actually passed/failed, and mark suspected causes as hypotheses rather than verified defects.

**October 24, 2026:** The root TOTP secret was rotated after accidental exposure in a private troubleshooting screenshot. No authenticators, backup XML files or raw screenshot evidence were added in this documentation update.
