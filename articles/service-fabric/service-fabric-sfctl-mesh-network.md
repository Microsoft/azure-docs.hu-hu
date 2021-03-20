---
title: Azure Service Fabric CLI – sfctl Mesh hálózat
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A Service Fabric Mesh hálózati erőforrások beolvasására és törlésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b2b2c444bb492fa6c6b945a82090e91963fb1a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245860"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh hálózata
Háló hálózati erőforrásainak beolvasása és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | A hálózati erőforrás törlése. |
| list | Felsorolja az összes hálózati erőforrást. |
| megjelenítés | Lekéri a megadott nevű hálózati erőforrást. |

## <a name="sfctl-mesh-network-delete"></a>sfctl Mesh hálózat törlése
A hálózati erőforrás törlése.

Törli a névvel azonosított hálózati erőforrást.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Name-n [kötelező] | A hálózat neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-network-list"></a>sfctl Mesh hálózat listája
Felsorolja az összes hálózati erőforrást.

Egy adott erőforráscsoport összes hálózati erőforrásával kapcsolatos információk beolvasása. Az információk közé tartozik a hálózat leírása és egyéb tulajdonságai.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-network-show"></a>sfctl Mesh Network show
Lekéri a megadott nevű hálózati erőforrást.

A megadott nevű hálózati erőforrással kapcsolatos információk beolvasása. Az információk közé tartozik a hálózat leírása és egyéb tulajdonságai.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Name-n [kötelező] | A hálózat neve. |

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
