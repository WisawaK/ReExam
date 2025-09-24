
# Network Device Configuration (Cisco Packet Tracer Lab)
# Author: Wisawa
# Description: Full CLI configuration for Switches and Routers in the lab

## 1) Switch S1 (Office1 — Ports: Fa0/1 to R1, Fa0/2 to S2, Fa0/3 to PC-A)

```
enable
configure terminal
hostname S1

! สร้าง VLANs
vlan 10
 name VLAN10
vlan 20
 name VLAN20

! พอร์ตไป PC-A (access VLAN10)
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 10
 no shutdown
 exit

! พอร์ตไป R1 (เป็น trunk) 
interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
 exit

! พอร์ตไป S2 (trunk)
interface FastEthernet0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
 exit

end
write memory
```

---

## 2) Switch S2 (Office2 — Ports: Fa0/1 to S1, Fa0/2 to PC-C VLAN10, Fa0/3 to PC-B VLAN20)

```
enable
configure terminal
hostname S2

! สร้าง VLANs (ถ้ายังไม่มี)
vlan 10
 name VLAN10
vlan 20
 name VLAN20

! พอร์ต PC-C (access VLAN10)
interface FastEthernet0/2
 switchport mode access
 switchport access vlan 10
 no shutdown
 exit

! พอร์ต PC-B (access VLAN20)
interface FastEthernet0/3
 switchport mode access
 switchport access vlan 20
 no shutdown
 exit

! พอร์ตไป S1 (trunk)
interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
 exit

end
write memory
```

---

## 3) Router R1 (Router-on-a-stick + WAN to R2)

```
enable
configure terminal
hostname R1

! ถ้า interface g0/0/1 ต่อไปยัง S1 (จะทำ subinterfaces)
interface GigabitEthernet0/0/1
 no shutdown
 exit

interface GigabitEthernet0/0/1.10
 encapsulation dot1Q 10
 ip address 192.15.10.1 255.255.255.0
 exit

interface GigabitEthernet0/0/1.20
 encapsulation dot1Q 20
 ip address 192.15.20.1 255.255.255.0
 exit

! WAN interface ไป R2
interface GigabitEthernet0/0/0
 ip address 209.165.200.230 255.255.255.248
 no shutdown
 exit

! Static route ไป network ของ R2 (loopback /27)
ip route 209.165.200.0 255.255.255.224 209.165.200.225

end
write memory
```

---

## 4) Router R2 (WAN + Loopback1)

```
enable
configure terminal
hostname R2

! WAN interface
interface GigabitEthernet0/0/0
 ip address 209.165.200.225 255.255.255.248
 no shutdown
 exit

! Loopback เป็นเป้าหมายที่ต้อง ping ถึง
interface Loopback1
 ip address 209.165.200.1 255.255.255.224
 exit

! Static routes กลับไปที่ LAN (ผ่าน R1)
ip route 192.15.10.0 255.255.255.0 209.165.200.230
ip route 192.15.20.0 255.255.255.0 209.165.200.230

end
write memory
```

---

## หมายเหตุ
- ตรวจสอบชื่อ interface ของอุปกรณ์ (ใน Packet Tracer อาจต่างกัน เช่น `FastEthernet` หรือ `GigabitEthernet`).
- หลังจาก config ทุกอย่างแล้ว ให้ทดสอบ:
  - PC-A / PC-B / PC-C ping ไป 192.15.10.1 หรือ 192.15.20.1 ได้
  - ping ข้าม network ถึง 209.165.200.1 (Loopback ของ R2)
