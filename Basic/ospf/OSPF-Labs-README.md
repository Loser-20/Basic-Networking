
# OSPF Labs: From Basics to Multi‑Area

> This repository accompanies the tutorial series **"OSPF Configuration and Concepts Explained."**
>
> **Chapters:**
> 1. OSPF (Open Shortest Path First) Protocol  
> 2. RIP v/s OSPF | Differences between RIP and OSPF  
> 3. IGP, EGP, and Autonomous System Explained  
> 4. OSPF Features, Advantages, Disadvantages  
> 5. OSPF Fundamental Terminology Explained  
> 6. OSPF LSA Types and LSA Flooding Explained  
> 7. OSPF Area Types and Concept Explained  
> 8. OSPF Hello Protocol and Packets Explained  
> 9. OSPF RID (Router ID) Explained  
> 10. OSPF Neighborship Condition and Requirement  
> 11. OSPF DR/BDR Selection Process Explained  
> 12. How OSPF Routers Build Adjacency Explained  
> 13. Shortest Path First (SPF) Algorithm Explained  
> 14. OSPF Single‑Area Configuration Explained  
> 15. OSPF Stub Area, Totally Stub Area, NSSA, and Totally NSSA  
> 16. OSPF Virtual Links Explained  
> 17. OSPF Authentication (Password and MD5) Explained  
> 18. OSPF Multi‑Area Configuration Explained

---

## 📦 Repository Structure

```
ospf-labs/
├── README.md                      # You are here
├── topologies/
│   ├── single-area-ascii.txt      # ASCII diagram
│   ├── multi-area-ascii.txt       # ASCII diagram
│   └── packet-tracer/             # (optional) .pkt files
├── configs/
│   ├── lab1-single-area/
│   │   ├── R1.txt
│   │   ├── R2.txt
│   │   └── R3.txt
│   ├── lab2-multi-area/
│   │   ├── R1.txt
│   │   ├── R2.txt
│   │   ├── R3.txt
│   │   └── R4.txt
│   ├── lab3-stub-areas/
│   ├── lab4-nssa/
│   ├── lab5-virtual-link/
│   └── lab6-authentication/
└── worksheets/
    ├── verification-checklist.md
    └── troubleshooting-playbook.md
```

> **Platform:** Examples use Cisco IOS‐XE/IOS classic syntax. Adaptations for other vendors are noted where useful.

---

## 🧰 Prerequisites

- Terminal emulator (SecureCRT, PuTTY, iTerm2) or a lab platform (Packet Tracer, GNS3, EVE‑NG, CML)
- IOS images/software compatible with OSPFv2 (IPv4)
- Basic IP addressing knowledge and console access to routers

> **Lab defaults**
> - OSPFv2 (process ID arbitrary/local)
> - Passive interfaces enabled on user‑facing LANs unless testing DR/BDR
> - Router IDs set explicitly for determinism

---

## 🗺️ Topology: Lab 1 — Single‑Area (Area 0)

```
     [LAN A]
   10.1.1.0/24
        |
      Gi0/0
       R1(1.1.1.1)
      Gi0/1 10.0.12.0/30
        |             
       R2(2.2.2.2)----Gi0/2 10.0.23.0/30----R3(3.3.3.3)
                       \
                        \
                         [LAN C 10.3.3.0/24]

All links in **Area 0**
```

### Address Plan
- R1–R2: 10.0.12.0/30 (R1= .1, R2= .2)
- R2–R3: 10.0.23.0/30 (R2= .2, R3= .3)
- LAN A: 10.1.1.0/24 (R1 SVI or routed interface)
- LAN C: 10.3.3.0/24 (R3 SVI or routed interface)
- Router IDs: R1=1.1.1.1, R2=2.2.2.2, R3=3.3.3.3

### Configuration (Cisco IOS)

**R1**
```cisco
hostname R1
interface g0/0
 ip address 10.1.1.1 255.255.255.0
 no shut
interface g0/1
 ip address 10.0.12.1 255.255.255.252
 no shut
router ospf 10
 router-id 1.1.1.1
 network 10.1.1.0 0.0.0.255 area 0
 network 10.0.12.0 0.0.0.3 area 0
 passive-interface g0/0
```

**R2**
```cisco
hostname R2
interface g0/1
 ip address 10.0.12.2 255.255.255.252
 no shut
interface g0/2
 ip address 10.0.23.2 255.255.255.252
 no shut
router ospf 10
 router-id 2.2.2.2
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.23.0 0.0.0.3 area 0
```

