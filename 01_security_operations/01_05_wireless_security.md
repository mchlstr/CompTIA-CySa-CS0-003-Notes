# Wireless Security

Wi-Fi remains one of the easiest attack surfaces in many environments — physical proximity is the only requirement. CySA+ tests recognition of wireless attacks and the encryption schemes that defend (or fail to defend) against them.

## Wireless encryption schemes

| Standard | Year | Encryption | Status |
|---|---|---|---|
| **WEP** | 1997 | RC4 + 24-bit IV | **Broken**. Crack in minutes. Never use. |
| **WPA** | 2003 | TKIP (RC4-based) | Stopgap. Deprecated. |
| **WPA2** | 2004 | AES-CCMP (Personal: PSK; Enterprise: 802.1X/EAP) | Standard for ~15 years; vulnerable to KRACK and offline PSK attacks. |
| **WPA3** | 2018 | AES-GCMP-256 (Enterprise) / SAE handshake (Personal) | Current standard. Forward secrecy, resists offline cracking. |

### Why each was broken
- **WEP** — IV reuse + weak RC4 key scheduling → key recoverable from enough captured packets.
- **WPA-TKIP** — RC4-based, susceptible to similar weaknesses.
- **WPA2-PSK** — 4-way handshake can be captured and brute-forced offline if PSK is weak. KRACK attack (2017) exploited handshake replay.
- **WPS (Wi-Fi Protected Setup)** — separate feature; brute-force the 8-digit PIN (Reaver attack). **Disable WPS.**

### Personal vs Enterprise
- **Personal (PSK)** — single shared password. If stolen / captured / cracked = network compromised.
- **Enterprise (802.1X)** — per-user authentication via RADIUS, often EAP-TLS (cert-based) or PEAP/EAP-MSCHAPv2 (password-based). Much stronger; revocation per user.

## Wireless attacks

### Passive

**Eavesdropping** — capture traffic.
- On unencrypted (open) Wi-Fi: trivial — see all plaintext.
- On WEP/WPA/WPA2 with the key known (or cracked): also see all decrypted.
- Tools: **airodump-ng**, **Wireshark** with monitor mode adapter, **Kismet**.

**Wardriving** — drive (or walk, or fly drone) around mapping wireless networks. Tools: **Kismet**, **WiGLE** (community DB).

### Active — handshake capture & PSK cracking
1. Capture the **WPA/WPA2 4-way handshake** between a client and AP.
2. If no client is connected, force one to reconnect via **deauthentication attack** (send a forged deauth frame).
3. Take the captured handshake offline; brute-force the PSK with **hashcat** or **aircrack-ng**.
4. With weak PSK, recovery is fast. With strong PSK (long, high-entropy), infeasible.

### Active — deauthentication / disassociation attack
- Send forged 802.11 deauth frames spoofing the AP's MAC, causing clients to disconnect.
- Used to: trigger reconnect (capture handshake), cause denial of service.
- Worked because management frames are unencrypted by default. **802.11w (Management Frame Protection)** mitigates; mandatory in WPA3.

