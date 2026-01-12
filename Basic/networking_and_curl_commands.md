# Windows Networking & Curl Commands Guide

## 1. `ipconfig /flushdns`

### **What it does**
Clears the local DNS resolver cache so the system resolves domain names anew.

### **When to use**
- After DNS changes or migration.
- Intermittent name resolution issues or stale entries.

### **Command**
```bash
   ipconfig /flushdns
```

### **Expected Output**
```
Windows IP Configuration
Successfully flushed the DNS Resolver Cache.
```

### **Notes**
- May require elevated prompt depending on DNS Client service permissions.
- Cache exists only if the DNS Client service is running.

---

## 2. `ipconfig /displaydns`

### **What it does**
Displays current DNS resolver cache entries (A/AAAA, CNAME, etc.).

### **When to use**
- Verify cached IP for a hostname.
- Check TTL remaining.
- Detect wrong/stale records (then flush).

### **Command**
```bash
   ipconfig /displaydns
```

### **Example Output (Truncated)**
```
example.com
Record Name: example.com
Record Type: 1
TTL: 300
A (Host) Record: 93.184.216.34
```

### **Tips**
- If empty, run `nslookup` first to trigger a lookup, then `displaydns`.
- Compare with authoritative DNS using `nslookup` or `dig`.

---

## 3. `arp -a -all`

### **What it does**
Shows ARP table (IPv4 IP ↔ MAC mappings) known on local subnet.

### **When to use**
- Troubleshoot LAN reachability/duplicates.
- Verify gateway/device MAC.

### **Command**
```bash
   arp -a -all
```

### **Example Output (Truncated)**
```
Interface: 192.168.1.10 --- 0x12
Internet Address    Physical Address    Type
192.168.1.1         00-11-22-33-44-55   dynamic
```

### **Notes**
- If empty, ping a local IP to populate, then re-run.
- `-all` may behave like default; use just `arp -a` if needed.

---
## 4. `nslookup (Windows)`

### **What it does**
Queries DNS servers to resolve domain names to IP addresses (and vice versa). Useful for troubleshooting DNS issues.

### **When to use**
- Verify DNS resolution for a hostname.
- Check authoritative DNS records.
- Diagnose DNS server problems.

### **Basic Command**
```bash
   nslookup <hostname>
```

### **Examples**

#### **Resolve a Domain**
```bash
nslookup example.com
```
**Output (simplified):**
```
Server:  dns.corp.local
Address: 192.168.1.1

Name:    example.com
Address: 93.184.216.34
```

#### **Reverse Lookup (IP to Hostname)**
```bash
   nslookup 93.184.216.34
```

#### **Specify DNS Server**
```bash
   nslookup example.com 8.8.8.8
```

#### **Interactive Mode**
```bash
   nslookup
   > set type=MX
   > example.com
```

### **Tips**
- Use `set type=ANY` or `set type=MX` for specific record types.
- Compare results with `ipconfig /displaydns` for cached vs authoritative data.
- For advanced checks, use `dig` on Linux/macOS.
---
## 5. `curl` Deep Dive

### **What it does**
`curl` is a CLI tool for HTTP(S)/FTP and more.  
Use `curl --help all` for comprehensive help.

### **Basic Syntax**
```bash
   curl [options] <URL>
```

### **Common Flags**
- `-i` include response headers
- `-v` verbose connection/TLS details
- `-L` follow redirects
- `-H` set header
- `-d` send data (POST by default)
- `-o/-O` save output

---

### **Examples**

#### **GET Request**
```bash
   curl -i https://api.example.com/health
```

#### **Follow Redirects & Save**
```bash
   curl -L https://example.com/file.zip -o file.zip
```

#### **JSON POST**
```bash
   curl -X POST https://api.example.com/items   -H "Content-Type: application/json"   -d '{"name":"switch","qty":2}'
```

#### **Auth (Bearer Token)**
```bash
   curl -H "Authorization: Bearer $TOKEN" https://api.example.com/me
```

#### **TLS Diagnostics**
```bash
   curl -v --tlsv1.2 https://secure.example.com
```

#### **Timing Metrics**
```bash
   curl -w "dns:%{time_namelookup} connect:%{time_connect} total:%{time_total}\n"   -s -o /dev/null https://site.example
```

#### **Retries**
```bash
   curl --retry 5 --retry-connrefused --retry-delay 2 https://unstable.example.com
```

#### **Proxy**
```bash
   curl -x http://proxy.corp.local:8080 https://api.example.com/data
```

#### **Cookies**
```bash
   curl -c cookies.txt https://example.com/login
   curl -b cookies.txt https://example.com/dashboard
```

### **Windows Note**
In PowerShell, `curl` may be an alias for `Invoke-WebRequest`; use `curl.exe` for real curl behavior.
