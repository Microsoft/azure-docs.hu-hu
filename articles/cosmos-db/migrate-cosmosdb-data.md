---
title: Több száz terabájtnyi adat migrálása az Azure Cosmos DB-be
description: Ez a dokumentum azt ismerteti, hogyan telepíthet át több terabájtos adathalmazt Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: b24ea79737c9e1f64abb7f62807352dbd9573695
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018071"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Több száz terabájtnyi adat migrálása az Azure Cosmos DB-be 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos DB több terabájtnyi adatot tud tárolni. Nagy léptékű adatmigrálás elvégzésével áthelyezheti éles számítási feladatait az Azure Cosmos DB-be. Ez a cikk azokat a kihívásokat ismerteti, amelyek a nagy léptékű adatok az Azure Cosmos DB-be való áthelyezésével kapcsolatosak, továbbá bemutatja az eszközt, amely segít a kihívások leküzdésében, és az adatok az Azure Cosmos DB-be történő migrálásában. Ebben az esettanulmányban az ügyfél a Cosmos DB SQL API-t használta.  

Mielőtt áttelepíti a teljes munkaterhelést Azure Cosmos DBre, áttelepítheti az adatok egy részhalmazát, hogy érvényesítse a különböző szempontokat, például a partíciós kulcs választását, a lekérdezési teljesítményt és az adatmodellezést. A koncepció ellenőrzése után áthelyezheti a teljes munkaterhelést Azure Cosmos DBra.  

## <a name="tools-for-data-migration"></a>Adatmigrálás eszközei 

Azure Cosmos DB áttelepítési stratégiák jelenleg eltérőek az API-k választása és az adatmennyiség alapján. Kisebb adatkészletek áttelepítéséhez – az adatmodellezés ellenőrzése, a lekérdezési teljesítmény, a partíciós kulcs választása stb. – kiválaszthatja az [áttelepítési eszközt](import-data.md) vagy a [Azure Data Factory Azure Cosmos db-összekötőjét](../data-factory/connector-azure-cosmos-db.md). Ha már ismeri a Sparkot, a [Azure Cosmos db Spark-összekötő](spark-connector.md) használatával is áttelepítheti az adatátvitelt.

## <a name="challenges-for-large-scale-migrations"></a>A nagyméretű Migrálás kihívásai 

Az Azure Cosmos DB az adatáttelepítés meglévő eszközei bizonyos korlátozásokkal rendelkeznek, amelyek különösen nagy léptékekben lesznek láthatók:

 * **Korlátozott kibővíthető képességek**: Ha a lehető leggyorsabban szeretné áttelepíteni a terabájtos adatmennyiséget a Azure Cosmos DBba, és hatékonyan felhasználja a teljes kiosztott átviteli sebességet, az áttelepítési ügyfeleknek képesnek kell lenniük a határozatlan idejű méretezésre.  

* A **folyamat nyomon követésének és ellenőrzésének hiánya**: fontos, hogy nyomon követhesse az áttelepítési folyamatot, és a nagyméretű adathalmazok áttelepítése során a rendszer a bejelentkezést is bemutasson. Ellenkező esetben az áttelepítés során felmerülő hibák miatt a rendszer leállítja az áttelepítést, és teljesen el kell indítania a folyamatot. Nem lenne hatékony a teljes áttelepítési folyamat újraindítása, amikor a 99%-a már befejeződött.  

* A **kézbesítetlen levelek várólistájának hiánya**: a nagyméretű adatkészleteken belül bizonyos esetekben problémák merülhetnek fel a forrásadatok részeivel. Emellett átmeneti problémák merülhetnek fel az ügyféllel vagy a hálózattal kapcsolatban. Ezen esetek egyike sem okozhatja a teljes áttelepítést. Annak ellenére, hogy a legtöbb áttelepítési eszköz robusztus újrapróbálkozási képességekkel rendelkezik, amelyek védelmet biztosítanak az időszakos problémák ellen, nem mindig elég. Ha például a forrásoldali adatdokumentumok kevesebb mint 0,01%-a mérete meghaladja a 2 MB-ot, akkor a dokumentum írása sikertelen lesz Azure Cosmos DB. Ideális esetben az áttelepítési eszköz számára hasznos, hogy a "sikertelen" dokumentumokat egy másik kézbesítetlen levelek várólistáján is megőrzi, amely az áttelepítés után dolgozható fel. 

A korlátozások nagy része az Azure-beli adatáttelepítési szolgáltatásokhoz hasonló eszközökhöz készült. 

## <a name="custom-tool-with-bulk-executor-library"></a>Egyéni eszköz tömeges végrehajtó könyvtárral 

