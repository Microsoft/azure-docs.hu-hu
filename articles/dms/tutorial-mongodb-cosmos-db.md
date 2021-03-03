---
title: 'Oktatóanyag: MongoDB migrálása offline Azure Cosmos DB API-ra a MongoDB-ben'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan telepítheti át a helyszíni MongoDB-ről a Azure Cosmos DB API-t a Azure Database Migration Service használatával offline MongoDB.
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
ms.openlocfilehash: b669870537ffb58d9ae7e8a5c65276d310ba6a7e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722024"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Oktatóanyag: MongoDB migrálása Azure Cosmos DB API-MongoDB offline állapotba a DMS használatával

A Azure Database Migration Service segítségével az adatbázisok offline (egyszeri) áttelepítését végezheti el a MongoDB helyszíni vagy Felhőbeli példányaiból, hogy Azure Cosmos DB API-ját a MongoDB.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * Az áttelepítés monitorozása.

Ebben az oktatóanyagban áttelepít egy adatkészletet egy Azure-beli virtuális gépen üzemeltetett MongoDB, hogy Azure Cosmos DB API-ját a MongoDB számára Azure Database Migration Service használatával. Ha még nincs beállítva MongoDB-forrás, tekintse [meg a MongoDB telepítése és konfigurálása Windowsos virtuális gépen az Azure-ban](/previous-versions/azure/virtual-machines/windows/install-mongodb)című cikket.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Hajtsa végre az áttelepítés előtti](../cosmos-db/mongodb-pre-migration.md) lépéseket, például az átviteli sebesség becslését, a partíciós kulcs kiválasztását és az indexelési házirendet.
* [Hozzon létre egy Azure Cosmos db API-ját a MongoDB-fiókhoz](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Servicehoz Azure Resource Manager üzembe helyezési modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával. A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg a [Virtual Network dokumentációt](../virtual-network/index.yml), és különösen a gyors üzembe helyezési cikkeket részletesen ismerteti.

    > [!NOTE]
    > Ha a virtuális hálózat beállítása során ExpressRoute használ a Microsoft számára, adja hozzá a következő szolgáltatási [végpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) ahhoz az alhálózathoz, amelyben a szolgáltatást kiépíti:
    >
    > * Céladatbázis végpontja (például SQL-végpont, Cosmos DB végpont stb.)
    > * Tárolási végpont
    > * Service Bus-végpont
    >
    > Erre a konfigurációra azért van szükség, mert Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózati hálózati biztonsági csoport (NSG) szabályai nem gátolják meg a következő kommunikációs portokat: 53, 443, 445, 9354 és 10000-20000. A Virtual Network NSG-forgalom szűrésével kapcsolatos további információkért tekintse meg a [hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/virtual-network-vnet-plan-design-arm.md)című cikket.
* Nyissa meg a Windows tűzfalat, hogy a Azure Database Migration Service hozzáférhessen a forrás MongoDB-kiszolgálóhoz, amely alapértelmezés szerint a 27017-es TCP-port.
* Ha a forrásadatbázis (ok) előtt tűzfal-berendezést használ, előfordulhat, hogy olyan tűzfalszabályok hozzáadására van szükség, amelyek lehetővé teszik a Azure Database Migration Service számára a forrás-adatbázis (ok) elérését az áttelepítéshez.

## <a name="configure-azure-cosmos-db-server-side-retries-for-efficient-migration"></a>Azure Cosmos DB kiszolgáló oldali újrapróbálkozások konfigurálása a hatékony áttelepítéshez

A MongoDB-ből áttelepített ügyfelek az erőforrás-irányítási képességektől Azure Cosmos DB előnyt élveznek, ami garantálja a kiépített RU/s teljesítmény teljes kihasználását. Azure Cosmos DB a Migrálás során egy adott adatáttelepítési szolgáltatási kérelmet, ha a kérelem túllépi a kiépített RU/s-t; ezt követően a kérést újra kell próbálkozni. Az adatáttelepítési szolgáltatás képes az újrapróbálkozásokra, azonban a hálózati ugrásban részt vevő adatáttelepítési szolgáltatás és a Azure Cosmos DB közötti oda-és visszautazási idő a kérés teljes válaszideje befolyásolja. A megnövelt kérelmek válaszideje lerövidítheti az áttelepítéshez szükséges teljes időt. A Azure Cosmos DB *kiszolgálóoldali újrapróbálkozási* funkciója lehetővé teszi, hogy a szolgáltatás lehallgassa a sávszélesség-szabályozási kódokat, és sokkal alacsonyabb időpontra próbálkozzon újra, ami jelentősen javítja a kérelmek válaszideje.

A kiszolgálóoldali újrapróbálkozás funkció a Azure Cosmos DB portál *szolgáltatások* paneljén található.

![MongoDB SSR szolgáltatás](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Ha pedig le van *tiltva*, akkor azt javasoljuk, hogy az alább látható módon engedélyezze azt.

![MongoDB SSR engedélyezése](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyben létre szeretné hozni a Azure Database Migration Service példányát. 

5. Válasszon egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

    A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás MongoDB-példányhoz és a cél Azure Cosmos DB fiókhoz.

    Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című cikket.

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

      ![Azure Database Migration Service összes példányának megkeresése](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service példány nevét, majd válassza ki a példányt.

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az **új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **forráskiszolgáló típusa** szövegmezőben válassza a **MongoDB** lehetőséget, a **célkiszolgáló típusa** szövegmezőben válassza a **CosmosDB (MongoDB API)** lehetőséget, majd a **tevékenység típusa** beállításnál válassza az **Offline adatáttelepítés** lehetőséget. 

    ![Database Migration Service projekt létrehozása](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **forrás részletei** képernyőn a forrás MongoDB-kiszolgáló kapcsolati adatait adhatja meg.

   > [!IMPORTANT]
   > A Azure Database Migration Service nem támogatja a Azure Cosmos DB forrásként.

    Három mód van a forráshoz való kapcsolódásra:
   * **Standard mód**, amely teljes tartománynevet vagy IP-címet, portszámot és a kapcsolatok hitelesítő adatait fogadja el.
   * A **kapcsolatok karakterláncának módja**, amely fogadja a MongoDB-kapcsolatok karakterláncát a következő témakörben ismertetett módon: a [kapcsolatok karakterláncának URI formátuma](https://docs.mongodb.com/manual/reference/connection-string/).
   * Az **Azure Storage-ból származó adatok**, amelyek elfogadják a blob Container sas URL-címét. Válassza a **blob BSON-memóriaképeket** , ha a blob-tárolóban a MongoDB [bsondump eszköz](https://docs.mongodb.com/manual/reference/program/bsondump/)által létrehozott BSON-memóriaképek találhatók, és törölje a jelölést, ha a tároló JSON-fájlokat tartalmaz.

     Ha ezt a beállítást választja, győződjön meg arról, hogy a Storage-fiókhoz tartozó kapcsolatok karakterlánca a következő formátumban jelenik meg:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Ez a blob-tároló SAS-kapcsolatok karakterlánca az Azure Storage Explorerben található. Az SAS az érintett tárolóhoz való létrehozásakor meg kell adnia az URL-címet a fent kért formátumban.
     
     Emellett az Azure Storage-beli memóriakép-információk alapján is tartsa szem előtt a következő adatokat.

     * A BSON-memóriaképek esetében a blob-tárolóban lévő adatfájloknak bsondump formátumúnak kell lenniük, így az adatfájlokat a tartalmazó adatbázisok mappába kell helyezni a gyűjtemény. BSON formátumban. A metaadat-fájlokat (ha vannak ilyenek) a Format *collection*.metadata.jshasználatával kell elnevezni.

     * A JSON-memóriaképek esetében a blob-tárolóban lévő fájlokat a tartalmazó adatbázisokat tartalmazó mappákba kell helyezni. Az egyes adatbázis-mappákban az adatfájlokat az "adat" nevű almappába kell helyezni, és a name *Collection*. JSON formátumot kell elnevezni. A metaadat-fájlokat (ha vannak ilyenek) a "metadata" nevű almappában kell elhelyezni, és ugyanazt a formátumot kell elnevezni, a *Collection*. JSON fájllal. A metaadat-fájloknak meg kell egyezniük a MongoDB bsondump eszközzel létrehozott formátummal.

    > [!IMPORTANT]
    > Nem ajánlott önaláírt tanúsítványt használni a Mongo-kiszolgálón. Ha azonban ilyet használ, kapcsolódjon a kiszolgálóhoz **kapcsolati sztring mód** használatával, és győződjön meg arról, hogy a kapcsolati karakterlánc ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Amikor a DNS-névfeloldás nem lehetséges, az IP-címet is használhatja.

   ![Forrás adatainak megadása](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az **áttelepítési cél részletei** képernyőn adja meg a cél Azure Cosmos db fiók kapcsolati adatait, amely az előre kiépített Azure Cosmos db API-ját a MongoDB-fiókhoz, amelyre a MongoDB-adatokat áttelepíti.

    ![Cél adatainak megadása](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Kattintson a **Mentés** gombra.

## <a name="map-to-target-databases"></a>Leképezés céladatbázisokra

1. A **térképen az adatbázisok megcélzása** képernyőn képezze le a forrást és a célként megadott adatbázist az áttelepítéshez.

    Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrás-adatbázis, akkor a Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázis-adatbázist.

    Ha a karakterlánc **létrehozása** az adatbázis neve mellett jelenik meg, az azt jelzi, hogy Azure Database Migration Service nem találta meg a célként megadott adatbázist, és a szolgáltatás létrehozza az adatbázist.

    Az áttelepítés ezen pontján [kiépítheti az átviteli sebességet](../cosmos-db/set-throughput.md). Cosmos DB az átviteli sebességet az adatbázis szintjén vagy egyenként, az egyes gyűjteményekhez is kiépítheti. Az átviteli sebességet a [kérelmek egységében](../cosmos-db/request-units.md) (RUs) mérjük. További információ a [Azure Cosmos db díjszabásáról](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Leképezés céladatbázisokra](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Kattintson a **Mentés** gombra.
3. A **gyűjtemény beállítása** képernyőn bontsa ki a gyűjtemények listáját, majd tekintse át az áttelepíteni kívánt gyűjtemények listáját.

    Azure Database Migration Service automatikusan kijelöli az összes olyan gyűjteményt, amely szerepel a forrás MongoDB-példányon, amely nem szerepel a cél Azure Cosmos DB fiókban. Ha olyan gyűjteményeket szeretne újratelepíteni, amelyek már tartalmaznak adatfájlokat, explicit módon ki kell választania a gyűjteményeket ezen a panelen.

    Megadhatja a gyűjtemények által használni kívánt RUs mennyiségét. A Azure Database Migration Service a gyűjtemény méretétől függően az intelligens alapértelmezett értékeket javasolja.

    > [!NOTE]
    > A Futtatás felgyorsításához hajtsa végre párhuzamosan az adatbázis áttelepítését és gyűjtését Azure Database Migration Service több példányával, ha szükséges.

    Megadhat egy szegmens kulcsot is, amellyel kihasználhatja a [particionálást Azure Cosmos db](../cosmos-db/partitioning-overview.md) az optimális méretezhetőség érdekében. Mindenképpen tekintse át az  [ajánlott eljárásokat a szegmens/partíciós kulcs kiválasztásához](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Gyűjtemények táblázatának kiválasztása](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Kattintson a **Mentés** gombra.

5. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

    ![A migrálás összegzése](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

    Megjelenik az áttelepítési tevékenység ablak, és a tevékenység **állapota** nincs **elindítva**.

    ![Tevékenység állapota](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása

* A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

   > [!NOTE]
   > Kiválaszthatja a tevékenységeket az adatbázis-és gyűjtési szintű áttelepítési metrikák részleteinek megtekintéséhez.

    ![Tevékenység állapota befejezve](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Cosmos DBban lévő adatellenőrzés

* Az áttelepítés befejeződése után ellenőrizheti a Azure Cosmos DB-fiókját, és ellenőrizheti, hogy az összes gyűjtemény sikeresen áttelepítése sikeres volt-e.

    ![Képernyőfelvétel: a Azure Cosmos DB-fiók ellenőrzésének helye, amely ellenőrzi, hogy az összes gyűjtemény sikeresen át lett-e telepítve.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Áttelepítés utáni optimalizálás

Miután áttelepítette a MongoDB-adatbázisban tárolt adatAzure Cosmos DB API-ját a MongoDB-hez, csatlakozhat Azure Cosmos DBhoz, és kezelheti az adatkezelési lehetőséget. Más áttelepítés utáni optimalizálási lépéseket is végrehajthat, például optimalizálhatja az indexelési házirendet, frissítheti az alapértelmezett konzisztencia-szintet, vagy konfigurálhatja a Azure Cosmos DB-fiók globális eloszlását. További információ: [áttelepítés utáni optimalizálási](../cosmos-db/mongodb-post-migration.md) cikk.

## <a name="additional-resources"></a>További források

* [Cosmos DB szolgáltatás adatai](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Következő lépések

* Tekintse át az áttelepítési útmutatót a Microsoft [Database áttelepítési útmutatóban](https://datamigration.microsoft.com/)található további forgatókönyvekhez.