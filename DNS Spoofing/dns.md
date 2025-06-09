
# DNS Spoofing Attack:

## Table of Contents

1. [Introduction](#introduction)
2. [Background](#background)

   * [DNS Functionality](#dns-functionality)
   * [DNS Spoofing (Cache Poisoning)](#dns-spoofing-cache-poisoning)
3. [Threat Model](#threat-model)
4. [Attack Walkthrough](#attack-walkthrough)

   * [Environment Setup](#environment-setup)
   * [Host Configuration](#host-configuration)
   * [Fake Web Server Deployment](#fake-web-server-deployment)
   * [Ettercap Configuration](#ettercap-configuration)
   * [Launching the Attack](#launching-the-attack)
5. [Demonstration Results](#demonstration-results)
6. [Defense Mechanisms](#defense-mechanisms)
7. [Conclusion](#conclusion)

---

## Introduction

DNS spoofing, also known as DNS cache poisoning, is a network-based attack where an attacker forges DNS responses to redirect legitimate traffic to a malicious server. This report details a practical demonstration of DNS spoofing using Kali Linux and Ettercap, illustrating how an attacker can intercept and manipulate DNS queries in a local network.

## Background

### DNS Functionality

* Clients resolve human-readable domain names (e.g., `kwtrain.com`) into IP addresses via DNS queries.
* A resolver (DNS server) returns the corresponding IP (e.g., `203.0.113.54`), which clients cache locally for subsequent lookups.

### DNS Spoofing (Cache Poisoning)

* Attackers inject false DNS records into a resolver’s cache, causing victims to connect to malicious IPs.
* Methods include:

  * Rogue DHCP servers providing malicious DNS settings.
  * Altering the victim's `/etc/hosts` file.
  * Man-in-the-middle attacks capturing and spoofing DNS responses.

## Threat Model

* **Attacker Capabilities**: Access to the same local network, ability to perform ARP poisoning, and run packet-capture/spoofing tools (e.g., Ettercap).
* **Victim**: A laptop configured to use the network’s default gateway (`192.168.1.1`) as its DNS server.
* **Goal**: Redirect DNS queries for `kwtrain.com` to an attacker-controlled web server at `192.168.1.102`.

## Attack Walkthrough

### Environment Setup

* **Attacker Machine (Kali Linux)**

  * IP address: `192.168.1.102`
  * Tools: Apache2, Ettercap

* **Victim Machine (Laptop)**

  * IP address: `192.168.1.105`
  * Default gateway (and DNS server): `192.168.1.1`

![schema](../blob/dns/Screenshot%202025-06-06%20153518.png)

### Host Configuration

1. On the Kali box, verify network interface:

   ```bash
   ifconfig
   # Confirm IP: 192.168.1.102
   ```
   ![schema](../blob/dns/Screenshot%202025-06-06%20153555.png)
2. On the victim laptop, verify network settings:

   ```bash
   ifconfig
   # Confirm IP: 192.168.1.105
   ```


### Fake Web Server Deployment

1. Start Apache on Kali:

   ```bash
   sudo service apache2 start
   ```
2. Verify default page from victim:

   * Browse to `http://192.168.1.102` → Apache default page.
   ![schema](../blob/dns/Screenshot%202025-06-06%20153432.png)
3. Replace `index.html` with a simple page:

   ```bash
   cd /var/www/html
   sudo mv index.html index-old.html
   sudo vi index.html
   # Insert: <b>evil site!</b>
   ```
4. Confirm on victim:

   * Reload `http://192.168.1.102` → displays “evil site!”

### Ettercap Configuration

1. Edit `/etc/ettercap/etter.conf`:

   * Set `uid = 0` and `gid = 0`.
   * Uncomment OS-specific sections for Linux (IPv4/IPv6).
   ![schema](../blob/dns/Screenshot%202025-06-06%20154532.png)
   ![schema](../blob/dns/Screenshot%202025-06-06%20154602.png)

2. Edit `/etc/ettercap/etter.dns`:

   ```text
   # Add entries at bottom:
   kwtrain.com    A    192.168.1.102
   *.kwtrain.com  A    192.168.1.102
   www.kwtrain.com PTR  192.168.1.102
   ```
   ![schema](../blob/dns/Screenshot%202025-06-06%20154629.png)

### Launching the Attack

1. Start Ettercap in graphical mode:

   ```bash
   sudo ettercap -G
   ```
2. Specify targets:

   * Target 1: Victim laptop (`192.168.1.105`)
   * Target 2: Default gateway (`192.168.1.1`)
   ![schema](../blob/dns/Screenshot%202025-06-06%20154444.png)
3. Enable ARP poisoning:

   * Navigate: `Mitm` → `ARP poisoning` → Select “Sniff remote connections”.
4. Enable DNS spoofing plugin:

   * `Plugins` → `Manage Plugins` → Double-click `dns_spoof`.
   ![schema](../blob/dns/Screenshot%202025-06-06%20154413.png)
5. On victim, browse to `http://kwtrain.com`:

   * Request is intercepted, spoofed DNS response returns `192.168.1.102`.
   * Victim sees “evil site!” page.

## Demonstration Results

* DNS query for `kwtrain.com` on victim was resolved to attacker’s server IP (`192.168.1.102`).
* Web traffic redirected to fake site, capturing credentials or data if entered.

## Defense Mechanisms

To mitigate DNS spoofing attacks:

* **DHCP Snooping**: Prevent rogue DHCP servers by validating DHCP messages.
* **Dynamic ARP Inspection (DAI)**: Block ARP poisoning by verifying ARP packet validity.
* **Port Security**: Restrict MAC addresses on switch ports to known devices.
* **Use of HTTPS / TLS**: Certificate validation prevents hosting look-alike HTTPS sites without valid certificates.
* **DNSSEC**: DNS Security Extensions ensure integrity of DNS records.

## Conclusion

DNS spoofing remains a critical threat that can facilitate credential theft and data breaches. By understanding the attack flow—from ARP poisoning to DNS cache poisoning—and implementing layered defenses such as DHCP snooping, DAI, and DNSSEC, organizations can significantly reduce the risk of such attacks.

---