---
title: Azure Service Fabric CLI – sfctl-tároló
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. Tartalmazza a fürt rendszerkép-tárolójában a fájl szintű műveletek végrehajtásához szükséges parancsok listáját.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: af5399be44d2946775622598c5c1db3d8355f7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257070"
---
# <a name="sfctl-store"></a>sfctl-tároló
Hajtsa végre az alapszintű fájl szintű műveleteket a fürt rendszerkép-tárolóján.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a meglévő rendszerkép-tároló tartalmát. |
| gyökér – információ | Lekéri a tartalom adatait a rendszerkép-tároló gyökerében. |
| stat | A rendszerkép-tároló tartalmának adatainak beolvasása. |

## <a name="sfctl-store-delete"></a>sfctl-tároló törlése
Törli a meglévő rendszerkép-tároló tartalmát.

Törli az adott rendszerkép-tároló relatív elérési útjában található meglévő rendszerkép-tároló tartalmát. Ezzel a paranccsal törölhetők a feltöltött alkalmazáscsomag az üzembe helyezésük után.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Content-Path [kötelező] | A fájl vagy mappa relatív elérési útja a rendszerkép-tárolóban a gyökeréből. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-store-root-info"></a>sfctl-tároló gyökerének adatai
Lekéri a tartalom adatait a rendszerkép-tároló gyökerében.

A rendszerkép-tároló gyökerénél lévő rendszerkép-tároló tartalmának adatait adja vissza.

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

## <a name="sfctl-store-stat"></a>sfctl Store-stat
A rendszerkép-tároló tartalmának adatainak beolvasása.

A megadott contentPath lévő rendszerkép-tároló tartalmának adatait adja vissza. A contentPath a rendszerkép-tároló gyökeréhez viszonyítva.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Content-Path [kötelező] | A fájl vagy mappa relatív elérési útja a rendszerkép-tárolóban a gyökeréből. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa](service-fabric-cli.md) be a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
