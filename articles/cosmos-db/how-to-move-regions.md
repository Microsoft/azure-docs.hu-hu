---
title: Azure Cosmos DB-fiók áthelyezése másik régióba
description: Megtudhatja, hogyan helyezhet át egy Azure Cosmos DB fiókot egy másik régióba.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342028"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Azure Cosmos DB-fiók áthelyezése másik régióba
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk a következőkkel foglalkozik:

- Helyezzen át egy olyan régiót, amelyben a rendszer replikálja az adatAzure Cosmos DB.
- A fiók (Azure Resource Manager) metaadatainak és adatainak áttelepíthetők az egyik régióból a másikba.

## <a name="move-data-from-one-region-to-another"></a>Adatok áthelyezése egyik régióból a másikba

A Azure Cosmos DB natív módon támogatja az adatreplikálást, így az adatok egyik régióból a másikba való áthelyezése egyszerű. Ezt a Azure Portal, Azure PowerShell vagy az Azure CLI használatával végezheti el. A következő lépésekkel jár:

1. Adjon hozzá egy új régiót a fiókhoz.

    Ha új régiót szeretne felvenni egy Azure Cosmos DB-fiókba, tekintse meg a [régiók hozzáadása/eltávolítása egy Azure Cosmos db-fiókhoz](how-to-manage-database-account.md#addremove-regions-from-your-database-account)című témakört.

1. Végezzen manuális feladatátvételt az új régióra.

    Ha az eltávolítandó régió jelenleg a fiók írási régiója, el kell indítania a feladatátvételt az előző lépésben hozzáadott új régióba. Ez egy nulla állásidőt okozó művelet. Ha egy olvasási régiót egy több régióból álló fiókba helyez át, akkor kihagyhatja ezt a lépést. 
    
    A feladatátvétel elindításához lásd: [manuális feladatátvétel végrehajtása Azure Cosmos-fiókon](how-to-manage-database-account.md#manual-failover).

1. Távolítsa el az eredeti régiót.

    Ha el szeretne távolítani egy régiót egy Azure Cosmos DB-fiókból, tekintse [meg a régiók hozzáadása/eltávolítása a Azure Cosmos db-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)című témakört.

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Azure Cosmos DB-fiók metaadatainak migrálása

Azure Cosmos DB nem támogatja natív módon a fiók metaadatainak áttelepítését az egyik régióból a másikba. Ha a fiók metaadatait és az ügyféladatokat is át szeretné telepíteni az egyik régióból a másikba, létre kell hoznia egy új fiókot a kívánt régióban, majd manuálisan kell átmásolnia az adatokat. 

Az SQL API-hoz közel nulla leállás miatti áttelepítés szükséges a [módosítási hírcsatorna](change-feed.md) vagy az azt használó eszköz használatához. Ha a MongoDB API-t, a Cassandra APIt vagy egy másik API-t telepít át, vagy további információt szeretne a fiókok közötti adatáttelepítés lehetőségeiről, tekintse [meg a helyszíni vagy a Felhőbeli információk áttelepítésének beállításait a Azure Cosmos db](cosmosdb-migrationchoices.md). 

A következő lépések bemutatják, hogyan telepíthet át egy Azure Cosmos DB fiókot az SQL API-hoz és az adatokhoz az egyik régióból a másikba:

1. Hozzon létre egy új Azure Cosmos DB fiókot a kívánt régióban.

    Ha új fiókot szeretne létrehozni a Azure Portal, a PowerShell vagy az Azure CLI használatával, tekintse meg a [Azure Cosmos db fiók létrehozása](how-to-manage-database-account.md#create-an-account)című témakört.

1. Hozzon létre egy új adatbázist és tárolót.

    Új adatbázis és tároló létrehozásához tekintse meg [Az Azure Cosmos-tároló létrehozása](how-to-create-container.md)című témakört.

1. Az adatáttelepítés a Azure Cosmos DB élő adatáttelepítési eszköz használatával történik.

    Ha közel nulla állásidővel szeretné áttelepíteni az adatátvitelt, tekintse meg [Azure Cosmos db élő adatáttelepítési eszközt](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator).

1. Frissítse az alkalmazás-összekapcsolási karakterláncot.

    Ha az élő adatáttelepítési eszköz továbbra is fut, frissítse a kapcsolódási adatokat az alkalmazás új központi telepítésében. Az alkalmazáshoz tartozó végpontokat és kulcsokat a Azure Portal kérheti le.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="A Azure Portal hozzáférés-vezérlése, amely a NoSQL adatbázis-biztonságot mutatja be.":::

1. Kérelmek átirányítása az új alkalmazásba.

    Miután az új alkalmazás csatlakoztatva lett Azure Cosmos DBhoz, átirányíthatja az ügyfelek kérelmeit az új központi telepítésre.

1. Törölje a már nem szükséges erőforrásokat.

    Ha a kérések mostantól teljesen átirányítva lettek az új példányra, törölheti a régi Azure Cosmos DB fiókot és az élő adatmigráló eszközt.

## <a name="next-steps"></a>Következő lépések

Az Azure Cosmos-fiók, valamint az adatbázisok és tárolók kezelésével kapcsolatos további információkért és példákért olvassa el a következő cikkeket:

* [Azure Cosmos-fiók kezelése](how-to-manage-database-account.md)
* [Adatcsatorna módosítása Azure Cosmos DB](change-feed.md)
