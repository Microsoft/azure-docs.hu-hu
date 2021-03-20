---
title: 'Azure ExpressRoute: Útválasztó-konfigurációk mintái'
description: Ezen interfész-és útválasztási konfigurációs mintákat használhatja a Cisco IOS-XE és Juniper MX sorozatú útválasztók számára, mint példákat az Azure ExpressRoute való együttműködésre.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: duau
ms.openlocfilehash: 3bc850f02884ae0547c2ecf56a46a57a4e66a752
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89397406"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Útválasztó-konfigurációs minták az Útválasztás beállításához és kezeléséhez
Ez az oldal a Cisco IOS-XE és Juniper MX sorozatú útválasztók felületi és útválasztási konfigurációs mintáit tartalmazza az Azure ExpressRoute használatakor.

> [!IMPORTANT]
> Az ezen a lapon található minták kizárólag útmutatást nyújtanak. A gyártó értékesítési/technikai csapatával és a hálózati csapatával együttműködve találja meg az igényeinek megfelelő konfigurációkat. A Microsoft nem támogatja az ezen a lapon felsorolt konfigurációkhoz kapcsolódó problémákat. Támogatási problémák megoldásához forduljon az eszköz gyártójához.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>MTU-és TCP MSS-beállítások az útválasztó-adaptereken
A ExpressRoute felület maximális átviteli egysége (MTU) a 1500, amely az útválasztó Ethernet-adapterének tipikus alapértelmezett MTU-ja. Ha az útválasztó alapértelmezés szerint eltérő MTU-értékkel rendelkezik, nem kell értéket megadnia az útválasztó felületen.

Az Azure VPN gatewaytől eltérően a ExpressRoute áramkörhöz tartozó TCP-szegmensek maximális méretét (MSS) nem kell megadni.

A cikkben szereplő útválasztó-konfigurációs minták minden társra érvényesek. Az útválasztással kapcsolatos további részletekért tekintse át a [ExpressRoute](expressroute-circuit-peerings.md) -társítások és a [ExpressRoute útválasztási követelményeit](expressroute-routing.md) .


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE alapú útválasztók
Az ebben a szakaszban szereplő minták az IOS-XE operációsrendszer-családot futtató összes útválasztóra érvényesek.

### <a name="configure-interfaces-and-subinterfaces"></a>Felületek és alkapcsolatok konfigurálása
Minden olyan útválasztón, amelyhez a Microsofthoz csatlakozik, egy alkapcsolatra van szüksége. Az alhálózati adapterek egy VLAN-AZONOSÍTÓval vagy egy halmozott VLAN-azonosítóval vagy egy IP-címmel azonosíthatók.

**Dot1Q interfész definíciója**

Ez a példa egy VLAN-AZONOSÍTÓval rendelkező alcsatoló alkapcsolati definícióját tartalmazza. A VLAN-azonosító társítása egyedi. Az IPv4-címek utolsó oktettje mindig páratlan szám lesz.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**QinQ interfész definíciója**

Ez a minta a két VLAN-azonosítóval rendelkező alinterfész alfelületi definícióját tartalmazza. A külső VLAN-azonosító (s-tag), ha használatban van, változatlan marad az összes csomóponton. A belső VLAN-azonosító (c-tag) egyedi. Az IPv4-címek utolsó oktettje mindig páratlan szám lesz.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>EBGP-munkamenetek beállítása
Minden egyes társi kapcsolathoz be kell állítania egy BGP-munkamenetet a Microsofttal. Hozzon létre egy BGP-munkamenetet a következő minta használatával. Ha az alkapcsolathoz használt IPv4-cím volt a. b. c. d, akkor a BGP-szomszéd (Microsoft) IP-címe az a. b. c. d + 1 lesz. A BGP-szomszéd IPv4-címeinek utolsó oktettje mindig páros szám lesz.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>A BGP-munkamenetben meghirdetett előtagok beállítása
Konfigurálja úgy az útválasztót, hogy a következő minta használatával hirdesse a Microsoft számára az előtagok kiválasztását.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>Útvonalak leképezése
Az útvonal-és előtag-listát használva szűrheti a hálózatra propagált előtagokat. Tekintse meg a következő mintát, és győződjön meg arról, hogy a megfelelő előtag-listát beállította.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
 exit-address-family
!
route-map <MS_Prefixes_Inbound> permit 10
 match ip address prefix-list <MS_Prefixes>
!
```

### <a name="configure-bfd"></a>BFD konfigurálása

A BFD két helyen konfigurálhatja: egyet az illesztőfelület szintjén, egy másikat pedig a BGP szintjén. Ez a példa a QinQ interfészre mutat. 

```console
interface GigabitEthernet<Interface_Number>.<Number>
 bfd interval 300 min_rx 300 multiplier 3
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>

router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> fall-over bfd
 exit-address-family
!
```


## <a name="juniper-mx-series-routers"></a>Juniper MX sorozatú útválasztók
Az ebben a szakaszban szereplő minták minden Juniper MX sorozatú útválasztóra érvényesek.

### <a name="configure-interfaces-and-subinterfaces"></a>Felületek és alkapcsolatok konfigurálása

**Dot1Q interfész definíciója**

Ez a példa egy VLAN-AZONOSÍTÓval rendelkező alcsatoló alkapcsolati definícióját tartalmazza. A VLAN-azonosító társítása egyedi. Az IPv4-címek utolsó oktettje mindig páratlan szám lesz.

```console
    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }
```


**QinQ interfész definíciója**

Ez a minta a két VLAN-azonosítóval rendelkező alinterfész alfelületi definícióját tartalmazza. A külső VLAN-azonosító (s-tag), ha használatban van, változatlan marad az összes csomóponton. A belső VLAN-azonosító (c-tag) egyedi. Az IPv4-címek utolsó oktettje mindig páratlan szám lesz.

```console
    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           
```

### <a name="set-up-ebgp-sessions"></a>EBGP-munkamenetek beállítása
Minden egyes társi kapcsolathoz be kell állítania egy BGP-munkamenetet a Microsofttal. Hozzon létre egy BGP-munkamenetet a következő minta használatával. Ha az alkapcsolathoz használt IPv4-cím volt a. b. c. d, akkor a BGP-szomszéd (Microsoft) IP-címe az a. b. c. d + 1 lesz. A BGP-szomszéd IPv4-címeinek utolsó oktettje mindig páros szám lesz.

```console
    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>A BGP-munkamenetben meghirdetett előtagok beállítása
Konfigurálja úgy az útválasztót, hogy a következő minta használatával hirdesse a Microsoft számára az előtagok kiválasztását.

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
                <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>Útvonal-házirendek
Az útválasztási leképezések és az előtag-listák segítségével szűrheti a hálózatra propagált előtagokat. Tekintse meg a következő mintát, és győződjön meg arról, hogy a megfelelő előtag-listát beállította.

```console
    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                    prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>BFD konfigurálása
Konfigurálja az BFD-t csak a protokoll BGP szakasza alatt.

```console
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }
```

### <a name="configure-macsec"></a>MACSec konfigurálása
A MACSec-konfigurációhoz a kapcsolati társítás kulcsa (CAK) és a kapcsolati társítási kulcs neve (CKN) a konfigurált értékekkel kell megegyeznie a PowerShell-parancsok használatával.

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>Következő lépések
További részletek: [ExpressRoute FAQ](expressroute-faqs.md) (ExpressRoute – gyakori kérdések).



