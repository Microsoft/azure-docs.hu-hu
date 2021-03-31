---
title: 'Azure ExpressRoute: útválasztó-konfigurációs minták – NAT'
description: Ez az oldal útválasztó-konfigurációs mintákat biztosít a Cisco és a Juniper útválasztók számára.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 01/07/2021
ms.author: duau
ms.openlocfilehash: ae0a39d65bf0f1bc5221cd5e46493c489f7630f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012665"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Útválasztó-konfigurációs minták a NAT beállításához és kezeléséhez

Ez a cikk NAT-konfigurációs mintákat biztosít a Cisco ASA és a Juniper SRX sorozatú útválasztók számára a ExpressRoute használatakor. Ezek az útválasztó konfigurációk csak útmutatásként szolgálnak, és nem használhatók a következőként:. A hálózatának megfelelő konfigurációkkal való együttműködéshez a gyártóval kell dolgoznia.

> [!IMPORTANT]
> Az ezen a lapon szereplő minták célja, hogy kizárólag útmutatásként szolgálnak. A gyártó értékesítési/technikai csapatával és a hálózati csapatával együtt kell működnie a megfelelő konfigurációkkal az igények kielégítése érdekében. A Microsoft nem támogatja az ezen a lapon felsorolt konfigurációkhoz kapcsolódó problémákat. Támogatási problémákért forduljon az eszköz gyártójához.
> 
> 

* Az alábbi útválasztó-konfigurációs minták érvényesek az Azure nyilvános és a Microsoft-partnerekre. Nem konfigurálja a NAT-t az Azure Private-társításához. További részletekért tekintse át a [ExpressRoute](expressroute-circuit-peerings.md) -összevonások és a [ExpressRoute NAT-követelmények](expressroute-nat.md) című leírást.

* Az internettel és a ExpressRoute való kapcsolódáshoz külön NAT IP-készleteket kell használnia. Ha ugyanazt a NAT IP-címkészletet használja az interneten és a ExpressRoute, az aszimmetrikus útválasztást és a kapcsolat elvesztését eredményezi.


## <a name="cisco-asa-firewalls"></a>Cisco ASA-tűzfalak
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>PAT-konfiguráció az ügyfél-hálózatról a Microsoft felé irányuló forgalomhoz

```console
object network MSFT-PAT
  range <SNAT-START-IP> <SNAT-END-IP>


object-group network MSFT-Range
  network-object <IP> <Subnet_Mask>

object-group network on-prem-range-1
  network-object <IP> <Subnet-Mask>

object-group network on-prem-range-2
  network-object <IP> <Subnet-Mask>

object-group network on-prem
  network-object object on-prem-range-1
  network-object object on-prem-range-2

nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range
```

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>A Microsofttól az ügyfél felé irányuló forgalom PAT-konfigurációja

**Felületek és irányok:**

Forrás felület (ahol a forgalom belép az ASA-ba): a célhelyen belüli felületen (ahol a forgalom kilép az ASA-ből): kívülről

**Configuration**

NAT-készlet:

```console
object network outbound-PAT
    host <NAT-IP>
```

Célkiszolgáló:

```console
object network Customer-Network
    network-object <IP> <Subnet-Mask>
```

Az ügyfél IP-címeihez tartozó objektumosztály:

```console
object-group network MSFT-Network-1
    network-object <MSFT-IP> <Subnet-Mask>

object-group network MSFT-PAT-Networks
    network-object object MSFT-Network-1
```

NAT-parancsok:

```console
nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network
```


## <a name="juniper-srx-series-routers"></a>Juniper SRX sorozatú útválasztók
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. redundáns Ethernet-felületek létrehozása a fürthöz

```console
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }
```

### <a name="2-create-two-security-zones"></a>2. hozzon létre két biztonsági zónát
* Megbízhatósági zóna a belső hálózat és a nem megbízható zónák számára a külső hálózatokra néző peremhálózati útválasztók esetében
* Megfelelő felületek kiosztása a zónákhoz
* Szolgáltatások engedélyezése a felületeken

```console
    security {
        zones {
            security-zone Trust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth0.100;
                }
            }
            security-zone Untrust {
                host-inbound-traffic {
                    system-services {
                        ping;
                    }
                    protocols {
                        bgp;
                    }
                }
                interfaces {
                    reth1.100;
                }
            }
        }
    }
```


### <a name="3-create-security-policies-between-zones"></a>3. biztonsági szabályzatok létrehozása zónák között

```console
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }
```

### <a name="4-configure-nat-policies"></a>4. NAT-házirendek konfigurálása
* Hozzon létre két NAT-készletet. Az egyik a Microsoft felé irányuló, illetve a Microsoft által az ügyfélnek kimenő NAT-forgalomra lesz felhasználva.
* Szabályok létrehozása a hálózati címfordításhoz a megfelelő forgalomhoz

```console
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }
```

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Állítsa be a BGP-t, hogy az egyes irányokban külön előtagokat Hirdessen
Tekintse át a mintákat az [útválasztási konfigurációs minták](expressroute-config-samples-routing.md) oldalon.

### <a name="6-create-policies"></a>6. szabályzatok létrehozása

```console
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }
```

## <a name="next-steps"></a>Következő lépések
További információért lásd az [ExpressRoute gyakori kérdéseit](expressroute-faqs.md).

