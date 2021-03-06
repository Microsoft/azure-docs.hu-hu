---
title: Erőforráskészlet-mintaszabályok létrehozása
description: Megtudhatja, hogyan hozhat létre erőforráskészlet-mintaszabályt az eszközök erőforráskészletekbe való csoportosításának felülírásaként
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: 61de2cf2e3ad9175d97378234d62f72ab3517b51
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587833"
---
# <a name="create-resource-set-pattern-rules"></a>Erőforráskészlet-mintaszabályok létrehozása

A nagy léptékű adatfeldolgozási rendszerek általában egyetlen táblát tárolnak egy lemezen több fájlként. Ezt a fogalmat erőforráskészletek képviselik az Azure Purview-ban. Az erőforráskészlet egyetlen objektum az adatkatalógusban, amely nagy számú objektumot képvisel a tárolóban. További információ: [Az erőforráskészletek ismertetése.](concept-resource-sets.md)

A tárfiókok vizsgálatakor az Azure Purview meghatározott minták halmazát használja annak megállapításához, hogy az eszközcsoportok erőforráskészletek-e. Bizonyos esetekben előfordulhat, hogy az Azure Purview erőforráskészlet-csoportosítása nem tükrözi pontosan az adathalmazt. Az erőforráskészlet-mintaszabályok segítségével testreszabhatja vagy felülbírálhatja, hogy az Azure Purview hogyan észleli, hogy mely eszközök vannak erőforráskészletként csoportosítva, és hogyan jelennek meg a katalógusban.

A mintaszabályok jelenleg a következő forrástípusokban támogatottak:
- 2. generációs Azure Data Lake Storage
- Azure Blob Storage
- Azure Files


## <a name="how-to-create-a-resource-set-pattern-rule"></a>Erőforráskészlet-mintaszabály létrehozása

Új erőforráskészlet-mintaszabály létrehozásához kövesse az alábbi lépéseket:

1. Ugrás a felügyeleti központra. Az **Erőforráskészletek** fejléc alatti menüben válassza a Mintaszabályok lehetőséget. Új **szabálykészlet létrehozásához** válassza az + Új lehetőséget.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-rule.png" alt-text="Új erőforráskészlet-mintaszabály létrehozása" border="true":::

1. Adja meg az erőforráskészlet-mintaszabály hatókörét. Válassza ki a tárfiók típusát és annak a tárfióknak a nevét, amely számára szabálykészletet kíván létrehozni. A rendszer minden szabálykészletet a Mappa elérési útja mezőben megadott mappaútvonal-hatókörhöz **viszonyítva** alkalmaz.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-scope.png" alt-text="Erőforráskészlet-mintaszabály-konfigurációk létrehozása" border="true":::

1. Konfigurációs hatókörre vonatkozó szabály beíratáshoz válassza az **+ Új szabály lehetőséget.**

