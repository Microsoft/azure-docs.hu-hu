---
title: Azure Service Fabric CLI – sfctl tulajdonság
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A tulajdonságok tárolására és lekérdezésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0a5ebd4822c5f0ff1735464bb4d5b42c436ee529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260328"
---
# <a name="sfctl-property"></a>sfctl-tulajdonság
Tulajdonságok tárolása és lekérdezése Service Fabric neve alatt.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| delete | Törli a megadott Service Fabric tulajdonságot. |
| get | Lekéri a megadott Service Fabric tulajdonságot. |
| list | Az adott név alatti összes Service Fabric tulajdonság információinak beolvasása. |
| Put | Létrehoz vagy frissít egy Service Fabric tulajdonságot. |

## <a name="sfctl-property-delete"></a>sfctl tulajdonság törlése
Törli a megadott Service Fabric tulajdonságot.

Törli az adott név alatti megadott Service Fabric tulajdonságot. A törlés előtt létre kell hozni egy tulajdonságot.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Name-ID [kötelező] | A Service Fabric neve a "Fabric \: " URI-séma nélkül. |
| --tulajdonság-név [kötelező] | A lekérdezni kívánt tulajdonság nevét adja meg. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-property-get"></a>sfctl tulajdonság beolvasása
Lekéri a megadott Service Fabric tulajdonságot.

Lekéri a megadott Service Fabric tulajdonságot egy adott név alatt. Ez mindig az értéket és a metaadatokat fogja visszaadni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Name-ID [kötelező] | A Service Fabric neve a "Fabric \: " URI-séma nélkül. |
| --tulajdonság-név [kötelező] | A lekérdezni kívánt tulajdonság nevét adja meg. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-property-list"></a>sfctl-tulajdonságok listája
Az adott név alatti összes Service Fabric tulajdonság információinak beolvasása.

A Service Fabric neve tartalmazhat egy vagy több olyan nevesített tulajdonságot, amely egyéni adatokat tárol. A művelet beolvassa a tulajdonságok adatait egy lapozható listában. Az adatok tartalmazzák az egyes tulajdonságokkal kapcsolatos nevet, értéket és metaadatokat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Name-ID [kötelező] | A Service Fabric neve a "Fabric \: " URI-séma nélkül. |
| --Folytatás-token | A folytatási jogkivonat paraméter az eredmények következő készletének beszerzésére szolgál. Egy nem üres értékkel rendelkező folytatási token szerepel az API válaszában, ha a rendszer eredményei nem illeszkednek egyetlen válaszhoz. Ha ezt az értéket átadja a következő API-hívásnak, az API az eredmények következő készletét adja vissza. Ha nincs további eredmény, akkor a folytatási jogkivonat nem tartalmaz értéket. A paraméter értéke nem lehet URL-kódolású. |
| --include-Values | Lehetővé teszi annak megadását, hogy a visszaadott tulajdonságok értékeit tartalmazza-e. Igaz, ha az értékeket vissza kell adni a metaadatokkal; False (hamis) – csak a tulajdonság metaadatainak visszaadása. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-property-put"></a>sfctl tulajdonság Put
Létrehoz vagy frissít egy Service Fabric tulajdonságot.

Létrehozza vagy frissíti a megadott Service Fabric tulajdonságot egy adott név alatt.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Name-ID [kötelező] | A Service Fabric neve a "Fabric \: " URI-séma nélkül. |
| --tulajdonság-név [kötelező] | A Service Fabric tulajdonság neve. |
| --Value [kötelező] | Egy Service Fabric tulajdonság értékét ismerteti. Ez egy JSON-karakterlánc. <br><br> A JSON-karakterláncnak két mezője van, az adat "Kind" értéke, valamint az adat "adat" formájában megadott érték. A "Kind" értéknek az első elemnek kell lennie, amely megjelenik a JSON-karakterláncban, és a "Binary", "Int64", "Double", "string" vagy "GUID" érték lehet. Az értéknek szerializálva kell lennie, hogy az adott típus legyen. A "Kind" és az "adatok" értékeket karakterláncként kell megadni. |
| – Egyéni azonosító – típus | A tulajdonság egyéni típusának azonosítója. Ennek a tulajdonságnak a használatával a felhasználó megcímkézheti a tulajdonság értékének típusát. |
| --időtúllépés-t | Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa be](service-fabric-cli.md) a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
