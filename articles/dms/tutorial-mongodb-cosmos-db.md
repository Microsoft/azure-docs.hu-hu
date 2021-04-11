---
title: 'Oktatóanyag: MongoDB migrálása offline Azure Cosmos DB API-ra a MongoDB-ben'
titleSuffix: Azure Database Migration Service
description: Migrálás a helyszíni MongoDB-ről Azure Cosmos DB API-ra a MongoDB kapcsolat nélküli üzemmódban, Azure Database Migration Service használatával.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 8977bb90087d9d3d4962d7eda50903d97da93539
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443867"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Oktatóanyag: a MongoDB migrálása Azure Cosmos DB API-ra offline MongoDB

A Azure Database Migration Service használatával offline, egyszeri áttelepítést hajthat végre a MongoDB helyszíni vagy Felhőbeli példányaiból a MongoDB Azure Cosmos DB API-ra.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.

Ebben az oktatóanyagban egy Azure-beli virtuális gépen üzemeltetett MongoDB-adatkészletet telepít át. Azure Database Migration Service használatával áttelepítheti az adatkészletet a MongoDB Azure Cosmos DB API-ra. Ha még nincs beállítva MongoDB-forrás, tekintse meg a [MongoDB telepítése és konfigurálása az Azure-ban Windowsos virtuális gépen](/previous-versions/azure/virtual-machines/windows/install-mongodb)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Hajtsa végre az áttelepítés előtti](../cosmos-db/mongodb-pre-migration.md) lépéseket, például a teljesítmény becslését és a partíciós kulcs kiválasztását.
* [Hozzon létre egy fiókot a MongoDB Azure Cosmos db API-hoz](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Servicehoz Azure Resource Manager használatával. Ez a telepítési modell helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára az [Azure ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg az [Azure Virtual Network dokumentációját](../virtual-network/index.yml), amely részletesen ismerteti a gyors üzembe helyezési útmutatót.

    > [!NOTE]
    > Ha a virtuális hálózat beállítása során ExpressRoute használ a Microsoft számára, adja hozzá a következő szolgáltatási [végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    >
    > * Céladatbázis végpontja (például SQL-végpont vagy Azure Cosmos DB végpont)
    > * Tárolási végpont
    > * Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózathoz tartozó hálózati biztonsági csoport (NSG) szabályai nem gátolják meg a következő kommunikációs portokat: 53, 443, 445, 9354 és 10000-20000. További információ: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Nyissa meg a Windows tűzfalat, hogy a Azure Database Migration Service hozzáférhessen a forrás MongoDB-kiszolgálóhoz, amely alapértelmezés szerint a 27017-es TCP-port.
* Ha tűzfalat használ a forrásadatbázis előtt, lehetséges, hogy a tűzfalszabályok hozzáadásával engedélyezni Azure Database Migration Service a forrásadatbázis elérését az áttelepítéshez.

## <a name="configure-the-server-side-retry-feature"></a>Kiszolgálóoldali újrapróbálkozás funkció konfigurálása

Ha a MongoDB-ről a Azure Cosmos DB-re migrál, kihasználhatja az erőforrás-irányítási képességeket. Ezekkel a képességekkel teljes mértékben használhatja a kiépített kérések egységét (RU/s). Azure Cosmos DB az áttelepítés során egy adott Database Migration Service-kérelem szabályozása is lehetséges, ha a kérelem meghaladja a tároló által kiosztott RU/mp-t. Ezt követően a kérést újra kell próbálkozni.

A Database Migration Service képes az újrapróbálkozások végrehajtására. Fontos tisztában lenni azzal, hogy a hálózati hop Database Migration Service és Azure Cosmos DB közötti időpontra való átállási idő befolyásolja a kérés teljes válaszideje. A megnövelt kérelmek válaszideje lerövidítheti az áttelepítéshez szükséges teljes időt.

A Azure Cosmos DB kiszolgálóoldali újrapróbálkozás funkciója lehetővé teszi a szolgáltatás számára a hibák szabályozását, és az újrapróbálkozást sokkal alacsonyabb időpontra csökkenti, ami jelentősen javítja a kérelmek válaszideje.

A kiszolgálóoldali újrapróbálkozás újbóli használatához a Azure Cosmos db portálon válassza a **szolgáltatások**  >  **kiszolgálóoldali újrapróbálkozás** elemet.

![A kiszolgálóoldali újrapróbálkozás funkció helyét bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Ha a szolgáltatás le van tiltva, válassza az **Engedélyezés** lehetőséget.

![A kiszolgálóoldali újrapróbálkozás engedélyezését bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![A portál előfizetéseit bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók** lehetőséget.

    ![Az erőforrás-szolgáltatókat bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Az erőforrás-szolgáltató regisztrálását bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Képernyőkép az Azure Marketplace-ről.](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Képernyőkép, amely bemutatja, hogyan hozható létre Azure Database Migration Service egy példánya.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. Az **áttelepítési szolgáltatás létrehozása** lapon adja meg a szolgáltatás nevét, az előfizetést és egy új vagy meglévő erőforráscsoportot.

4. Válassza ki azt a helyet, amelyben létre szeretné hozni a Azure Database Migration Service példányát. 

5. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás MongoDB-példányhoz és a cél Azure Cosmos DB fiókhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című témakört.

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![A Azure Database Migration Service példányának konfigurációs beállításait bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrehozása után keresse meg a Azure Portalon belül, majd nyissa meg. Ezután hozzon létre egy új áttelepítési projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Képernyőkép, amely bemutatja, hogyan lehet megkeresni a Azure Database Migration Service összes példányát.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service példány nevét, majd válassza ki a példányt.

3. Válassza az **+ új áttelepítési projekt** lehetőséget.

4. Az **új áttelepítési projektben** adja meg a projekt nevét, és a **forráskiszolgáló típusa** szövegmezőben válassza a **MongoDB** lehetőséget. A **célkiszolgáló típusa** szövegmezőben válassza a **COSMOSDB (MongoDB API)** lehetőséget, majd a **tevékenység típusa** beállításnál válassza az **Offline adatáttelepítés** lehetőséget. 

    ![A projekt beállításait bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **forrás részletei** képernyőn a forrás MongoDB-kiszolgáló kapcsolati adatait adhatja meg.

   > [!IMPORTANT]
   > A Azure Database Migration Service nem támogatja a Azure Cosmos DB forrásként.

    Három mód van a forráshoz való kapcsolódásra:
   * **Standard mód**, amely teljes tartománynevet vagy IP-címet, portszámot és a kapcsolatok hitelesítő adatait fogadja el.
   * A kapcsolatok **karakterláncának módja**, amely fogadja a MongoDB-kapcsolatok karakterláncát a [kapcsolatok karakterlánc URI-formátuma](https://docs.mongodb.com/manual/reference/connection-string/)szerint.
   * Az **Azure Storage-ból származó adatok**, amelyek elfogadják a blob Container sas URL-címét. Válassza a **blob BSON-memóriaképeket** , ha a blob-tárolóban a MongoDB [bsondump eszköz](https://docs.mongodb.com/manual/reference/program/bsondump/)által létrehozott BSON-memóriaképek találhatók. Ne válassza ezt a beállítást, ha a tároló JSON-fájlokat tartalmaz.

     Ha ezt a beállítást választja, győződjön meg arról, hogy a Storage-fiókhoz tartozó kapcsolatok karakterlánca a következő formátumban jelenik meg:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Ez a blob-tároló SAS-kapcsolatok karakterlánca megtalálható az Azure Storage Explorerben. Az SAS az érintett tárolóhoz való létrehozása a kért formátumban adja meg az URL-címet.
     
     Továbbá az Azure Storage-beli memóriakép-információk alapján tartsa szem előtt a következőket:

     * A BSON-memóriaképek esetében a blob-tárolóban lévő összes értéknek bsondump formátumúnak kell lennie. Az adatfájlokat a *gyűjtemény. bson* formátumban található adatbázisokat tartalmazó mappákba helyezheti. Nevezze el az összes metaadat-fájlt a *collection.metadata.js* formátumával.

     * A JSON-memóriaképek esetében a blob-tárolóban lévő fájlokat a tartalmazó adatbázisokat tartalmazó mappákba kell helyezni. Az egyes adatbázis-mappákban az adatfájlokat *az adatfájlok nevű* almappába kell helyezni, és a nevet a következő formátumban kell megadni: *collection.json*. Helyezzen el minden metaadat-fájlt a *metaadatok* nevű almappában, és a neve legyen ugyanazzal a formátummal, *collection.jsa* következővel:. A metaadat-fájloknak meg kell egyezniük a MongoDB bsondump eszközzel létrehozott formátummal.

    > [!IMPORTANT]
    > Nem ajánlott önaláírt tanúsítványt használni a MongoDB-kiszolgálón. Ha az egyiket kell használnia, kapcsolódjon a kiszolgálóhoz a kapcsolati sztring mód használatával, és győződjön meg arról, hogy a kapcsolati sztring idézőjelekkel ("") rendelkezik.
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Az IP-címet olyan helyzetekben is használhatja, amelyekben a DNS-névfeloldás nem lehetséges.

   ![A forrás részleteinek megadását bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az **áttelepítési cél részletei** képernyőn határozza meg a cél Azure Cosmos db fiók kapcsolati adatait. Ez a fiók az előre kiépített Azure Cosmos DB API azon MongoDB-fiókhoz, amelyre a MongoDB-adatait áttelepíti.

    ![A cél részleteinek megadását bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Kattintson a **Mentés** gombra.

## <a name="map-to-target-databases"></a>Leképezés céladatbázisokra

1. A **térképen az adatbázisok megcélzása** képernyőn képezze le a forrást és a célként megadott adatbázist az áttelepítéshez.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrás-adatbázis, akkor a Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázis-adatbázist.

    Ha a **Létrehozás** megjelenik az adatbázis neve mellett, az azt jelzi, hogy Azure Database Migration Service nem találta meg a célként megadott adatbázist, és a szolgáltatás létrehozza az adatbázist.

    Az áttelepítés ezen pontján [kiépítheti az átviteli sebességet](../cosmos-db/set-throughput.md). Azure Cosmos DB az átviteli sebességet az adatbázis szintjén vagy egyenként, az egyes gyűjteményekhez is kiépítheti. Az átviteli sebességet a rendszer a [kérelmek egységében](../cosmos-db/request-units.md)méri. További információ a [Azure Cosmos db díjszabásáról](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![A cél adatbázisokhoz való leképezést bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Kattintson a **Mentés** gombra.
3. A **gyűjtemény beállítása** képernyőn bontsa ki a gyűjtemények listáját, majd tekintse át az áttelepíteni kívánt gyűjtemények listáját.

    Azure Database Migration Service automatikusan kiválasztja az összes olyan gyűjteményt, amely szerepel a forrás MongoDB-példányon, amely nem szerepel a cél Azure Cosmos DB fiókban. Ha olyan gyűjteményeket szeretne újratelepíteni, amelyek már tartalmaznak adatfájlokat, explicit módon ki kell választania a gyűjteményeket ezen az ablaktáblán.

    Megadhatja, hogy hány RUs-t kíván használni a gyűjtemények számára. A Azure Database Migration Service a gyűjtemény méretétől függően az intelligens alapértelmezett értékeket javasolja.

    > [!NOTE]
    > Párhuzamosan végezze el az adatbázis áttelepítését és gyűjtését. Ha szükséges, a Azure Database Migration Service több példányát is használhatja a Futtatás felgyorsításához.

    Megadhat egy szegmens kulcsot is, amellyel kihasználhatja a [particionálást Azure Cosmos db](../cosmos-db/partitioning-overview.md) az optimális méretezhetőség érdekében. Tekintse át a szegmens [/partíciós kulcs kiválasztásának ajánlott eljárásait](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![A gyűjtemények táblázatának kiválasztását bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Kattintson a **Mentés** gombra.

5. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

    ![A nigration összegzését bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

Válassza a **Migrálás futtatása** lehetőséget. Megjelenik az áttelepítési tevékenység ablak, és a tevékenység állapota nincs **elindítva**.

![A tevékenység állapotát bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása

Az áttelepítési tevékenység képernyőn válassza a **frissítés** lehetőséget a Megjelenítés frissítéséhez, amíg az áttelepítés állapota **befejezettként** nem jelenik meg.

> [!NOTE]
> Kiválaszthatja a tevékenységeket az adatbázis-és gyűjtési szintű áttelepítési metrikák részleteinek megtekintéséhez.

![A tevékenység állapotát bemutató screnshot.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Azure Cosmos DBban lévő adatellenőrzés

Az áttelepítés befejeződése után megtekintheti a Azure Cosmos DB fiókját, és ellenőrizheti, hogy az összes gyűjtemény sikeresen áttelepítése sikeres volt-e.

![Képernyőfelvétel: a Azure Cosmos DB-fiók ellenőrzésének helye, amely ellenőrzi, hogy az összes gyűjtemény sikeresen át lett-e telepítve.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Áttelepítés utáni optimalizálás

Miután áttelepítette a MongoDB-adatbázisban tárolt adatAzure Cosmos DB API-t a MongoDB-hez, csatlakozhat Azure Cosmos DBhoz, és kezelheti azokat. Más áttelepítés utáni optimalizálási lépéseket is végrehajthat. Ilyen lehet például az indexelési házirend optimalizálása, az alapértelmezett konzisztencia-szint frissítése vagy a Azure Cosmos DB fiók globális terjesztésének konfigurálása. További információ: [áttelepítés utáni optimalizálás](../cosmos-db/mongodb-post-migration.md).

## <a name="next-steps"></a>Következő lépések

Tekintse át az áttelepítési útmutatót az [Azure Database áttelepítési útmutatóban](https://datamigration.microsoft.com/)található további forgatókönyvekhez.