A fentiekben ismertetett kihívások megoldhatók egy olyan egyéni eszköz használatával, amely könnyen méretezhető több példányon, és rugalmas az átmeneti hibákkal szemben. Emellett az egyéni eszköz szüneteltetheti és folytathatja az áttelepítés különböző ellenőrzőpontokon való futtatását. Azure Cosmos DB már a [tömeges végrehajtó függvénytárat](./bulk-executor-overview.md) is tartalmazza, amely tartalmazza ezeket a funkciókat. A tömeges végrehajtó függvénytár például már rendelkezik az átmeneti hibák kezeléséhez szükséges funkciókkal, és a szálakat egyetlen csomóponton is kibővítheti, hogy a rendszer a csomópontok 500 K/s RUs-t használja. A tömeges végrehajtó függvénytár a forrás-adatkészletet az ellenőrzőpont-kezelés formájában egymástól függetlenül működő mikro-kötegekre is particionálja.  

Az egyéni eszköz a tömeges végrehajtó függvénytárat használja, és támogatja a több ügyfél közötti skálázást és a hibák követését a betöltési folyamat során. Az eszköz használatához a forrásadatokat Azure Data Lake Storage (ADLS) különálló fájlokba kell particionálni, hogy a különböző áttelepítési munkatársak minden fájlt felvegyenek, és betöltsék azokat a Azure Cosmos DBba. Az egyéni eszköz egy különálló gyűjteményt használ, amely metaadatokat tárol a ADLS lévő egyes forrásfájlok áttelepítési folyamatával kapcsolatban, és nyomon követi a velük kapcsolatos hibákat.  

Az alábbi ábra az áttelepítési folyamatot írja le az egyéni eszközzel. Az eszköz virtuális gépeken fut, és minden egyes virtuális gép lekérdezi a nyomkövetési gyűjteményt Azure Cosmos DBban, hogy beszerezzen egy bérletet az egyik forrásoldali adatpartíción. Ha ez megtörtént, a rendszer beolvassa a forrásoldali adatpartíciót, és betölti a Azure Cosmos DBba a tömeges végrehajtó függvénytár használatával. Ezután a rendszer frissíti a nyomkövetési gyűjteményt, hogy rögzítse az adatfeldolgozási folyamat állapotát és az észlelt hibákat. Az adatpartíció feldolgozása után az eszköz megkísérli lekérdezni a következő rendelkezésre álló forrásoldali partíciót. Továbbra is feldolgozza a következő forrás partíciót, amíg az összes adatáttelepítés be nem fejeződik. Az eszköz forráskódja a [Azure Cosmos db tömeges](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion) betöltési tárházban érhető el.  

 
:::image type="content" source="./media/migrate-cosmosdb-data/migrationsetup.png" alt-text="Áttelepítési eszköz beállítása" border="false":::
 

 

A nyomkövetési gyűjtemény dokumentumokat tartalmaz, az alábbi példában látható módon. Ezeket a dokumentumokat a forrásadatok minden egyes partíciója esetében látni fogja.  Minden dokumentum tartalmazza a forrás adatpartíció metaadatait, például a helyét, az áttelepítési állapotot és a hibákat (ha vannak):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Az adatáttelepítés előfeltételei 

Az áttelepítés megkezdése előtt néhány előfeltételt figyelembe kell venni:  

#### <a name="estimate-the-data-size"></a>Az adat méretének becslése:  

Előfordulhat, hogy a forrásadatok mérete nem felel meg pontosan a Azure Cosmos DB adatméretének. A forrásból néhány példa is beilleszthető az adatok méretének Azure Cosmos DB való vizsgálatához. A mintául szolgáló dokumentum méretétől függően a Azure Cosmos DB az áttelepítés utáni teljes adatméretet is becsülheti. 

Ha például az Azure Cosmos DB-ban való áttelepítést követően minden dokumentum körülbelül 1 KB méretű, és ha a forrás adatkészletben körülbelül 60 000 000 000 dokumentum található, az azt jelenti, hogy a Azure Cosmos DB becsült mérete a 60 TB-hoz közel van. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Előre létrehozott tárolók elég RUs-vel: 

Bár a Azure Cosmos DB automatikusan kibővíti a tárolót, nem tanácsos a legkisebb méretű tárolótól elindulni. A kisebb méretű tárolók alacsonyabb átviteli sebességgel rendelkeznek, ami azt jelenti, hogy az áttelepítés sokkal hosszabb időt is igénybe veheti. Ehelyett érdemes létrehozni a tárolókat a végső adatmérettel (az előző lépésben becsült értékként), és győződjön meg arról, hogy az áttelepítési munkaterhelés teljes mértékben felhasználja a kiosztott átviteli sebességet.  

Az előző lépésben. mivel az adatméretet körülbelül 60 TB-ra becsülik, legalább 2,4 M RUs tárolóra van szükség a teljes adatkészlet befogadásához.  

 

#### <a name="estimate-the-migration-speed"></a>Az áttelepítési sebesség becslése: 

