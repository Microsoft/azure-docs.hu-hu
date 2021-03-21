---
title: Diagnosztikai naplók engedélyezése és lekérdezése
titleSuffix: Azure Digital Twins
description: 'Lásd: a naplózás engedélyezése a diagnosztikai beállításokkal, valamint a naplók azonnali megtekintésének lekérdezése.'
author: baanders
ms.author: baanders
ms.date: 11/9/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c600ced8896a3847b80d854c9e230310cca4c98d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100588595"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins hibaelhárítása: diagnosztika naplózása

Az Azure Digital Twins a szolgáltatáshoz tartozó naplókat gyűjthet a teljesítmény, a hozzáférés és az egyéb adatok figyeléséhez. Ezekkel a naplókkal megismerheti, hogy mi történik az Azure Digital Twins-példányban, és hogyan végezheti el a problémák kiváltó okait anélkül, hogy kapcsolatba kellene lépnie az Azure ügyfélszolgálatával.

Ez a cikk bemutatja, hogyan [**konfigurálhatja a diagnosztikai beállításokat**](#turn-on-diagnostic-settings) a [Azure Portalban](https://portal.azure.com) a naplók Azure Digital Twins-példányból való gyűjtésének megkezdéséhez. Megadhatja azt is, hogy a naplók hol legyenek tárolva (például Log Analytics vagy egy tetszőleges Storage-fiók).

Ez a cikk az Azure Digital Twins által gyűjtött összes [naplózási kategóriát](#log-categories) és [naplózási sémát](#log-schemas) is tartalmazza.

A naplók beállítása után [**lekérdezheti a naplókat**](#view-and-query-logs) az egyéni elemzések gyors összegyűjtéséhez.

## <a name="turn-on-diagnostic-settings"></a>Diagnosztikai beállítások bekapcsolása 

A diagnosztikai beállítások bekapcsolásával megkezdheti a naplók gyűjtését az Azure Digital Twins-példányon. Azt a célhelyet is kiválaszthatja, ahová az exportált naplókat tárolni szeretné. Itt megtudhatja, hogyan engedélyezheti az Azure Digital Twins-példány diagnosztikai beállításait.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure Digital Twins-példányhoz. A nevét a portál keresési sávjába írja be. 

2. Válassza a menüből a **diagnosztikai beállítások** elemet, majd **adja hozzá a diagnosztikai** beállításokat.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="A diagnosztikai beállítások lapot és a hozzáadni kívánt gombot ábrázoló képernyőkép" lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. Az alábbi lapon adja meg a következő értékeket:
     * **Diagnosztikai beállítás neve**: adjon meg egy nevet a diagnosztikai beállításoknak.
     * **Kategória részletei**: válassza ki a figyelni kívánt műveleteket, majd jelölje be a jelölőnégyzeteket a diagnosztika engedélyezéséhez a műveletekhez. A diagnosztikai beállítások által a következő műveleteket lehet jelenteni:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Ezekről a kategóriákról és a bennük található információkról további részleteket az alábbi [*log categoriess*](#log-categories) szakaszban talál.
     * **Célhely részletei**: válassza ki, hová szeretné elküldeni a naplókat. A három lehetőség bármely kombinációját kiválaszthatja:
        - Küldés a Log Analyticsnek
        - Archiválás tárfiókba
        - Streamelés eseményközpontba

        Előfordulhat, hogy további részleteket is meg kell adnia, ha azok a célhely kiválasztásához szükségesek.  
    
4. Mentse az új beállításokat. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Képernyőfelvétel: a diagnosztikai beállítások oldal, ahol a felhasználó kitöltötte a diagnosztikai beállítások nevét, és a kategória részletei és a célhely részletei jelölőnégyzettel rendelkezik. A Mentés gomb ki van emelve." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezután a naplók megjelennek a konfigurált célhelyen a példány **diagnosztikai beállítások** lapján. 

A diagnosztikai beállításokkal és a telepítési lehetőségekkel kapcsolatos további információkért látogasson el a [*diagnosztikai beállítások létrehozása lehetőségre a platform-naplók és-metrikák különböző célhelyekre küldéséhez*](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="log-categories"></a>Naplók kategóriái

Az alábbiakban további információkat talál az Azure Digital Twins által gyűjtött naplók kategóriái.

| Naplókategória | Description |
| --- | --- |
| ADTModelsOperation | A modellekre vonatkozó összes API-hívás naplózása |
| ADTQueryOperation | A lekérdezésekre vonatkozó összes API-hívás naplózása |
| ADTEventRoutesOperation | Naplózza az esemény-útvonalakra vonatkozó összes API-hívást, valamint az Azure Digital Twins-ból érkező eseményeket egy olyan végponti szolgáltatáshoz, mint például a Event Grid, a Event Hubs és a Service Bus |
| ADTDigitalTwinsOperation | Az Azure digitális Twins-hoz tartozó összes API-hívás naplózása |

Minden naplózási kategória írási, olvasási, törlési és művelet-műveletből áll.  Ezek a leképezések a következőképpen REST API hívásokat:

| Eseménytípus | REST API műveletek |
| --- | --- |
| Írás | PUT és PATCH |
| Olvasás | GET |
| Törlés | DELETE |
| Művelet | POST |

Itt látható a műveletek és a megfelelő [Azure digitális Twins REST API](/rest/api/azure-digitaltwins/) a különböző kategóriákba bejelentkezett hívások részletes listája. 

>[!NOTE]
> Minden naplózási kategória több művelet/REST API hívást tartalmaz. Az alábbi táblázatban minden naplózási kategória az összes művelet/REST API hívására vonatkozik, amíg meg nem jelenik a következő napló kategóriája. 

| Naplókategória | Művelet | Hívások és egyéb események REST API |
| --- | --- | --- |
| ADTModelsOperation | Microsoft. DigitalTwins/modellek/írás | Digital Twin models frissítési API |
|  | Microsoft. DigitalTwins/modellek/olvasás | Az ID és a List API-k által lekérdezett digitális kettős modellek |
|  | Microsoft. DigitalTwins/models/delete | Digital Twin models – API törlése |
|  | Microsoft. DigitalTwins/modellek/művelet | Digitális kettős modellek API hozzáadása |
| ADTQueryOperation | Microsoft. DigitalTwins/lekérdezés/művelet | Az ikrek API lekérdezése |
| ADTEventRoutesOperation | Microsoft. DigitalTwins/eventroutes/Write | Event Routes API hozzáadása |
|  | Microsoft. DigitalTwins/eventroutes/READ | AZONOSÍTÓ és lista API-k által beolvasott esemény-útvonalak |
|  | Microsoft. DigitalTwins/eventroutes/delete | Esemény-útvonalak törlése API |
|  | Microsoft. DigitalTwins/eventroutes/művelet | Hiba történt az események végponti szolgáltatásba való közzétételekor (nem API-hívás) |
| ADTDigitalTwinsOperation | Microsoft. DigitalTwins/DigitalTwins/Write | Digitális ikrek hozzáadása, kapcsolat hozzáadása, frissítés, összetevő frissítése |
|  | Microsoft. DigitalTwins/DigitalTwins/READ | Digitális ikrek által beolvasott azonosító, összetevő beolvasása, kapcsolat beolvasása azonosító alapján, bejövő kapcsolatok listázása, kapcsolatok listázása |
|  | Microsoft. DigitalTwins/DigitalTwins/delete | Digitális ikrek törlése, kapcsolat törlése |
|  | Microsoft. DigitalTwins/DigitalTwins/művelet | Digitális Twins – összetevő telemetria küldése, telemetria küldése |

## <a name="log-schemas"></a>Naplózási sémák 

Minden naplózási kategória tartalmaz egy sémát, amely meghatározza, hogyan történik az adott kategóriában lévő események jelentése. Minden egyes naplóbejegyzés szövegként tárolódik, és JSON-blobként van formázva. A log és a example JSON-törzs mezői az alábbi naplókhoz vannak megadva. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` és `ADTQueryOperation` egy konzisztens API log-sémát használ, `ADTEventRoutesOperation` a saját külön sémával rendelkezik.

### <a name="api-log-schemas"></a>API-naplózási sémák

Ez a naplózási séma konzisztens a `ADTDigitalTwinsOperation` , és rendszerhez `ADTModelsOperation` `ADTQueryOperation` . Az Azure Digital Twins-példány API-hívásainak információit tartalmazza.

Az API-naplók mező-és tulajdonság-leírása itt található.

| Mező neve | Adattípus | Leírás |
|-----|------|-------------|
| `Time` | DateTime | Az esemény bekövetkezett dátuma és időpontja (UTC) |
| `ResourceID` | Sztring | Annak az erőforrásnak a Azure Resource Manager erőforrás-azonosítója, amelyben az esemény lezajlott |
| `OperationName` | Sztring  | Az esemény során végrehajtandó művelet típusa |
| `OperationVersion` | Sztring | Az esemény során használt API-verzió |
| `Category` | Sztring | A kibocsátott erőforrás típusa |
| `ResultType` | Sztring | Az esemény eredménye |
| `ResultSignature` | Sztring | Az eseményhez tartozó http-állapotkód |
| `ResultDescription` | Sztring | További információk az eseményről |
| `DurationMs` | Sztring | Mennyi ideig tartott az esemény végrehajtása ezredmásodpercben |
| `CallerIpAddress` | Sztring | Az esemény maszkolt forrás IP-címe |
| `CorrelationId` | Guid | Az ügyfél egyedi azonosítót adott meg az eseményhez |
| `Level` | Sztring | Az esemény naplózási súlyossága |
| `Location` | Sztring | Az a régió, ahol a rendezvény lezajlott |
| `RequestUri` | URI | Az esemény során használt végpont |

Az alábbi példa JSON-törzseket mutat be az ilyen típusú naplókhoz.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31"
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "935",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "255",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
}
```

### <a name="egress-log-schemas"></a>Kimenő naplózási sémák

Ez a naplók sémája `ADTEventRoutesOperation` . Ezek a kivételekre és az Azure Digital Twins-példányhoz csatlakozó kimenő végpontokra vonatkozó API-műveletekre vonatkozó részleteket tartalmaznak.

|Mező neve | Adattípus | Leírás |
|-----|------|-------------|
| `Time` | DateTime | Az esemény bekövetkezett dátuma és időpontja (UTC) |
| `ResourceId` | Sztring | Annak az erőforrásnak a Azure Resource Manager erőforrás-azonosítója, amelyben az esemény lezajlott |
| `OperationName` | Sztring  | Az esemény során végrehajtandó művelet típusa |
| `Category` | Sztring | A kibocsátott erőforrás típusa |
| `ResultDescription` | Sztring | További információk az eseményről |
| `Level` | Sztring | Az esemény naplózási súlyossága |
| `Location` | Sztring | Az a régió, ahol a rendezvény lezajlott |
| `EndpointName` | Sztring | Az Azure Digital Ikrekben létrehozott kimenő forgalom végpontjának neve |

Az alábbi példa JSON-törzseket mutat be az ilyen típusú naplókhoz.

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "category": "EventRoutesOperation",
  "resultDescription": "Unable to send EventGrid message to [my-event-grid.westus-1.eventgrid.azure.net] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "level": "3",
  "location": "southcentralus",
  "properties": {
    "endpointName": "endpointEventGridInvalidKey"
  }
}
```

## <a name="view-and-query-logs"></a>Naplók megtekintése és lekérdezése

A cikk korábbi szakaszaiban konfigurálta a naplók típusát a tárolási helyük tárolásához és megadásához.

A naplók használatával kapcsolatos problémák megoldásához és elemzések létrehozásához létrehozhat **Egyéni lekérdezéseket**. A kezdéshez igénybe veheti a szolgáltatás által biztosított néhány példa lekérdezés előnyeit is, amelyek az ügyfelek által a példánnyal kapcsolatos gyakori kérdésekkel foglalkoznak.

Itt megtudhatja, hogyan kérdezheti le a példányhoz tartozó naplókat.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az Azure Digital Twins-példányhoz. A nevét a portál keresési sávjába írja be. 

2. A napló lekérdezési oldalának megnyitásához válassza a menü **naplók** elemét. A lap egy *lekérdezések* nevű ablakra nyílik meg.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="Az Azure Digital Twins-példány naplók lapját ábrázoló képernyőkép. Egy lekérdezési ablak jelenik meg, amely a különböző naplózási beállítások (például a DigitalTwin API késése és a modell API-késés) utáni előre összekészített lekérdezéseket jeleníti meg." lightbox="media/troubleshoot-diagnostics/logs.png":::

    Ezek a különböző naplókhoz írt, előre összeépített példák. A lekérdezések egyikét kiválasztva betöltheti azt a lekérdezés-Szerkesztőbe, és futtathatja, hogy megtekintse a példányhoz tartozó naplókat.

    A *lekérdezési ablakot úgy* is lezárhatja, hogy semmit nem kell futtatnia anélkül, hogy közvetlenül a lekérdezés-szerkesztő lapra ugorjon, ahol egyéni lekérdezési kódokat írhat vagy szerkeszthet.

3. A *lekérdezések* ablakból való kilépés után megjelenik a fő lekérdezés-szerkesztő oldal. Itt megtekintheti és szerkesztheti a példaként szolgáló lekérdezések szövegét, vagy megírhatja a saját lekérdezéseit a semmiből.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Az Azure Digital Twins-példány naplók lapját ábrázoló képernyőkép. A lekérdezési ablak eltűnik, és helyette a különböző naplók listája, egy szerkesztési ablaktábla, amely a szerkeszthető lekérdezési kódokat és a lekérdezési előzményeket megjelenítő ablaktáblát jeleníti meg." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    A bal oldali panelen 
    - A *Tables (táblák* ) lapon láthatók azok a különböző Azure digitális Twins- [naplózási kategóriák](#log-categories) , amelyek a lekérdezésekben használhatók. 
    - A *lekérdezések* lapon a szerkesztőbe betölthető példákat tartalmazó lekérdezések láthatók.
    - A *szűrő* lapon testreszabhatja a lekérdezés által visszaadott adatok szűrt nézetét.

A naplózási lekérdezésekkel és azok írásával kapcsolatos részletesebb információkért tekintse meg a [*Azure monitorban található naplók áttekintését*](../azure-monitor/logs/log-query-overview.md).

## <a name="next-steps"></a>Következő lépések

* A diagnosztika konfigurálásával kapcsolatos további információkért lásd: [*adatok gyűjtése és felhasználása az Azure-erőforrásokból*](../azure-monitor/essentials/platform-logs-overview.md).
* További információ az Azure digitális Twins metrikáinak használatáról [*: a metrikák megtekintése Azure Monitorsal*](troubleshoot-metrics.md).
* Ha szeretné megtudni, hogyan engedélyezheti a riasztásokat a metrikák számára, tekintse meg a [*Hibaelhárítás: riasztások beállítása*](troubleshoot-alerts.md)című témakört.