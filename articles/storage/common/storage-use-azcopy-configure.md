---
title: Hibákat & feladatok az AzCopy (Azure Storage) alkalmazásbeli naplókban való folytatása | Microsoft Docs
description: Megtudhatja, hogyan diagnosztizálhatja a hibákat a naplók használatával, és hogyan folytathatja a szüneteltetett feladatokat tervfájlokkal.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d3b956803e9a796c49288f90873e88c3b69f1c7b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502895"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>Hibák megkeresása és feladatok folytatása napló- és tervfájlokkal az AzCopyban

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ez a cikk segítséget nyújt a naplóknak a hibák diagnosztizálásához, majd a fájlok tervfájlokkal való folytatásához. Ez a cikk azt is bemutatja, hogyan konfigurálhatja a napló- és tervfájlokat a részletességi szintjük és a tárolásuk alapértelmezett helyének módosításával.

> [!NOTE]
> Ha olyan tartalmat keres, amely segít az AzCopy első lépésekben, lásd: [Az AzCopy első](storage-use-azcopy-v10.md)lépések.

## <a name="log-and-plan-files"></a>Naplófájlok naplózása és megterve

Az AzCopy *napló- és* *tervfájlokat* hoz létre minden feladathoz. Ezekkel a naplókval megvizsgálhatja és elháríthatja a potenciális problémákat. 

A naplók tartalmazzák a hiba (, , és ) állapotát, a teljes elérési utat és a `UPLOADFAILED` `COPYFAILED` hiba `DOWNLOADFAILED` okát.

Alapértelmezés szerint a napló- és tervfájlok a Windows vagy Mac és Linux könyvtárában találhatók, de ez `%USERPROFILE%\.azcopy` `$HOME$\.azcopy` a hely megváltoztatható. 

A releváns hiba nem feltétlenül az első hiba, amely megjelenik a fájlban. Hálózati hibák, időtúllépések és foglalt kiszolgálóhibák esetén az AzCopy legfeljebb 20 alkalommal próbálkozik újra, és az újrapróbálkozási folyamat általában sikeres.  Az első észlelt hiba ártalmatlan lehet, és a rendszer sikeresen újraküldte az újra.  Ezért ahelyett, hogy a fájl első hibakódját nézi, keresse meg a , vagy közelében lévő `UPLOADFAILED` `COPYFAILED` `DOWNLOADFAILED` hibákat. 

> [!IMPORTANT]
> Amikor kérelmet nyújt be a Microsoft ügyfélszolgálata számára (vagy egy harmadik féltől származó probléma elhárítása érdekében), ossza meg a végrehajtani kívánt parancs kiíratott verzióját. Ez biztosítja, hogy az SAS-t ne osztják meg véletlenül senkivel. A kiíratott verziót a naplófájl elején találja.

## <a name="review-the-logs-for-errors"></a>Tekintse át a naplókat, hogy vannak-e hibák

A következő parancs az állapottal kapcsolatos összes hibát `UPLOADFAILED` le fogja kapni a `04dc9ca9-158f-7945-5933-564021086c79` naplóból:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>Feladatok megtekintése és folytatása

Minden átviteli művelet létrehoz egy AzCopy-feladatot. A következő paranccsal megtekintheti a feladatok előzményeit:

```
azcopy jobs list
```

A feladatstatisztikák megtekintéséhez használja a következő parancsot:

```
azcopy jobs show <job-id>
```

Az átvitelek állapot alapján történő szűréséhez használja a következő parancsot:

```
azcopy jobs show <job-id> --with-status=Failed
```

A következő paranccsal folytathatja a meghiúsult/megszakított feladatot. Ez a parancs az azonosítóját és az SAS-jogkivonatot használja, mivel az biztonsági okokból nem állandó:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Az elérésiút-argumentumokat, például az SAS-jogkivonatot zárja egyszeres idézőjelek közé (''). Használjon egyszeres idézőjeleket az összes parancshéjban, kivéve a Windows parancshéjat (cmd.exe). Ha Windows parancshéjat (cmd.exe) használ, az elérésiút-argumentumokat idézőjelek ("") közé kell tenni a single quotes ('' helyett).

A feladat folytatásakor az AzCopy a feladatterv fájlját nézi. A tervfájl felsorolja a feladat első létrehozásakor feldolgozásra azonosított összes fájlt. A feladat folytatásakor az AzCopy megkísérli a csomagfájlban felsorolt összes olyan fájl átvitelét, amely még nem lett átadva.

## <a name="change-the-location-of-plan-files"></a>Csomagfájlok helyének módosítása

Használja az alábbi parancsok bármelyikét.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | Powershell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> A parancssorban használja a következőt: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

A változó aktuális értékének ellenőrzéshez használja `azcopy env` a értéket. Ha az érték üres, a rendszer az alapértelmezett helyre írja a tervfájlokat.

## <a name="change-the-location-of-log-files"></a>Naplófájlok helyének módosítása

Használja az alábbi parancsok bármelyikét.

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | Powershell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> A parancssorban használja a következőt: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

A változó `azcopy env` aktuális értékének ellenőrzéshez használja a értéket. Ha az érték üres, a naplók az alapértelmezett helyre vannak írva.

## <a name="change-the-default-log-level"></a>Az alapértelmezett naplózási szint módosítása

Alapértelmezés szerint az AzCopy naplózási szintje a következőre van beállítva: `INFO` . Ha csökkenteni szeretné a napló részletességét a lemezterület megtakarítása érdekében, írja felül ezt a beállítást az ``--log-level`` kapcsolóval. 

A rendelkezésre álló naplószintek a következőek: `NONE` , , , , , és `DEBUG` `INFO` `WARNING` `ERROR` `PANIC` `FATAL` .

## <a name="remove-plan-and-log-files"></a>Terv- és naplófájlok eltávolítása

Ha el szeretné távolítani az összes csomag- és naplófájlt a helyi gépről, hogy helyet takarítson meg, használja az `azcopy jobs clean` parancsot.

Ha csak egy feladathoz társított tervet és naplófájlokat szeretne eltávolítani, használja a `azcopy jobs rm <job-id>` következőt: . Cserélje le `<job-id>` a példában lévő helyőrzőt a feladat feladatazonosítójára.

## <a name="see-also"></a>Lásd még

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
