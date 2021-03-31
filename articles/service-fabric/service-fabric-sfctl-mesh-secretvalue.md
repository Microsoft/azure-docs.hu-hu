---
title: Azure Service Fabric CLI – sfctl Mesh secretvalue
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A Service Fabric Mesh secretvalue erőforrásainak beolvasására és törlésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 985fb505aae96f4ebd1ba8aeb61679081f303243
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245772"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh titkos kódjának értéke
Háló secretvalue erőforrásainak beolvasása és törlése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a névvel ellátott titkos erőforrás megadott értékét. |
| list | A megadott titkos erőforrás összes értékének listázása. |
| megjelenítés | A titkos erőforrás megadott értékét listázza. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl Mesh secretvalue törlése
Törli a névvel ellátott titkos erőforrás megadott értékét.

Törli a név alapján azonosított titkos érték erőforrást. Az erőforrás neve általában az adott értékhez társított verzió. A törlés sikertelen lesz, ha a megadott érték használatban van.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Secret-Name-n [kötelező] | A titkos erőforrás neve. |
| --Version-v [kötelező] | A titkos verzió neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl Mesh secretvalue listája
A megadott titkos erőforrás összes értékének listázása.

Információk beolvasása a megadott titkos erőforrás minden titkos érték erőforrásáról. Az információ tartalmazza a titkos érték erőforrásainak nevét, de nem a tényleges értékeket.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Secret-Name-n [kötelező] | A titkos erőforrás neve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl Mesh secretvalue show
A titkos erőforrás megadott értékét listázza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Secret-Name-n [kötelező] | A titkos erőforrás neve. |
| --Version-v [kötelező] | A titkos verzió neve. |
| --show-Value | A titkos verzió tényleges értékének megjelenítése. |

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
