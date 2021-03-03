---
title: Virtuális hálózatok kezelése – Azure Portal-Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Virtuális hálózatok létrehozása és kezelése Azure Database for PostgreSQL-rugalmas kiszolgálókhoz a Azure Portal használatával
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 746f15d2d712f4b571d3f27e3535c69f5f4f9732
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732768"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Virtuális hálózatok létrehozása és kezelése Azure Database for PostgreSQL-rugalmas kiszolgálókhoz a Azure Portal használatával

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Azure Database for PostgreSQL – a rugalmas kiszolgáló kétféle kölcsönösen kizárólagos hálózati kapcsolati módszert támogat a rugalmas kiszolgálóhoz való csatlakozáshoz. A két lehetőség a következő:

* Nyilvános hozzáférés (engedélyezett IP-címek)
* Privát hozzáférés (VNet-integráció)

Ebben a cikkben a PostgreSQL-kiszolgáló és a **privát hozzáférés (VNet-integráció)** Azure Portal használatával történő létrehozását fogjuk összpontosítani. A privát hozzáféréssel (VNet-integrációval) a rugalmas kiszolgáló üzembe helyezhető saját Azure- [Virtual Network](../../virtual-network/virtual-networks-overview.md). Az Azure Virtual Network privát és biztonságos hálózati kommunikációt biztosít. A privát hozzáféréssel a PostgreSQL-kiszolgálóhoz való csatlakozások csak a virtuális hálózatra korlátozódnak. További tudnivalókért tekintse meg a [privát hozzáférés (VNet-integráció)](./concepts-networking.md#private-access-vnet-integration)című témakört.

A kiszolgáló létrehozása során virtuális hálózatra és alhálózatra helyezheti üzembe a rugalmas kiszolgálót. A rugalmas kiszolgálót az üzembe helyezése után nem helyezheti át másik virtuális hálózatra és alhálózatra, vagy *nyilvános hozzáférésre (engedélyezett IP-címekre)* .

## <a name="prerequisites"></a>Előfeltételek
Egy rugalmas kiszolgáló virtuális hálózatban való létrehozásához a következőkre lesz szüksége:
- Egy [virtuális hálózat](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > A virtuális hálózatnak és az alhálózatnak ugyanabban a régióban és előfizetésben kell lennie, mint a rugalmas kiszolgálónak.

-  [Alhálózat delegálása](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) a **Microsoft. DBforPostgreSQL/flexibleServers**. Ez a delegálás azt jelenti, hogy csak Azure Database for PostgreSQL rugalmas kiszolgálók használhatják ezt az alhálózatot. Az alhálózatra semmilyen más típusú Azure-erőforrás nem delegálható.
-  Adja hozzá a `Microsoft.Storage` szolgáltatás végpontját a rugalmas kiszolgálókra delegált alhálózat számára. Ezt a következő lépések végrehajtásával végezheti el:
     1. Nyissa meg a virtuális hálózat lapot.
     2. Válassza ki azt a VNET, amelyben a rugalmas kiszolgáló üzembe helyezését tervezi.
     3. Válassza ki a rugalmas kiszolgáló számára delegált alhálózatot.
     4. A lekérési képernyőn, a **szolgáltatás végpontja** alatt válassza ki `Microsoft.storage` a legördülő menüből.
     5. Mentse a módosításokat.


## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Azure Database for PostgreSQL-rugalmas kiszolgáló létrehozása már meglévő virtuális hálózatban

1. A portál bal felső sarkában válassza az **erőforrás létrehozása** (+) lehetőséget.
2. Válassza az **adatbázisok**  >  **Azure Database for PostgreSQL** elemet. A **PostgreSQL** -t a keresőmezőbe is megadhatja a szolgáltatás megtalálásához.
3. Központi telepítési lehetőségként válassza a **rugalmas kiszolgáló** lehetőséget.
4. Töltse ki az **alapvető beállítások** űrlapot.
5. Lépjen a **hálózat** lapra, és konfigurálja, hogyan szeretne csatlakozni a kiszolgálóhoz.
6. A **kapcsolódási módszer** területen válassza a **privát hozzáférés (VNet-integráció)** lehetőséget. Lépjen **Virtual Network** , és válassza ki a már meglévő *virtuális hálózatot* és *alhálózatot* , amely a fenti előfeltételek részeként lett létrehozva.
7. A rugalmas kiszolgáló konfigurációjának áttekintéséhez válassza a **felülvizsgálat + létrehozás** elemet.
8. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A kiépítés néhány percet is igénybe vehet.

>[!Note]
> A rugalmas kiszolgáló virtuális hálózatra és alhálózatra történő telepítése után nem helyezheti át a nyilvános hozzáférésre (engedélyezett IP-címek).
## <a name="next-steps"></a>Következő lépések
- [Azure Database for PostgreSQL-rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése az Azure CLI-](./how-to-manage-virtual-network-cli.md)vel.
- További információ a [Azure Database for PostgreSQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md)
- További információ a [Azure Database for PostgreSQL-rugalmas kiszolgáló virtuális hálózatáról](./concepts-networking.md#private-access-vnet-integration).