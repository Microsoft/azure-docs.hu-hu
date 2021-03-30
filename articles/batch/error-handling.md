---
title: Hibakezelés és észlelés a Azure Batchban
description: Ismerje meg, hogyan kezelheti a Batch szolgáltatás munkafolyamatait fejlesztési szempontból.
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 3bd460598dae08fa18415e1c9865249f3ca4c9c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85964277"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Hibakezelés és észlelés a Azure Batchban

Időnként előfordulhat, hogy a Batch-megoldáson belül a feladatok és az alkalmazások hibáinak kezelésére is szükség van. Ez a cikk a hibák típusairól és azok megoldásáról beszél.

## <a name="error-codes"></a>Hibakódok

A hibák általános típusai a következők:

- Hálózati hibák a köteget soha nem elérő kérések esetén, vagy ha a Batch-válasz nem érte el időben az ügyfelet.
- Belső kiszolgálói hibák (standard 5xx HTTP-válasz).
- A szabályozással kapcsolatos hibák, például az 429-es vagy a 503-es állapotkód HTTP-válaszok az újrapróbálkozási fejléctel.
- 4xx hibák, például a már létezik és a InvalidOperation. Ez azt jelenti, hogy az erőforrás nincs megfelelő állapotban az állapot átváltásához.

Az adott hibakódokkal kapcsolatos részletes információkat, beleértve a REST API, a Batch szolgáltatás és a feladat/ütemezés hibakódját: [Batch-állapot és hibakódok](/rest/api/batchservice/batch-status-and-error-codes).

## <a name="application-failures"></a>Alkalmazáshibák

A futtatás során az alkalmazások diagnosztikai kimenetet készíthetnek, amely felhasználható a hibák elhárításához. A [fájlok és könyvtárak](files-and-directories.md)című témakörben leírtak szerint a Batch szolgáltatás szabványos kimenetet és standard hibaüzeneteket ír a `stdout.txt` `stderr.txt` számítási csomóponton található feladatok mappájába.

A fájlokat az Azure Portal webhelyről vagy valamelyik Batch SDK útján lehet letölteni. Ezeket, illetve más hibaelhárítási célú fájlokat letöltheti például a [ComputeNode.GetNodeFile](/dotnet/api/microsoft.azure.batch.computenode) és a [CloudTask.GetNodeFile](/dotnet/api/microsoft.azure.batch.cloudtask) metódussal, amely a Batch .NET-kódtárában érhető el.

## <a name="task-errors"></a>Feladat hibái

A feladat hibái több kategóriába esnek.

### <a name="pre-processing-errors"></a>Előfeldolgozási hibák

Ha egy tevékenység nem indul el, a tevékenységhez egy előfeldolgozási hiba lesz beállítva.  

Az Előfeldolgozási hibák akkor fordulhatnak elő, ha a tevékenység erőforrás-fájljai át lettek helyezve, a Storage-fiók már nem érhető el, vagy egy másik probléma merült fel, amely megakadályozta a fájlok sikeres másolását a csomópontra.

### <a name="file-upload-errors"></a>Fájlfeltöltés-hibák

Ha egy tevékenységhez megadott fájlok bármilyen okból nem tölthetők fel, a feladathoz egy fájlfeltöltés-hiba van beállítva.

Fájlfeltöltési hiba akkor fordulhat elő, ha az Azure Storage eléréséhez megadott közös hozzáférésű jogosultságkód (SAS) érvénytelen vagy nem biztosít írási jogosultságot, ha a tárfiók már nem érhető el, vagy egyéb probléma miatt nem sikerült a fájlok másolása a csomópontról.

### <a name="application-errors"></a>Alkalmazáshibák

A tevékenység parancssora által meghatározott folyamat is meghiúsulhat. A folyamat sikertelennek minősül, amikor nullától eltérő kilépési kódot ad vissza a tevékenység által végrehajtott folyamat (lásd az alábbi, *Tevékenységekhez tartozó kilépési kódok* című fejezetet).

Alkalmazáshiba esetén a Batch beállítható úgy, hogy a megadott számú alkalommal automatikusan újrapróbálkozzon a feladattal.

