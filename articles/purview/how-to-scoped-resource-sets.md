---
title: 'Útmutató: hatókörön belüli erőforrás-készlet konfigurációjának létrehozása'
description: Megtudhatja, hogyan hozhat létre egy hatókörrel rendelkező erőforrás-készlet konfigurációs szabályt, amely felülírja, hogy az eszközök hogyan legyenek csoportosítva az erőforrások csoportjaiba
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 517b07eecdbc63754f46fcf1051bf5b987dbc20e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654445"
---
# <a name="create-scoped-resource-set-configuration-rules"></a>Hatókörön belüli erőforrás-készlet konfigurációs szabályainak létrehozása

A méretezés alatt álló adatfeldolgozási rendszerek általában egyetlen táblát tárolnak egy lemezen több fájlként. Ez a koncepció az Azure hatáskörébe tartozik az erőforrás-készletek használatával. Az erőforrás-készlet egyetlen objektum a adatkatalógusban, amely nagy mennyiségű eszközt képvisel a tárolóban. További információ: az [erőforráscsoportok ismertetése](concept-resource-sets.md).

Egy Storage-fiók vizsgálata során az Azure-beli hatáskörébe definiált mintázatok határozzák meg, hogy az eszközök egy csoportja erőforrás-készletet használ-e. Bizonyos esetekben előfordulhat, hogy az Azure hatáskörébe tartozó erőforrás-készlet csoportosítása nem pontosan tükrözi az adattárházat. A hatókörrel rendelkező erőforrás-készlet szabályai lehetővé teszik annak testreszabását vagy felülbírálását, hogy az Azure hatáskörébe tartozik-e, hogy mely eszközök legyenek erőforrás-készletként csoportosítva, és hogyan jelenjenek meg a katalógusban.

## <a name="how-to-create-a-scoped-resource-set-configuration"></a>Hatókörrel rendelkező erőforrás-készlet konfigurációjának létrehozása

Kövesse az alábbi lépéseket egy új, hatókörön belüli erőforrás-készlet konfigurációjának létrehozásához:

1. Nyissa meg a felügyeleti központot. Válassza ki a listából a **hatókörön belüli erőforrás-készleteket** . Új konfigurációs szabálykészlet létrehozásához kattintson az **+ új** elemre.
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-rule.png" alt-text="Új hatókörön belüli erőforrás-set szabály létrehozása" border="true":::

1. Adja meg a hatókörön belüli erőforrás-készlet konfigurációjának hatókörét. Válassza ki a Storage-fiók típusát és annak a Storage-fióknak a nevét, amelyhez szabályt kíván létrehozni. Az egyes szabályok a mappa **elérési útja** mezőben megadott mappa elérési útjának hatóköréhez képest érvényesek. 
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-scope.png" alt-text="Új hatókörön belüli erőforrás-set szabály létrehozása" border="true":::

