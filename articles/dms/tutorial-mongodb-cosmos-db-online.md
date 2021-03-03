---
title: 'Oktatóanyag: a MongoDB migrálása a Azure Cosmos DB API-ra a MongoDB-ben'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan telepíthet át a helyszíni MongoDB-ről Azure Cosmos DB API-ra a MongoDB online-ra Azure Database Migration Service használatával.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-nov-2020
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 84eed7d48dfe0230ea023d171e2b640bdf50dbe3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715666"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Oktatóanyag: a MongoDB migrálása Azure Cosmos DB API-ra a MongoDB online-hoz a DMS használatával

A Azure Database Migration Service segítségével az adatbázisok online (minimális állásidő) áttelepítését végezheti el egy helyszíni vagy Felhőbeli MongoDB, hogy Azure Cosmos DB API-ját a MongoDB.

Ez az oktatóanyag azt ismerteti, hogyan lehet a Azure Database Migration Service használatával áttelepíteni a MongoDB-fájlokat a Azure Cosmos DBba:
> [!div class="checklist"]
> 
> * Hozzon létre egy Azure Database Migration Service-példányt. 
> * Hozzon létre egy áttelepítési projektet. 
> * Határozza meg a forrást. 
> * Határozza meg a célt. 
> * Leképezés a célként megadott adatbázisokra. 
> * A migrálás futtatása. 
> * Az áttelepítés monitorozása. 
> * Azure Cosmos DBban lévő adatellenőrzés. 
> * Ha elkészült, fejezze be az áttelepítést. 

Ebben az oktatóanyagban áttelepít egy adatkészletet egy Azure-beli virtuális gépen üzemeltetett MongoDB, Azure Cosmos DB API-ját a MongoDB számára minimális állásidővel Azure Database Migration Service használatával. Ha még nincs beállítva MongoDB-forrás, tekintse [meg a MongoDB telepítése és konfigurálása Windowsos virtuális gépen az Azure-ban](/previous-versions/azure/virtual-machines/windows/install-mongodb)című cikket.

