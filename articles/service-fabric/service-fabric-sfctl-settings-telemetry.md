---
title: Azure Service Fabric CLI – sfctl beállítások telemetria
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A sfctl-telemetria konfigurálására szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ef720a14617b4131474d50875701d0ef27df4151
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245507"
---
# <a name="sfctl-settings-telemetry"></a>sfctl-beállítások telemetriája
Konfigurálja a helyi telemetria-beállításokat a sfctl ezen példányára.

A Sfctl telemetria a megadott paraméterek nélkül gyűjti a parancs nevét, illetve azok értékeit, a Sfctl verzióját, az operációs rendszer típusát, a Python verzióját, a parancs sikerességét vagy hibáját. a visszaadott hibaüzenet.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| set-telemetria | Kapcsolja be vagy ki a telemetria. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl-beállítások telemetria set-telemetria
Kapcsolja be vagy ki a telemetria.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| – ki | Kapcsolja ki a telemetria. |
| – bekapcsolva | Kapcsolja be a telemetria. Ez az alapértelmezett érték. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

Kapcsolja ki a telemetria.

```
sfctl settings telemetry set_telemetry --off
```

Kapcsolja be a telemetria.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