Feltételezve, hogy az áttelepítési munkaterhelés a teljes kiépített átviteli sebességet is felhasználhatja, az egész számú kiépített mennyiség becslést nyújt az áttelepítési sebességről. Az előző példa folytatásához 5 RUs szükséges egy 1 KB-os dokumentum Azure Cosmos DB SQL API-fiókba való írásához.  2 400 000 RUs lehetővé tenné a 480 000-dokumentumok másodpercenkénti átvitelét (vagy 480 MB/s). Ez azt jelenti, hogy a 60 TB teljes migrálása 125 000 másodpercet vagy körülbelül 34 órát vesz igénybe.  

Ha azt szeretné, hogy az áttelepítés egy napon belül befejeződik, növelje a kiépített átviteli sebességet 5 000 000 RUs-ra. 

 

#### <a name="turn-off-the-indexing"></a>Az Indexelés kikapcsolása:  

Mivel az áttelepítést a lehető leghamarabb el kell végezni, célszerű minimálisra csökkenteni az időt és az RUs-t a betöltött dokumentumok indexek létrehozásához.  Azure Cosmos DB automatikusan indexeli az összes tulajdonságot, érdemes az indexelést a kiválasztott néhány kifejezésre csökkenteni, vagy teljesen kikapcsolni az áttelepítés során. A tároló indexelési házirendjét úgy is kikapcsolhatja, hogy a indexingMode a none értékre változtatja, az alábbi ábrán látható módon:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Az áttelepítés befejezése után frissítheti az indexelést.  

## <a name="migration-process"></a>Migrálási folyamat 

Az előfeltételek teljesítése után az alábbi lépéseket követve áttelepítheti az adatátvitelt:  

1. Először importálja az adatait a forrásból az Azure Blob Storageba. A Migrálás sebességének növelése érdekében hasznos lehet a különböző integrálással között. Az áttelepítés megkezdése előtt a forrás adatkészletet a 200 MB méretű méretű fájlokra kell particionálni.   

2. A tömeges végrehajtó függvénytár vertikális felskálázást hajt végre, hogy a 500 000 RUs-t egyetlen ügyfél virtuális gépen használja fel. Mivel a rendelkezésre álló átviteli sebesség 5 000 000 RUs, 10 Ubuntu 16,04 virtuális gépet (Standard_D32_v3) kell kiépíteni ugyanabban a régióban, ahol az Azure Cosmos-adatbázis található. Ezeket a virtuális gépeket az áttelepítési eszközzel és a hozzá tartozó beállítási fájllal készítse elő.  

3. Futtassa az üzenetsor-lépést az egyik ügyfél virtuális gépen. Ez a lépés létrehozza a nyomkövetési gyűjteményt, amely megvizsgálja a ADLS tárolót, és egy folyamatjelző dokumentumot hoz létre az egyes forrásoldali adatkészletek partíciós fájljaihoz.  

4. Ezután futtassa az importálás lépést az összes ügyfél virtuális gépen. Mindegyik ügyfél elvégezheti a tulajdonjogot egy forrásoldali partíción, és betöltheti az adatmennyiségét Azure Cosmos DBba. Miután befejeződött, és az állapota frissült a nyomkövetési gyűjteményben, az ügyfelek lekérhetik a következő rendelkezésre álló forrásoldali partíciót a nyomkövetési gyűjteményben.  

5. Ez a folyamat addig folytatódik, amíg a forrásoldali partíciók teljes készlete nem volt betöltve. Az összes forrásoldali partíció feldolgozása után az eszközt újra kell futtatni a hibajavítási módban ugyanazon a nyomkövetési gyűjteményen. Ez a lépés a hibák miatt újra feldolgozandó forrásfájlok azonosításához szükséges.  

6. Ezen hibák némelyike a forrásadatok helytelen dokumentumai miatt lehet. Ezeket azonosítani és rögzíteni kell. Ezt követően újra kell futtatnia az importálás lépést a sikertelen partíciók között a visszatöltéshez. 

Az áttelepítés befejezése után ellenőrizheti, hogy a dokumentumok száma Azure Cosmos DB-e, és megegyezik-e a forrás-adatbázisban lévő dokumentumok számával. Ebben a példában a Azure Cosmos DB teljes mérete 65 terabájtra van kialakítva. A Migrálás után az indexelés szelektíven bekapcsolható, és az RUs a számítási feladatok műveletei által megkövetelt szintre is csökkenthető.

## <a name="next-steps"></a>Következő lépések

* További információ: a [.net](bulk-executor-dot-net.md) és a [Java](bulk-executor-java.md)szolgáltatásban a tömeges végrehajtó függvénytárat használó minta alkalmazások kipróbálása. 
* A tömeges végrehajtó függvénytár integrálva van a Cosmos DB Spark-összekötőbe, és további információt a [Azure Cosmos db Spark-összekötő](spark-connector.md) című cikkben talál.  
* A nagyméretű áttelepítéssel kapcsolatos további segítségért lépjen kapcsolatba a Azure Cosmos DB termék csapatával, és nyisson meg egy támogatási jegyet az "általános tanácsadó" probléma típusa és a "nagy (TB +) Migrálás" problémás altípusban.