---
title: A módosítási hírcsatorna támogatásának használata Azure Cosmos DB
description: A Azure Cosmos DB módosítás-visszavételi támogatással nyomon követheti a dokumentumok, az eseményvezérelt feldolgozás, például az eseményindítók változásait, valamint naprakészen tarthatja a gyorsítótárait és az elemzési rendszereket.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18, "seo-nov-2020"
ms.openlocfilehash: c6856a0cb70123f1a3570b611c81660a592fdc1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98027769"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Változáscsatorna az Azure Cosmos DB-ben
[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]

A Azure Cosmos DBban lévő adatcsatorna módosítása a tároló változásainak állandó rekordja a megrendelésük sorrendjében. A változáscsatorna támogatása az Azure Cosmos DB-ben egy Azure Cosmos-tároló változásainak figyelésével működik. Ezután a módosításuk sorrendjében felsorolja a módosított dokumentumokat. A megőrzött módosítások aszinkron és növekményes feldolgozással is feldolgozhatók, a kimenet pedig egy vagy több felhasználó között terjeszthető párhuzamos feldolgozásra.

További információ a [hírcsatorna kialakítási mintáinak módosításáról](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>Támogatott API-k és ügyféloldali SDK-k

Ezt a funkciót jelenleg a következő Azure Cosmos DB API-k és ügyféloldali SDK-k támogatják.

| **Ügyfél-illesztőprogramok** | **SQL API** | **Azure Cosmos DB a Cassandra-hez készült API** | **MongoDB-hez készült Azure Cosmos DB API** | **Gremlin API**|**Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Igen | Igen | Igen | Igen | Nem |
|Java|Igen|Igen|Igen|Igen|Nem|
|Python|Igen|Igen|Igen|Igen|Nem|
|Node/JS|Igen|Igen|Igen|Igen|Nem|

## <a name="change-feed-and-different-operations"></a>A hírcsatorna és a különböző műveletek módosítása

Napjainkban az összes Beszúrás és frissítés megjelenik a változási hírcsatornában. Egy adott típusú művelet esetében nem lehet szűrni a változási csatornát. Az egyik lehetséges alternatíva az, hogy egy "lágy jelölőt" ad hozzá a frissítésekhez és a szűrőhöz a változási csatornán lévő elemek feldolgozásakor.

Az aktuális változási csatorna nem törli a naplókat. Az előző példához hasonlóan hozzáadhat egy lágy jelölőt is a törölt elemekhez. Hozzáadhat például egy attribútumot a "törölt" nevű elemhez, és beállíthatja "true" értékre, és beállíthatja az elem ÉLETTARTAMát, hogy az automatikusan törölhető legyen. Elolvashatja a változási csatornát a korábbi elemek esetében (az elemnek megfelelő legutóbbi módosítást, nem tartalmazza a köztes változásokat), például az öt évvel ezelőtt hozzáadott elemeket. A változási csatornát visszatekintheti a tároló forrásaként, de ha töröl egy elemet, a rendszer eltávolítja a változási hírcsatornából.

### <a name="sort-order-of-items-in-change-feed"></a>Elemek rendezési sorrendje a változási hírcsatornában

A hírcsatorna-elemek módosítása a módosítási idő sorrendjében történik. Ez a rendezési sorrend logikai partíciós kulcs alapján garantált.

### <a name="consistency-level"></a>Konzisztenciaszint

Miközben a változási csatornát egy végleges konzisztencia-szinten fogyasztja, előfordulhat, hogy a későbbi módosítási hírcsatorna olvasási műveletei között ismétlődő események szerepelnek (az egyik olvasási művelet utolsó eseménye a következőként jelenik meg).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>A többrégiós Azure Cosmos-fiókok csatornájának módosítása

Ha egy többrégiós Azure Cosmos-fiókban egy írási régió feladatátvételt hajt végre, akkor a változási csatorna a manuális feladatátvételi művelet során fog működni, és összefüggő lesz.

### <a name="change-feed-and-time-to-live-ttl"></a>A hírcsatorna és az élettartam (TTL) módosítása

Ha a TTL (time to Live) tulajdonság egy elemre van állítva, akkor a változási csatorna örökre fennmarad. Ha a rendszer nem törli az adatvesztést, akkor a változási hírcsatornában marad.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>A hírcsatorna és a _etag módosítása, _lsn vagy _ts

A _etag formátuma belső, és nem érdemes attól függővé tenni, mert bármikor megváltozhat. _ts egy módosítás vagy egy létrehozási időbélyeg. A _ts a kronológiai összehasonlításhoz használható. _lsn egy olyan batch-azonosító, amely csak a változási hírcsatornához lett hozzáadva; Ez a tranzakció AZONOSÍTÓját jelöli. Előfordulhat, hogy számos elemnek ugyanaz a _lsna. A FeedResponse lévő ETag eltér az elemen látható _etagtól. a _etag egy belső azonosító, amelyet a rendszer a Egyidejűség-vezérléshez használ. Az _etag tulajdonság az elem verziójáról szól, míg a ETag tulajdonságot a rendszer a hírcsatorna előkészítésére használja.

## <a name="working-with-change-feed"></a>A változási csatorna használata

A Change feed a következő beállításokkal használható:

* [A Change feed használata a Azure Functions](change-feed-functions.md)
* [Módosítási hírcsatorna használata a Change feed processzorral](change-feed-processor.md) 

A módosítási hírcsatorna a tárolóban lévő minden logikai partíciós kulcshoz elérhető, és az alábbi képen látható módon osztható fel egy vagy több felhasználó között párhuzamos feldolgozásra.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Azure Cosmos DB változási csatorna elosztott feldolgozása" border="false":::

## <a name="features-of-change-feed"></a>A változási hírcsatorna funkciói

* A módosítási hírcsatorna alapértelmezés szerint engedélyezve van az összes Azure Cosmos-fiók esetében.

* Az Azure Cosmos-adatbázishoz társított régiókban a [kiépített átviteli sebesség](request-units.md) használatával a változási csatornát ugyanúgy használhatja, mint bármely más Azure Cosmos db művelethez.

* A módosítási hírcsatorna tartalmazza a tárolóban lévő elemekhez tartozó lapkákat és frissítési műveleteket. A törlések rögzítéséhez állítson be egy "Soft-Delete" jelzőt az elemeken belül (például dokumentumok) a törlés helyett. Másik lehetőségként megadhat egy véges lejárati időszakot az elemekhez az [élettartam lehetőséggel](time-to-live.md). Tegyük fel például, hogy 24 óra, és az adott tulajdonság értékeként rögzíti a törléseket. Ezzel a megoldással a módosításokat a TTL lejárati idejénél rövidebb idő elteltével kell feldolgoznia.

* Egy elem minden módosítása pontosan egyszer jelenik meg a változási hírcsatornában, és az ügyfeleknek az ellenőrzőpont-logikát kell kezelnie. Ha el szeretné kerülni az ellenőrzőpontok kezelésének bonyolultságát, akkor a változási hírcsatorna-feldolgozó automatikus ellenőrzőpontot és "legalább egyszer" szemantikaot biztosít. Lásd: [a Change feed használata a Change feed Processor használatával](change-feed-processor.md).

* A módosítási napló csak az adott elemek legutóbbi módosítását tartalmazza. Előfordulhat, hogy a köztes változások nem érhetők el.

* A változási csatornát a logikai partíciók minden egyes kulcsának értékén belüli módosítás sorrendjében rendezi a rendszer. Nincs garantált megrendelés a partíciós kulcs értékei között.

* A módosítások bármilyen időpontból szinkronizálhatók, azaz nincs olyan rögzített adatmegőrzési időszak, amelynél a módosítások elérhetők.

* A módosítások párhuzamosan érhetők el egy Azure Cosmos-tároló összes logikai partíciós kulcsához. Ez a funkció lehetővé teszi, hogy a nagyméretű tárolók módosításait több fogyasztó is párhuzamosan dolgozza fel.

* Az alkalmazások egyszerre több módosítási csatornát is igényelhetnek ugyanazon a tárolón. A ChangeFeedOptions. Starter használható a kezdeti kiindulási pont biztosításához. Például az adott időponthoz tartozó folytatási token megkereséséhez. Ha meg van adva, a Continuationtoken argumentumot használja elsőbbséget élvez a kezdő és a StartFromBeginning értékekkel szemben. A ChangeFeedOptions. kezdő időpont pontossága ~ 5 másodperc.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Adatcsatorna módosítása a Cassandra és a MongoDB API-khoz

A MongoDB API-ban és a Cassandra API-ben predikátummal végzett lekérdezésekben a változási csatorna funkciói módosulnak. Ha többet szeretne megtudni a MongoDB API megvalósítási részleteiről, tekintse meg a [streamek módosítása Azure Cosmos db a MONGODB API-ban](mongodb-change-streams.md)című témakört.

A natív Apache Cassandra lehetővé teszi az adatváltozások rögzítését (CDC), egy olyan mechanizmust, amely az archiváláshoz adott táblákat jelöl, valamint elutasítja az írásokat az adott táblákba, amint a CDC-naplóhoz konfigurálható méretű lemez van elérve. A Cassandra Azure Cosmos DB API-ban az adatcsatorna módosítása funkció javítja a CQL-on keresztüli predikátumok lekérdezésének lehetőségét. További információ a megvalósítás részleteiről: [a Azure Cosmos db API használata a Cassandra-ben](cassandra-change-feed.md)című témakörben.

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat talál a hírcsatornák változásáról:

* [Beállítások a változási csatorna olvasásához](read-change-feed.md)
* [A Change feed használata a Azure Functions](change-feed-functions.md)
* [A csatorna módosítása](change-feed-processor.md)
