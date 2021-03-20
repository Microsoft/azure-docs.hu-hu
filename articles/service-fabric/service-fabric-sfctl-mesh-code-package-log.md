---
title: Azure Service Fabric CLI-sfctl Mesh Code-Package-log
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A megadott programkódhoz tartozó naplók beszerzésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9ac1d85a1a498f9f6fcd0a03f8f819d1cdfcac33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257297"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh kódcsomagnaplója
Szerezze be a naplófájlokat az adott szolgáltatás replikájának megadott csomagjának tárolójában.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| get | Lekéri a tárolóból a naplókat. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl Mesh Code-Package-log Get
Lekéri a tárolóból a naplókat.

Beolvassa a szolgáltatás replikájának megadott kódjának tárolóját tartalmazó naplókat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --App-Name--Application-Name [kötelező] | Az alkalmazás neve. |
| --Code-Package-Name [kötelező] | A szolgáltatás kódjának neve. |
| --replika-név [kötelező] | Service Fabric replika neve. |
| – szolgáltatásnév [kötelező] | A szolgáltatás neve. |
| – farok | A naplók végétől megjelenítendő sorok száma. Az alapértelmezett érték a 100. az "all" (összes) a teljes naplókat jeleníti meg. |

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
