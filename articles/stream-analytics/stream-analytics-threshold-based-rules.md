---
title: A Azure Stream Analytics konfigurálható küszöbérték-alapú szabályai
description: Ez a cikk azt ismerteti, hogyan használhatók a hivatkozási eredmények olyan riasztási megoldás eléréséhez, amely Azure Stream Analyticsban konfigurálható küszöbérték-alapú szabályokat tartalmaz.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: 2f9d132084f0254486be533daea6b54239f4e450
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019975"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Konfigurálható küszöbérték-alapú szabályok feldolgozása Azure Stream Analytics
Ez a cikk azt ismerteti, hogyan használhatók a hivatkozási eredmények olyan riasztási megoldás eléréséhez, amely a Azure Stream Analytics konfigurálható küszöbértékeken alapuló szabályokat használ.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Forgatókönyv: riasztás az állítható szabályok küszöbértékei alapján
Előfordulhat, hogy a riasztást kimenetként kell létrehoznia, amikor a bejövő adatfolyam-események elértek egy bizonyos értéket, vagy ha egy összesített érték a bejövő adatfolyam-események alapján meghaladja a megadott küszöbértéket. Egyszerűen beállíthat egy Stream Analytics lekérdezést, amely összehasonlítja az értéket egy rögzített és előre meghatározott statikus küszöbértékkel. A rögzített küszöbértékek egyszerű numerikus összehasonlításokkal (nagyobb, mint, kevesebb mint, és egyenlőség) használhatók az adatfolyam-lekérdezési szintaxisban.

Bizonyos esetekben a küszöbértékek módosítása nélkül kell egyszerűbben konfigurálni a küszöbértékeket a lekérdezési szintaxis szerkesztésével. Más esetekben előfordulhat, hogy az azonos lekérdezés által feldolgozott számos eszközre vagy felhasználóra szüksége van, és mindegyiknek különböző küszöbértékekkel kell rendelkeznie az egyes eszközökön. 

Ez a minta használható a küszöbértékek dinamikus konfigurálására, szelektíven kiválaszthatja, hogy a küszöbérték milyen típusú eszközre vonatkozzon a bemeneti adatok szűrésével, valamint a kimenetben szerepeltetni kívánt mezők szelektív kiválasztásával.

## <a name="recommended-design-pattern"></a>Ajánlott kialakítási minta
A riasztási küszöbértékek keresésekor használjon egy Stream Analyticsi feladathoz tartozó hivatkozás-adatbevitelt:
- Tárolja a küszöbértékeket a hivatkozási adatokban, kulcs szerint egy értéket.
- Csatlakoztassa a streaming adatbeviteli eseményeket a kulcs oszlopban található hivatkozási adatokhoz.
- A hivatkozási adatokból származó kulcsos értéket használja küszöbértékként.

## <a name="example-data-and-query"></a>Példa az adatelemzésre és a lekérdezésre
A példában a riasztások akkor jönnek létre, amikor egy percen belül az eszközökről származó adatátvitelek összesítése megegyezik a szabályban megadott szabályokban rögzített értékekkel.

A lekérdezésben minden egyes deviceId esetében, valamint a deviceId alatt található minden egyes metricName esetében 0 és 5 dimenzió közötti értékre állíthatja a CSOPORTOSÍTÁSt. Csak a megfelelő szűrő értékekkel rendelkező események vannak csoportosítva. A csoportosítást követően a min., a max., az átlag, az ablakos összesítések kiszámítása a 60 másodperces időszakra történik. A rendszer ezt követően kiszámítja az összesített értékek szűrőit a hivatkozáson beállított küszöbérték alapján a riasztás kimeneti eseményének létrehozásához.

Tegyük fel például, hogy van egy olyan Stream Analytics-feladata, amely egy **szabályok** nevű, adatbevitelre szolgáló adatbevitelt tartalmaz, és elnevezte a **metrikákat**. 

