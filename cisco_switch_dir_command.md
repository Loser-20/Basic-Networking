
# Cisco Switch `dir` Command — What It Shows and How to Check Storage

This guide explains how to use the `dir` command on Cisco switches (IOS, IOS-XE, and NX-OS) to inspect file systems, what typical directories contain, and useful verification and housekeeping commands.

---

## 1) What does `dir` do?
`dir` lists files and directories in a given file system (e.g., `flash:`, `bootflash:`). It’s similar to `ls` on Linux.

- **Basic usage:**
  ```
  dir
  dir flash:
  dir bootflash:
  ```
- **Recursive options:** Not generally supported like Linux `ls -R`; browse into subfolders with `cd`/`pwd` (where available) or by specifying full paths.

---

## 2) Common file systems on Cisco switches
The exact names vary by platform and software train:

- **IOS / IOS-XE (Catalyst, ISR, etc.):**
  - `flash:` or `bootflash:` – Primary local storage for images and files
  - `nvram:` – Startup configuration storage (historically), small
  - `crashinfo:` – Crash logs and diagnostics
  - `usbflash0:` / `usb0:` – USB storage if present
- **NX-OS (Nexus):**
  - `bootflash:` – Primary local storage
  - `volatile:` – Volatile RAM-backed file system
  - `sup-bootflash:` (on dual-supervisor platforms)
  - `usb1:` / `usb2:` – USB storage

> **Tip:** Use `show file systems` to see what your platform supports.

``` 
show file systems
```
You’ll see the type, size, free space, permissions (ro/rw), and the current working file system marked with an asterisk `*`.

---

## 3) How to check storage and space

- **List files in the default file system:**
  ```
  dir
  ```
- **List specific file systems:**
  ```
  dir flash:
  dir bootflash:
  dir crashinfo:
  dir usbflash0:
  ```
- **Check free space:** (displayed at bottom of `dir` output) or use `show file systems` to view size/used/avail.
- **Change working file system (where supported):**
  ```
  cd flash:
  pwd
  ```

---

## 4) Typical contents you may find

- **System images:**
  - `cat9k_iosxe.<version>.SPA.bin`, `c2960x-universalk9-mz.<version>.bin`, `n3000-uk9.<version>.bin`
- **Consolidated image packages (IOS-XE):**
  - `.bin` (monolithic) or expanded packages under `:packages.conf`
- **Configuration and data files:**
  - `config.text` (legacy), `startup-config` (nvram), `private-config.text`
  - `vlan.dat` (VLAN database on some platforms)
  - `license_*` or `*.lic` (older licensing)
  - `bootvar`/`boot.cfg`/`packages.conf` (boot variables or package manifests)
- **Logs and diagnostics:**
  - `crashinfo:*` directories with crash logs and core files
  - `core/` directories containing core dumps
- **Smart Install / scripts / tech-support bundles**
- **Your uploads:**
  - Any scripts, images, or backups you’ve copied to the device

---

## 5) Interpreting `dir` output
Example (IOS-XE):
```
Switch# dir flash:
Directory of flash:/

  11  -rw-     7952688  Jan 10 2026 12:03:14 +00:00  cat9k_iosxe.17.9.3.SPA.bin
  12  drw-           -  Jan 10 2026 12:05:01 +00:00  crashinfo
  13  -rw-        1024  Jan 10 2026 12:05:20 +00:00  vlan.dat
  14  -rw-       32768  Jan 10 2026 12:06:33 +00:00  packages.conf

1597151232 bytes total (847093760 bytes free)
```
- **Flags:** `-rw-` = regular file (read/write), `drw-` = directory
- **Footer:** Total and free bytes

---

## 6) Verifying files (integrity & size)

- **MD5/SHA verification (IOS / IOS-XE):**
  ```
  verify /md5 flash:cat9k_iosxe.17.9.3.SPA.bin
  verify /sha256 flash:cat9k_iosxe.17.9.3.SPA.bin
  ```
- **NX-OS:**
  ```
  show file bootflash:filename md5
  show file bootflash:filename sha256
  ```
- **Check size/date quickly:**
  ```
  dir flash: | include <filename>
  ```

---

## 7) Copying to/from the device

``` 
copy tftp: flash:
copy scp: flash:
copy usbflash0: flash:
copy flash: tftp:
```
> Ensure you have reachability/credentials for remote protocols (TFTP/SCP/SFTP) and sufficient free space on target.

---

## 8) Cleaning up space

- **Remove old images or large logs:**
  ```
  delete /force /recursive flash:/crashinfo
  delete flash:old-image.bin
  squeeze flash:   ! (older IOS with Class C file systems)
  ```
- **Safeguards:** Keep **at least one known-good** bootable image and required files (e.g., `packages.conf`, `vlan.dat`).

---

## 9) Boot variables and image selection

- **IOS / IOS-XE:**
  ```
  show boot
  conf t
    boot system flash:cat9k_iosxe.17.9.3.SPA.bin
  end
  write memory
  ```
- **NX-OS (install all or boot variables):**
  ```
  show boot
  install all nxos bootflash:nxos.<version>.bin
  ```

---

## 10) Troubleshooting tips

- **`%Error opening flash:`** — File system corrupted or not mounted; try `show file systems`, power-cycle, or ROMMON checks.
- **Not enough space** — Delete unneeded files; consider USB/SCP offload.
- **Verification mismatch** — Re-download the image; check transport integrity.
- **Cannot find image on boot** — Verify `show boot` and that the file actually exists in `dir` output.

---

## 11) Quick command cheatsheet

``` 
show file systems
pwd
cd flash:
dir
Dir flash:
dir bootflash:
dir crashinfo:
verify /md5 flash:<file>
verify /sha256 flash:<file>
show file bootflash:<file> md5    ! (NX-OS)
copy tftp: flash:
copy scp: flash:
delete flash:<file>
show boot
```

---

## 12) FAQs

**Q1: How do I know which file system is the default?**  
Run `show file systems`; the default has an asterisk `*`.

**Q2: Where is the startup configuration?**  
Traditionally `nvram:startup-config`, though many modern platforms virtualize this. Use `show startup-config` to view.

**Q3: Where are VLANs stored?**  
On some IOS switches: `flash:vlan.dat`. On newer platforms/VSS/stack, behavior may differ.

**Q4: What if `dir` is very slow?**  
Large directories, USB devices, or crashinfo folders can be slow. Filter with `| include` or clean up.

---

## 13) Examples by platform

### IOS (Catalyst 2960-X)
```
Switch# show file systems
Switch# dir flash:
Switch# verify /md5 flash:c2960x-universalk9-mz.152-7.E2.bin
```

### IOS-XE (Catalyst 9300)
```
Switch# dir flash:
Switch# more flash:packages.conf
Switch# show boot
Switch# verify /sha256 flash:cat9k_iosxe.17.9.3.SPA.bin
```

### NX-OS (Nexus 3K/5K/7K/9K)
```
N9K# dir bootflash:
N9K# show file bootflash:nxos.9.3(11).bin md5
N9K# show boot
```

---

## 14) Safety & best practices
- Always validate image integrity (`verify`) before changing boot variables.
- Maintain at least one fallback image in storage.
- Back up configuration and key files (e.g., `vlan.dat`) before cleanup.
- Prefer secure transfer methods (SCP/SFTP) when possible.

---

**TL;DR**: Use `show file systems` to discover storage, `dir <fs>:` to list contents, `verify` to check image integrity, `show boot` to confirm what will load, and clean up old images/logs to maintain free space.

