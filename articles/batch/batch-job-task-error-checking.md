---
title: Hibakeresés feladatokban és tevékenységekben
description: A feladatok és a feladatok hibakereséséhez és hibaelhárításához szükséges hibák megismerése.
author: mscurrell
ms.topic: how-to
ms.date: 11/23/2020
ms.author: markscu
ms.openlocfilehash: d8cf3b5e28d4455e00e0bdcbae2063771d3e8acd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95736799"
---
# <a name="job-and-task-error-checking"></a>Feladat-és feladattal kapcsolatos hibák ellenőrzése

A feladatok és a feladatok hozzáadásakor különböző hibák merülhetnek fel. A műveletekkel kapcsolatos hibák észlelése egyszerű, mert az API, a CLI vagy a felhasználói felület által azonnal visszaadott hibákat. Vannak azonban olyan hibák is, amelyek később megtörténnek, amikor a feladatok és a feladatok ütemezve vannak és futnak.

Ez a cikk azokat a hibákat ismerteti, amelyek a feladatok és a tevékenységek elküldése után, valamint a feladatok vizsgálatának és kezelésének elvégzése után fordulnak elő.

## <a name="jobs"></a>Feladatok

A feladat egy vagy több feladat csoportosítása, amelyek ténylegesen meghatározzák a futtatandó parancssorokat.

Feladat hozzáadásakor a következő paraméterek adhatók meg, amelyek befolyásolhatják a feladat sikertelenségét:

