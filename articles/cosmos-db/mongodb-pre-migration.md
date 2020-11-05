---
title: Áttelepítés előtti lépések a Azure Cosmos DB API-MongoDB való áttelepítéshez
description: Ez a dokumentum áttekintést nyújt a MongoDB és a Cosmos DB közötti adatáttelepítés előfeltételeiről.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/01/2020
ms.author: chrande
ms.openlocfilehash: 8ad164f79f150e0cd6ab4a083f21b22c59f7c729
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93361585"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Áttelepítés előtti lépések a MongoDB-ből Azure Cosmos DB API-MongoDB való áttelepítéshez
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Mielőtt áttelepíti az adatait a MongoDB-ből (akár a helyszínen, akár a felhőben) Azure Cosmos DB API-ját a MongoDB-hez, a következőket kell tennie:

1. [A MongoDB-hez készült Azure Cosmos DB API használatának főbb szempontjai](#considerations)
2. [Válassza ki az adatáttelepítés lehetőségét](#options)
3. [A munkaterhelésekhez szükséges átviteli sebesség becslése](#estimate-throughput)
4. [Válasszon ki egy optimális partíciós kulcsot az adataihoz](#partitioning)
5. [Az adataihoz beállítható indexelési szabályzat ismertetése](#indexing)

Ha már elvégezte a fenti előfeltételeket az áttelepítéshez, [áttelepítheti a MongoDB-Azure Cosmos db API-ját a MongoDB Azure Database Migration Service használatával](../dms/tutorial-mongodb-cosmos-db.md). Ha még nem hozott létre fiókot, böngészhet bármely olyan rövid útmutatóban [, amely megjeleníti a fiók](create-mongodb-dotnet.md) létrehozásának lépéseit.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Szempontok a Azure Cosmos DB API-MongoDB való használatához

A MongoDB Azure Cosmos DB API-ját a következők konkrét jellemzőkkel bírnak:

- **Kapacitási modell** : az Azure Cosmos db adatbázis-kapacitása egy átviteli alapú modellen alapul. Ez a modell másodpercenkénti [kérelmeken](request-units.md)alapul, ami egy olyan egység, amely az adatbázis-műveletek másodpercenkénti számát mutatja. Ez a kapacitás [egy adatbázis vagy gyűjtemény szintjén](set-throughput.md)foglalható le, és kiosztható egy kiosztási modellben, vagy az [autoscale kiépített átviteli sebesség](provision-throughput-autoscale.md)használatával.

- **Kérelmek egységei** : minden adatbázis-művelethez tartozik egy társított kérési egység (RUs) díja Azure Cosmos db. A művelet végrehajtásakor a rendszer kivonja az elérhető kérelmek egységének szintjéről az adott másodpercben. Ha egy kérelem több RUs-t igényel, mint a jelenleg lefoglalt RU/s, két lehetőség van a probléma megoldására – növelje az RUs mennyiségét, vagy várjon, amíg a következő másodperc elindul, majd próbálja megismételni a műveletet.

- **Rugalmas kapacitás** : egy adott gyűjtemény vagy adatbázis kapacitása bármikor megváltozhat. Ez lehetővé teszi, hogy az adatbázis rugalmasan alkalmazkodjon a számítási feladatok átviteli követelményeihez.

- **Automatikus** skálázás: a Azure Cosmos db egy automatikus particionálási rendszer, amely csak egy szegmens (vagy egy partíciós kulcs) megadását igényli. Az [automatikus particionálási mechanizmus](partitioning-overview.md) az összes Azure Cosmos db API-ban meg van osztva, és lehetővé teszi a zökkenőmentes adattárolást és a horizontális eloszláson keresztüli teljes méretezést.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Áttelepítési lehetőségek a Azure Cosmos DB API-MongoDB

A [Azure Cosmos db API-MongoDB való Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) egy olyan mechanizmust biztosít, amely leegyszerűsíti az adatáttelepítést azáltal, hogy teljes körűen felügyelt üzemeltetési platformot biztosít, áttelepítési figyelési lehetőségeket és automatikus szabályozási kezelést tesz lehetővé. A lehetőségek teljes listája a következő:

|**Áttelepítés típusa**|**Megoldás**|**Megfontolások**|
|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé <br/>&bull; Nagyméretű adatkészletekhez alkalmas, és gondoskodik az élő módosítások replikálásáról <br/>&bull; Csak más MongoDB-forrásokkal működik|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé <br/>&bull; Nagyméretű adatkészletekhez alkalmas, és gondoskodik az élő módosítások replikálásáról <br/>&bull; Csak más MongoDB-forrásokkal működik|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; Egyszerűen beállítható és támogatott több forrás <br/>&bull; A Azure Cosmos DB tömeges végrehajtó függvénytár használatát teszi lehetővé <br/>&bull; Nagyméretű adatkészletekhez alkalmas <br/>&bull; Az ellenőrzőpontok hiánya azt jelenti, hogy az áttelepítés során esetlegesen felmerülő problémákhoz a teljes áttelepítési folyamat újraindítása szükséges.<br/>&bull; A kézbesítetlen levelek várólistájának hiánya azt jelenti, hogy néhány hibás fájl nem tudja leállítani a teljes áttelepítési folyamatot <br/>&bull; Egyéni kóddal kell rendelkeznie bizonyos adatforrások olvasási teljesítményének növeléséhez|
|Offline|[Meglévő Mongo-eszközök (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Egyszerűen beállítható és integrálható <br/>&bull; Egyéni kezelést igényel a szabályozáshoz|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> A számítási feladatokhoz szükséges átviteli sebesség becslése

Azure Cosmos DB az átviteli sebességet előre kiépítve, és a rendszer a kérelmek egységében (RU) mérve másodpercenként méri. A virtuális gépekkel és a helyszíni kiszolgálókkal ellentétben a RUs bármikor könnyedén méretezhető. A kiosztott RUs-ket azonnal megváltoztathatja. További információ: [kérelmek egységei a Azure Cosmos DBban](request-units.md).

A [Azure Cosmos db kapacitás-kalkulátor](https://cosmos.azure.com/capacitycalculator/) segítségével meghatározhatja a kérelmek mennyiségét az adatbázis-fiók konfigurációja, az adatmennyiség, a dokumentum mérete és a szükséges olvasási és írási műveletek alapján.

A következő kulcsfontosságú tényezők befolyásolják a szükséges RUs-ket:
- **Dokumentum mérete** : az elem/dokumentum méretének növekedésével az elem/dokumentum olvasásához vagy írásához felhasznált RUs száma is nő.

- **Dokumentum tulajdonságainak száma** : a dokumentumok létrehozásához vagy frissítéséhez felhasznált RUs száma a tulajdonságok számával, összetettségével és hosszával kapcsolatos. Az [indexelt tulajdonságok számának korlátozásával](mongodb-indexing.md)csökkentheti a kérések egység általi felhasználását írási műveletekhez.

- **Lekérdezési minták** : a lekérdezés bonyolultsága befolyásolja, hogy a lekérdezés hány kérési egységet használ fel. 

A lekérdezési költségek megismerésének legjobb módja a Azure Cosmos DBban található mintaadatok használata, és a [MongoDB-rendszerhéjból származó mintavételi lekérdezések futtatása](connect-mongodb-account.md) a parancs használatával a `getLastRequestStastistics` kérések díja, amely a felhasználható RUs mennyiségét fogja kinyerni:

`db.runCommand({getLastRequestStatistics: 1})`

Ez a parancs a következőhöz hasonló JSON-dokumentumot fog kiadni:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

A [diagnosztikai beállítások](cosmosdb-monitor-resource-logs.md) segítségével megismerheti a Azure Cosmos DBon végrehajtott lekérdezések gyakoriságát és mintáit. A diagnosztikai naplók eredményei elküldhetők egy Storage-fiókba, egy EventHub-példányba vagy egy [Azure-log Analyticsba](../azure-monitor/log-query/get-started-portal.md).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Válassza ki a partíciós kulcsot
A particionálás (más néven horizontális skálázás) az adatáttelepítés előtt kulcsfontosságú szempont. A Azure Cosmos DB a teljes körűen felügyelt particionálással növelheti a kapacitást egy adatbázisban a tárolási és átviteli sebességre vonatkozó követelmények teljesítése érdekében. Ennek a szolgáltatásnak nincs szüksége az útválasztási kiszolgálók üzemeltetésére vagy konfigurálására.   

Hasonló módon a particionálási funkció automatikusan bővíti a kapacitást, és ennek megfelelően kiegyensúlyozza az adatelosztást. Az adatok megfelelő partíciós kulcsának kiválasztásával kapcsolatos részletekért és javaslatokért tekintse meg a [partíciós kulcs kiválasztása című cikket](partitioning-overview.md#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Adatok indexelése

A MongoDB Server 3,6-es verziójában a Azure Cosmos DB API automatikusan indexeli a `_id` mezőt. Ezt a mezőt nem lehet eldobni. Automatikusan kikényszeríti a mező egyediségét `_id` . További mezők indexeléséhez a MongoDB indexkezelési parancsait használja. Ez az automatikus indexelési szabályzat különbözik az Azure Cosmos DB SQL API-tól, amely alapértelmezés szerint az összes mezőt indexeli.

A Azure Cosmos DB által biztosított indexelési képességek közé tartozik az összetett indexek, az egyedi indexek és az élettartam (TTL) indexek hozzáadása. Az index felügyeleti felülete a parancsra van leképezve `createIndex()` . További információ: [Azure Cosmos db API-MongoDB című cikk indexelése](mongodb-indexing.md).

A [Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) automatikusan áttelepíti a MongoDB-gyűjteményeket egyedi indexekkel. Az áttelepítést megelőzően azonban létre kell hozni az egyedi indexeket. A Azure Cosmos DB nem támogatja az egyedi indexek létrehozását, ha már szerepelnek a gyűjtemények adatai. További információ: [Azure Cosmos DBban található egyedi kulcsok](unique-keys.md).

## <a name="next-steps"></a>Következő lépések
* [Telepítse át a MongoDB-adatait Cosmos DB a Database Migration Service használatával.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Átviteli sebesség az Azure Cosmos-tárolók és-adatbázisok számára](set-throughput.md)
* [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
* [Globális eloszlás Azure Cosmos DB](distribute-data-globally.md)
* [Indexelés az Azure Cosmos DB-ben](index-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
