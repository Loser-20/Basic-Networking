
# Checking Sockets / Ports in Windows Using CMD

## 🔍 How to Check Active Sockets and Listening Ports
Windows provides built‑in commands in **CMD** to view open ports, listening sockets, and active network connections.

---

## ✅ 1. Show All Open Ports (Sockets)
```cmd
netstat -an
```
Displays:
- Active TCP/UDP connections
- Listening ports
- Connection states (LISTENING, ESTABLISHED)

---

## ✅ 2. Show Ports With Process ID (PID)
```cmd
netstat -ano
```
Use PID to identify which application opened the port:
```cmd
tasklist /FI "PID eq <PID>"
```

---

## ✅ 3. Check Which Process Is Using a Specific Port
Example for port **443**:
```cmd
netstat -ano | findstr :443
```
Then identify the process:
```cmd
tasklist /FI "PID eq <PID>"
```

---

## ✅ 4. Show Only Listening Ports
```cmd
netstat -an | findstr LISTENING
```

---

## ✅ 5. Show Only Active (Established) Connections
```cmd
netstat -an | findstr ESTABLISHED
```

---

## ✅ 6. Real-Time Port Monitoring (Refresh Every 5 Seconds)
```cmd
netstat -an 5
```

---

## ✅ 7. Show Executable Names for Ports (Requires Admin)
```cmd
netstat -ab
```
Shows:
- Which EXE opened the port
- Detailed socket information

---

## 📘 Summary
- **netstat** is the main tool to inspect sockets in Windows CMD.
- Use **tasklist** to map PIDs to applications.
- Great for troubleshooting: blocked ports, malware, server issues, etc.

---