- [Feladatok megkötései](/rest/api/batchservice/job/add#jobconstraints)
  - A tulajdonság beállítható úgy `maxWallClockTime` , hogy beállítsa azt a maximális időtartamot, ameddig a feladatok aktívak vagy futtathatók. Ha túllépi a műveletet, a rendszer megszakítja a feladatot a `terminateReason` feladatokhoz tartozó [executionInfo](/rest/api/batchservice/job/get#cloudjob) beállított tulajdonsággal.
- [Feladat-előkészítési feladat](/rest/api/batchservice/job/add#jobpreparationtask)
  - Ha meg van adva, a feladat-előkészítési feladat az első alkalommal fut, amikor egy feladatot futtatnak egy adott csomóponton. A feladat-előkészítési feladat meghiúsulhat, ami a nem futtatott feladat és a feladat befejezését eredményezi.
- [Feladat kiadási feladata](/rest/api/batchservice/job/add#jobreleasetask)
  - Feladat-felszabadítási feladat csak akkor adható meg, ha a feladat-előkészítési feladat konfigurálva van. Ha egy feladat leáll, a feladat kiadására szolgáló feladat minden olyan készlet-csomóponton fut, amelyen a feladat-előkészítési feladat futott. A feladat kiadási feladatai sikertelenek lehetnek, de a feladat továbbra is `completed` állapotba kerül.

### <a name="job-properties"></a>Feladatok tulajdonságai

A következő feladatok tulajdonságainak ellenőrzése szükséges:

- "[executionInfo](/rest/api/batchservice/job/get#jobexecutioninformation)":
  - A `terminateReason` tulajdonság tartalmazhat olyan értékeket, amelyek jelzik `maxWallClockTime` , hogy a, a feladatokra vonatkozó megkötésekben megadott érték túl lett lépve, ezért a rendszer megszakította a feladatot. Azt is beállíthatja, hogy egy feladat nem sikerült, ha a feladat `onTaskFailure` tulajdonság beállítása megfelelő volt.
  - A [schedulingError](/rest/api/batchservice/job/get#jobschedulingerror) tulajdonság akkor van beállítva, ha ütemezési hiba történt.

### <a name="job-preparation-tasks"></a>Feladat-előkészítési feladatok

Ha egy feladathoz egy feladat-előkészítési feladat van megadva, akkor az adott feladat egy példánya lesz futtatva, amikor a feladathoz tartozó feladat először fut egy csomóponton. A feladathoz konfigurált feladat-előkészítési feladat sablonként is megtekinthető, és több feladat-előkészítési feladattal rendelkező példány fut a készletben lévő csomópontok számának megfelelően.

A feladat-előkészítési feladathoz tartozó példányokat ellenőrizni kell, hogy vannak-e hibák:

- Feladat-előkészítési feladat futtatásakor a feladat-előkészítési feladatot kiváltó feladat [állapota](/rest/api/batchservice/task/get#taskstate) a következő lesz:. `preparing` Ha a feladat-előkészítési feladat meghiúsul, az indító feladat visszaállítja az `active` állapotot, és nem fog futni.
- A feladat-előkészítési feladat összes példánya beszerezhető a feladatból a [lista előkészítése és a kiadási feladat állapota](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) API használatával. A feladatokhoz hasonlóan olyan [végrehajtási információk](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) is rendelkezésre állnak, mint a `failureInfo` , a `exitCode` és a `result` .
- Ha a feladat-előkészítési feladatok meghiúsulnak, akkor az aktiválási feladatok nem fognak futni, a feladat nem fejeződik be, és a rendszer elakad. Előfordulhat, hogy a készlet nem használható fel, ha nincsenek más feladatok ütemezhető feladatokkal.

### <a name="job-release-tasks"></a>Feladat kiadásával kapcsolatos feladatok

Ha egy feladat kiadási feladata meg van adva egy feladathoz, akkor a feladat leállításakor a feladat-előkészítési feladat egy példánya fut minden olyan készlet-csomóponton, amelyen a feladat-előkészítési feladat futott. A feladat kiadási feladatának példányait ellenőrizni kell, hogy vannak-e hibák:

- A feladat-felszabadítási feladat futtatásának összes példánya a feladatból az API- [lista előkészítése és a kiadási feladat állapota](/rest/api/batchservice/job/listpreparationandreleasetaskstatus)alapján szerezhető be. A feladatokhoz hasonlóan olyan [végrehajtási információk](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) is rendelkezésre állnak, mint a `failureInfo` , a `exitCode` és a `result` .
- Ha egy vagy több feladat-kiadási feladat meghiúsul, akkor a feladat továbbra is leáll, és a rendszer egy állapotba helyezi át `completed` .

## <a name="tasks"></a>Feladatok

A feladat feladatai több okból is meghiúsulnak:

- A feladat parancssora meghiúsul, és nem nulla kilépési kóddal tér vissza.
- `resourceFiles`Egy feladathoz van megadva, de hiba történt, mert egy vagy több fájl nem lett letöltve.
- `outputFiles`Egy feladathoz van megadva, de hiba történt, mert egy vagy több fájlt nem töltöttek fel.
- A feladat `maxWallClockTime` [megkötések](/rest/api/batchservice/task/add#taskconstraints)tulajdonságában megadott eltelt idő túllépve.

Minden esetben a következő tulajdonságokat kell ellenőrizni a hibákkal és a hibákkal kapcsolatos információkkal:

- A Tasks [executionInfo](/rest/api/batchservice/task/get#taskexecutioninformation) tulajdonság több olyan tulajdonságot tartalmaz, amelyek információt biztosítanak a hibáról. az [eredmény](/rest/api/batchservice/task/get#taskexecutionresult) azt jelzi, hogy a feladat bármilyen okból meghiúsult-e, `exitCode` és `failureInfo` További információt biztosít a hibáról.
- A feladat mindig az `completed` [állapotba](/rest/api/batchservice/task/get#taskstate)kerül, függetlenül attól, hogy sikeres vagy sikertelen volt-e.

Figyelembe kell venni a feladat meghibásodásának hatását a feladatra és a feladatok függőségeire. A [exitConditions](/rest/api/batchservice/task/add#exitconditions) tulajdonság megadható egy feladathoz, amely egy műveletet konfigurál a függőségekhez és a feladathoz.

- A függőségek esetében a [DependencyAction](/rest/api/batchservice/task/add#dependencyaction) szabályozza, hogy a sikertelen tevékenységtől függő feladatok blokkolva vannak-e vagy futnak-e.
- A [JobAction](/rest/api/batchservice/task/add#jobaction) azt szabályozza, hogy a sikertelen feladat a letiltott, leállított vagy nem módosult feladatot eredményezi-e.

### <a name="task-command-line-failures"></a>Feladat parancssori hibái

A Feladat parancssorának futtatásakor a kimenet a következőre íródik: `stderr.txt` és `stdout.txt` . Az alkalmazás Emellett írhat az alkalmazásspecifikus naplófájlokra is.

Ha a készlet olyan csomópontja, amelyen még fut egy feladat, a naplófájlok beszerezhetők és megtekinthetők. Például a Azure Portal listázza és megtekintheti a feladatok vagy a készlet-csomópontok naplófájljait. Több API is lehetővé teszi, hogy a feladatok fájljai megjelenjenek és beszerezhetők legyenek, például [a beolvasás feladatból](/rest/api/batchservice/file/getfromtask).

Mivel a készletek és a készlet csomópontjai gyakran elmúlóak, a csomópontok folyamatos hozzáadásával és törlésével a naplófájlok mentését javasoljuk. A [feladat kimeneti fájljai](./batch-task-output-files.md) kényelmes módon menthetők a naplófájlok az Azure Storage-ba.

A számítási csomópontokon végzett feladatok által végrehajtott parancssorok nem egy rendszerhéj alatt futnak, így nem tudják natív módon kihasználni a rendszerhéj funkcióit, például a környezeti változók kiterjesztését. Az ilyen funkciók kihasználásához a [rendszerhéjt a parancssorban kell meghívni](batch-compute-node-environment-variables.md#command-line-expansion-of-environment-variables).

### <a name="output-file-failures"></a>Kimeneti fájlok hibái

Minden fájl feltöltésekor a Batch két naplófájlt ír a számítási csomópontba, `fileuploadout.txt` és `fileuploaderr.txt` . Megvizsgálhatja ezeket a naplófájlokat, ha többet szeretne megtudni egy adott hibáról. Azokban az esetekben, amikor a fájlfeltöltés soha nem történt meg, például azért, mert maga a feladat nem tudott futni, akkor ezek a naplófájlok nem fognak létezni.  

## <a name="next-steps"></a>Következő lépések

- Győződjön meg arról, hogy az alkalmazás átfogó hibaellenőrzés-ellenőrzést végez. kritikus fontosságú lehet a problémák azonnali észlelése és diagnosztizálása.
- További információ a feladatokról [és a feladatokról](jobs-and-tasks.md).