---
title: Azure Service Fabric CLI – sfctl Chaos-ütemterv
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A Chaos-ütemezéshez tartozó parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: dd911574cd37c18e3043cfc2482bd1fcd5f3857b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260854"
---
# <a name="sfctl-chaos-schedule"></a>sfctl-káosz ütemterv
A Chaos-ütemterv beolvasása és beállítása.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| get | Szerezze be a káosz-ütemtervet, amely meghatározza, hogy mikor és hogyan kell futtatni a káoszt. |
| halmaz | Állítsa be a káosz által használt ütemtervet. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl Chaos-ütemterv Get
Szerezze be a káosz-ütemtervet, amely meghatározza, hogy mikor és hogyan kell futtatni a káoszt.

Beolvassa a használatban lévő Chaos-ütemterv verzióját, valamint azt a káosz-ütemtervet, amely meghatározza, hogy mikor és hogyan kell futtatni a káoszt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl Chaos-ütemterv beállítása
Állítsa be a káosz által használt ütemtervet.

A Chaos automatikusan ütemezni fogja a futtatásokat a Chaos-ütemterv alapján. A Chaos-ütemterv akkor frissül, ha a megadott verzió megegyezik a kiszolgálón található verzióval. A Chaos-ütemterv frissítésekor a kiszolgálón lévő verzió eggyel nő. A kiszolgáló verziószáma 0 lesz, miután elérte a nagy számot. Ha a káosz fut a híváskor, a hívás sikertelen lesz.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Chaos-Parameters-Dictionary | A JSON-kódolású lista a feladatok által használandó ChaosParameters karakterlánc-nevek hozzárendelését jelképezi. |
| --lejárat dátuma – UTC | Az a dátum és idő, ameddig a rendszer leállítja a káoszt az ütemterv használatával.  Alapértelmezett \: 9999-12-31T23 \: 59 \: 59.999 z. |
| – feladatok | JSON-kódolású ChaosScheduleJobs-lista, amely azt jelképezi, hogy mikor fusson a káosz, és hogy milyen paraméterekkel kell futtatni a Chaos-t |
| --Start-Date-UTC | Annak a dátumnak és időpontnak a dátuma és ideje, hogy mikor kell elkezdeni az ütemtervet ütemezni.  Alapértelmezett \: 1601-01-01T00 \: 00 \: 00.000 z. |
| --időtúllépés-t | Alapértelmezett \: 60. |
| --Version | Az ütemezett verzió verziószáma. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

A következő parancs egy ütemtervet állít be (feltételezve, hogy az aktuális ütemezett verzió 0), amely 2016-01-01-kor kezdődik, és 2038-01-01 a Chaos 24 órányi nap, a hét 7 napján fut le.
A káosz a fürtön lesz ütemezve.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
