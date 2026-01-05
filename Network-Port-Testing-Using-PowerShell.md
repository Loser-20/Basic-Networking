# **Testing Network Connectivity with PowerShell**

## **Why This Command Is Useful**
To check whether a specific port is open on a PC, we use the `Test-NetConnection` command in PowerShell.  
**Example scenario:**  
Recently, I faced an issue where I was unable to take VNC access to a remote PC. The problem was that traffic was halted, so I needed to confirm whether the VNC port was open on that PC.

### **About VNC Ports**
- **TCP 5900** is used for the first display (usually `:0`).
- For additional displays, the port increments by 1 for each display number:
  - **Display :0 → Port 5900**
  - **Display :1 → Port 5901**
  - **Display :2 → Port 5902**, and so on.

So, if you want to access a remote PC via VNC, you typically connect to **port 5900**, unless the VNC server is configured differently.

---

## **Command**

```powershell
Test-NetConnection -ComputerName 10.190.22.3 -Port 5900
```

---

## **Explanation of Parameters**

### **Test-NetConnection**
- A PowerShell cmdlet used to test network connectivity.
- Supports checking:
  - **ICMP (Ping)**
  - **TCP ports**
  - **Routing**

---

### **-ComputerName**
- Specifies the target system to check connectivity.
- Can be:
  - **IP Address** (e.g., `10.190.22.3`)
  - **Hostname** (e.g., `PC-Remote01` if DNS resolves it)
- In this example:
  ```powershell
  -ComputerName 10.190.22.3
  ```

---

### **-Port**
- The specific **TCP port** to test.
- For **VNC**, the default port is **5900**.
- Example:
  ```powershell
  -Port 5900
  ```

---

## **What It Does**
- Attempts to establish a **TCP connection** to `10.190.22.3` on port `5900`.
- **If successful:**
  ```
  TcpTestSucceeded : True
  ```
- **If blocked or unreachable:**
  ```
  TcpTestSucceeded : False
  ```

---

## ✅ **Example Output**
```
ComputerName     : 10.190.22.3
RemoteAddress    : 10.190.22.3
RemotePort       : 5900
InterfaceAlias   : Ethernet
TcpTestSucceeded : True
```

---

## **Notes**
- **Use this command to verify if a remote service (like VNC) is accessible.**
- **If `TcpTestSucceeded` is False, check firewall settings or network routing.**
- **Common ports for remote access:**
  - **5900** – VNC 
    - To Check if VNC remote access is enabled.
  - **3389** – RDP
    - For Remote Desktop (Windows systems).
  - **22** – SSH
    - For remote administration (Linux/Unix systems).
  - **80/443** – HTTP
    - Used for web traffic if the PC runs a local web service or needs to serve content.
  - **443** - HTTPS
    - Secure web traffic; often open for browsers and apps to communicate securely.

  - **135, 445** – Windows RPC & SMB
    - Used for RPC and SMB (file sharing, printer sharing).
  - **53** - DNS 
    - For DNS queries (usually outbound, not listening unless it's a DNS server).