1. Szabály létrehozásához írja be a következő mezőket:

   1. **Szabály neve:** A konfigurációs szabály neve. Ez a mező nincs hatással azokra az eszközökre, amelyekre a szabály vonatkozik.

   1. **Minősített név:** Minősített elérési út, amely szöveg, dinamikus cserélők és statikus cserélők kombinációját használja az eszközök és a konfigurációs szabály egyeztetésére. Ez az elérési út a konfigurációs szabály hatókörének megfelelő. A minősített [nevek megadásának](#syntax) részletes utasításait az alábbi szintaxisszakaszban olvashatja el.

   1. **Megjelenítendő név:** Az eszköz megjelenítendő neve. A mező nem kötelező. Egyszerű szöveg és statikus cserélők használatával szabhatja testre, hogyan jelennek meg az eszközök a katalógusban. Részletesebb utasításokat az alábbi [szintaxisszakaszban](#syntax) olvashat.

   1. **Ne csoportosan erőforráskészletként:** Ha engedélyezve van, az egyező erőforrás nem lesz erőforráskészletbe csoportosítva.

      :::image type="content" source="media/how-to-resource-set-pattern-rules/scoped-resource-set-rule-example.png" alt-text="Hozzon létre új konfigurációs szabályt." border="true":::

1. Mentse a szabályt a Hozzáadás **gombra kattintva.**

> [!NOTE]
> A mintaszabály létrehozása után az összes új vizsgálat alkalmazza a szabályt a beolvasás során. Az adatkatalógus meglévő eszközei egy háttérfolyamattal frissülnek, amely akár néhány órát is igénybe vehet. 

## <a name="pattern-rule-syntax"></a><a name="syntax"></a> Mintaszabály szintaxisa

Erőforráskészlet-mintaszabályok létrehozásakor a következő szintaxissal adhatja meg, hogy mely eszközökre vonatkoznak szabályok.

### <a name="dynamic-replacers-single-brackets"></a>Dinamikus cserélők (egy zárójel)

Az egy zárójeles zárójelek dinamikus **cserélőkként használhatók** a mintaszabályokban. Adjon meg egy dinamikus lecserélőt a minősített névben a következő `{<replacerName:<replacerType>}` formátumban: . Egyezés esetén a dinamikus lecserélők csoportosítási feltételként vannak használva, amely azt jelzi, hogy az eszközöket erőforráskészletként kell ábrázolni. Ha az adateszközök egy erőforráskészletbe vannak csoportosítva, az erőforráskészlet minősített elérési útja tartalmazza a `{replacerName}` lecserélő helyének a beállítását.

Ha például két és szabálynak megfeleltethető eszköz, az erőforráskészlet egyetlen `folder1/file-1.csv` `folder2/file-2.csv` entitás `{folder:string}/file-{NUM:int}.csv` `{folder}/file-{NUM}.csv` lesz.

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Speciális eset: Dinamikus lecserélők, ha nem erőforráskészletbe csoportosít

Ha *a Ne csoportosan erőforráskészletként* beállítás engedélyezve legyen egy mintaszabályhoz, a lecserélő neve nem kötelező mező. `{:<replacerType>}` A érvényes szintaxis. A például sikeresen megfelel a és a értéknek, és két különböző adateszközt hoz létre `file-{:int}.csv` `file-1.csv` az `file-2.csv` erőforráskészlet helyett.

### <a name="static-replacers-double-brackets"></a>Statikus cserélők (két zárójel)

A kettős zárójelek statikus **cserélőkként használatosak** a mintaszabály minősített nevében. Adjon meg egy statikus lecserélőt a minősített névben a következő `{{<replacerName>:<replacerType>}}` formátumban: . Egyezés esetén az egyedi statikus lecserélő értékek minden készlete különböző erőforráskészlet-csoportosításokat hoz létre.

Ha például két és egyező eszköz felel meg a `folder1/file-1.csv` `folder2/file-2.csv` szabálynak, két `{{folder:string}}/file-{NUM:int}.csv` erőforráskészlet jön `folder1/file-{NUM}.csv` létre és `folder2/file-{NUM}.csv` .

A statikus lecserélők segítségével megadható egy mintaszabálynak megfelelő eszköz megjelenítendő neve. A szabály megjelenítendő nevében a az egyező értéket fogja használni az `{{<replacerName>}}` eszköz nevében.

### <a name="available-replacement-types"></a>Rendelkezésre álló helyettesítő típusok

Az alábbiakban a statikus és dinamikus cserélőkben használható típusok érhetők el:

| Típus | Struktúra |
| ---- | --------- |
| sztring | 1 vagy több Unicode-karakterből áll, beleértve az elválasztó karaktereket, például a szóközöket. |
| int | Egy 1 vagy több 0–9 ASCII karakterből állhat, amely 0 előtaggal (például 0001) is lehet. |
| guid | Egy UUID 32 vagy 8-4-4-4-12 sztringként való ábrázolása az [RFC 4122](https://tools.ietf.org/html/rfc4122)szabványban meghatározottdefa alapján. |
| dátum | 6 vagy 8 0–9 ASCII-karaktersorozat választható elválasztókkal: yyyymmdd, yy-mm-dd, yymmdd, yy-mm-dd, az [RFC 3339-ben](https://tools.ietf.org/html/rfc3339)megadott . |
| time | 4 vagy 6 0–9 ASCII-karakterek sorozata választható elválasztókkal: HHmm, HH:mm, HHmmss, HH:mm:mm, az [RFC 3339-ben](https://tools.ietf.org/html/rfc3339)megadott . |
| időbélyeg | 12 vagy 14 0–9 ASCII-karakterből áll, választható elválasztókkal: y-mm-ddTHH:mm, yyyymmddhhmm, yy-mm-ddTHH:mm:mm, yyyymmddHHmmss meghatározott [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| boolean | A "true" (igaz) vagy a "false" (hamis) értékeket tartalmazhatja, a kis- és a kis- és a nagy |
| szám | Egy 0 vagy több 0–9 ASCII karakterből állhat, amely 0 előtaggal (pl. 0001) és opcionálisan egy ponttal (. ) és egy 1–9 ASCII karakterből áll, és 0 utótaggal (például .100) lehet. |
| Hex | A 0–1 és az A–F értékből származó, 1 vagy több ASCII-karakterből állhat, és az érték 0 előtaggal is lehet |
| területi beállítás | Az [RFC 5646](https://tools.ietf.org/html/rfc5646)szabványban megadott szintaxisnak megfelelő sztring. |

## <a name="order-of-resource-set-pattern-rules-getting-applied"></a>Az erőforráskészlet-mintaszabályok alkalmazásának sorrendje

Az alábbiakban látható a mintaszabályok alkalmazásához szükséges műveletek sorrendje:

1. A pontosabb hatókörök elsőbbséget élveznek, ha egy eszköz két szabálynak is megfelel. Egy hatókör szabályai például a hatókör szabályai előtt `container/folder` lesznek `container` érvényben.

1. Szabályok sorrendje egy adott hatókörben. Ez a felhasználói felületről szerkeszthető.

1. Ha egy eszköz nem felel meg egyik megadott szabálynak sem, az alapértelmezett erőforráskészlet-heurisztikák érvényesek.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa

SAP-adatok kinyerése teljes és változásterhelésekbe

#### <a name="inputs"></a>Bevitelek

Fájlokat:

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="pattern-rule"></a>Mintaszabály

**Hatókör:**`https://myazureblob.blob.core.windows.net/bar/`

**Megjelenítendő név:** "Külső ügyfél"

**Minősített név:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Erőforráskészlet:** true (igaz)

#### <a name="output"></a>Kimenet

Egy erőforráskészlet-eszköz

**Megjelenítendő név:** Külső ügyfél

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>2\. példa

IoT-adatok avro formátumban

#### <a name="inputs"></a>Bevitelek

Fájlokat:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rules"></a>Mintaszabályok

**Hatókör:**`https://myazureblob.blob.core.windows.net/bar/`

1. szabály

**Megjelenített név:** "machine-89"

**Minősített név:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Erőforráskészlet:** true (igaz)

2. szabály

**Megjelenített név:** "machine-90"

**Minősített név:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

**Erőforráskészlet:** true (igaz)

#### <a name="outputs"></a>Kimenetek

2 erőforráskészlet

1. erőforráskészlet

**Megjelenített név:** machine-89

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

2. erőforráskészlet

**Megjelenített név:** machine-90

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>3\. példa

IoT-adatok avro formátumban

#### <a name="inputs"></a>Bevitelek

Fájlokat:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Mintaszabály

**Hatókör:**`https://myazureblob.blob.core.windows.net/bar/`

**Megjelenítendő név:** 'Machine-{{machineid}}'

**Minősített név:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Erőforráskészlet:** true (igaz)

#### <a name="outputs"></a>Kimenetek

1. erőforráskészlet

**Megjelenített név:** machine-89

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

2. erőforráskészlet

**Megjelenített név:** machine-90

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>4\. példa

Ne csoportosítsa az erőforráskészleteket

#### <a name="inputs"></a>Bevitelek

Fájlokat:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Mintaszabály

**Hatókör:**`https://myazureblob.blob.core.windows.net/bar/`

**Megjelenítendő név:**`Machine-{{machineid}}`

**Minősített név:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Erőforráskészlet:** false (hamis)

#### <a name="outputs"></a>Kimenetek

4 egyéni eszköz

1. eszköz

**Megjelenített név:** machine-89

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

2. eszköz

**Megjelenített név:** machine-89

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

3. eszköz

**Megjelenített név:** machine-89

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

4. eszköz

**Megjelenített név:** machine-90

**Minősített név:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Következő lépések

Első lépések: [Azure Data Lake Gen2-tárfiók regisztrálása és beolvasása.](register-scan-adls-gen2.md)