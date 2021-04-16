---
title: Konfigurálja a privát végpontokat Azure Cosmos DB elemzési tárolóhoz.
description: Megtudhatja, hogyan állíthat be felügyelt privát végpontokat Azure Cosmos DB elemzési tárolóhoz a hálózati hozzáférés korlátozásához.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: fd0b3ada5fec283562cee9727e3f805a7d34c532
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479049"
---
# <a name="configure-azure-private-link-for-azure-cosmos-db-analytical-store"></a>Az Azure Private Link konfigurálása Azure Cosmos DB elemzési tárolóhoz
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Ebből a cikkből megtudhatja, hogyan állíthat be felügyelt privát végpontokat az Azure Cosmos DB elemzési tárolóhoz. Ha a tranzakciós tárolót használja, tekintse meg a tranzakciós tároló privát [végpontjaival kapcsolatos cikket.](how-to-configure-private-endpoints.md) Felügyelt [privát végpontok használatával](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)korlátozhatja a Azure Cosmos DB elemzési tároló hálózati hozzáférését a Virtual Network munkaterületéhez társított felügyelt Azure Synapse számára. A felügyelt privát végpontok privát kapcsolatot hoznak létre az elemzési tárolóval.

## <a name="enable-a-private-endpoint-for-the-analytical-store"></a>Privát végpont engedélyezése az elemzési tárolóhoz

### <a name="set-up-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Munkaterület Azure Synapse Analytics virtuális hálózattal való beállítása

