# OpenTAKServer — Technical Design: EUD Handler (TCP/SSL/UDP Connections)

| | |
|---|---|
| **Document ID** | TO-006 |
| **Version** | 0.2 |
| **Status** | DRAFT |
| **Date** | 2026-05-28 |
| **Author** | DrDocu Agent |
| **Owner** | ML6 |
| **Related TLD/LD** | TLD-001 / LD-001 |
| **Classification** | Internal |

---

## 1. Purpose

This document describes the configuration of the EUD (End User Device) Handler in OpenTAKServer. The EUD Handler is responsible for receiving and processing CoT (Cursor on Target) XML messages from TAK clients via three transport protocols: TCP on port 8088 (unencrypted), SSL/TLS on port 8089 (encrypted with mutual authentication), and UDP on port 8087 (broadcast). This document covers port configuration, firewall settings, network interface binding, and verification methods.

---

## 2. Applicability

| Environment | Version | Valid From |
|---|---|---|
| Ubuntu 22.04 LTS | OTS latest (PyPI) | 2026-05-28 |
| Ubuntu 24.04 LTS | OTS latest (PyPI) | 2026-05-28 |
| ATAK / WinTAK / iTAK | Any current version | 2026-05-28 |

---

## 3. Reference Documents

| ID | Title | Location |
|---|---|---|
| REF-001 | OpenTAKServer GitHub Repository | https://github.com/brian7704/OpenTAKServer |
| REF-002 | OTS EUD Handler Documentation | https://docs.opentakserver.io |
| REF-003 | TO-005 | Certificate Authority Configuration |
| REF-004 | CoT XML Schema Specification | v2.0 |

---

## 4. Prerequisites

- [ ] OpenTAKServer installed and running
- [ ] For SSL connections: CA and client certificates configured (see TO-005)
- [ ] Ports 8087 (UDP), 8088 (TCP), 8089 (SSL) open in firewall
- [ ] TAK client devices reachable on the same network or via port forwarding
- [ ] `ufw` or equivalent firewall management tool available

---

## 5. Procedure

### Step 1 — Open required firewall ports

**Action:** Allow the EUD Handler ports through the firewall.

```bash
sudo ufw allow 8087/udp comment "OTS UDP CoT"
sudo ufw allow 8088/tcp comment "OTS TCP CoT (plaintext)"
sudo ufw allow 8089/tcp comment "OTS SSL CoT (encrypted)"
sudo ufw reload
```

**Expected Result:** All three ports open and accepting connections.

**Verification:** `sudo ufw status` shows rules for 8087/udp, 8088/tcp, 8089/tcp as `ALLOW`.

---

### Step 2 — Verify EUD Handler configuration in OTS config

**Action:** Confirm the EUD Handler port settings in the OTS configuration file.

```bash
sudo cat /etc/opentakserver/config.yml | grep -E "TCP|UDP|SSL|PORT|EUD"
```

Expected relevant fields:

```yaml
OTS_TCP_STREAMING_PORT: 8088
OTS_SSL_STREAMING_PORT: 8089
OTS_UDP_PORT: 8087
OTS_BIND_ADDRESS: "0.0.0.0"
```

**Expected Result:** Ports match the opened firewall rules.

**Verification:** Values confirmed in the config file.

---

### Step 3 — Restart OTS and verify listening ports

**Action:** Restart OTS and confirm it is listening on all three ports.

```bash
sudo systemctl restart ots
sleep 5
sudo ss -tlnup | grep -E "8087|8088|8089"
```

**Expected Result:** OTS process listening on ports 8087 (UDP), 8088 (TCP), and 8089 (TCP).

**Verification:** `ss` output shows `LISTEN` on all three ports bound to `0.0.0.0`.

---

### Step 4 — Connect a TAK client via TCP (plaintext)

**Action:** Configure ATAK to connect to OTS via TCP.

In ATAK: *Settings → Network → Server Connections → Add Server*
- Protocol: TCP
- Address: `<server IP>`
- Port: `8088`

**Expected Result:** ATAK connects; device appears in OTS WebUI → EUDs.

**Verification:** OTS log shows `New connection from <client IP>:8088`; WebUI lists the device.

---

### Step 5 — Connect a TAK client via SSL

**Action:** Configure ATAK to connect to OTS via SSL with client certificate.

In ATAK: *Settings → Network → Server Connections → Add Server*
- Protocol: SSL
- Address: `<server IP>`
- Port: `8089`
- Certificate: select the enrolled `.p12` certificate (see TO-005)

**Expected Result:** Mutual TLS handshake succeeds; device appears in OTS WebUI → EUDs.

**Verification:** OTS log shows SSL connection accepted; device listed with SSL indicator in WebUI.

---

### Step 6 — Test UDP broadcast

**Action:** Send a UDP CoT message to verify broadcast reception.

```bash
# Simple UDP test from another machine on the same network
echo '<event version="2.0" uid="test-udp" type="a-f-G-U-C" time="2026-05-28T10:00:00Z" start="2026-05-28T10:00:00Z" stale="2026-05-28T10:10:00Z" how="m-g"><point lat="52.37" lon="4.89" hae="0" ce="9999999" le="9999999"/></event>' | nc -u <server IP> 8087
```

**Expected Result:** Message received by OTS and visible in the CoT log.

**Verification:** OTS WebUI → CoT shows the test message; `uid=test-udp` present in the database.

---

## 6. Verification and Acceptance Criteria

| Criterion | Verification Method | Acceptance Value |
|---|---|---|
| Port 8087/UDP open | `sudo ufw status` | `ALLOW` |
| Port 8088/TCP open | `sudo ufw status` | `ALLOW` |
| Port 8089/TCP open | `sudo ufw status` | `ALLOW` |
| OTS listening on all ports | `ss -tlnup \| grep 808` | All three ports in `LISTEN` state |
| TCP client connects | ATAK TCP connection + WebUI | Device appears in EUDs |
| SSL client connects | ATAK SSL connection + WebUI | Device appears in EUDs with cert |
| UDP message received | nc UDP test + WebUI | CoT message visible |

---

## 7. Rollback Procedure

1. Stop OTS: `sudo systemctl stop ots`
2. Close firewall ports if needed:
   ```bash
   sudo ufw delete allow 8087/udp
   sudo ufw delete allow 8088/tcp
   sudo ufw delete allow 8089/tcp
   sudo ufw reload
   ```
3. Revert any port changes in `config.yml` to previous values.
4. Restart OTS: `sudo systemctl start ots`

---

## 8. Known Issues and Workarounds

| Issue | Circumstance | Workaround |
|---|---|---|
| TAK client cannot connect | Firewall blocking ports | Verify `ufw status`; check cloud security group if on a VM |
| SSL connection rejected | Client cert not signed by OTS CA | Re-enrol device via Marti API (see TO-005) |
| UDP messages not received behind NAT | Client on different network | Use TCP or SSL instead; configure port forwarding for UDP |
| Multiple clients disconnect simultaneously | gevent worker overload | Increase `OTS_COT_PARSER_PROCESSES` in config |
| Port 8088 in use by another process | Pre-existing service conflict | Check `ss -tlnp \| grep 8088`; stop conflicting service or change OTS port |

---

## 9. Change History

| Version | Date | Author | Change |
|---|---|---|---|
| 0.1 | 2026-05-28 | DrDocu Agent | Initial document |
| 0.2 | 2026-05-28 | DrDocu Agent | Translated to English, full procedure added, restructured to new TO template |
