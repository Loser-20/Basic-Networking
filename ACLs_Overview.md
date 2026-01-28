
# **Access Control Lists (ACLs) – Detailed Overview**

> *Access Control Lists (ACLs) are essential components of network security, used to regulate traffic flow by permitting or denying packets based on defined conditions.*

---

## 🔷 **What Are ACLs?**
Access Control Lists (ACLs) are rule‑based filtering mechanisms applied on routers, firewalls, and Layer 3 switches. They enhance network security and traffic control by evaluating packets against predefined rules.

ACLs can:
- **Permit or deny** traffic
- Control **packet forwarding**
- Apply **QoS policies**
- Restrict **remote access**
- Define **NAT rules** (when used with route‑maps)

There are **two primary types**:
1. **Standard ACLs**
2. **Extended ACLs**

---

# ✳️ **Standard ACLs**

## **📝 Functionality**
Standard ACLs filter traffic based **only on the source IP address**.

They *do not* evaluate:
- Destination IP  
- Protocol type  
- Port numbers  

### Example Logic:
```
access-list 10 permit 192.168.10.0 0.0.0.255
access-list 10 deny any
```

---

## **🔢 Numbering Range**
- **1 – 99**
- **1300 – 1999** (expanded range)

---

## **📍 Placement**
> *Placed close to the **destination** of the traffic*  
(to avoid accidentally dropping other traffic from the same source)

---

## **📘 Typical Use Cases**
- Limiting access to a server from specific subnets  
- Simple internal network segmentation  
- Restricting management access (e.g., allowing only IT subnet)

---

## **⚠️ Limitations**
- No protocol or port filtering  
- No destination-based control  
- Low granularity → May block unintended traffic  

---

# ✳️ **Extended ACLs**

## **📝 Functionality**
Extended ACLs provide **fine‑grained control** by filtering based on:

| Criteria | Examples |
|---------|----------|
| **Source & Destination IP** | 10.0.0.5 → 10.0.0.10 |
| **Protocols** | TCP, UDP, ICMP, IP, GRE |
| **Ports** | HTTP(80), SSH(22), DNS(53) |
| **Flags** | TCP ACK, SYN |

### Example Logic:
```
access-list 101 deny tcp 192.168.1.0 0.0.0.255 any eq 80
access-list 101 permit ip any any
```

---

## **🔢 Numbering Range**
- **100 – 199**
- **2000 – 2699** (expanded range)

---

## **📍 Placement**
> *Placed close to the **source** of the traffic*  
(to block unwanted traffic early and save bandwidth)

---

## **📘 Use Cases**
- Allow only HTTP traffic to a DMZ server  
- Restrict SSH access to network devices  
- Block ICMP (ping) from specific external hosts  
- Filter application-specific traffic (FTP, DNS, RDP, etc.)

---

## ⭐ **Advantages**
- Highly flexible  
- Supports port-level filtering  
- Suitable for enterprise-grade layered security  
- Better support for **complex security policies**

---

# 🔧 **Additional Technical Points (Enhanced Section)**

### **1. ACL Processing Order**
- ACLs are processed **top‑to‑bottom**.
- First match is applied immediately.
- There is an **implicit `deny any`** at the end of every ACL.

### **2. Wildcard Masks**
ACLs rely on wildcard masks (inverse of subnet masks):
```
255.255.255.0  → wildcard: 0.0.0.255
```

### **3. ACLs & Security Zones**
Used in:
- Firewalls
- Router edge interfaces
- VPN tunnels
- DMZ segmentations

### **4. Named ACLs**
```
ip access-list extended BLOCK_WEBSITES
 deny tcp any any eq 80
 permit ip any any
```

### **5. ACL Logging**
```
deny tcp any any eq 23 log
```

### **6. ACL Optimization**
```
access-list 101 remark Permit HTTPS from branch to DC
```

### **7. Applying ACLs to Interfaces**
```
interface GigabitEthernet0/0
 ip access-group 101 in
```

### **8. ACLs with VTY**
```
access-list 12 permit 10.10.10.0 0.0.0.255
line vty 0 4
 access-class 12 in
 transport input ssh
```

### **9. ACLs and NAT**
```
access-list 20 permit 192.168.50.0 0.0.0.255
route-map NAT-PERMIT permit 10
 match ip address 20
```

### **10. Verification**
```
show access-lists
show ip interface
show running-config | section access-list
```

---

# 🏁 **Conclusion**
Standard ACLs offer simple, source-based filtering and are ideal for basic network control.  
Extended ACLs allow comprehensive, protocol‑aware filtering suitable for modern, secure, and scalable environments.