### Active — Evil Twin
- Attacker stands up a rogue AP with the **same SSID** as the legitimate one (and often stronger signal).
- Client devices auto-connect (especially if they've connected to that SSID before).
- Attacker MITMs all traffic, captures credentials (especially via fake captive portal asking for "Wi-Fi password" / corporate creds).
- Tools: **airbase-ng**, **hostapd-mana**, **Wifiphisher**.

### Active — KARMA attack
- Devices probe for **previously-known networks** ("Are you here, MyHomeWiFi?").
- Attacker AP responds **affirmatively to every probe** ("Yes, I'm MyHomeWiFi") → client connects.
- Modern OSes (iOS, Android, Windows 10+) are mostly hardened against this.

### Active — KRACK (Key Reinstallation Attack, 2017)
- Targets the WPA/WPA2 4-way handshake by forcing reinstallation of an already-used encryption key.
- Allows decryption of some traffic and (in some cases) injection.
- Patched in client OS updates. Highlights why patching is required.

### Active — Dragonblood (2019, against WPA3)
- Side-channel and downgrade attacks against the SAE handshake in early WPA3.
- Largely addressed via firmware updates.

### Active — WPS PIN brute force (Reaver / Pixie Dust)
- WPS uses an 8-digit PIN, but the protocol effectively splits it so brute force requires only ~11,000 attempts.
- **Pixie Dust** attack (offline) reduces it to seconds against vulnerable chipsets.
- **Mitigation:** disable WPS.

### Bluetooth (related, often grouped)
- **Bluejacking** — sending unsolicited messages.
- **Bluesnarfing** — unauthorized access to data.
- **Bluebugging** — taking control of features.
- **BlueBorne** — RCE in older Bluetooth stacks.
- **Modern mitigations:** Secure Simple Pairing, low-power LE Secure Connections, app-level encryption.

## Detection (defender's view)

**Indicators of wireless attack:**
- **Multiple deauth frames** in capture — possible deauth flood / handshake harvesting.
- **Multiple APs with same SSID** but different BSSIDs (especially with stronger signal than legitimate APs) — possible evil twin.
- **Unknown clients connecting to corporate AP** — rogue device on network (covered by NAC).
- **Rogue APs broadcasting near corporate space** — wireless IDS detection.
- **Probe requests for unusual SSIDs** from corporate devices — possible compromised device.
- **Sudden brute-force-style WPS attempts** at the AP.

**Tools:**
- **Wireless IDS (WIDS) / WIPS** — purpose-built (Cisco WIPS, Aruba RFProtect, Mojo Networks, Hak5 dWall).
- **Kismet** — passive monitoring + intrusion detection.
- **Aruba/Cisco/Meraki controllers** — built-in rogue detection.

## Defenses

- **Use WPA3 where supported.** Otherwise WPA2-AES-CCMP (Personal with strong PSK, or Enterprise).
- **Disable WPS.**
- **Enable Management Frame Protection (802.11w / PMF).**
- **Hide guest from corporate** — separate SSIDs and VLANs; guest network out to internet only.
- **MAC filtering / NAC** — supplemental, not primary (MACs are spoofable).
- **Strong PSK** — long, high-entropy (or use Enterprise).
- **Enterprise 802.1X with EAP-TLS** for corporate devices — cert-based, no shared secret.
- **WIDS/WIPS** to detect rogue APs and active attacks.
- **Patch wireless infrastructure and clients** — KRACK and Dragonblood demonstrate why.
- **VPN over Wi-Fi** for sensitive traffic, especially on public/guest networks.
- **HTTPS everywhere + HSTS** — assume Wi-Fi may be hostile; encrypt at the application layer too.
- **Disable auto-connect to open networks** on managed devices.

## Exam tips

- **WEP = always wrong answer** for current security.
- **WPA2-PSK with weak password** = vulnerable to offline brute force after handshake capture.
- **Evil twin** = attacker AP impersonating a legitimate one.
- **Deauth attack** = used to force reconnect (often for handshake capture).
- **Reaver / WPS attack** = brute-force the WPS PIN (defense: disable WPS).
- **WPA3** = current best practice (SAE replaces PSK handshake → resists offline crack).
- If a scenario mentions Aircrack-ng or a `.cap` handshake file → think WPA/WPA2 PSK cracking.
- If it mentions capturing data on open Wi-Fi → no crypto needed, just monitor mode.

---

← Back: [01_04 Network Architecture](01_04_network_architecture.md) — Next: [01_06 DNS Security](01_06_dns_security.md) →

## Related

**Internal:**
- [01_04 Network architecture](01_04_network_architecture.md) — broader networking context
- [01_10 Malicious activity detection](01_10_malicious_activity_detection.md) — including rogue devices
- [05_tools — Aircrack-ng](../05_tools/Aircrack-ng%20Suite.md)
- [05_tools — Reaver](../05_tools/Reaver.md)

**External:**
- [Wi-Fi Alliance — WPA3](https://www.wi-fi.org/discover-wi-fi/security)
- [KRACK Attacks](https://www.krackattacks.com/)
- [Aircrack-ng documentation](https://www.aircrack-ng.org/documentation.html)
