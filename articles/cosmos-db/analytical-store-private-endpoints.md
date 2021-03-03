---
title: Magánhálózati végpontok konfigurálása Azure Cosmos DB analitikus tárolóhoz.
description: Megtudhatja, hogyan állíthatja be a felügyelt privát végpontokat a Azure Cosmos DB analitikus tárolóhoz a hálózati hozzáférés korlátozása érdekében.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 26c99beb95d60b476fff255321d85c0db8aa7363
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679484"
---
# <a name="configure-private-endpoints-for-azure-cosmos-db-analytical-store"></a>Privát végpontok konfigurálása Azure Cosmos DB analitikus tárolóhoz
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Ebből a cikkből megtudhatja, hogyan állíthatja be a felügyelt magánhálózati végpontokat Azure Cosmos DB analitikus tárolóhoz. Ha a tranzakciós tárolót használja, tekintse meg [a tranzakciós áruházhoz tartozó magánhálózati végpontok](how-to-configure-private-endpoints.md) című cikket. Felügyelt privát végpontok használatával korlátozhatja Azure Cosmos DB analitikus tároló hálózati hozzáférését az Azure szinapszis által felügyelt virtuális hálózatra. A felügyelt privát végpontok létrehozhatnak egy privát hivatkozást az analitikus tárolóhoz.

## <a name="enable-private-endpoint-for-the-analytical-store"></a>Privát végpont engedélyezése az analitikai tárolóhoz

### <a name="set-up-an-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Azure szinapszis Analytics-munkaterület beállítása felügyelt virtuális hálózattal

