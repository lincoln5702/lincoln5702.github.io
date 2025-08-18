### **How to resolve domain issues in windows?**

Resolving DNS issue requires steps by steps understanding of all the DNS processes and DNS records in windows system. Fixing DNS issue usually requires resetting the dns cache, reconfiguring the DNS resolver and more.

How to view dns cache in Windows:
 ````
 ipconfig /displaydns
`````

* This command lists all cached DNS records stored on your system.
- It includes entries for recently visited websites.

##### Where is the Cache Stored?

- The DNS cache is stored in memory, not in a file.
- Windows manages it through the **DNS Client Service (`dnscache`)**.
- When you flush the cache (`ipconfig /flushdns`), it gets cleared from RAM.

#### Common DNS fixes

**1. Restart Your Router and Computer**
**How it helps**:
- Clears temporary network glitches.
- Re-establishes connection to your ISP’s DNS servers.
- Resets the IP address assignment if there’s a conflict.

**2. Flush DNS Cache**
**How it helps**:
- Clears outdated or incorrect DNS records stored in your system.
- Ensures that your computer fetches fresh DNS data from servers.

 **3. Reset Network Settings**
````
netsh int ip reset
netsh winsock reset
````
**How it helps**:
- Resets all network-related settings to default.
- Fixes corrupted network configurations that might be blocking DNS resolution.
- The `winsock reset` command restores default settings for handling internet connections.

 **4. Restart DNS Client Service**
````
net stop dnscache
net start dnscache
````
**How it helps**:
- The DNS Client Service stores DNS lookups for faster browsing.
- If it gets stuck or corrupt, restarting the service forces a refresh, solving connectivity issues.

#### **How the `netsh` Command Works**

**`netsh` (Network Shell)** is a command-line utility that allows you to manage network settings in Windows.

##### **Key Features of `netsh`**

- Configures network interfaces, IP addresses, and firewall settings.
- Works in interactive mode (`netsh` alone) or directly executes commands.
- Helps troubleshoot and reset network configurations.

#### **What is `winsock reset`?**

The `winsock reset` command resets the **Windows Sockets (Winsock) Catalog**, which controls how Windows handles network requests.
#### **How it Works:**

- **Winsock (Windows Sockets API)** manages communication between Windows programs and network services.
- Sometimes, malware or software changes can corrupt Winsock settings, causing:
    - No internet access.
    - DNS resolution failures.
    - Issues with network-dependent applications (browsers, email clients).
- Resetting Winsock **removes all custom configurations** and restores default settings.

#### **When to Use It:**

- If you see errors like **"Limited connectivity"** or **"Unable to resolve DNS"**.
- If specific apps can’t connect to the internet.
- After removing malware that may have modified network settings.
### **`netsh int ip reset` Command Explained**

```
netsh int ip reset
```
#### **How It Works:**

This command resets **TCP/IP settings** to their default state. It:

1. **Deletes and reinstalls the TCP/IP stack** in Windows.
2. **Removes all custom network configurations** (like static IPs or DNS settings).
3. **Fixes issues caused by corrupted TCP/IP settings**, such as:
    - Slow or no internet connection.
    - "No valid IP configuration" errors.
    - Issues after a network driver update.

#### **What Exactly Does It Reset?**

- **Registry keys related to TCP/IP**:  
   The command modifies and resets registry entries under:
```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\DHCP\Parameters\
```
- **Resets all network adapters**:

- If any adapter is misconfigured, this resets it.
- To check adapters before and after reset:
```
netsh interface show interface
```
- **Clears DHCP configurations**:
    - If your system is failing to get an IP from the router, this helps.
- **Removes custom DNS settings**:
    - If an incorrect DNS was set, this restores the default automatic setting.

#### **When to Use It:**

- If **changing DNS servers** doesn’t fix connection issues.
- If websites **don’t resolve, even after a cache flush**.
- If your computer has **"Unidentified Network"** errors.

#### **After Running It, What Should You Do?**

1. Restart your computer.
2. If using static IP/DNS settings, reconfigure them (it resets them).
3. Test your internet connection.
---
### **How to resolve domain issues in Ubuntu?**

#### **1. Restart Network**
```
sudo systemctl restart NetworkManager
```

**How it helps**:
- Refreshes network connections.
- Fixes issues caused by misconfigured network settings.
- Useful when switching from wired to wireless or vice versa.

#### **2. Flush DNS Cache**
```
sudo systemd-resolve --flush-caches
sudo resolvectl flush-caches
```
**How it helps**:
- Clears stored DNS records that might be outdated.
- Forces the system to request fresh DNS data.
- Fixes problems where websites don’t load after a domain's IP address changes.
#### **3. Change DNS Server**
**How it helps**:
- Works the same as in Windows—switching to a public DNS server can improve speed, reliability, and security.
- If your ISP’s DNS is unreliable, setting `8.8.8.8` (Google) or `1.1.1.1` (Cloudflare) ensures a better connection.
#### **4. Use NetworkManager to Set DNS**
```
nm-connection-editor
```
**How it helps**:

- NetworkManager controls network connections in many Linux distributions.
- Manually setting a DNS server ensures that even after a reboot, your system uses a reliable DNS.

#### 5. Restart Systemd Resolver
```
sudo systemctl restart systemd-resolved
```
**How it helps**:

- `systemd-resolved` is a service that manages DNS queries in modern Ubuntu versions.
- Restarting it fixes situations where it's stuck or misconfigured.
- Helps when DNS resolution is slow or failing randomly.
