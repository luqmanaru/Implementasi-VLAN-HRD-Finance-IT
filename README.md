# Implementasi-VLAN-HRD-Finance-IT

Repository ini berisi tugas individu mata kuliah Jaringan Komputer Terapan 1 tentang implementasi VLAN dengan konfigurasi router dan DHCP server.

## Deskripsi

Tugas ini bertujuan untuk mengimplementasikan jaringan VLAN dengan konfigurasi router untuk inter-VLAN routing. Implementasi dilakukan menggunakan dua switch (SW1, SW2) dan satu router (R1) yang berfungsi sebagai inter-VLAN routing untuk tiga departemen: HRD, Finance, dan IT.

## Topologi Jaringan

```
        +------+
        |  R1  |
        +------+
           |
           |
        +------+
        |  SW1 |
        +------+
           |
           |
        +------+
        |  SW2 |
        +------+
```

## Pembagian VLAN

| VLAN ID | Nama VLAN | IP Network       | Switch yang Mengandung VLAN |
|---------|-----------|------------------|----------------------------|
| 10      | HRD       | 192.168.10.0/24 | SW1, SW2                   |
| 20      | Finance   | 192.168.20.0/24 | SW1, SW2                   |
| 30      | IT        | 192.168.30.0/24 | SW1, SW2                   |

## Konfigurasi Router

```
R1#show running-config

Building configuration...

Current configuration : 1234 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R1
!
interface FastEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
!
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
!
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
!
ip classless
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
end
```

## Konfigurasi Switch

### SW1

```
SW1#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
10   HRD                              active    Fa0/3, Fa0/4
20   Finance                          active    Fa0/5, Fa0/6
30   IT                               active    Fa0/7, Fa0/8
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

```
SW1#show interfaces trunk

Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1
Fa0/2       on           802.1q         trunking      1

Port        Vlans allowed on trunk
Fa0/1       1-1005
Fa0/2       1-1005

Port        Vlans allowed and active in management domain
Fa0/1       1,10,20,30
Fa0/2       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1,10,20,30
Fa0/2       1,10,20,30
```

### SW2

```
SW2#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/3, Fa0/4, Fa0/5, Fa0/6
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
10   HRD                              active    
20   Finance                          active    
30   IT                               active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

```
SW2#show interfaces trunk

Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1
Fa0/2       on           802.1q         trunking      1

Port        Vlans allowed on trunk
Fa0/1       1-1005
Fa0/2       1-1005

Port        Vlans allowed and active in management domain
Fa0/1       1,10,20,30
Fa0/2       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1,10,20,30
Fa0/2       1,10,20,30
```

## Hasil Testing

### Testing Antar VLAN yang Sama

| Dari VLAN | Ke VLAN | Dari Device       | Ke Device         | Hasil |
|-----------|---------|-------------------|-------------------|-------|
| 10        | 10      | PC HRD SW1        | PC HRD SW2        | Success |
| 20        | 20      | PC Finance SW1     | PC Finance SW2    | Success |
| 30        | 30      | PC IT SW1         | PC IT SW2         | Success |

### Testing Antar VLAN yang Berbeda

| Dari VLAN | Ke VLAN | Dari Device       | Ke Device         | Hasil |
|-----------|---------|-------------------|-------------------|-------|
| 10        | 20      | PC HRD SW1        | PC Finance SW1    | Success |
| 10        | 30      | PC HRD SW1        | PC IT SW1         | Success |
| 20        | 30      | PC Finance SW1     | PC IT SW1         | Success |
| 10        | 20      | PC HRD SW2        | PC Finance SW2    | Success |
| 10        | 30      | PC HRD SW2        | PC IT SW2         | Success |
| 20        | 30      | PC Finance SW2     | PC IT SW2         | Success |

### Testing ke Gateway

| VLAN     | Device       | Gateway          | Hasil |
|----------|--------------|------------------|-------|
| VLAN 10  | PC HRD       | 192.168.10.1     | Success |
| VLAN 20  | PC Finance   | 192.168.20.1     | Success |
| VLAN 30  | PC IT        | 192.168.30.1     | Success |

## Kesimpulan

Dari hasil pengujian, dapat disimpulkan bahwa:
1. Konfigurasi VLAN telah berhasil diimplementasikan pada semua switch.
2. Konfigurasi router sebagai inter-VLAN routing berfungsi dengan baik.
3. Perangkat dalam satu VLAN yang sama dapat saling berkomunikasi meskipun berada pada switch yang berbeda.
4. Perangkat dalam VLAN yang berbeda dapat saling berkomunikasi melalui router.
5. Semua perangkat dapat terhubung ke gateway masing-masing VLAN dengan baik.

---
**luqmanaru**