**R3**
```cisco
hostname R3
interface g0/2
 ip address 10.0.23.3 255.255.255.252
 no shut
interface g0/3
 ip address 10.3.3.1 255.255.255.0
 no shut
router ospf 10
 router-id 3.3.3.3
 network 10.0.23.0 0.0.0.3 area 0
 network 10.3.3.0 0.0.0.255 area 0
 passive-interface g0/3
```

### Verification
```cisco
show ip ospf neighbor
show ip route ospf
show ip ospf interface brief
show ip ospf database
ping 10.3.3.1 source 10.1.1.1
```

---

## 🗺️ Topology: Lab 2 — Multi‑Area (Backbone + Area 10)

```
           Area 0                     Area 10
 [LAN A]--R1----R2(ABR)====R3====[LAN B]
           |      |               |
         (0)    (0/10)           (10)
```
- R2 is ABR between Area 0 and Area 10
- Addressing (sample):
  - R1–R2: 10.0.12.0/30
  - R2–R3: 10.0.23.0/30
  - LAN A: 10.1.1.0/24 (Area 0 via R1)
  - LAN B: 10.2.2.0/24 (Area 10 via R3)

### Configuration Highlights

**R2 (ABR)**
```cisco
router ospf 10
 router-id 2.2.2.2
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.23.0 0.0.0.3 area 10
```

**R3 (Area 10)**
```cisco
router ospf 10
 router-id 3.3.3.3
 network 10.0.23.0 0.0.0.3 area 10
 network 10.2.2.0 0.0.0.255 area 10
 passive-interface g0/3
```

### Verification Focus
- Inter‑area routes on Area 0 and Area 10
- `show ip ospf database summary` on the ABR
- Check that Area 10 sees only **summary LSAs** for Area 0 networks

---

## 🧩 Lab 3 — Stub & Totally‑Stub Areas

**Goal:** Reduce routing table size in non‑transit areas.

**Design:**
- R2 is ABR for Area 10
- Convert Area 10 to **stub** first, then to **totally‑stub** (no inter‑area routes, only a default from ABR)

**ABR (R2)**
```cisco
router ospf 10
 area 10 stub
! for totally-stub on ABR
no router ospf 10
router ospf 10
 area 10 stub no-summary
```

**Internal Router (R3)**
```cisco
router ospf 10
 area 10 stub
! (no change needed on internal when ABR is totally-stub; it stays 'stub')
```

**Verify**
```cisco
show ip route ospf
show ip ospf
show ip ospf database
```
- In **stub**: external LSAs filtered; default route injected
- In **totally‑stub**: both external and inter‑area LSAs filtered; only default route remains

---

## 🧩 Lab 4 — NSSA (Not‑So‑Stubby Area) & Totally NSSA

**Goal:** Allow limited external injection into a mostly stubby area.

**Design:**
- Area 10 becomes **NSSA**
- R3 redistributes a connected loopback into OSPF as Type‑7 LSA

**ABR (R2)**
```cisco
router ospf 10
 no area 10 stub
 area 10 nssa
! optional: totally NSSA (suppress inter-area routes)
 area 10 nssa no-summary
```

**NSSA Internal (R3)**
```cisco
interface lo0
 ip address 172.16.10.1 255.255.255.0
router ospf 10
 area 10 nssa
 redistribute connected subnets
```

**Verify**
```cisco
show ip ospf database nssa-external
show ip route ospf
```
- ABR translates Type‑7 to Type‑5 (by default on one ABR)

---

## 🧩 Lab 5 — Virtual Link (Backbone Extension)

**Use‑case:** Connect a detached area to Area 0 via transit area.

**Design:**
- Area 0 — R1 — Area 10 (R2) — Area 20 (R4)
- Area 20 has no direct backbone connection, so establish a **virtual link** between **R2 (ABR)** and **R4 (ABR)** through Area 10.

**R2 (Transit Area 10)**
```cisco
router ospf 10
 area 10 virtual-link 4.4.4.4
```

**R4 (To be a backbone ABR)**
```cisco
router ospf 10
 router-id 4.4.4.4
 area 10 virtual-link 2.2.2.2
```

**Verify**
```cisco
show ip ospf virtual-links
show ip ospf neighbor
```

> Ensure stable reachability between the **router IDs** over the transit area before creating the virtual link.

---

## 🔐 Lab 6 — OSPF Authentication (Plain & MD5)

