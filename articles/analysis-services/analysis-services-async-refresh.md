---
title: Azure Analysis Services modellek aszinkron frissítése | Microsoft Docs
description: Ismerteti, hogyan használható a Azure Analysis Services REST API a modell adatai aszinkron frissítésének kódolásához.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: e9fd20fd42e9fe1eb0e98766798e5c759c974c97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92013899"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Aszinkron frissítés a REST API-val

A REST-hívásokat támogató programozási nyelvek használatával aszinkron adatfrissítési műveleteket hajthat végre Azure Analysis Services táblázatos modelleken. Ez magában foglalja a írásvédett replikák szinkronizálását a lekérdezés kibővítő számára. 

Az adatfrissítési műveletek több tényezőt is igénybe vehetnek, többek között az adatmennyiségtől, a partíciók használatának szintjétől stb. Ezeknek a műveleteknek a meghívása hagyományos módon történt olyan meglévő módszerekkel, mint például a [Tom](/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (táblázatos objektummodell), a [PowerShell](/analysis-services/powershell/analysis-services-powershell-reference) -parancsmagok vagy a [TMSL](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (táblázatos modell programozási nyelv). Ezek a módszerek azonban gyakran megbízhatatlan, hosszan futó HTTP-kapcsolatokat igényelhetnek.

A Azure Analysis Services REST API lehetővé teszi, hogy az adatfrissítési műveletek aszinkron módon legyenek végrehajtva. A REST API használatával nem szükséges az ügyfélalkalmazások hosszú ideig futó HTTP-kapcsolatainak használata. Más beépített funkciók is léteznek a megbízhatósághoz, például automatikus újrapróbálkozások és kötegelt véglegesítés.

## <a name="base-url"></a>Kiindulási URL-cím

Az alap URL-cím a következő formátumot követi:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Vegyünk például egy AdventureWorks nevű modellt egy nevű kiszolgálón `myserver` az USA nyugati régiója Azure-régióban. A kiszolgáló neve:

```
asazure://westus.asazure.windows.net/myserver 
```

A kiszolgálónév alap URL-címe:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Az alap URL-cím használatával a következő paraméterek alapján lehet hozzáfűzni az erőforrásokat és a műveleteket: 

![Aszinkron frissítés](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Minden, ami az **s** -ben végződik, egy gyűjtemény.
- Minden, ami a **()** függvényre végződik.
- Minden más erőforrás/objektum.

Használhatja például a frissítések gyűjtemény utáni műveletét a frissítési művelet végrehajtásához:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Hitelesítés

Minden hívást az engedélyezési fejlécben érvényes Azure Active Directory (OAuth 2) jogkivonattal kell hitelesíteni, és meg kell felelnie az alábbi követelményeknek:

- A tokennek felhasználói jogkivonatnak vagy egyszerű alkalmazási szolgáltatásnak kell lennie.
- A tokennek a megfelelő célközönséget kell beállítania `https://*.asazure.windows.net` .
- A kért hívás elvégzéséhez a felhasználónak vagy az alkalmazásnak megfelelő engedélyekkel kell rendelkeznie a kiszolgálón vagy a modellben. Az engedélyezési szintet a modellben vagy a kiszolgálón lévő felügyeleti csoportban lévő szerepkörök határozzák meg.

    > [!IMPORTANT]
    > Jelenleg a **kiszolgáló-rendszergazdai** szerepkörre vonatkozó engedélyek szükségesek.

## <a name="post-refreshes"></a>/Refreshes közzététele

A frissítési művelet végrehajtásához használja a/refreshes-gyűjtemény POST műveletét egy új frissítési elem hozzáadásához a gyűjteményhez. A válaszban található Location fejléc tartalmazza a frissítési azonosítót. Az ügyfélalkalmazás leválaszthatja az állapotot, és szükség esetén később is ellenőrizhető, mert aszinkron módon történik.

Egy modell esetében egyszerre csak egy frissítési műveletet fogadunk el. Ha folyamatban van egy futó frissítési művelet, és egy másik is elküldve, a rendszer a 409-as ütközést okozó HTTP-állapotkódot adja vissza.

A törzs a következőhöz hasonló lehet:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Paraméterek

Paraméterek megadása nem kötelező. A rendszer az alapértelmezett értéket alkalmazza.

| Név             | Típus  | Leírás  |Alapértelmezett  |
|------------------|-------|--------------|---------|
| `Type`           | Enumeráció  | A végrehajtandó feldolgozás típusa. A típusok összhangban vannak a TMSL [frissítési parancs](/analysis-services/tmsl/refresh-command-tmsl) típusával: Full, clearValues, számítsuk, dataOnly, Automatic és defragment. A Hozzáadás típusa nem támogatott.      |   automatikus      |
| `CommitMode`     | Enumeráció  | Meghatározza, hogy az objektumok kötegekben lesznek-e véglegesítve, vagy csak akkor, ha a művelet befejeződött. A módok a következők: alapértelmezett, tranzakciós, partialBatch.  |  tranzakciós       |
| `MaxParallelism` | Int   | Ez az érték határozza meg, hogy legfeljebb hány szálon futtathatók a feldolgozási parancsok párhuzamosan. Ez az érték a MaxParallelism tulajdonsággal van összhangban, amely a TMSL [Sequence parancsban](/analysis-services/tmsl/sequence-command-tmsl) vagy más módszerek használatával állítható be.       | 10        |
| `RetryCount`     | Int   | Azt jelzi, hogy a művelet hány alkalommal próbálkozzon újra a hiba után.      |     0    |
| `Objects`        | Tömb | A feldolgozandó objektumok tömbje. Minden objektum tartalmazza a "Table" kifejezést a teljes tábla vagy a "tábla" és a "partíció" feldolgozásakor a partíció feldolgozása során. Ha nincs megadva objektum, a teljes modell frissül. |   A teljes modell feldolgozása      |

A CommitMode egyenlő a partialBatch. Ez akkor használatos, ha olyan nagyméretű adatkészletek kezdeti terhelését hajtja végre, amelyek órákat is igénybe vehetnek. Ha a frissítési művelet sikertelen volt egy vagy több köteg sikeres véglegesítése után, a sikeresen véglegesített kötegek véglegesítése továbbra is megmarad (a kötegek sikeres véglegesítése nem történik meg).

> [!NOTE]
> Az írás időpontjában a köteg mérete a MaxParallelism érték, de ez az érték változhat.

### <a name="status-values"></a>Állapotüzenetek

|Állapotérték  |Leírás  |
|---------|---------|
|`notStarted`    |   A művelet még nincs elindítva.      |
|`inProgress`     |   A művelet folyamatban van.      |
|`timedOut`     |    A művelet túllépte az időkorlátot a felhasználó által megadott időtúllépés alapján.     |
|`cancelled`     |   A műveletet a felhasználó vagy a rendszer megszakította.      |
|`failed`     |   A művelet sikertelen volt.      |
|`succeeded`      |   A művelet sikeresen befejeződött.      |

## <a name="get-refreshesrefreshid"></a>/Refreshes/beolvasása\<refreshId>

A frissítési művelet állapotának megtekintéséhez használja a GET műveletet a frissítési AZONOSÍTÓban. Íme egy példa a válasz törzsére. Ha a művelet folyamatban van, a `inProgress` visszaadott állapotba kerül.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>/Refreshes beolvasása

A modell korábbi frissítési műveleteinek listájának lekéréséhez használja a GET műveletet a/refreshes gyűjteményben. Íme egy példa a válasz törzsére. 

> [!NOTE]
> Az írás időpontjában a frissítési műveletek utolsó 30 napja tárolódik és visszakerül, de ez a szám változhat.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-07T02:06:57.1838734Z",
        "endTime": "2017-12-07T02:07:00.4929675Z",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T01:05:54.157324Z",
        "endTime": "2017-12-07T01:05:57.353371Z",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>/Refreshes/törlése\<refreshId>

A folyamatban lévő frissítési művelet megszakításához használja a frissítési AZONOSÍTÓban a DELETE műveletet.

## <a name="post-sync"></a>/Sync közzététele

A frissítési műveletek végrehajtásakor szükség lehet az új adatok szinkronizálására a lekérdezési felskálázáshoz szükséges replikákkal. Egy modell szinkronizálási műveletének végrehajtásához használja a POST műveletet a/Sync függvényben. A válaszban található Location fejléc tartalmazza a szinkronizálási művelet AZONOSÍTÓját.

## <a name="get-sync-status"></a>/Sync állapotának beolvasása

Egy szinkronizálási művelet állapotának megtekintéséhez használja a GET műveletet paraméterként a művelet AZONOSÍTÓjának beolvasása parancs használatával. Íme egy példa a válasz törzsére:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Értékek a következőhöz `syncstate` :

- 0: replikálás. Az adatbázisfájlok replikálódnak a célmappába.
- 1: rehidratálás. Az adatbázis a csak olvasható kiszolgálói példány (ok) ra lesz kiszáradva.
- 2: befejezve. A szinkronizálási művelet sikeresen befejeződött.
- 3: nem sikerült. A szinkronizálási művelet nem sikerült.
- 4: véglegesítés. A szinkronizálási művelet befejeződött, de karbantartási lépéseket hajt végre.

## <a name="code-sample"></a>Kódminta

Itt egy C#-kódrészletet talál az első lépésekhez, [RestApiSample a githubon](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>A mintakód használata

1.    A tárház klónozása vagy letöltése. Nyissa meg a RestApiSample megoldást.
2.    Keresse meg a sor **ügyfelet. BaseAddress =..** . és adja meg az [alap URL-címét](#base-url).

A mintakód a [szolgáltatás egyszerű](#service-principal) hitelesítését használja.

### <a name="service-principal"></a>Szolgáltatásnév

Az egyszerű szolgáltatásnév beállításával és a szükséges engedélyek az Azure-ban való hozzárendelésével kapcsolatos további információkért lásd: [egyszerű szolgáltatásnév létrehozása – Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md) és [egy egyszerű szolgáltatásnév hozzáadása a kiszolgálói rendszergazdai szerepkörhöz](analysis-services-addservprinc-admins.md) . A lépések elvégzése után végezze el a következő további lépéseket:

1.    A kód mintában keresse meg a **String Authority =... karakterláncot**, cserélje le a **commit** a szervezet bérlői azonosítójával.
2.    Megjegyzés/Megjegyzés: a ClientCredential osztály a cred objektum létrehozásához használatos. Győződjön meg arról, hogy a \<App ID> és az \<App Key> értékek biztonságos módon érhetők el, vagy használjon tanúsítványalapú hitelesítést az egyszerű szolgáltatásokhoz.
3.    Futtassa a mintát.


## <a name="see-also"></a>Lásd még

[Minták](analysis-services-samples.md)   
[REST API](/rest/api/analysisservices/servers)