---
title: Kieséses ablak trigger függőségeinek létrehozása
description: Megtudhatja, hogyan hozhat létre függőséget egy kieséses ablakos triggerből Azure Data Factoryban.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/03/2020
ms.openlocfilehash: f969c06a3419a8017cfc5ebc0de19caa67c8dc68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361468"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Függőség létrehozása átfedésmentes ablak eseményindítójához
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan hozhat létre függőséget egy kieséses ablakos triggeren. Az ablakos eseményindítókkal kapcsolatos általános információkért lásd: [a kieséses ablakos eseményindító létrehozása](how-to-create-tumbling-window-trigger.md).

Függőségi lánc létrehozásához és annak ellenőrzéséhez, hogy egy trigger csak egy másik trigger sikeres végrehajtása után fusson az adatgyárban, ezzel a speciális funkcióval hozzon létre egy ablakos függőséget.

Ahhoz, hogy a Azure Data Factory függő folyamatokat hozzon létre a kieséses ablakos triggerrel, tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Függőség létrehozása a Data Factory felhasználói felületen

Ha függőséget szeretne létrehozni egy triggerhez, válassza az **aktiválás > speciális > új** lehetőséget, majd válassza ki a megfelelő eltolással és mérettel függő triggert. Válassza a **Befejezés** lehetőséget, és tegye közzé a függőségek érvénybe léptetéséhez szükséges adatfeldolgozó-módosításokat.

![Függőség létrehozása](media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png "Függőség létrehozása")

## <a name="tumbling-window-dependency-properties"></a>Ablak függőségi tulajdonságainak kiesése

A függőséggel rendelkező kieséses ablakos trigger a következő tulajdonságokkal rendelkezik:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

Az alábbi táblázat tartalmazza az ablak függőségének definiálásához szükséges attribútumok listáját.

| **Tulajdonság neve** | **Leírás**  | **Típus** | **Kötelező** |
|---|---|---|---|
| típus  | Ebben a legördülő ablakban minden meglévő ablak-eseményindító megjelenik. Válassza ki a függőség bekapcsolásához szükséges triggert.  | TumblingWindowTriggerDependencyReference vagy SelfDependencyTumblingWindowTriggerReference | Yes |
| offset | A függőségi trigger eltolása. Adja meg az időtartomány formátumú értéket, és a negatív és a pozitív eltolás is engedélyezett. Ez a tulajdonság akkor kötelező, ha az trigger saját magától függ, és minden más esetben nem kötelező. Az önfüggőségnek mindig negatív eltolásnak kell lennie. Ha nincs megadva érték, az ablak ugyanaz, mint maga az trigger. | Időtartomány<br/>(óó: PP: SS) | Önálló függőség: igen<br/>Egyéb: nem |
| size | A függőséget jelző ablak mérete Adjon meg egy pozitív TimeSpan értéket. Ez a tulajdonság nem kötelező. | Időtartomány<br/>(óó: PP: SS) | No  |

> [!NOTE]
> A kihelyezett ablakos eseményindítók legfeljebb öt másik eseményindítótól függnek.

## <a name="tumbling-window-self-dependency-properties"></a>A kiesési ablak önfüggőségi tulajdonságai

Azokban az esetekben, amelyekben a trigger nem folytassa a következő ablakba, amíg az előző ablak sikeresen be nem fejeződik, hozzon létre egy önfüggőséget. Egy önfüggőségi trigger, amely az előző órában a korábbi futtatások sikerességének függvénye, az alábbi kódban szereplő tulajdonságokkal fog rendelkezni.

> [!NOTE]
> Ha az aktivált folyamat a korábban aktivált Windows-folyamatokban lévő folyamatok kimenetére támaszkodik, javasoljuk, hogy csak a legördülő ablakos trigger önfüggőségét használja. A párhuzamos triggerek futtatásának korlátozásához állítsa be a maximimum trigger egyidejűségét.

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Használati forgatókönyvek és példák

Alább láthatók a forgatókönyvek és az ablak függőségi tulajdonságainak használata.

### <a name="dependency-offset"></a>Függőségi eltolás

![Eltolási példa](media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png "Eltolási példa")

### <a name="dependency-size"></a>Függőség mérete

![Példa a méretre](media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png "Példa a méretre")

### <a name="self-dependency"></a>Önálló függőség

![Önálló függőség](media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png "Önálló függőség")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Függőség egy másik kiesést jelző ablakos triggertől

A napi telemetria-feldolgozási feladatok attól függően, hogy az elmúlt hét nap folyamán egy másik napi feladatot összesítenek, és hét napos időszakot állítanak elő:

![Függőségi példa](media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png "Függőségi példa")

### <a name="dependency-on-itself"></a>Függőség önmagában

A feladatokhoz tartozó kimeneti adatfolyamok hiánya nélküli napi feladatok:

![Saját függőségi példa](media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png "Saját függőségi példa")

## <a name="monitor-dependencies"></a>Függőségek figyelése

A függőségi láncot és a megfelelő Windows-t a trigger futtatásának figyelése lapról követheti nyomon. Navigáljon a  **figyelés > triggerek futtatásához**. Ha egy kiugró ablak-trigger függőségekkel rendelkezik, akkor a trigger neve a függőség figyelési nézetére mutató hivatkozást tartalmaz.  

![Eseményindító-futtatások monitorozása](media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png "Riasztási futtatások figyelése – a teljes ablak függőség nézete")

Az trigger függőségeinek megtekintéséhez kattintson a trigger nevére. A jobb oldali panelen a részletes trigger-futtatási információk, például a RunID, az ablak időpontja, az állapot és így tovább láthatók.

![A függőségek listájának figyelése](media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png "A függőségek listájának figyelése")

Megtekintheti a függőségek állapotát és a Windowst az egyes függő triggerekhez. Ha az egyik függőségi eseményindító meghibásodik, a függő eseményindító futtatásához sikeresen újra kell futtatnia a parancsot.

A kiugró ablakos trigger az időtúllépés előtt _hét napig_ megvárja a függőségeket. Hét nap elteltével a trigger futtatása sikertelen lesz.

Az trigger függőségi ütemtervének megjelenítéséhez kattintson a Gantt-nézetre.

![Függőségek figyelése Gantt-diagram](media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png "Függőségek figyelése Gantt-diagram nézetben")

Az átlátszó mezők megjelenítik a függőségi ablakokat az egyes adatfolyam-függőségi triggerekhez, míg az egyes ablakokat megjelenítő, egyszínű színes dobozok is megjelennek. Íme néhány tipp a Gantt-diagram nézet értelmezéséhez:

* Átlátszó doboz kék színnel jelenik meg, ha a függő ablakok függőben vagy futó állapotban vannak
* Miután az összes Windows sikeresen elvégezte a függő triggert, az átlátszó doboz zöldre vált.
* A transzparens mező piros színnel jeleníti meg, ha néhány függő ablak meghibásodik. Keressen egy folytonos piros mezőt a hiba-ablak futtatásának azonosításához

Egy ablak Gantt-diagram nézetben való újrafuttatásához jelölje be az ablakhoz tartozó Solid Color (folytonos szín) jelölőnégyzetet, és a műveleti panel a részletek és az újrafuttatási beállítások használatával jelenik meg.

## <a name="next-steps"></a>Következő lépések

* [A bukdácsoló ablakos trigger létrehozásának](how-to-create-tumbling-window-trigger.md) áttekintése
