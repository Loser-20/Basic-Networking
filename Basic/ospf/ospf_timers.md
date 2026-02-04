
# OSPF Show Commands for Hello and Dead Timers in Cisco

The **Hello** and **Dead** timers in OSPF are critical for maintaining neighbor relationships.
- **Hello interval**: how often OSPF sends Hello packets.
- **Dead interval**: how long a router waits before declaring a neighbor down if no Hellos are received.

---
## 🔍 Key Commands to Verify Hello and Dead Timers

### 1) `show ip ospf interface`
Displays OSPF details for an interface, including **Hello** and **Dead** intervals.

**Example:**
```
Router# show ip ospf interface FastEthernet 0/0
```

**Sample Output:**
```
FastEthernet0/0 is up, line protocol is up
Internet Address 192.168.1.1/24, Area 0
Process ID 1, Router ID 192.168.1.1, Network Type BROADCAST, Cost: 1
Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
Hello due in 00:00:05
```

---
### 2) `show ip ospf neighbor`
Shows OSPF neighbors, their states, and the **Dead Time** countdown.

**Example:**
```
Router# show ip ospf neighbor
```

**Sample Output:**
```
Neighbor ID     Pri   State       Dead Time   Address        Interface
192.168.1.2     1     FULL/DR     00:00:35    192.168.1.2    FastEthernet0/0
```

---
### 3) `show ip ospf`
Provides general OSPF process information.

**Example:**
```
Router# show ip ospf
```

**Sample Output:**
```
Routing Process "ospf 1" with ID 192.168.1.1
SPF schedule delay 5 secs, Hold time between two SPFs 10 secs
Minimum LSA interval 5 secs. Minimum LSA arrival 1 secs
```

---
## ✏️ Adjusting Hello and Dead Timers

```
Router(config)# interface FastEthernet 0/0
Router(config-if)# ip ospf hello-interval 5
Router(config-if)# ip ospf dead-interval 20
```

---
## ⚠️ Important Considerations
- **Timer mismatch** prevents adjacency formation.
- **Default Broadcast timers:** Hello 10s, Dead 40s.
- **Lower timers** = faster convergence but higher CPU load.

---
