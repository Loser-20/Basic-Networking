# EtherChannel Introduction, LACP & PAgP

## 1. Introduction to EtherChannel
EtherChannel is a technology used to bundle multiple physical Ethernet links into a single logical link. This provides:
- Higher bandwidth
- Redundancy and load balancing
- Simplified management (one logical interface instead of multiple physical interfaces
- Across Cisco platforms, the standard EtherChannel capacity is:➡️ Up to 8 active links per EtherChannel - 8 × 1G = 8 Gbps (1 GB/s)

EtherChannel works by grouping several physical interfaces into a port-channel interface.

---

## 2. LACP (Link Aggregation Control Protocol)
**LACP** is an IEEE standard protocol (802.3ad) for negotiating and managing EtherChannel. 

### **LACP Modes**
- **Active**: Actively tries to form EtherChannel
- **Passive**: Responds to LACP packets but does not initiate

> EtherChannel forms when **Active ↔ Passive** or **Active ↔ Active**.

### **LACP Use Cases**
- Multi-vendor environments
- Standardized and reliable aggregation
- Better link failure detection

### **LACP Configuration (Cisco IOS)**
```bash
# Interface-level configuration
enable
configure terminal
interface range g1/0/1 - 2
 channel-group 1 mode active
exit

# Verify commands
show etherchannel summary
show interfaces port-channel 1
```

---

## 3. PAgP (Port Aggregation Protocol)
**PAgP** is a Cisco proprietary protocol for EtherChannel negotiation.

### **PAgP Modes**
- **Desirable**: Actively forms EtherChannel
- **Auto**: Responds but does not initiate

> EtherChannel forms when **Desirable ↔ Desirable** or **Desirable ↔ Auto**.

### **PAgP Use Cases**
- Cisco-only networks
- Simple deployment without interoperability concerns

### **PAgP Configuration (Cisco IOS)**
```bash
# Interface-level configuration
enable
configure terminal
interface range g1/0/1 - 2
 channel-group 2 mode desirable
exit

# Verify commands
show etherchannel summary
show pagp neighbor
```

---

## 4. Differences Between LACP and PAgP
| Feature | LACP | PAgP |
|--------|------|-------|
| Standard | IEEE 802.3ad | Cisco proprietary |
| Modes | Active, Passive | Desirable, Auto |
| Multi‑vendor Support | Yes | No |
| Reliability | High | Medium |
| Best Use | Modern networks | Cisco-only networks |

---

## 5. Additional Useful Commands
### **General EtherChannel Commands**
```bash
show etherchannel summary
show etherchannel port-channel
show running-config interface port-channel 1
```

### **Shutdown EtherChannel**
```bash
interface port-channel 1
 shutdown
```

---

## End of Document