> [!NOTE]
> A Azure Database Migration Service használata az online áttelepítés végrehajtásához a prémium szintű díjszabás alapján kell létrehoznia egy példányt.

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy Azure Database Migration Service-példányt hozzon létre ugyanabban az Azure-régióban, mint a célként megadott adatbázis. Az adatáthelyezés régiókban vagy földrajzi területeken lelassíthatja az áttelepítési folyamatot.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk a MongoDB-ről Azure Cosmos DB API-MongoDB való online áttelepítést ismerteti. Offline áttelepítés esetén tekintse [meg a MongoDB áttelepítése a Azure Cosmos db API-ra a MongoDB-ben offline kapcsolattal a DMS használatával](tutorial-mongodb-cosmos-db.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Hajtsa végre az áttelepítés előtti](../cosmos-db/mongodb-pre-migration.md) lépéseket, például az átviteli sebesség becslését, a partíciós kulcs kiválasztását és az indexelési házirendet.
* [Hozzon létre egy Azure Cosmos db API-ját a MongoDB-fiókhoz](https://ms.portal.azure.com/#create/Microsoft.DocumentDB) , és győződjön meg arról, hogy a [SSR (kiszolgálóoldali újrapróbálkozás)](../cosmos-db/prevent-rate-limiting-errors.md) engedélyezve van.
* Hozzon létre egy Microsoft Azure Virtual Network a Azure Database Migration Servicehoz Azure Resource Manager üzembe helyezési modell használatával, amely helyek közötti kapcsolatot biztosít a helyszíni forráskiszolgáló számára a [ExpressRoute](../expressroute/expressroute-introduction.md) vagy a [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)használatával.

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

![Képernyőkép a MongoDB Server-Side újrapróbálkozás funkcióról.](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-feature.png)

Ha pedig le van *tiltva*, akkor azt javasoljuk, hogy az alább látható módon engedélyezze azt.

![Képernyőkép a MongoDB Server-Side az Engedélyezés gombra.](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-enable.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyben létre kívánja hozni a Azure Database Migration Service példányát, majd válassza az **erőforrás-szolgáltatók** lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd a **Microsoft. DataMigration** jobb oldalán válassza a **regisztráció** lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyben létre szeretné hozni a Azure Database Migration Service példányát.

5. Válasszon ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

   A virtuális hálózat Azure Database Migration Service hozzáférést biztosít a forrás MongoDB-példányhoz és a cél Azure Cosmos DB fiókhoz.

   Ha további információt szeretne arról, hogyan hozhat létre virtuális hálózatot a Azure Portalban, tekintse meg a [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)című cikket.

6. Válasszon ki egy SKU-t a prémium szintű díjszabási csomagból.

    > [!NOTE]
    > Az online áttelepítések csak a prémium szint használata esetén támogatottak. További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Azure Database Migration Service összes példányának megkeresése](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service példány nevét, majd válassza ki a példányt.

    Másik lehetőségként az Azure Database Migration Service-példányt a Azure Portal keresési paneljéről derítheti fel.

    ![A Azure Portal keresési paneljének használata](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az **új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **forráskiszolgáló típusa** szövegmezőben válassza a **MongoDB** lehetőséget, a **célkiszolgáló típusa** szövegmezőben válassza a **CosmosDB (MongoDB API)** lehetőséget, majd a **tevékenység típusának** kiválasztásához válassza az **online adatáttelepítés [előzetes verzió]** lehetőséget.

    ![Database Migration Service projekt létrehozása](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Válassza a **Mentés** lehetőséget, majd válassza a **Létrehozás és Futtatás tevékenység** lehetőséget a projekt létrehozásához és az áttelepítési tevékenység futtatásához.

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

     Emellett az Azure Storage-beli memóriakép-információk alapján is tartsa szem előtt a következő adatokat.

     * A BSON-memóriaképek esetében a blob-tárolóban lévő adatfájloknak bsondump formátumúnak kell lenniük, így az adatfájlokat a tartalmazó adatbázisok mappába kell helyezni a gyűjtemény. BSON formátumban. A metaadat-fájlokat (ha vannak ilyenek) a Format *collection*.metadata.jshasználatával kell elnevezni.

     * A JSON-memóriaképek esetében a blob-tárolóban lévő fájlokat a tartalmazó adatbázisokat tartalmazó mappákba kell helyezni. Az egyes adatbázis-mappákban az adatfájlokat az "adat" nevű almappába kell helyezni, és a name *Collection*. JSON formátumot kell elnevezni. A metaadat-fájlokat (ha vannak ilyenek) a "metadata" nevű almappában kell elhelyezni, és ugyanazt a formátumot kell elnevezni, a *Collection*. JSON fájllal. A metaadat-fájloknak meg kell egyezniük a MongoDB bsondump eszközzel létrehozott formátummal.

    > [!IMPORTANT]
    > Nem ajánlott önaláírt tanúsítványt használni a Mongo-kiszolgálón. Ha azonban ilyet használ, kapcsolódjon a kiszolgálóhoz **kapcsolati sztring mód** használatával, és győződjön meg arról, hogy a kapcsolati karakterlánc ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

    Használhatja az IP-címet olyan helyzetekben, amikor a DNS-névfeloldás nem lehetséges.

   ![Forrás adatainak megadása](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Kattintson a **Mentés** gombra.

   > [!NOTE]
   > A forráskiszolgáló címe legyen az elsődleges, ha a forrás egy replikakészlet, és az útválasztó, ha a forrás egy szilánkos MongoDB-fürt. A többplatformos MongoDB-fürtök esetében a Azure Database Migration Servicenak képesnek kell lennie csatlakozni a fürtben lévő egyes szegmensekhez, ami szükségessé teheti a tűzfal megnyitását több gépen.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az **áttelepítési cél részletei** képernyőn adja meg a cél Azure Cosmos db fiók kapcsolati adatait, amely az előre kiépített Azure Cosmos db API-ját a MongoDB-fiókhoz, amelyre a MongoDB-adatokat áttelepíti.

    ![Cél adatainak megadása](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Kattintson a **Mentés** gombra.

## <a name="map-to-target-databases"></a>Leképezés céladatbázisokra

1. A **térképen az adatbázisok megcélzása** képernyőn képezze le a forrást és a célként megadott adatbázist az áttelepítéshez.

   Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrás-adatbázis, akkor a Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázis-adatbázist.

   Ha a karakterlánc **létrehozása** az adatbázis neve mellett jelenik meg, az azt jelzi, hogy Azure Database Migration Service nem találta meg a célként megadott adatbázist, és a szolgáltatás létrehozza az adatbázist.

   Ha az áttelepítés ezen pontján a megosztás átviteli sebességét szeretné használni az adatbázison, adja meg az átviteli sebességet (RU). Cosmos DB az átviteli sebességet az adatbázis szintjén vagy egyenként, az egyes gyűjteményekhez is kiépítheti. Az átviteli sebességet a [kérelmek egységében](../cosmos-db/request-units.md) (RUs) mérjük. További információ a [Azure Cosmos db díjszabásáról](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Leképezés céladatbázisokra](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Kattintson a **Mentés** gombra.

3. A **gyűjtemény beállítása** képernyőn bontsa ki a gyűjtemények listáját, majd tekintse át az áttelepíteni kívánt gyűjtemények listáját.

   Azure Database Migration Service automatikusan kijelöli az összes olyan gyűjteményt, amely szerepel a forrás MongoDB-példányon, amely nem szerepel a cél Azure Cosmos DB fiókban. Ha olyan gyűjteményeket szeretne újratelepíteni, amelyek már tartalmaznak adatfájlokat, explicit módon ki kell választania a gyűjteményeket ezen a képernyőn.

   Megadhatja, hogy hány RUs-t kíván használni a gyűjtemények számára. A legtöbb esetben a 500 (1000 minimum a szilánkos gyűjtemények esetében) és a 4000 közötti értéknek elegendőnek kell lennie. A Azure Database Migration Service a gyűjtemény méretétől függően az intelligens alapértelmezett értékeket javasolja.

    > [!NOTE]
    > A Futtatás felgyorsításához hajtsa végre párhuzamosan az adatbázis áttelepítését és gyűjtését Azure Database Migration Service több példányával, ha szükséges.

   Megadhat egy szegmens kulcsot is, amellyel kihasználhatja a [particionálást Azure Cosmos db](../cosmos-db/partitioning-overview.md) az optimális méretezhetőség érdekében. Mindenképpen tekintse át az  [ajánlott eljárásokat a szegmens/partíciós kulcs kiválasztásához](../cosmos-db/partitioning-overview.md#choose-partitionkey). Ha nem rendelkezik partíciós kulccsal, a jobb teljesítmény érdekében mindig használhatja a **_id** .

   ![Gyűjtemények táblázatának kiválasztása](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Kattintson a **Mentés** gombra.

5. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

    ![A migrálás összegzése](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

   Megjelenik az áttelepítési tevékenység ablak, és megjelenik a tevékenység **állapota** .

   ![Tevékenység állapota](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása

* Az áttelepítési tevékenység képernyőn válassza a **frissítés** lehetőséget a Megjelenítés frissítéséhez, amíg az áttelepítés nem jelenik meg a **visszajátszás** **állapotában** .

   > [!NOTE]
   > Kiválaszthatja a tevékenységeket az adatbázis-és gyűjtési szintű áttelepítési metrikák részleteinek megtekintéséhez.

   ![Tevékenység állapotának visszajátszása](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Cosmos DBban lévő adatellenőrzés

1. Módosítsa a forrás MongoDB-adatbázisát.
2. Kapcsolódjon a COSMOS DB-hez, és ellenőrizze, hogy az adatok replikálódnak-e a forrás MongoDB-kiszolgálóról.

    ![Képernyőkép, amely azt mutatja be, hogy hol lehet ellenőrizni az adatreplikálást.](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

* Miután a forrás összes dokumentuma elérhető a COSMOS DB-tárolóban, válassza a **Befejezés** lehetőséget az áttelepítési tevékenység helyi menüjéből az áttelepítés befejezéséhez.

    Ez a művelet befejezi az összes függőben lévő módosítás ismételt lejátszását, és befejezi az áttelepítést.

    ![A befejezési menüt bemutató képernyőkép.](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Áttelepítés utáni optimalizálás

Miután áttelepítette a MongoDB-adatbázisban tárolt adatAzure Cosmos DB API-ját a MongoDB-hez, csatlakozhat Azure Cosmos DBhoz, és kezelheti az adatkezelési lehetőséget. Más áttelepítés utáni optimalizálási lépéseket is végrehajthat, például optimalizálhatja az indexelési házirendet, frissítheti az alapértelmezett konzisztencia-szintet, vagy konfigurálhatja a Azure Cosmos DB-fiók globális eloszlását. További információ: [áttelepítés utáni optimalizálási](../cosmos-db/mongodb-post-migration.md) cikk.

## <a name="additional-resources"></a>További források

* [Cosmos DB szolgáltatás adatai](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Következő lépések

* Tekintse át az áttelepítési útmutatót a Microsoft [Database áttelepítési útmutatóban](https://datamigration.microsoft.com/)található további forgatókönyvekhez.