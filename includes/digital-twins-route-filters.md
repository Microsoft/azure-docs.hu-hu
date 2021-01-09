---
author: baanders
description: fájl belefoglalása az Azure Digital Twins útvonal-szűrési beállításaihoz
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: e06e660a43aaa0ff5eb79bc00bd8a5d2c61c6580
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045315"
---
| Szűrő neve | Leírás | Szöveges séma szűrése | Támogatott értékek | 
| --- | --- | --- | --- |
| Igaz/hamis | Lehetővé teszi az útvonalak szűrés nélküli létrehozását, illetve az útvonal letiltását, így nincs esemény elküldése | `<true/false>` | `true` = az útvonal engedélyezve van szűrés nélkül <br> `false` = az útvonal le van tiltva |
| Típus | A digitális kettős példányon keresztül áramló [esemény típusa](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) | `type = '<eventType>'` | Itt láthatók a lehetséges eseménytípus értékei: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Forrás | Az Azure Digital Twins-példány neve | `source = '<hostname>'`| Itt láthatja a lehetséges állomásnév-értékeket: <br> **Értesítésekhez**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **Telemetria esetén**: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| Tárgy | Az esemény leírása a fenti eseményforrás kontextusában | `subject = '<subject>'` | Itt láthatók a lehetséges tárgyi értékek: <br>**Értesítésekhez**: a tulajdonos `<twinid>` <br> vagy a témák URI-formátuma, amelyeket a több rész vagy azonosító egyedileg azonosít:<br>`<twinid>/relationships/<relationshipid>`<br> **Telemetria esetén**: a tulajdonos az összetevő elérési útja (ha a telemetria egy Twin összetevőből származik), például: `comp1.comp2` . Ha a telemetria nem egy összetevőből származik, akkor a tárgy mezőjének üresnek kell lennie. |
| Adatséma | DTDL modell azonosítója | `dataschema = '<model-dtmi-ID>'` | **Telemetria esetében**: az Adatséma a Twin vagy a telemetria kibocsátó összetevő modell-azonosítója. Például: `dtmi:example:com:floor4;2` <br>**Értesítésekhez (létrehozás/törlés)**: az Adatséma az értesítési törzsben érhető el `$body.$metadata.$model` . <br>**Értesítések (frissítés)**: az Adatséma az értesítési törzsben érhető el `$body.modelId`|
| Tartalomtípus | Adatérték tartalomtípusa | `datacontenttype = '<contentType>'` | A tartalom típusa `application/json` |
| Spec verziója | Az Ön által használt esemény-séma verziója | `specversion = '<version>'` | A verziónak a-nek kell lennie `1.0` . Ez a CloudEvents séma 1,0-es verzióját jelzi. |
| Értesítés törzse | Egy értesítés mezőjében szereplő bármely tulajdonságra hivatkozhat `data` | `$body.<property>` | Tekintse meg a következő témakört: tudnivalók az eseményekről az értesítések példáinak [*megismeréséhez*](../articles/digital-twins/how-to-interpret-event-data.md) . A mező bármely tulajdonsága `data` hivatkozhat a `$body`

Vegye figyelembe, hogy több szűrőt is hozzáadhat a következőhöz hasonló kérésekhez: 

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter-multiple.json":::

A következő adattípusok támogatottak a fenti adatokra mutató hivatkozásokkal visszaadott értékekként:

| Adattípus | Példa |
|-|-|-|
|**Sztring**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**Egész szám**|`$body.errorCode > 200`|
|**Duplán**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

Az útválasztási szűrők definiálásakor a következő operátorok támogatottak:

|Family (Család)|Operátorok|Példa|
|-|-|-|
|Logikai|ÉS, VAGY, ()|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Összehasonlítás|<, <=, >, >=, =,! =|`$body.temperature <= 5.5`

Az útválasztási szűrők definiálásakor a következő függvények támogatottak:

|Függvény|Leírás|Példa|
|--|--|--|
|STARTS_WITH (x, y)|Igaz értéket ad vissza, ha az érték `x` a sztringtel kezdődik `y` .|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH (x, y) | Igaz értéket ad vissza, ha az érték `x` a sztringtel végződik `y` .|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|TARTALMAZZA (x, y)| Igaz értéket ad vissza, ha az érték `x` tartalmazza a karakterláncot `y` .|`CONTAINS(subject, '<twinID>')`|

Egy szűrő implementálása vagy frissítése során a módosítás néhány percet is igénybe vehet, hogy az adatfolyamatot tükrözze.