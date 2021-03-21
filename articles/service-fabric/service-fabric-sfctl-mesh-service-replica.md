---
title: Azure Service Fabric CLI-sfctl Mesh Service – replika
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. Az alkalmazás-erőforrások replikájának beolvasására szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: cbfdba30663e2aa531ab1db955b0e035a0588709
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245721"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh szolgáltatásreplika
A replika adatainak beolvasása és az adott szolgáltatás replikáinak listázása egy alkalmazás-erőforrásban.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| list | A szolgáltatás összes replikáját listázza. |
| megjelenítés | Egy alkalmazás szolgáltatásának adott replikájának beolvasása. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl Mesh Service – replika lista
A szolgáltatás összes replikáját listázza.

Lekéri egy szolgáltatás összes replikájának adatait. Az információk közé tartozik a szolgáltatás replikájának leírása és egyéb tulajdonságai.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --App-Name--Application-Name [kötelező] | Az alkalmazás neve. |
| – szolgáltatásnév [kötelező] | A szolgáltatás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl Mesh Service – replika megjelenítés
Egy alkalmazás szolgáltatásának adott replikájának beolvasása.

Lekéri a szolgáltatás replikájának adatait a megadott névvel. Az információk közé tartozik a szolgáltatás replikájának leírása és egyéb tulajdonságai.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --App-Name--Application-Name [kötelező] | Az alkalmazás neve. |
| --Name-n [kötelező] | A szolgáltatás replikájának neve. |
| – szolgáltatásnév [kötelező] | A szolgáltatás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
