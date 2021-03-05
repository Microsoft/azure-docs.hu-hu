---
title: Trigger-információk továbbítása a folyamatnak
description: Útmutató trigger-metaadatok hivatkozásához a folyamatban
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 57024d6cb23ab273aa69bf59f4ec436a6f873a05
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193494"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Hivatkozás-trigger metaadatainak a folyamat futtatásakor

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használhatók a folyamat futásakor az trigger metaadatainak, például a trigger indítási ideje.

A folyamatnak időnként meg kell ismernie és olvasnia a metaadatokat az triggertől, amely meghívja azt. Ha például az ablak indítási és befejezési időpontját futtató ablakos trigger fut, a folyamat különböző adatszeleteket vagy mappákat fog feldolgozni. Azure Data Factory a paraméterezés és a [System változó](control-flow-system-variables.md) használatával továbbíthatja a metaadatokat az triggerről a folyamatba.

Ez a minta különösen az [ablakos triggerek](how-to-create-tumbling-window-trigger.md)esetében hasznos, ahol a trigger ablak kezdési és befejezési időpontját jeleníti meg.

> [!NOTE]
> A különböző trigger-típusok különböző meta-adatokat biztosítanak. További információ: [System változó](control-flow-system-variables.md)

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

Ez a szakasz bemutatja, hogyan adhat át metaadatokat a triggerről a folyamatra a Azure Data Factory felhasználói felületen belül.

1. Ugrás a **szerzői műveletek vászonra** és a folyamat szerkesztése

1. Kattintson az üres vászonra a folyamat beállításainak megadásához. Ne válasszon tevékenységet. Előfordulhat, hogy a vászon aljáról kell lekérnie a beállítás panelt, mivel az összecsukása megtörtént.

1. Válassza a **Parameters (paraméterek** ) szakaszt, és válassza a **+ új** lehetőséget a paraméterek hozzáadásához

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Képernyőfelvétel a folyamat paramétereinek definiálásáról.":::

1. Eseményindítók hozzáadása a folyamathoz a **+ trigger** lehetőségre kattintva.

1. Hozzon létre vagy csatoljon egy triggert a folyamathoz, és kattintson **az OK** gombra.

1. A következő lapon adja meg az egyes paraméterekhez tartozó meta-adattípust. A [rendszerváltozóban](control-flow-system-variables.md) definiált formátumot használja az trigger adatainak lekéréséhez. Nem kell kitöltenie az összes paraméter információit, csak azokat, amelyek a metaadat-értékeket fogják feltételezni. Itt például a trigger futtatásának kezdési idejét rendeljük *parameter_1hoz*.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Képernyőkép az trigger definíciós oldaláról, amely bemutatja, hogyan lehet átadni a trigger információit a folyamat paramétereinek.":::

1. A folyamaton belüli értékek használatához használja a Parameters _@pipeline (). Parameters. parameterName_, __nem__ a System változót a csővezeték-definíciókban. Ha például az indítás kezdési idejét szeretné beolvasni, a következő hivatkozást fogjuk @pipeline .Parameters.parameter_1.

## <a name="json-schema"></a>JSON-séma

Ahhoz, hogy a trigger információit átadja a folyamat futásának, az triggert és a folyamat JSON-fájlját is frissíteni kell a _Parameters (paraméterek_ ) szakasz használatával.

### <a name="pipeline-definition"></a>Folyamat definíciója

A **Tulajdonságok** szakaszban adja hozzá a paraméter-definíciókat a **Parameters** szakaszhoz.

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Trigger definíciója

A **folyamatok** szakaszban a **Paraméterek** szakaszban rendeljen hozzá paramétereket. Nem kell kitöltenie az összes paraméter információit, csak azokat, amelyek a metaadat-értékeket fogják feltételezni.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Trigger-információk használata a folyamatban

A folyamaton belüli értékek használatához használja a Parameters _@pipeline (). Parameters. parameterName_, __nem__ a System változót a csővezeték-definíciókban.

## <a name="next-steps"></a>Következő lépések

Az eseményindítókkal kapcsolatos részletes információkért lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#trigger-execution).
