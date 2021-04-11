---
title: Tárolók lekérdezése az Azure Cosmos DB-ben
description: Megtudhatja, hogyan kérdezheti le a tárolókat a Azure Cosmos DB partíciós és több partíciós lekérdezések használatával
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 5cd90e994e620960e0d974ef7609a67f8a5eb58b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448542"
---
# <a name="query-an-azure-cosmos-container"></a>Azure Cosmos-tároló lekérdezése
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez a cikk azt ismerteti, hogyan lehet lekérdezni egy tárolót (gyűjtemény, gráf vagy tábla) a Azure Cosmos DBban. Ez különösen azt ismerteti, hogyan működik a partíciós és a több partíciós lekérdezések Azure Cosmos DBban.

## <a name="in-partition-query"></a>Partíción belüli lekérdezés

Ha tárolóból kérdez le adatlekérdezést, akkor a Azure Cosmos DB automatikusan optimalizálja a lekérdezést. A lekérdezés a szűrőben megadott partíciós kulcs értékeinek megfelelő [fizikai partíciókhoz](partitioning-overview.md#physical-partitions) irányítja a lekérdezést.

Vegyük például az alábbi lekérdezést egy Esélyegyenlőségi szűrővel `DeviceId` . Ha ezt a lekérdezést egy-on particionált tárolón futtatjuk `DeviceId` , akkor ez a lekérdezés egyetlen fizikai partícióra lesz szűrve.

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

A korábbi példához hasonlóan a lekérdezés is egyetlen partícióra fog szűrni. A további szűrő hozzáadása nem `Location` változtatja meg a következőt:

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Az alábbiakban egy olyan lekérdezés található, amely a partíciós kulcson tartomány-szűrővel rendelkezik, és nem lesz egyetlen fizikai partícióra kiterjedően. A partíción belüli lekérdezéshez a lekérdezésnek rendelkeznie kell egy olyan egyenlőségi szűrővel, amely tartalmazza a partíciós kulcsot:

```sql
SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Többpartíciós kiterjedő lekérdezés

A következő lekérdezés nem rendelkezik szűrővel a partíciós kulcson ( `DeviceId` ). Ezért az összes olyan fizikai partícióra ki kell állnia, amely az egyes partíciók indexén fut:

```sql
SELECT * FROM c WHERE c.Location = 'Seattle`
```

Mindegyik fizikai partíció saját indextel rendelkezik. Ezért ha egy tárolón több partíciós *lekérdezést futtat, akkor fizikai* partíción keresztül gyakorlatilag egy lekérdezést futtat. A Azure Cosmos DB automatikusan összesíti az eredményeket a különböző fizikai partíciók között.

A különböző fizikai partíciókon lévő indexek egymástól függetlenek. Nincs globális index a Azure Cosmos DBban.

## <a name="parallel-cross-partition-query"></a>Párhuzamos többpartíciós lekérdezés

Az Azure Cosmos DB SDK-k 1.9.0, és később támogatják a párhuzamos lekérdezés-végrehajtási lehetőségeket. A párhuzamos többpartíciós lekérdezésekkel kis késésű többpartíciós lekérdezések hajthatók végre.

A lekérdezések párhuzamos végrehajtását az alábbi paraméterek beállításával kezelheti:

- **MaxConcurrency**: a tároló partícióinak egyidejű hálózati kapcsolatainak maximális számát állítja be. Ha ezt a tulajdonságot a értékre állítja `-1` , az SDK kezeli a párhuzamosság mértékét. Ha a értékre van  `MaxConcurrency` állítva `0` , a tároló partícióinak egyetlen hálózati kapcsolatai vannak.

- **MaxBufferedItemCount**: kompromisszumot alakít ki a lekérdezések késése és az ügyféloldali memóriahasználat között. Ha ez a beállítás nincs megadva, vagy az-1 értékre van állítva, az SDK kezeli a párhuzamos lekérdezés végrehajtása során pufferelt elemek számát.

Mivel a Azure Cosmos DB a több partíciós lekérdezéseket is integrálással, a lekérdezési késés általában jól méretezhető, és a rendszer [fizikai partíciókat](partitioning-overview.md#physical-partitions)ad hozzá. Az RU-díj azonban jelentősen megnő, mivel a fizikai partíciók száma növekszik.

Ha több partíciós lekérdezést futtat, a rendszer lényegében külön lekérdezést hajt végre az egyes fizikai partíciók esetében. Míg a több partíciós lekérdezések lekérdezései az indexet fogják használni, ha elérhetők, még nem annyira hatékonyak, mint a partíciós lekérdezésekben.

## <a name="useful-example"></a>Hasznos példa

A következő analógia a több partíciós lekérdezések jobb megismerése:

Tegyük fel, hogy Ön egy kézbesítési illesztőprogram, amelynek a csomagjait különböző apartmankomplexum-komplexekhez kell továbbítania. Minden apartman-komplexum rendelkezik egy listával azon a helyszínen, amely rendelkezik az összes rezidens egység számával. Az összes apartmankomplexum összehasonlítható egy fizikai partícióval és minden listával a fizikai partíció indexével.

A következő példa használatával összehasonlíthatja a partíciós és a több partíciós lekérdezéseket:

### <a name="in-partition-query"></a>Partíción belüli lekérdezés

Ha a kézbesítési illesztőprogram ismeri a megfelelő apartman-komplexet (fizikai partíció), akkor azonnal elvégezheti a megfelelő épület elvégzését. Az illesztőprogram megtekintheti a rezidens egység számai (az index) listáját, és gyorsan továbbíthatja a megfelelő csomagokat. Ebben az esetben az illesztőprogram nem pazarol el semmilyen időt vagy fáradságot arra, hogy egy lakás-összetételre váltson, és ellenőrizze, hogy vannak-e a csomagok címzettjei.

### <a name="cross-partition-query-fan-out"></a>Több partíciós lekérdezés (ventilátor kijelentkezése)

Ha a kézbesítési illesztőprogram nem ismeri a megfelelő lakás-összetételt (fizikai partíciót), minden egyes lakás-épülethez kell vezetnie, és ellenőriznie kell a listát az összes rezidens egység számával (az indextel). Miután minden egyes apartmankomplexum megérkeznek, továbbra is használhatja az egyes rezidensek címeinek listáját. Azonban ellenőriznie kell az összes lakópark listáját, függetlenül attól, hogy a csomag címzettjei ott is laknak. A több partíciós lekérdezések működése. Habár használhatják az indexet (nem kell minden egyes ajtót kiütni), külön kell ellenőriznie az indexet minden fizikai partíciónál.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Több partíciós lekérdezés (hatóköre csak néhány fizikai partícióra vonatkozik)

Ha a kézbesítési illesztőprogram tudja, hogy az összes csomag címzettjei egy bizonyos lakás-komplexumon belül vannak, akkor nem kell minden egyeshez vezetniük. A néhány apartman-komplexumhoz való vezetés továbbra is több munkát igényel, mint a csak egyetlen épület meglátogatása, a kézbesítési illesztőprogram továbbra is jelentős időt és fáradságot takarít meg. Ha egy lekérdezésben a kulcsszóval rendelkező szűrőben a partíció kulcsa szerepel `IN` , a rendszer csak a megfelelő fizikai partíció indexeit fogja megkeresni az adatokat.

## <a name="avoiding-cross-partition-queries"></a>A több partíciós lekérdezések elkerülése

A legtöbb tároló esetében elkerülhetetlen, hogy több partíciós lekérdezésre lesz szüksége. A több partíciós lekérdezések használata rendben van! Majdnem minden lekérdezési művelet támogatott a partíciók között (a logikai partíció kulcsai és a fizikai partíciók is). A Azure Cosmos DB számos optimalizációt is tartalmaz a lekérdezési motor és az ügyfél SDK-k között, hogy integrálással a lekérdezések végrehajtását a fizikai partíciók között.

A legtöbb olvasási nehéz forgatókönyv esetében ajánlott egyszerűen kiválasztani a leggyakoribb tulajdonságot a lekérdezési szűrőkben. Győződjön meg arról is, hogy a partíciós kulcs megfelel a többi [partíciós kulcs kiválasztására vonatkozó ajánlott eljárásoknak](partitioning-overview.md#choose-partitionkey).

A több partíciós lekérdezések elkerülése általában csak nagy méretű tárolókkal kapcsolatos. Minden alkalommal, amikor egy fizikai partíció indexét a lekérdezés szűrője alapján megtekinti, akkor is, ha a fizikai partíció egyik eleme sem felel meg az eredményeknek, akkor is legalább 2,5 RU díjat számítunk fel. Ilyen esetben, ha csak egy (vagy csak néhány) fizikai partícióval rendelkezik, a több partíciós lekérdezés nem fogja lényegesen több, mint a partíciós lekérdezéseket használni.

A fizikai partíciók száma a kiosztott RU-k mennyiségétől van kötve. Minden fizikai partíció legfeljebb 10 000 üzembe helyezést tesz lehetővé, és akár 50 GB-nyi adat tárolására is képes. A Azure Cosmos DB automatikusan felügyeli a fizikai partíciókat. A tárolóban lévő fizikai partíciók száma a kiosztott átviteli sebességtől és a felhasznált tárterülettől függ.

Próbálkozzon a több partíciós lekérdezések elkerülésével, ha a munkaterhelés megfelel az alábbi feltételeknek:
- Több mint 30 000 RU-t tervez kiépíteni
- 100 GB-nál több adat tárolását tervezi

## <a name="next-steps"></a>Következő lépések

A particionálással kapcsolatos tudnivalókat az alábbi cikkekben találja Azure Cosmos DB:

- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
- [Szintetikus partíciókulcsok az Azure Cosmos DB-ben](synthetic-partition-keys.md)