## <a name="reference-data"></a>Hivatkozási érték
Ez a példás példa azt mutatja be, hogyan lehet egy küszöbérték-alapú szabályt megjeleníteni. Egy JSON-fájl tárolja a hivatkozási adatokat, és az Azure Blob Storage-ba menti, és a blob Storage-tárolót a **szabályok** nevű hivatkozásként használja. Felülírhatja ezt a JSON-fájlt, és lecserélheti a szabály konfigurációját az idő bekapcsolásával a folyamatos átviteli feladatok leállítása vagy elindítása nélkül.

- A példában szereplő szabály egy állítható riasztást jelöl, ha a processzor mérete meghaladja az értéket (az átlag nagyobb vagy egyenlő) a százalékos értéknél `90` . A `value` mező igény szerint konfigurálható.
- Figyelje meg, hogy a szabály egy **operátor** mezővel rendelkezik, amelyet később a lekérdezési szintaxisban kell értelmezni `AVGGREATEROREQUAL` . 
- A szabály egy adott dimenzió kulcsának értékét szűri az `2` értékkel `C1` . A többi mező üres karakterlánc, amely azt jelzi, hogy az adott esemény mezői nem szűrik a bemeneti adatfolyamot. További CPU-szabályokat is beállíthat, hogy szükség szerint szűrje a többi egyező mezőt.
- Nem minden oszlopnak szerepelnie kell a kimeneti riasztási eseményben. Ebben az esetben a `includedDim` kulcs száma `2` bekapcsolva `TRUE` értékre van kapcsolva, amely azt jelzi, hogy az adatfolyamban lévő Event-adatmező 2. száma szerepel a megfelelő kimeneti eseményekben. A többi mező nem szerepel a riasztás kimenetében, de a mezőlista módosítható.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Példa adatfolyam-lekérdezésre
Ez a példa Stream Analytics lekérdezés összekapcsolja a fenti példában szereplő **szabályokra** hivatkozó adatokat egy **mérőszámok** nevű bemeneti adatfolyamba.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Példa adatfolyam-bevitelsel kapcsolatos esemény-adatokra
Ez a példa JSON-adatok a fenti adatfolyam-lekérdezésben használt **mérőszámok** bemeneti adatait jelölik. 

- Az 1 perces TimeSpan az érték három példát mutat be `T14:50` . 
- Mindhárom `deviceId` érték megegyezik `978648` .
- A CPU-metrika értékei különbözőek lehetnek az egyes eseményeken (, `98` `95` `80` ). Csak az első két példa esemény haladja meg a szabályban megállapított CPU riasztási szabályt.
- A riasztási szabály includeDim mezőjében a 2. kulcs szerepel. A Példaban szereplő események neve a megfelelő kulcs 2 mezőben található `NodeName` . A három példa eseményeinek értéke `N024` : `N024` , és `N014` . A kimenetben csak a csomópont jelenik meg, `N024` mint az egyetlen olyan adat, amely megfelel a magas CPU riasztási feltételeinek. `N014` nem felel meg a magas CPU-küszöbértéknek.
- A riasztási szabály `filter` csak a 2. számú kulcson van konfigurálva, amely a `cluster` minta események mezőjének felel meg. A három példa eseményeinek mindegyike értékkel rendelkezik `C1` , és megfelel a szűrési feltételeknek.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Példa kimenetre
Ez a példa a kimeneti JSON-adatokat jeleníti meg egyetlen riasztási esemény a hivatkozási adatokat definiáló CPU-küszöbérték alapján. A kimeneti esemény tartalmazza a riasztás nevét, valamint a figyelembe vett mezők összesített (átlagos, minimális és maximális) értékét. A kimeneti esemény adatokat tartalmaz `NodeName` `N024` a szabály konfigurálása miatti 2. számú kulcsmező. (A JSON úgy lett módosítva, hogy megjelenjenek a sortörések az olvashatóság érdekében.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
