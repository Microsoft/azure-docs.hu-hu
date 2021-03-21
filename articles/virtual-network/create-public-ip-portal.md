---
title: Nyilvános IP-Azure Portal létrehozása
description: Megtudhatja, hogyan hozhat létre nyilvános IP-címet a Azure Portalban
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: e6b7648188e2307da4ef40e0ab3daf6201f9d89d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694869"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Nyilvános IP-cím létrehozása a Azure Portal használatával

Ez a cikk bemutatja, hogyan hozható létre nyilvános IP-cím erőforrás a Azure Portal használatával. 

További információ a nyilvános IP-címekhez kapcsolódó erőforrásokról, valamint az alapszintű és a standard SKU közötti különbségről: [nyilvános IP-címek](./public-ip-addresses.md). 

Ez a cikk az IPv4-címekre koncentrál. További információ az IPv6-címekről: [IPv6 for Azure VNet](./ipv6-overview.md).

# <a name="standard-sku"></a>[**Standard termékváltozat**](#tab/option-create-public-ip-standard-zones)

A következő lépések végrehajtásával hozzon létre egy **myStandardZRPublicIP** nevű szabványos, redundáns nyilvános IP-címet.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza az **Erőforrás létrehozása** lehetőséget. 
3. A keresőmezőbe írja be a **nyilvános IP-címet**. Válassza ki a **nyilvános IP-címet** a keresési eredmények között.
4. A **nyilvános IP-cím** lapon válassza a **Létrehozás** lehetőséget.
5. A **nyilvános IP-cím létrehozása** lapon adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                       |
    | ---                     | ---                         |
    | IP-verzió              | IPv4 kiválasztása                 |    
    | Termékváltozat                     | **Standard** kiválasztása         |
    | Tier                   | **Régió** kiválasztása         |
    | Name                    | **MyStandardZRPublicIP** megadása          |
    | IP-cím hozzárendelése   | Megjegyzés: Ez a kijelölés "Static"-ként van zárolva                                        |
    | Útválasztási beállítások      | Hagyja meg az alapértelmezett **Microsoft-hálózatot**. </br> Az útválasztási beállításokkal kapcsolatos további információkért lásd: [Mi az útválasztási preferencia (előzetes verzió)?](./routing-preference-overview.md). |
    | Üresjárati időkorlát (perc)  | Hagyja meg az alapértelmezett **4-es** értéket.        |
    | DNS-névcímke          | Hagyja üresen az értéket.    |
    | Előfizetés            | Válassza ki előfizetését.   |
    | Erőforráscsoport          | Válassza az **új létrehozása** lehetőséget, és adja meg a **myResourceGroup**. </br> Válassza az **OK** lehetőséget. |
    | Hely                | Válassza az **USA 2. keleti** régióját      |
    | Rendelkezésre állási zóna       | Válassza a **zóna – redundáns**, nincs zóna vagy az adott zóna kiválasztása lehetőséget (lásd az alábbi megjegyzést) |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Szabványos IP-cím létrehozása Azure Portal" border="false":::

> [!NOTE]
> Ezek a beállítások a [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)-vel rendelkező régiókban érvényesek. </br>
Kiválaszthat egy adott zónát ezekben a régiókban, de nem lesz rugalmas a zóna meghibásodása miatt. </br> További információ a rendelkezésre állási zónákról: a [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview).

\* = A réteg a [régión belüli terheléselosztó](../load-balancer/cross-region-overview.md) funkcióra vonatkozik, amely jelenleg előzetes verzióban érhető el.

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-create-public-ip-basic)

Ebben a szakaszban hozzon létre egy **myBasicPublicIP** nevű alapszintű nyilvános IP-címet. 

> [!NOTE]
> Az alapvető nyilvános IP-címek nem támogatják a rendelkezésre állási zónákat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza az **Erőforrás létrehozása** lehetőséget. 
3. A keresőmezőbe írja be a **nyilvános IP-címet**. Válassza ki a **nyilvános IP-címet** a keresési eredmények között.
4. A **nyilvános IP-cím** lapon válassza a **Létrehozás** lehetőséget.
5. A **nyilvános IP-cím létrehozása** lapon adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                       |
    | ---                     | ---                         |
    | IP-verzió              | IPv4 kiválasztása                 |    
    | Termékváltozat                     | **Alapszintű** kiválasztása         |
    | Name                    | *MyBasicPublicIP* megadása          |
    | IP-cím hozzárendelése   | Válassza a **statikus** lehetőséget (lásd az alábbi megjegyzést)                                     |
    | Üresjárati időkorlát (perc)  | Hagyja meg az alapértelmezett **4-es** értéket.       |
    | DNS-névcímke          | Hagyja üresen az értéket    |
    | Előfizetés            | Válassza ki előfizetését.   |
    | Erőforráscsoport          | Válassza az **új létrehozása** lehetőséget, és adja meg a **myResourceGroup**. </br> Válassza az **OK** lehetőséget. |
    | Hely                | Válassza az **USA 2. keleti** régióját      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Szabványos IP-cím létrehozása Azure Portal" border="false":::

Ha elfogadható az IP-cím időbeli változása, akkor kiválaszthatja a **dinamikus** IP-hozzárendelést.

---

## <a name="additional-information"></a>További információ 

A fent felsorolt egyedi mezőkkel kapcsolatos további információkért lásd: [nyilvános IP-címek kezelése](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Következő lépések
- [Nyilvános IP-cím hozzárendelése virtuális géphez](./associate-public-ip-address-vm.md#azure-portal)
- További információ az Azure [-beli nyilvános IP-címekről](./public-ip-addresses.md#public-ip-addresses) .
- További információ az összes [nyilvános IP-cím beállításról](virtual-network-public-ip-address.md#create-a-public-ip-address).