[Hozzon létre egy munkaterületet az Azure szinapszis Analyticsben, amelyeken engedélyezve van az kiszűrése.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Az [kiszűrése védelme](../synapse-analytics/security/workspace-data-exfiltration-protection.md)révén biztosíthatja, hogy a rosszindulatú felhasználók nem másolhatnak és nem vihetnek át az Azure-erőforrások adatait a szervezet hatókörén kívüli helyekre.

Az alábbi hozzáférési korlátozások érvényesek, ha az kiszűrése-védelem be van kapcsolva az Azure szinapszis Analytics-munkaterülethez:

* Ha Azure-beli Sparkot használ az Azure szinapszis Analyticshez, a hozzáférés csak a jóváhagyott felügyelt privát végpontok számára engedélyezett Azure Cosmos DB analitikus tárolóhoz.

* Ha a szinapszis kiszolgáló nélküli SQL-készleteket használja, az Azure szinapszis hivatkozás használatával bármilyen Azure Cosmos DB fiókot lekérhet. A [külső táblákat a Select (CETAS) értékkel létrehozó](../synapse-analytics/sql/develop-tables-cetas.md) kérések esetében azonban csak a jóváhagyott privát végpontok számára engedélyezett a munkaterület virtuális hálózatában.

> [!NOTE]
> A felügyelt virtuális hálózat és az kiszűrése konfigurálása a munkaterület létrehozása után nem módosítható.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Felügyelt privát végpont hozzáadása Azure Cosmos DB analitikus tárolóhoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A Azure Portal navigáljon a szinapszis Analytics-munkaterülethez, és nyissa meg az **Áttekintés** ablaktáblát.

1. Indítsa el a szinapszis Studio alkalmazást **első lépések** ablaktáblán navigálva, és válassza a **Megnyitás** elemet a **nyílt szinapszis Studióban**.

1. A szinapszis Studióban nyissa meg a **kezelés** lapot.

1. Navigáljon a **felügyelt privát végpontokhoz** , és válassza az **új** lehetőséget.

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Hozzon létre egy új privát végpontot az analitikai tárolóhoz." border="true":::

1. Válassza a **Azure Cosmos db (SQL API)** fiók típusát > **Folytatás** elemre.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Privát végpont létrehozásához válassza Azure Cosmos DB SQL API-t." border="true":::

1. Töltse ki az **új felügyelt privát végpont** űrlapot a következő részletekkel:

   * A felügyelt magánhálózati végpont **neve** . Ez a név nem frissíthető a létrehozása után.
   * **Leírás** – adjon meg egy rövid leírást a privát végpont azonosításához.
   * **Azure-előfizetés** – válasszon ki egy Azure Cosmos db fiókot az Azure-előfizetésekben elérhető fiókok listájából.
   * **Azure Cosmos db fiók neve** – válasszon ki egy SQL vagy MongoDB típusú meglévő Azure Cosmos db fiókot.
   * **Cél erőforrás** – válasszon egyet a következő lehetőségek közül: **analitikai**: Ha hozzá szeretné adni a privát végpontot Azure Cosmos db analitikus tárolóhoz.
     **SQL** (vagy **MongoDB**): Ha OLTP vagy tranzakciós fiókhoz tartozó végpontot szeretne hozzáadni.

   > [!NOTE]
   > Az Azure szinapszis Analytics-munkaterületen a tranzakciós tároló és az analitikus tároló magánhálózati végpontokat is hozzáadhatja ugyanahhoz a Azure Cosmos DB-fiókhoz. Ha csak analitikai lekérdezéseket szeretne futtatni, akkor előfordulhat, hogy csak az analitikai magánhálózati végpontot szeretné leképezni.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Válassza az analitikai lehetőséget a cél alerőforráshoz." border="true":::

1. A létrehozás után nyissa meg a privát végpont nevét, és válassza a **Azure Portal a jóváhagyások kezelése** elemet.

1. Navigáljon a Azure Cosmos DB-fiókjához, válassza ki a privát végpontot, és válassza a **jóváhagyás** lehetőséget.

1. Váltson vissza a szinapszis Analytics-munkaterületre, és kattintson a **felügyelt privát végpontok** ablaktábla **frissítés** gombjára. Ellenőrizze, hogy a magánhálózati végpont **jóváhagyott** állapotban van-e.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Győződjön meg arról, hogy a privát végpont jóváhagyva." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Az Azure szinapszis Analytics Apache Spark használata

Ha olyan Azure szinapszis-munkaterületet hozott létre, amelyen be van kapcsolva az kiszűrése-védelem, a rendszer alapértelmezés szerint letiltja a Szinapszisból a Azure Cosmos DB-fiókokhoz való kimenő hozzáférést. Emellett, ha a Azure Cosmos DB már rendelkezik egy meglévő privát végponttal, a szinapszis Spark le lesz tiltva a hozzáféréshez.

Azure Cosmos DB-adatelérés engedélyezése:

* Ha Azure szinapszis-hivatkozást használ a Azure Cosmos DB-adatlekérdezéshez, adjon hozzá egy felügyelt **analitikai** magánhálózati végpontot a Azure Cosmos db-fiókhoz.

* Ha kötegelt írási/olvasási és/vagy adatfolyam-írási/olvasási műveleteket használ a tranzakciós tárolóba, vegyen fel egy felügyelt *SQL* -vagy *MongoDB* magánhálózati végpontot a Azure Cosmos db fiókhoz. Emellett a *connectionMode* az *átjáróhoz* is be kell állítania, ahogy az a következő kódrészletben látható:

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

## <a name="using-synapse-serverless-sql-pools"></a>Szinapszis kiszolgáló nélküli SQL-készletek használata

A szinapszis kiszolgáló nélküli SQL-készletei olyan több-bérlős funkciókat használnak, amelyeket nem felügyelt virtuális hálózatba helyeznek üzembe. Ha a Azure Cosmos DB fiók rendelkezik egy meglévő privát végponttal, a rendszer letiltja a (z)-kiszolgáló nélküli SQL-készletet a fiókhoz való hozzáféréshez a Azure Cosmos DB fiók hálózati elkülönítésének ellenőrzése miatt.

A fiók hálózati elkülönítésének konfigurálása a szinapszis munkaterületről:

1. Annak engedélyezése, hogy a szinapszis munkaterület hozzáférjen a Azure Cosmos DB fiókhoz a `NetworkAclBypassResourceId` fiók beállításának megadásával.

   **A PowerShell használata**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Az Azure parancssori felület használata**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > Megjegyzés Azure Cosmos DB fióknak és az Azure szinapszis Analytics-munkaterületnek ugyanahhoz a Azure Active Directory (AD) bérlőhöz kell tartoznia.

2. Most már hozzáférhet a fiókhoz a kiszolgáló nélküli SQL-készletekből, T-SQL-lekérdezésekkel az Azure szinapszis-kapcsolaton keresztül. Ahhoz azonban, hogy az analitikus tárolóban lévő adatelkülönítés biztosítható legyen, hozzá kell adnia egy **analitikus** felügyelt magánhálózati végpontot ehhez a fiókhoz. Ellenkező esetben az analitikus tárolóban lévő adatok nem lesznek letiltva a nyilvános hozzáférésből.

> [!IMPORTANT]
> Ha az Azure szinapszis-hivatkozást használja, és hálózati elkülönítésre van szüksége az adataihoz az analitikus tárolóban, akkor a Azure Cosmos DB fiókot az **analitikus** felügyelt magánhálózati végpont használatával kell leképeznie a szinapszis munkaterületre.

## <a name="next-steps"></a>Következő lépések

* Ismerkedés az [analitikai tár lekérdezésével az Azure szinapszis sparktal](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Ismerkedés az [analitikai tár lekérdezésével az Azure szinapszis kiszolgáló nélküli SQL-készletekkel](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
