# 🌐 Network Configuration Project

---

## 🖥️ End Devices

### 💻 PC-A (Office2)
- IP Address: `192.15.1.2`  
- Subnet Mask: `255.255.255.0`  
- Default Gateway: `192.15.1.1`  

---

### 💻 PC-B (Office1)
- IP Address: `192.15.2.2`  
- Subnet Mask: `255.255.255.0`  
- Default Gateway: `192.15.2.1`  

---

### 💻 PC-C (Office1)
- IP Address: `192.15.1.3`  
- Subnet Mask: `255.255.255.0`  
- Default Gateway: `192.15.1.1`  

---

### 🌐 Web Server
- IP Address: `209.165.200.34`  
- Subnet Mask: `255.255.255.224`  
- Default Gateway: `209.165.200.33`  

---

## 🔌 Switch Configuration

### 🟦 Switch S1 (Office2)

```bash
enable
conf t
hostname S1

vlan 10
 name OFFICE2
exit

vlan 20
 name OFFICE1
exit

interface f0/1
 switchport mode trunk
exit

interface f0/2
 switchport mode trunk
exit

interface f0/3
 switchport mode access
 switchport access vlan 10
exit
```

---

### 🟩 Switch S2 (Office1)

```bash
enable
conf t
hostname S2

vlan 10
 name OFFICE2
exit

vlan 20
 name OFFICE1
exit

interface f0/1
 switchport mode trunk
exit

interface f0/2
 switchport mode access
 switchport access vlan 10
exit

interface f0/3
 switchport mode access
 switchport access vlan 20
exit
```

---

## 🌐 Router Configuration

### 🟥 Router R1 (ISR4331 - Data Center Room)

```bash
enable
conf t
hostname R1

interface g0/0/0
 ip address 209.165.200.230 255.255.255.248
 no shutdown
exit

interface g0/0/1.1
 encapsulation dot1Q 10
 ip address 192.15.1.1 255.255.255.0
exit

interface g0/0/1.2
 encapsulation dot1Q 20
 ip address 192.15.2.1 255.255.255.0
exit

ip route 0.0.0.0 0.0.0.0 209.165.200.225
```

---

### 🟨 Router R2 (ISR4331 - Web Server Side)

```bash
enable
conf t
hostname R2

interface g0/0/0
 ip address 209.165.200.225 255.255.255.248
 no shutdown
exit

interface g0/0/1
 ip address 209.165.200.33 255.255.255.224
 no shutdown
exit

interface loopback1
 ip address 209.165.200.1 255.255.255.224
exit

ip route 0.0.0.0 0.0.0.0 209.165.200.230
```
### 🟥 Router R1 (ISR4331 - Data Center Room)
```bash
enable configure terminal hostname R2 ! WAN interface interface GigabitEthernet0/0/0 ip address 209.165.200.225 255.255.255.248 no shutdown exit ! Loopback เป็นเป้าหมายที่ต้อง ping ถึง interface Loopback1 ip address 209.165.200.1 255.255.255.224 exit ! Static routes กลับไปที่ LAN (ผ่าน R1) ip route 192.15.10.0 255.255.255.0 209.165.200.230 ip route 192.15.20.0 255.255.255.0 209.165.200.230 end write memory