**Interface‑level (simple password)**
```cisco
interface g0/1
 ip ospf authentication
 ip ospf authentication-key cisco123
```

**Area‑level MD5**
```cisco
router ospf 10
 area 0 authentication message-digest
!
interface g0/1
 ip ospf message-digest-key 1 md5 S3cureKey!
```

**Verify**
```cisco
show ip ospf interface
! Look for Authentication: Simple password or Message digest
```

---

## 🧪 Optional Lab — Network Types & DR/BDR

- On broadcast segments: verify DR/BDR election using priorities
```cisco
interface g0/0
 ip ospf priority 100
```
- On point‑to‑point: confirm no DR/BDR
```cisco
interface s0/0
 ip ospf network point-to-point
```

---

## ✅ Verification Checklist (Quick Reference)

- Neighbors Full/DR/BDR as expected (`show ip ospf neighbor`)
- Interfaces in correct areas and network types (`show ip ospf interface brief`)
- LSDB contents match design (`show ip ospf database`) 
- Routes present and summarized as intended (`show ip route`) 
- Authentication aligned on both ends; MTU matches on adjacencies
- No duplicate Router IDs; loopbacks preferred as stable RID sources

---

## 🧯 Troubleshooting Playbook

1. **Stuck in INIT/2‑Way?**  
   - Hello/dead timers, network type mismatch, or missing network statements.  
   - Check Layer‑2 (VLAN, trunks) and ACLs blocking 224.0.0.5/6.
2. **No Inter‑Area Routes?**  
   - ABR placement, area type (totally‑stub/NSSA) filters, summarization.  
   - Validate area borders and `show ip ospf database summary`.
3. **Flapping Adjacencies?**  
   - MTU mismatches, unstable links, authentication/key mismatch.  
   - Compare `show interface` errors and `debug ip ospf adj` (use carefully).
4. **Virtual Link Down?**  
   - RID reachability over transit area; ensure correct peer RIDs.  
   - Verify `show ip ospf virtual-links` and LSDB in transit area.

---

## 📝 Study Map to Chapters

- **Ch. 05, 08–13** underpin Labs 1–2 (neighbors, DR/BDR, SPF, LSAs)  
- **Ch. 15** maps to Labs 3–4 (stub, NSSA)  
- **Ch. 16** maps to Lab 5 (virtual links)  
- **Ch. 17** maps to Lab 6 (authentication)  
- **Ch. 14, 18**: single vs multi‑area configs overall

---

## 🔧 Useful Command Reference (Cisco IOS)

```cisco
show ip ospf
show ip ospf neighbor
show ip ospf interface [brief]
show ip ospf database [router|network|summary|external|nssa-external]
show ip route ospf
clear ip ospf process
```

---

## 📚 Notes for Other Vendors

- **Juniper (Junos):** Use `set protocols ospf area <id> interface <if>`; set `router-id` under `routing-options`; MD5 under interface `authentication md5 key 1 key <text>`.
- **Arista EOS:** Very similar to IOS; OSPF under `router ospf <pid>`; MD5 `ip ospf message-digest-key`.
- **MikroTik (RouterOS v7):** Use OSPF instances/areas in routing config; set area IDs and interfaces; network types similar.

---

## 📥 How to Use This Repo

1. Replicate one lab at a time; verify each milestone.  
2. Save device configs to `configs/<lab-name>/` for reproducibility.  
3. Use `worksheets/verification-checklist.md` during validation.

---

## ✅ Success Criteria per Lab

- **Lab 1:** Full adjacency across Area 0; end‑to‑end ping succeeds; LSDB shows only Area 0 LSAs.  
- **Lab 2:** Inter‑area reachability; ABR shows summaries; non‑backbone routers see summary LSAs.  
- **Lab 3:** Stub → default route only for externals; Totally‑stub → only a single default.  
- **Lab 4:** External network appears via Type‑7/Type‑5 translation.  
- **Lab 5:** Virtual link up; Area 20 participates in backbone.  
- **Lab 6:** Adjacency forms only with correct auth; failures logged with auth mismatch.

---

## 🧭 Next Steps

- Add IPv6 OSPFv3 labs (address families, authentication)
- Add route summarization and filtering examples on ABRs
- Include Packet Tracer/GNS3 project files under `topologies/`

---

### Attribution
This material is crafted for hands‑on practice by network engineers/students. Commands and behaviors align with standard OSPF concepts defined by RFC 2328/5340 and common Cisco IOS implementations.

