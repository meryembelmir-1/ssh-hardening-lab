| Indicator | Before (default install) | fail2ban added | After (hardened + bastion) |
|---|---|---|---|
| Authentication methods offered | `publickey,password` | `publickey,password` | `publickey` |
| Root login | Password login refused (Ubuntu default `prohibit-password`), password prompt still shown | Same as before | Refused (`PermitRootLogin no`, `AllowUsers`), no password prompt |
| Brute force (Hydra) | Password found in 7 s | Connection refused: client IP already banned (without the ban, the password can still be found before fail2ban triggers) | Impossible: password authentication not accepted |
| Banning of repeated failures | None | fail2ban active | fail2ban active |
| ssh-audit: failures / warnings | 6 / 9 | Not measured (sshd unchanged) | 0 / 0 |
| Weak algorithms | `ecdh-sha2-nistp256/384/521`, `ecdsa-sha2-nistp256`, `hmac-sha1`, `hmac-sha1-etm` (fail); `diffie-hellman-group14-sha256`, `umac-64`, `umac-64-etm`, `umac-128`, `hmac-sha2-256`, `hmac-sha2-512` (warn) | Not measured (sshd unchanged) | None |
| Logging | Default level (`INFO`) | Default level (`INFO`) | `VERBOSE` (login/logout activity and key fingerprints) |
| Internal server exposure | N/A | N/A | Not directly reachable, filtered by `ufw` |
| Forwarding and agent | Allowed by default | Allowed by default | Disabled (`AllowTcpForwarding no`, `AllowAgentForwarding no`, `X11Forwarding no`); bastion allows only `local` forwarding, limited by `PermitOpen` |
