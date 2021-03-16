---
title: Lekérdezés nyelve
titleSuffix: Azure Digital Twins
description: Ismerje meg az Azure digitális Twins lekérdezési nyelvének alapjait.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: fc9cd95063f84a9af7f989af9a65ce8f99852dc1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490976"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Az Azure Digital Twins lekérdezési nyelvének ismertetése

Ne felejtse el, hogy az Azure Digital Twins középpontja a digitális ikrekből és kapcsolatokból kialakított [Twin gráf](concepts-twins-graph.md). 

Ez a gráf lekérdezhető a digitális ikrek és a benne található kapcsolatok adatainak lekérdezéséhez. Ezek a lekérdezések egy egyed, SQL-szerű lekérdezési nyelven vannak megírva, amelyet **Azure Digital Twins lekérdezési nyelvnek** neveznek. Ez hasonló a [IoT hub lekérdezési nyelvhez](../iot-hub/iot-hub-devguide-query-language.md) , amely számos hasonló funkcióval rendelkezik.

Ez a cikk a lekérdezés nyelvének és képességeinek alapjait ismerteti. A lekérdezési szintaxissal és a lekérdezési kérelmek futtatásával kapcsolatos részletesebb példákat a [*útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md)című témakörben talál.

## <a name="about-the-queries"></a>Tudnivalók a lekérdezésekről

Az Azure digitális Twins lekérdezési nyelvével lekérheti a digitális ikreket a saját...
* tulajdonságok (a [címke tulajdonságaival](how-to-use-tags.md)együtt)
* modellek
* kapcsolatok
  - a kapcsolatok tulajdonságai

Ha egy ügyfélalkalmazás számára szeretne lekérdezést küldeni a szolgáltatásnak, az Azure Digital Twins [**lekérdezési API**](/rest/api/digital-twins/dataplane/query)-ját fogja használni. Az API használatának egyik módja az Azure Digital Twins-hoz készült [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) -k egyike.

### <a name="considerations-for-querying"></a>A lekérdezés szempontjai

Az Azure Digital Twins-lekérdezések írásakor a következő szempontokat vegye figyelembe:
* Kis-és nagybetűk **megkülönböztetése**: az Azure Digital Twins összes lekérdezési művelete megkülönbözteti a kis-és nagybetűket, ezért ügyeljen arra, hogy a modellekben definiált pontos neveket használja. Ha a tulajdonságok neve hibásan van megadva vagy helytelenül van megadva, az eredményhalmaz üres, és nem ad vissza hibát.
* **Escape-idézőjelek**: Ha a lekérdezés szövege egyetlen idézőjel karaktert tartalmaz az adataiban, az idézőjelet a karakterrel kell megszökni `\` . Íme egy példa, amely a *Deli* tulajdonság értékét tárgyalja:

  :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="EscapedSingleQuote":::

## <a name="reference-expressions-and-conditions"></a>Hivatkozás: kifejezések és kikötések

Ez a szakasz azokat a kezelőket és funkciókat ismerteti, amelyek elérhetők az Azure Digital Twins-lekérdezések írásához. A szolgáltatások használatát szemléltető lekérdezések például a következő témakörben olvashatók [*: útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md).

### <a name="operators"></a>Operátorok

A következő operátorok támogatottak:

| Family (Család) | Operátorok |
| --- | --- |
| Logikai |`AND`, `OR`, `NOT` |
| Összehasonlítás | `=`, `!=`, `<`, `>`, `<=`, `>=` |
| Contains | `IN`, `NIN` |

### <a name="functions"></a>Functions

A következő típusú ellenőrzési és öntési függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| `IS_DEFINED` | Egy logikai értéket ad vissza, amely azt jelzi, hogy a tulajdonsághoz hozzá van-e rendelve érték. Ez csak akkor támogatott, ha az érték egy egyszerű típus. Az egyszerű típusok például a következők: string, Boolean, numerikus vagy `null` . `DateTime`, az Objektumtípusok és a tömbök nem támogatottak. |
| `IS_OF_MODEL` | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott iker megfelel-e a megadott modell típusának. |
| `IS_BOOL` | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa logikai. |
| `IS_NUMBER` | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa szám-e. |
| `IS_STRING` | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa sztring-e. |
| `IS_NULL` | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa NULL-e. |
| `IS_PRIMITIVE` | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa primitív (karakterlánc, logikai, numerikus vagy `null` ). |
| `IS_OBJECT` | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa JSON-objektum-e. |

A következő karakterlánc-függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| `STARTSWITH(x, y)` | Olyan logikai érték beolvasása, amely azt jelzi, hogy az első karakterlánc-kifejezés a másodikval kezdődik-e. |
| `ENDSWITH(x, y)` | Egy olyan logikai érték beolvasása, amely azt jelzi, hogy az első karakterlánc kifejezése a másodperctel végződik-e |

## <a name="query-limitations"></a>Lekérdezési korlátozások

Ez a szakasz a lekérdezési nyelv korlátozásait ismerteti.

* Időzítés: akár 10 másodperces késés is előfordulhat, mielőtt a példányban történt változások megjelennek a lekérdezésekben. Ha például olyan műveletet hajt végre, mint az ikrek létrehozása vagy törlése a DigitalTwins API-val, akkor előfordulhat, hogy az eredmény nem jelenik meg azonnal a lekérdezési API-kérelmekben. A rövid ideig tartó várakozásnak elegendőnek kell lennie a feloldáshoz.
* Az utasítás nem támogatja az allekérdezéseket `FROM` .
* `OUTER JOIN` a szemantika nem támogatott, ami azt jelenti, hogy ha a kapcsolat nulla rangú, akkor a rendszer a teljes "sort" kizárja a kimeneti eredményhalmaz alapján.
* A gráf bejárási mélysége `JOIN` lekérdezési szintenként legfeljebb öt szintre van korlátozva.
* Az Azure Digital Twins kapcsolatai nem kérhetők le független entitásként; Emellett meg kell adnia a forráshoz tartozó, a kapcsolatból származó külön adatokat is. Ez azt jelenti, hogy a művelethez bizonyos korlátozások vonatkoznak, `JOIN` amelyek a kapcsolatok lekérdezésére szolgálnak, hogy megbizonyosodjon arról, hogy a lekérdezés elkezdődik-e a kettős (ak). Erre példákat a következő témakörben talál: [*lekérdezés kapcsolat alapján*](how-to-query-graph.md#query-by-relationship) című rész, *útmutató: lekérdezés a Twin Graph* -ról.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan írhat lekérdezéseket, és hogyan tekintheti meg az ügyféloldali kód példáit [*: a Twin gráf lekérdezése*](how-to-query-graph.md).