1. Egy konfigurációs hatókörhöz tartozó szabály megadásához válassza az **+ új szabály** lehetőséget.
1. Szabály létrehozásához adja meg a következő mezőket:
    1. **Szabály neve:** A konfigurációs szabály neve. Ez a mező nem befolyásolja azon eszközöket, amelyekre a szabály vonatkozik.
    1. **Minősített név:** Olyan minősített elérési út, amely szöveg, dinamikus replacers és statikus replacers kombinációját használja az eszközöknek a konfigurációs szabályhoz való egyeztetéséhez. Ez az elérési út a konfigurációs szabály hatóköréhez képest relatív. A minősített nevek megadásával kapcsolatos részletes utasításokért tekintse meg az alábbi [szintaxist](#syntax) . 
    1. **Megjelenítendő név:** Az eszköz megjelenített neve. A mező nem kötelező. Az egyszerű szöveg és a statikus replacers használatával testreszabhatja, hogy az eszköz hogyan jelenjen meg a katalógusban. Részletesebb útmutatást az alábbi [szintaxis](#syntax) című szakaszban talál.
    1. **Csoportosítás nem erőforrás-készletként:** Ha engedélyezve van, a megfeleltetett erőforrás nem lesz csoportosítva egy erőforrás-készletbe. 
        :::image type="content" source="media/how-to-scoped-resource-sets/scoped-resource-set-rule-example.png" alt-text="Új hatókörön belüli erőforrás-set szabály létrehozása" border="true"::: 
1. Mentse a szabályt a **Hozzáadás** gombra kattintva. 

## <a name="scoped-resource-set-syntax"></a><a name="syntax"></a> Hatókörön belüli erőforrás-készlet szintaxisa

A hatókörön belüli erőforrás-set szabályok létrehozásakor a következő szintaxissal adhatja meg, hogy mely eszközökre vonatkozik a.

### <a name="static-replacers-single-brackets"></a>Statikus replacers (egyetlen zárójel)

Az egyes zárójelek **dinamikus replacers** használatosak egy hatókörön belüli erőforrás-készlet szabályban. Adjon meg egy dinamikus áthelyező nevet a minősített névben a format paranccsal `{<replacerName:<replacerType>}` . Ha egyeznek, a dinamikus replacers olyan csoportosítási feltételként használatosak, amely azt jelzi, hogy az eszközöknek erőforrás-készletként kell szerepelniük. Ha az eszközök egy erőforrás-készletbe vannak csoportosítva, az erőforrás-készlet minősített elérési útja tartalmazni fogja, `{replacerName}` hogy hol lett megadva a áthelyező.

Ha például két eszköz megfelel a `folder1/file-1.csv` `folder2/file-2.csv` szabálynak `{folder:string}/file-{NUM:int}.csv` , az erőforrás-készlet egyetlen entitás lenne `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Speciális eset: dinamikus replacers, ha nem csoportosítja az erőforrás-készletet

Ha a nem *csoport, mint erőforrás-készlet* engedélyezve van egy hatókörön belüli erőforrás-készlet szabályhoz, a cserélhető elem neve egy választható mező. `{:<replacerType>}` érvényes szintaxis. Például a (z) és a (z) és a (z) és a (z) és a `file-{:int}.csv` (z `file-1.csv` `file-2.csv` ) egy erőforrás-készlet helyett

### <a name="static-replacers-double-brackets"></a>Statikus replacers (dupla zárójel)

A kettős zárójelek **statikus replacers** használatosak a hatókörön belüli erőforrás-set szabály minősített neve alatt. Adjon meg egy statikus áthelyező nevet a minősített névben a format paranccsal `{{<replacerName>:<replacerType>}}` . Ha egyezést ad meg, az egyedi statikus áthelyező értékek mindegyike különböző erőforráscsoport-csoportokat hoz létre.

Ha például két eszköz `folder1/file-1.csv` `folder2/file-2.csv` felel meg a szabálynak, a rendszer `{{folder:string}}/file-{NUM:int}.csv` két erőforrást hoz létre, `folder1/file-{NUM}.csv` és `folder2/file-{NUM}.csv` .

A statikus replacers megadható a hatókörön belüli erőforrás-set szabálynak megfelelő objektum megjelenített neve. A (z) használatával a (z `{{<replacerName>}}` ) egy szabály megjelenített nevében az egyező értéket fogja használni az eszköz neve alatt.

### <a name="available-replacement-types"></a>Elérhető helyettesítő típusok

A statikus és a dinamikus replacers használható elérhető típusok alább láthatók:

| Típus | Struktúra |
| ---- | --------- |
| sztring | 1 vagy több Unicode-karakterből álló sorozat, beleértve az elválasztó karaktereket, például A szóközöket. |
| int | 1 vagy több 0-9 ASCII karakterből álló sorozat lehet 0 előtag (például 0,001). |
| guid | Egy 32-es vagy 8-4-4-4-12-es karakterlánc-ábrázolás egy olyan defineddefa, amely a https://tools.ietf.org/html/rfc4122 |
| dátum | 6 vagy 8 0-9 ASCII-karakterből álló sorozat, opcionálisan elválasztó: ÉÉÉÉHHNN, éééé-hh-nn, ÉÉHHNN, éé-hh-nn, megadva a (z) https://tools.ietf.org/html/rfc3339 |
| time | 4 vagy 6 0-9 ASCII-karakterek sorozata opcionálisan elválasztó karakterrel: óópp, óó: PP, HHmmss, HH: PP: mm https://tools.ietf.org/html/rfc3339 |
| időbélyeg | 12 vagy 14 0-9 ASCII-karakterből álló sorozat, opcionálisan elválasztó: éééé-hh-NNTóó: PP, yyyyMMddHHmm, éééé-hh-NNTóó: PP: mm, yyyymmddHHmmss megadva https://tools.ietf.org/html/rfc3339 |
| boolean | "True" vagy "false", kis-és nagybetűket nem tartalmazó. |
| szám | 0 vagy több 0-9 ASCII karakterből álló sorozat lehet 0 előre rögzített (például 0,001), amelyet opcionálisan egy pont ("") követ. továbbá egy 1 vagy több 0-9 ASCII-karakterből álló sorozat lehet 0 Postfix (például. 100) | 
| Hex | 1 vagy több, a 0-1-es és A-F készletből származó ASCII-karakterből álló sorozat, az érték lehet 0 előtag |
| területi beállítás | Egy karakterlánc, amely megfelel a megadott szintaxisnak https://tools.ietf.org/html/rfc5646 |

## <a name="order-of-scoped-resource-set-rules-getting-applied"></a>A hatókörön belüli erőforrás-set szabályok sorrendje.

Az alábbiakban a hatókörön belüli erőforrás-set-szabályok alkalmazására vonatkozó műveletek sorrendje látható:

1. Ha egy eszköz két szabálynak felel meg, az egyes hatókörök prioritást kapnak. Egy hatókör szabályai például a `container/folder` hatókörben szereplő szabályok előtt lesznek érvényesek `container` . 
1. Szabályok sorrendje egy adott hatókörön belül. Ezt az UX-ben lehet szerkeszteni.
1. Ha egy eszköz nem felel meg egyetlen megadott szabálynak sem, az alapértelmezett erőforrás-készlet heurisztikus.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa

SAP-Adatkiemelés teljes és Delta terheléssel

*Bevitelek*

Fájlokat
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`


*Hatókörön belüli erőforrás-set szabály*

**Hatókör:**https://myazureblob.blob.core.windows.net/bar/

**Megjelenítendő név:** "Külső ügyfél"

**Minősített név:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Erőforrás-készlet:** True

*Kimenet*

Egy erőforrás-beállító eszköz

**Megjelenítendő név:** Külső ügyfél

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>2\. példa

IoT az Avro formátumban

*Bevitelek*

Fájlokat
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*Hatókörön belüli erőforrás-készletre vonatkozó szabályok*

**Hatókör:**https://myazureblob.blob.core.windows.net/bar/

1. szabály

**Megjelenítendő név:** "Machine-89"

**Minősített név:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Erőforrás-készlet:** True

2. szabály

**Megjelenítendő név:** "Machine-90"

**Minősített név:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

**Erőforrás-készlet: true**

*Kimenetek*

2 erőforrás-készlet 

1. erőforrás-készlet

**Megjelenítendő név:** machine-89

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

2. erőforrás-készlet

**Megjelenítendő név:** machine-90

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>3\. példa

IoT az Avro formátumban

*Bevitelek*

Fájlokat
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*Hatókörön belüli erőforrás-set szabály*

**Hatókör:**https://myazureblob.blob.core.windows.net/bar/

**Megjelenítendő név:** "Machine-{{machineid}}"

**Minősített név:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Erőforrás-készlet:** True

*Kimenetek*

1. erőforrás-készlet

**Megjelenítendő név:** machine-89

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

2. erőforrás-készlet

**Megjelenítendő név:** machine-90

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>4\. példa

Ne csoportosítsa az erőforrás-készleteket

*Bevitelek*

Fájlokat
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*Hatókörön belüli erőforrás-set szabály*

**Hatókör:**https://myazureblob.blob.core.windows.net/bar/

**Megjelenítendő név:** "Machine-{{machineid}}"

**Minősített név:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Erőforrás-készlet:** hamis

*Kimenetek*

4 egyedi eszköz

1. eszköz

**Megjelenítendő név:** machine-89

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

2. eszköz

**Megjelenítendő név:** machine-89

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

3. eszköz

**Megjelenítendő név:** machine-89

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

4. eszköz

**Megjelenítendő név:** machine-90

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Következő lépések

Első lépésként [regisztrálja és beolvashatja Azure Data Lake Gen2 Storage-fiókját](register-scan-adls-gen2.md).