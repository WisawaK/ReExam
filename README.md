1) ตั้งค่า Switch S1 (Office1 — พอร์ตไป R1 = Fa0/1, ไป S2 = Fa0/2, PC-A = Fa0/3)

คัดลอก–วางทั้งหมดนี้ลงที่ CLI ของ S1

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

2) ตั้งค่า Switch S2 (Office2 — พอร์ตไป S1 = Fa0/1, PC-C = Fa0/2 (VLAN10), PC-B = Fa0/3 (VLAN20))

คัดลอก–วางทั้งหมดนี้ลงที่ CLI ของ S2

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

3) ตั้งค่า Router R1 (เป็น Router ที่ interface ไป Switch เป็น trunk/subinterfaces และมี WAN ไป R2)

คัดลอก–วางทั้งหมดนี้ลงที่ CLI ของ R1

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
! no shutdown ไม่จำเป็นสำหรับ subint แต่ใส่ปกติ
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


หมายเหตุ: ถ้าราเตอร์ใน Packet Tracer มี interface ชื่ออื่น (เช่น GigabitEthernet0/0) ให้ปรับชื่อให้ตรงกับอุปกรณ์ของคุณก่อนวาง

4) ตั้งค่า Router R2 (ต่อกับ R1 ทาง WAN และมี Loopback1)

คัดลอก–วางทั้งหมดนี้ลงที่ CLI ของ R2

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
