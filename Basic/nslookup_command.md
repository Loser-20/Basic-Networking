## nslookup (Windows)

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
