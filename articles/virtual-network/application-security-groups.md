---
title: Azure-alkalmazás biztonsági csoportjai – áttekintés
titlesuffix: Azure Virtual Network
description: Az alkalmazás biztonsági csoportjai használatának megismerése.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 4d8ffe8451b2b2a08ab30761eaf3a928b5e117b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99537628"
---
# <a name="application-security-groups"></a>Alkalmazásbiztonsági csoportok

Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat. A biztonsági szabályokat újrahasznosíthatja nagy léptékben is a konkrét IP-címek manuális karbantartása nélkül. A platform képes kezelni a konkrét IP-címek és a szabálykészletek jelentette összetettséget, így Ön az üzleti logikára összpontosíthat. Az alkalmazásbiztonsági csoportok könnyebb megértése érdekében tekintsük a következő példát:

![Alkalmazásbiztonsági csoportok](./media/security-groups/application-security-groups.png)

Az előző képen az *NIC1* és az *NIC2* az *AsgWeb* alkalmazásbiztonsági csoport tagjai. Az *NIC3* az *AsgLogic* alkalmazásbiztonsági csoport tagja. Az *NIC4* az *AsgDb* alkalmazásbiztonsági csoport tagja. Bár ebben a példában az egyes hálózati adapterek csak egy hálózati biztonsági csoport tagjai, a hálózati adapter több alkalmazás-biztonsági csoport tagja lehet, akár az [Azure-korlátokig](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Egyik hálózati adapterhez sincs hálózati biztonsági csoport társítva. Az *NSG1* mindkét alhálózathoz társítva van, és a következő szabályokat tartalmazza:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Ez a szabály az internetről a webkiszolgálókra irányuló forgalom engedélyezéséhez szükséges. Mivel az internetről bejövő forgalmat az alapértelmezett **DenyAllInbound** biztonsági szabály tiltja, az *AsgLogic* és az *AsgDb* alkalmazásbiztonsági csoportok esetében nincs szükség további szabályokra.

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Engedélyezés |

## <a name="deny-database-all"></a>Deny-Database-All

Mivel az alapértelmezett **AllowVNetInBound** biztonsági szabály az azonos virtuális hálózaton lévő erőforrások között minden kommunikációt engedélyez, ez a szabály az összes erőforrástól érkező forgalom tiltásához szükséges.

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Bármelyik | Megtagadás |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Ez a szabály engedélyezi az *AsgLogic* alkalmazásbiztonsági csoportról az *AsgDb* alkalmazásbiztonsági csoportra irányuló forgalmat. A szabály prioritása magasabb a *Deny-Database-All* szabály prioritásánál. Ennek eredményeként ez a szabály a *Deny-Database-All* szabály előtt lesz kiértékelve, ezért az *AsgLogic* alkalmazásbiztonsági csoporttól érkező forgalom engedélyezve lesz, az összes többi forgalom pedig le lesz tiltva.

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Engedélyezés |

Az alkalmazásbiztonsági csoportokat forrásként vagy célként megadó szabályok csak az adott alkalmazásbiztonsági csoport tagságába tartozó hálózati adapterekre lesznek alkalmazva. Ha a hálózati adapter nem tagja az alkalmazásbiztonsági csoportnak, a szabály nem lesz az adapterre alkalmazva, még akkor sem, ha a hálózati biztonsági csoport az alhálózathoz van rendelve.

Az alkalmazásbiztonsági csoportok a következő korlátozásokkal rendelkeznek:

-    Előfizetésenként korlátozott számú alkalmazásbiztonsági csoporttal rendelkezhet az egyes előfizetésekben, valamint egyéb korlátozások is vonatkoznak az alkalmazásbiztonsági csoportokra. További részletek: [Az Azure korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- A biztonsági szabályokban forrásként és célként egy biztonsági csoportot adhat meg. Nem adhat meg több alkalmazásbiztonsági csoportot a forrásban vagy a célban.
- Az egyes alkalmazásbiztonsági csoportokhoz rendelt összes hálózati adapternek ugyanazon a virtuális hálózaton kell lennie, amelyen az alkalmazásbiztonsági csoporthoz rendelt első hálózati adapter található. Például ha az *AsgWeb* nevű alkalmazásbiztonsági csoporthoz rendelt első hálózati adapter a *VNet1* virtuális hálózaton található, akkor az *ASGWeb* csoporthoz rendelt összes többi hálózati adapternek is a *VNet1* hálózaton kell lennie. Nem adhat különböző virtuális hálózatokról származó hálózati adaptereket ugyanahhoz az alkalmazásbiztonsági csoporthoz.
- Ha egy biztonsági szabály forrásaként és céljaként határoz meg alkalmazásbiztonsági csoportokat, mindkét alkalmazásbiztonsági csoport hálózati adaptereinek ugyanazon a virtuális hálózaton kell lenniük. Ha például az *AsgLogic* a *VNet1* hálózatról tartalmaz hálózati adaptereket, az *AsgDb* pedig a *VNet2* hálózatról, nem rendelheti hozzá az *AsgLogic* csoportot forrásként az *AsgDb* csoportot pedig célként egy adott szabályban. A forrás és a cél alkalmazásbiztonsági csoporthoz tartozó összes hálózati adapternek ugyanazon a virtuális hálózaton kell lennie.

> [!TIP]
> A szükséges biztonsági szabályok számának csökkentése, valamint a szabályok módosíthatósága érdekében tervezze meg a szükséges alkalmazásbiztonsági csoportokat, és ha lehetséges, szolgáltatáscímkék vagy alkalmazásbiztonsági csoportok használatával hozza létre a szabályokat, ne egyedi IP-címek vagy IP-címtartományok alapján.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [a hálózati biztonsági csoportok létrehozását](tutorial-filter-network-traffic.md).