### <a name="constraint-errors"></a>Megkötési hibák

Olyan korlátozás is beállítható, amely meghatározza a feladat vagy a tevékenység maximális végrehajtási időtartamát. Ez a *maxWallClockTime* érték. Ez olyan feladatok leállításához lehet hasznos, amelyek nem haladnak.

Ha eltelik a maximális időtartam, a tevékenység *befejezve* állapotot kap, de a kilépési kódhoz a `0xC000013A` értéket, a *schedulingError* mezőhöz pedig a `{ category:"ServerError", code="TaskEnded"}` értéket társítja a rendszer.

## <a name="task-exit-codes"></a>Tevékenységek kilépési kódjai

Ahogy azt korábban említettük, a Batch szolgáltatás sikertelenként értékeli a tevékenységet, ha a tevékenység által végrehajtott folyamat kilépési kódja nullától eltérő. Amikor a tevékenységek folyamatokat hajtanak végre, a Batch a folyamat visszatérési kódjával tölti ki a tevékenység kilépési kód tulajdonságát.

Fontos tudni, hogy a tevékenységek kilépési kódját nem a Batch szolgáltatás határozza meg. Egy tevékenység kilépési kódját maga a folyamat határozza meg, vagy az operációs rendszer, amelyen a folyamatot végrehajtották.

## <a name="task-failures-or-interruptions"></a>Feladat hibái vagy megszakításai

A tevékenységek időnként meghiúsulhatnak vagy megszakadhatnak. Maga a feladat sikertelen lehet, mert a csomópont, amelyen a feladat fut, újraindulhat, vagy előfordulhat, hogy a csomópontot a rendszer az átméretezési művelet során eltávolítja a készletből (ha a készlet felszabadítási házirendje a csomópontok azonnali eltávolítására van beállítva, a feladatok befejezésére való várakozás nélkül). A tevékenységet a Batch minden esetben képes automatikusan újra a várólistára helyezni, hogy egy másik csomópont hajtsa végre.

Az is előfordulhat, hogy egy időszakos hiba miatt a feladat nem válaszol, vagy túl sokáig tart a végrehajtás. Beállíthatja a tevékenységekhez engedélyezett maximális végrehajtási intervallumot is. Ha átlépi a maximális végrehajtási intervallumot, a Batch szolgáltatás megszakítja a tevékenységalkalmazást.

## <a name="connect-to-compute-nodes"></a>Kapcsolódás számítási csomópontokhoz

Ha távolról bejelentkezik a számítási csomópontra, további hibakeresési lehetőségeket használhat. Az Azure Portal webhely segítségével letöltheti az RDP-fájlt a windowsos csomópontokra, vagy beszerezheti az SSH-kapcsolathoz szükséges információkat a linuxos csomópontokhoz. Ezt a Batch API-k, például a [Batch .net](/dotnet/api/microsoft.azure.batch.computenode) vagy a [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh)használatával is elvégezheti.

> [!IMPORTANT]
> Ha RDP-n vagy SSH-n keresztül szeretne csatlakozni a csomóponthoz, először létre kell hoznia egy felhasználót a csomóponton. Ehhez használja az Azure Portal webhelyet: [adjon hozzá egy felhasználót a csomóponthoz](/rest/api/batchservice/computenode/adduser) a Batch REST API segítségével, és hívja meg a [ComputeNode.CreateComputeNodeUser](/dotnet/api/microsoft.azure.batch.computenode) metódust a Batch .NET-ben, vagy az [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) metódust a Batch Python modulban.

Ha korlátoznia kell vagy le kell tiltania a számítási csomópontok RDP- vagy SSH-hozzáférését, tekintse át az [Azure Batch-készletbe tartozó számítási csomópontok távelérésének konfigurálásával vagy letiltásával](pool-endpoint-configuration.md) foglalkozó cikket.

## <a name="troubleshoot-problem-nodes"></a>Probléma csomópontjainak elhárítása

Olyan esetekben, ahol néhány tevékenység meghiúsul, a Batch ügyfélalkalmazás vagy szolgáltatás megvizsgálhatja a meghiúsult tevékenységek metaadatait a rosszul működő csomópontok azonosítása érdekében. A készletek minden csomópontja egyedi azonosítót kap, és a tevékenységet futtató csomópont szerepel a tevékenység metaadataiban. Ha sikerült azonosítani a problematikus csomópontot, számos különböző műveletet elvégezhet vele:

