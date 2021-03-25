---
title: Virtuális hálózatok kezelése – Azure Portal-Azure Database for MySQL – rugalmas kiszolgáló
description: Virtuális hálózatok létrehozása és kezelése Azure Database for MySQL-rugalmas kiszolgálókhoz a Azure Portal használatával
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4906ce9f562910f0a087cd25167457ec1fb301ec
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109994"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Virtuális hálózatok létrehozása és kezelése Azure Database for MySQL-rugalmas kiszolgálókhoz a Azure Portal használatával

> [!IMPORTANT]
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

A rugalmas Azure Database for MySQL-kiszolgáló két, egymást kölcsönösen kizáró hálózati kapcsolati módszert támogat a rugalmas kiszolgálóhoz való csatlakozáshoz. A két lehetőség a következő:

- Nyilvános hozzáférés (engedélyezett IP-címek)
- Privát hozzáférés (VNet-integráció)

Ebben a cikkben a MySQL-kiszolgáló és a **privát hozzáférés (VNet-integráció)** létrehozásával foglalkozunk a Azure Portal használatával. A privát hozzáféréssel (VNet-integrációval) a rugalmas kiszolgáló üzembe helyezhető saját Azure- [Virtual Network](../../virtual-network/virtual-networks-overview.md). Az Azure Virtual Network privát és biztonságos hálózati kommunikációt biztosít. A privát hozzáféréssel a MySQL-kiszolgálóhoz való csatlakozások csak a virtuális hálózatra korlátozódnak. További tudnivalókért tekintse meg a [privát hozzáférés (VNet-integráció)](./concepts-networking.md#private-access-vnet-integration)című témakört.

A kiszolgáló létrehozása során virtuális hálózatra és alhálózatra helyezheti üzembe a rugalmas kiszolgálót. A rugalmas kiszolgálót az üzembe helyezése után nem helyezheti át másik virtuális hálózatra és alhálózatra, vagy *nyilvános hozzáférésre (engedélyezett IP-címekre)* .

## <a name="prerequisites"></a>Előfeltételek
Egy rugalmas kiszolgáló virtuális hálózatban való létrehozásához a következőkre lesz szüksége:
- Egy [virtuális hálózat](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > A virtuális hálózatnak és az alhálózatnak ugyanabban a régióban és előfizetésben kell lennie, mint a rugalmas kiszolgálónak.

-  [Delegálnia kell egy alhálózatot](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) a **Microsoft.DBforMySQL/flexibleServers** helyre. Ez a delegálás azt jelenti, hogy csak a rugalmas Azure Database for MySQL-kiszolgálók használhatják az alhálózatot. Az alhálózatra semmilyen más típusú Azure-erőforrás nem delegálható.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>Azure Database for MySQL rugalmas kiszolgáló létrehozása már meglévő virtuális hálózatban

1. A portál bal felső sarkában válassza az **erőforrás létrehozása** (+) lehetőséget.
2. Válassza az **adatbázisok**  >  **Azure Database for MySQL** elemet. Megadhatja a **MySQL** kifejezést is a keresőmezőbe a szolgáltatás megtalálásához.
3. Központi telepítési lehetőségként válassza a **rugalmas kiszolgáló** lehetőséget.
4. Töltse ki az **alapvető beállítások** űrlapot.
5. Lépjen a **hálózat** lapra, és konfigurálja, hogyan szeretne csatlakozni a kiszolgálóhoz.
6. A **kapcsolódási módszer** területen válassza a **privát hozzáférés (VNet-integráció)** lehetőséget. Lépjen **Virtual Network** , és válassza ki a már meglévő *virtuális hálózatot* és *alhálózatot* , amely a fenti előfeltételek részeként lett létrehozva.
7. A rugalmas kiszolgáló konfigurációjának áttekintéséhez válassza a **felülvizsgálat + létrehozás** elemet.
8. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A kiépítés néhány percet is igénybe vehet.

>[!Note]
> A rugalmas kiszolgáló virtuális hálózatra és alhálózatra történő telepítése után nem helyezheti át a nyilvános hozzáférésre (engedélyezett IP-címek).

## <a name="next-steps"></a>Következő lépések
- [Azure Database for MySQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése az Azure CLI-](./how-to-manage-virtual-network-cli.md)vel.
- További információ a [Azure Database for MySQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md)
- További információ a [Azure Database for MySQL rugalmas kiszolgáló virtuális hálózatáról](./concepts-networking.md#private-access-vnet-integration).