[Munkaterület létrehozása a Azure Synapse Analytics engedélyezett adatkiszivárgással.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Az [adatkiszivárgás](../synapse-analytics/security/workspace-data-exfiltration-protection.md)elleni védelemmel biztosíthatja, hogy a rosszindulatú felhasználók ne másolják vagy továbbítják az adatokat az Azure-erőforrásokból a szervezet hatókörén kívüli helyekre.

A következő hozzáférési korlátozások vonatkoznak arra az esetben, ha az adatkiszivárgás elleni védelem be van kapcsolva egy Azure Synapse Analytics munkaterületen:

* Ha az Azure Sparkot használja a Azure Synapse Analytics, a hozzáférés csak a jóváhagyott felügyelt privát végpontok számára engedélyezett Azure Cosmos DB elemzési tárolóhoz.

* Ha kiszolgáló nélküli Synapse SQL-készleteket használ, bármely Azure Cosmos DB a Link használatával Azure Synapse le. A külső táblákat select [(CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) létrehozására vonatkozó írási kérések azonban csak a munkaterület virtuális hálózatának jóváhagyott privát végpontjai számára engedélyezettek.

> [!NOTE]
> A felügyelt virtuális hálózat és az adatkiszivárgás konfigurációja a munkaterület létrehozása után nem változtatható meg.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Felügyelt privát végpont hozzáadása Azure Cosmos DB elemzési tárolóhoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A Azure Portal nyissa meg a munkaterületet Synapse Analytics és nyissa meg az **Áttekintés panelt.**

1. A Synapse Studio nyissa meg az  Első lépések panelt, és válassza a Megnyitás **lehetőséget** a **Megnyitás Synapse Studio.**

1. A Synapse Studio nyissa meg a **Kezelés** lapot.

1. Lépjen a **Felügyelt privát végpontok lapra, és** válassza az Új **lehetőséget**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Hozzon létre egy új privát végpontot az elemzési tárolóhoz." border="true":::

1. Válassza **Azure Cosmos DB (SQL API)** fióktípust a Folytatás > **lehetőséget.**

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Privát Azure Cosmos DB létrehozásához válassza az SQL API létrehozása lehetőséget." border="true":::

1. Töltse ki az **Új felügyelt privát végpont űrlapot** a következő adatokkal:

   * **Név** – A felügyelt privát végpont neve. Ez a név a létrehozása után nem frissíthető.
   * **Leírás** – Adjon meg egy rövid leírást a privát végpont azonosításához.
   * **Azure-előfizetés** – Válasszon egy Azure Cosmos DB-fiókot az Azure-előfizetésében elérhető fiókok listájából.
   * **Azure Cosmos DB fiók neve** – Válasszon ki egy SQL Azure Cosmos DB MongoDB típusú meglévő fiókot.
   * **Cél részösszeg –** Válasszon egyet a következő lehetőségek közül: **Elemzés:** Ha hozzá szeretné adni a privát végpontot az Azure Cosmos DB elemzési tárolóhoz.
     **Sql** (vagy **MongoDB):** Ha OLTP-t vagy tranzakciós fiókvégpontot szeretne hozzáadni.

   > [!NOTE]
   > A tranzakciós és az elemzésitár privát végpontokat is hozzáadhat ugyanakhoz a Azure Cosmos DB fiókhoz egy Azure Synapse Analytics munkaterületen. Ha csak elemzési lekérdezéseket szeretne futtatni, akkor csak az elemzési privát végpontot szeretné leképezni.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Válassza az elemzés lehetőséget a cél alforráshoz." border="true":::

1. A létrehozás után válassza a privát végpont nevét, és válassza a **Jóváhagyások kezelése** a Azure Portal.

1. Lépjen a saját Azure Cosmos DB, válassza ki a privát végpontot, majd válassza a **Jóváhagyás lehetőséget.**

1. Lépjen vissza a Synapse Analytics munkaterületre, és kattintson a **Frissítés** elemre a **Felügyelt privát végpontok panelen.** Ellenőrizze, hogy a privát végpont **Jóváhagyott állapotban** van-e.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Ellenőrizze, hogy a privát végpont jóvá van-e hagyva." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>A Apache Spark használata Azure Synapse Analytics

Ha olyan Azure Synapse-munkaterületet hozott létre, amely be van kapcsolva az adatkiszivárgás elleni védelemmel, a Synapse Sparkból az Azure Cosmos DB-fiókok felé irányuló kimenő hozzáférés alapértelmezés szerint le lesz tiltva. Emellett ha a Azure Cosmos DB már rendelkezik egy meglévő privát végponttal, a Synapse Spark nem fér hozzá.

A hozzáférés engedélyezése Azure Cosmos DB adatokhoz:

* Ha az Azure Synapse Linket használja a Azure Cosmos DB adatok lekérdezéséhez, adjon hozzá egy felügyelt analitikai privát végpontot a Azure Cosmos DB fiókhoz. 

* Ha kötegelt írásokat/olvasásokat és/vagy streamelési írásokat/olvasásokat használ a tranzakciós tárolóhoz, adjon hozzá egy felügyelt *SQL-* vagy *MongoDB-privát* végpontot a Azure Cosmos DB fiókhoz. Emellett a *connectionMode (kapcsolatmód)* beállításnál is az *Átjárót* kell beállítania, ahogy az az alábbi kódrészletben látható:

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Kiszolgáló nélküli Synapse SQL-készletek használata

A kiszolgáló nélküli Synapse SQL-készletek több-bérlős képességeket is tartalmaznak, amelyek nem felügyelt virtuális hálózatban vannak telepítve. Ha a Azure Cosmos DB-fiók már rendelkezik egy meglévő privát végponttal, a kiszolgáló nélküli Synapse SQL-készlet hozzáférése a fiókhoz a fiók hálózati elkülönítési ellenőrzései miatt Azure Cosmos DB le.

A fiók hálózatelszigetelésének konfigurálása Synapse-munkaterületről:

1. Engedélyezze, hogy a Synapse-munkaterület hozzáférjen Azure Cosmos DB fiókhoz a fiók `NetworkAclBypassResourceId` beállításának megadásával.

   **A PowerShell használata**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Az Azure parancssori felület használata**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB fióknak és Azure Synapse Analytics munkaterületnek ugyanabban a Azure Active Directory -bérlőben kell lennie.

2. Most már elérheti a fiókot kiszolgáló nélküli SQL-készletekből, T-SQL-lekérdezésekkel a Azure Synapse Linken keresztül. Az elemzési tárolóban lévő adatok hálózati elkülönítésének biztosításához azonban hozzá kell adni egy elemzési felügyelt privát végpontot ehhez **a** fiókhoz. Ellenkező esetben az elemzési tárolóban található adatok nem lesznek letiltva a nyilvános hozzáférésben.

> [!IMPORTANT]
> Ha az Azure Synapse Linket használja, és az elemzési tárolóban lévő adatokhoz hálózati elkülönítésre van szüksége,  az elemzési felügyelt privát végponttal kell leképeznie a Azure Cosmos DB-fiókot a Synapse-munkaterületre.

## <a name="next-steps"></a>Következő lépések

* Az elemzési [tárolják a Sparkkal való lekérdezésének Azure Synapse első lépések](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Az elemzési tároló kiszolgáló nélküli [SQL-készletekkel Azure Synapse lekérdezésének első lépések](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