- **A csomópont újraindítása** ([Rest](/rest/api/batchservice/computenode/reboot)  |  [.net](/dotnet/api/microsoft.azure.batch.computenode.reboot)))

    A csomópont újraindítása néha segít a rejtett problémák, például az elakadt vagy összeomlott folyamatok megoldásában. Ha a készlet indítási tevékenységet használ, vagy a feladat feladat-előkészítési feladatot használ, azokat a rendszer a csomópont újraindításakor hajtja végre.
- **Alaphelyzetbe állíthatja a csomópont rendszerképét** ([REST](/rest/api/batchservice/computenode/reimage) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reimage))

    Ez újratelepíti az operációs rendszert a csomóponton. A csomópontok újraindításához hasonlóan újrafuttatja az indítási tevékenységeket és a feladat-előkészítési tevékenységeket a csomópont rendszerképének alaphelyzetbe állítása után.
- **Eltávolíthatja a csomópontot a készletből** ([REST](/rest/api/batchservice/pool/removenodes) | [.NET](/dotnet/api/microsoft.azure.batch.pooloperations))

    Néha teljesen el kell távolítani a csomópontot a készletből.
- **Letilthatja a tevékenységütemezést a csomóponton** ([REST](/rest/api/batchservice/computenode/disablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    Ez tulajdonképpen offline állapotba helyezi a csomópontot, így ahhoz nem rendel további tevékenységeket, de engedélyezi, hogy a csomópont továbbra is fusson a készletben. Ez lehetővé teszi, hogy tovább vizsgálja a hibák okait a meghiúsult tevékenység adatainak elvesztése nélkül, és anélkül, hogy a csomópont további tevékenységhibákat okozna. Letilthatja például a tevékenységütemezést a csomóponton, majd távolról bejelentkezhet a csomópont eseménynaplóinak megvizsgálása vagy egyéb hibaelhárítás elvégzése érdekében. A vizsgálat befejezése után visszaállíthatja a csomópontot online állapotba a feladatütemezés engedélyezésével ([Rest](/rest/api/batchservice/computenode/enablescheduling)  |  [.net](/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)vagy a korábban tárgyalt egyéb műveletek valamelyikének elvégzésével).

> [!IMPORTANT]
> A fent ismertetett műveletekkel a youc megadhatja, hogy a rendszer hogyan kezelje a csomóponton jelenleg futó feladatokat a művelet végrehajtásakor. Amikor például a Batch .NET ügyfélkódtára segítségével letiltja a tevékenységütemezést egy csomóponton, megadhat egy [DisableComputeNodeSchedulingOption](/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption) enumerálási értéket, amellyel megszabhatja, hogy mit szeretne tenni: **leállítani** a futó tevékenységeket, **újból várólistára helyezni** azokat más csomópontokon való ütemezéshez, vagy engedélyezni a futó tevékenységek befejezését a művelet elvégzése előtt (**TaskCompletion**).

## <a name="retry-after-errors"></a>Újrapróbálkozás a hibák után

Ha hiba lép fel, a Batch API-k értesítik. Mindegyiket újra lehet próbálni, és mindegyikhez tartozik egy globális újrapróbálkozási kezelő erre a célra. A legjobb megoldás a beépített mechanizmus használata.

Hiba után várjon egy kicsit (több másodpercet az újrapróbálkozások között) az újrapróbálkozás előtt. Ha túl gyakran vagy túl gyorsan próbálkozik újra, a rendszer az újrapróbálkozási kezelőt fogja szabályozni.

## <a name="next-steps"></a>Következő lépések

- Útmutató a [készlet-és csomópont-hibák kereséséhez](batch-pool-node-error-checking.md).
- Útmutató a [feladatok és a feladatok hibáinak kereséséhez](batch-job-task-error-checking.md).
- Tekintse át a [Batch-állapot és a hibakódok](/rest/api/batchservice/batch-status-and-error-codes)listáját.
