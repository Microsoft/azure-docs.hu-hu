---
title: Azure Service Fabric CLI – sfctl Mesh üzembe helyezése
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A Service Fabric Mesh-erőforrások létrehozására szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fb2adafab88eb1d3855cdec8268601fb4e15dcbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257268"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh üzembe helyezése
Hozzon létre Service Fabric Mesh-erőforrásokat.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | Service Fabric Mesh-erőforrások központi telepítését hozza létre. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl háló üzembe helyezésének létrehozása
Service Fabric Mesh-erőforrások központi telepítését hozza létre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --input-YAML-Files [kötelező] | Az összes YAML-fájl vagy a YAML-fájlokat tartalmazó könyvtár relatív vagy abszolút elérési útjának vesszővel tagolt relatív vagy abszolút fájlelérési útja. |
| – paraméterek | Egy YAML-fájl vagy egy JSON-objektum relatív vagy abszolút elérési útja, amely a felülbírálni kívánt paramétereket tartalmazza. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

### <a name="examples"></a>Példák

Összevonja és telepíti az összes erőforrást a fürtbe úgy, hogy felülbírálja a YAML fájlban említett paramétereket.
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

A YAML fájlban említett paraméterek felülbírálásával összevonja és telepíti a címtárban lévő összes erőforrást a fürtbe.

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Egy címtárban lévő összes erőforrás konszolidálása és üzembe helyezése a közvetlenül JSON-objektumként átadott paraméterek felülbírálásával
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
