---
title: Felügyelt privát végpontok
description: Az Azure szinapszis Analytics szolgáltatásban felügyelt privát végpontokat ismertető cikk
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 65794c695fa4b36586b23a308845b1f12a20b7cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98569946"
---
# <a name="synapse-managed-private-endpoints"></a>Szinapszis felügyelt privát végpontok

Ez a cikk ismerteti a felügyelt privát végpontokat az Azure szinapszis Analyticsben.

## <a name="managed-private-endpoints"></a>Felügyelt privát végpontok

A felügyelt privát végpontok az Azure szinapszis-munkaterülethez társított felügyelt Virtual Networkban létrehozott privát végpontok. A felügyelt privát végpontok az Azure-erőforrásokra mutató privát hivatkozást létesítenek. Az Azure szinapszis ezeket a privát végpontokat az Ön nevében kezeli. Az Azure szinapszis-munkaterületről felügyelt privát végpontokat hozhat létre az Azure-szolgáltatások (például az Azure Storage vagy a Azure Cosmos DB) és az Azure által üzemeltetett ügyfél/partner szolgáltatások eléréséhez.

Felügyelt privát végpontok használata esetén az Azure szinapszis-munkaterület és más Azure-erőforrások közötti forgalom teljes egészében a Microsoft gerinc hálózatán halad át. Felügyelt privát végpontok védik az kiszűrése. A felügyelt privát végpontok a felügyelt Virtual Network magánhálózati IP-címet használnak, így az Azure-szolgáltatás hatékonyan bekerül az Azure-beli szinapszis-munkaterületre a Virtual Networkba. A felügyelt privát végpontok egy adott erőforrásra vannak leképezve az Azure-ban, és nem a teljes szolgáltatás. Az ügyfelek korlátozhatják a szervezete által jóváhagyott adott erőforráshoz való kapcsolódást. 

További információ a [privát kapcsolatokról és a privát végpontokról](../../private-link/index.yml).

>[!IMPORTANT]
>A felügyelt magánhálózati végpontok csak felügyelt munkaterülettel rendelkező Azure szinapszis-munkaterületeken támogatottak Virtual Network.

>[!NOTE]
>Azure szinapszis-munkaterület létrehozásakor beállíthatja, hogy felügyelt Virtual Network rendeljen hozzá. Ha úgy dönt, hogy felügyelt Virtual Network rendel hozzá a munkaterülethez, úgy is dönthet, hogy a munkaterületről érkező kimenő forgalmat csak a jóváhagyott célokra korlátozza. Felügyelt magánhálózati végpontokat kell létrehoznia a célokhoz. 


A privát végponti kapcsolatok "függő" állapotban jönnek létre, amikor felügyelt privát végpontot hoz létre az Azure Szinapszisban. A rendszer elindít egy jóváhagyási munkafolyamatot. A magánhálózati kapcsolat erőforrásának tulajdonosa a kapcsolat jóváhagyása vagy elutasítása. Ha a tulajdonos jóváhagyja a kapcsolatot, a magánhálózati kapcsolat létrejött. Ha azonban a tulajdonos nem hagyja jóvá a kapcsolatot, a magánhálózati kapcsolat nem lesz létrehozva. Mindkét esetben a felügyelt magánhálózati végpont a kapcsolatok állapotával lesz frissítve. A felügyelt magánhálózati végponthoz társított privát kapcsolati erőforráshoz csak egy felügyelt magánhálózati végpont használható.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Felügyelt privát végpontok dedikált SQL-készlethez és kiszolgáló nélküli SQL-készlethez

A dedikált SQL-készlet és a kiszolgáló nélküli SQL-készlet az Azure szinapszis-munkaterületének analitikai képességei. Ezek a képességek olyan több-bérlős infrastruktúrát használnak, amely nincs telepítve a [felügyelt munkaterületen Virtual Network](./synapse-workspace-managed-vnet.md).

Munkaterületek létrehozásakor az Azure szinapszis két felügyelt magánhálózati végpontot hoz létre a munkaterületen, egyet a dedikált SQL-készlethez, egy pedig a kiszolgáló nélküli SQL-készlethez. 

Ez a két felügyelt magánhálózati végpont szerepel a szinapszis Studióban. Válassza a **kezelés** lehetőséget a bal oldali navigációs sávon, majd válassza a **felügyelt privát végpontok** lehetőséget a Studióban való megjelenítéshez.

A felügyelt magánhálózati végpont, amely az SQL-készletet célozza, *szinapszis- \<workspacename\> ws-SQL--* nek nevezi, és a kiszolgáló nélküli SQL-készletet tároló egyik neve *szinapszis-ws-sqlOnDemand-- \<workspacename\>*.

![Felügyelt privát végpontok dedikált SQL-készlethez és kiszolgáló nélküli SQL-készlethez](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Ez a két felügyelt magánhálózati végpont automatikusan létrejön az Azure szinapszis-munkaterület létrehozásakor. Ez a két felügyelt magánhálózati végpont esetében nem számítunk fel díjat.

## <a name="next-steps"></a>Következő lépések

További információt a [felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md) című cikkben talál.