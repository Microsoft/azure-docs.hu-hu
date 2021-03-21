---
title: Azure import/export-feladatok állapotának megtekintése | Microsoft Docs
description: Megtudhatja, hogyan tekintheti meg az Azure import/export-feladatok és a használt meghajtók állapotát. Ismerje meg azokat a tényezőket, amelyek befolyásolják, hogy mennyi ideig tart a feladatok feldolgozása.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.custom: contperf-fy21q3
ms.openlocfilehash: 8ef18ea663f3a77589d61ed89c50df38f5cf0d0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176147"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Az Azure Import/Export-feladatok állapotának megtekintése

Ez a cikk azt ismerteti, hogyan lehet megtekinteni a meghajtó és a feladat állapotát az Azure import/export feladatok számára. Az Azure import/export szolgáltatás használatával nagy mennyiségű adatok biztonságosan vihetők át az Azure-Blobokra és a Azure Filesra. A szolgáltatás az Azure Blob Storage-ból származó adatok exportálására is használható.  

## <a name="view-job-and-drive-status"></a>Feladatok és meghajtók állapotának megtekintése
Az importálási vagy exportálási feladatok állapotát a Azure Portal **Importálás/exportálás** lapján követheti nyomon.
1. Jelentkezzen be a következőre: https://portal.azure.com/ .
2. **Importálási/exportálási feladatok** keresése.

    ![Importálási/exportálási feladatok keresése](./media/storage-import-export-view-drive-status/open-import-export-tab.png)

 3. Az importálási/exportálási feladatok listája megjelenik az oldalon.

    ![Feladatok állapotának megtekintése](./media/storage-import-export-view-drive-status/job-state.png)

4. Válassza ki, majd kattintson a feladatra a feladatok részleteinek megtekintéséhez.

   ![Részletes feladatok állapotának megtekintése](./media/storage-import-export-view-drive-status/job-detail.png)
  
## <a name="view-job-status"></a>Feladat állapotának megtekintése

A következő feladatok egyike jelenik meg attól függően, hogy a meghajtó hol található a folyamatban.

| Feladatok állapota | Description |
|:--- |:--- |
| Létrehozás | A feladatok létrehozása után az állapota a **Létrehozás** értékre van állítva. Amíg a feladatsor **létrehozza** az állapotot, az importálási/exportálási szolgáltatás feltételezi, hogy nem szállították el az adatközpontba a meghajtókat. A feladatok akár két hétig is maradhatnak ebben az állapotban, ami után a szolgáltatás automatikusan törli azt. |
| Szállítás | A csomag szállítása után frissítenie kell a követési információkat a Azure Portal.  Így a feladatot a **szállítási** állapotba kapcsolhatja. A feladatok legfeljebb két hétig maradnak a **szállítási** állapotban. 
| Megérkezett | Miután az összes meghajtó beérkezett az adatközpontba, a feladatsor értéke **fogadott**. |
| Átvitel folyamatban | Ha legalább egy meghajtó megkezdte a feldolgozást, a feladattípus az **átvitelre** van beállítva. További információért lépjen a [meghajtó állapota](#view-drive-status)elemre. |
| Csomagolás | Miután az összes meghajtó befejezte a feldolgozást, a rendszer **csomagolási** állapotba helyezi a feladatot, amíg a meghajtókat vissza nem szállítja. |
| Befejeződött | Miután az összes meghajtót visszaküldi a rendszer, ha a feladatot hibák nélkül végezte el, a rendszer **Befejezettre** állítja a feladatot. A feladatot a rendszer a **befejezett** állapot 90 napja után automatikusan törli. |
| Lezárt | Miután az összes meghajtót visszaküldi a rendszer, ha a feladatok feldolgozásakor hiba történt, a rendszer **Lezártra** állítja a feladatot. A feladatot a rendszer a 90 nap után automatikusan törli a **lezárt** állapotban. |

## <a name="view-drive-status"></a>A meghajtók állapotának megtekintése

Az alábbi táblázat az egyes meghajtók életciklusát mutatja be az importálási vagy exportálási feladatokhoz való áttérés során. A feladatok egyes meghajtóinak aktuális állapota a Azure Portal látható.

A következő táblázat ismerteti az egyes állapotokat, amelyeket a feladatok egyes meghajtói továbbítanak.

| Meghajtó állapota | Description |
|:--- |:--- |
| Megadott | Importálási feladatokhoz, ha a feladatot a Azure Portal hozza létre, a meghajtó kezdeti állapota meg van **adva**. Exportálási feladatokhoz, mivel a rendszer nem ad meg meghajtót a feladatok létrehozásakor, a rendszer a kezdeti meghajtó állapotát **fogadja**. |
| Megérkezett | A meghajtó átvált a **fogadott** állapotba, amikor az importálási/exportálási szolgáltatás feldolgozta az importálási feladatokhoz a hajózási vállalattól érkezett meghajtókat. Exportálási feladatok esetén a kezdeti meghajtó állapota a **fogadott** állapot. |
| NeverReceived | A meghajtó a **NeverReceived** állapotba kerül, amikor a csomag egy adott feladatokhoz érkezik, de a csomag nem tartalmazza a meghajtót. A meghajtó akkor is ebbe az állapotba kerül, ha az adatközpont még nem kapta meg a csomagot, és a szolgáltatás legalább két héttel ezelőtt kapta meg a szállítási adatokat. |
| Átvitel folyamatban | A meghajtó áthelyezi az **átadási** állapotba, amikor a szolgáltatás megkezdi az adatok átvitelét a meghajtóról az Azure Storage-ba. |
| Befejeződött | A meghajtó a **befejezett** állapotba kerül, ha a szolgáltatás sikeresen áthelyezte az összes olyan hibát, amely hibák nélkül történt.
| CompletedMoreInfo | A meghajtó a **CompletedMoreInfo** állapotba kerül, amikor a szolgáltatás problémát észlelt, miközben a vagy a meghajtóról másolta az adatokból. Az információ tartalmazhat hibákat, figyelmeztetéseket és tájékoztató üzeneteket a Blobok felülírásával kapcsolatban.
| ShippedBack | A meghajtó a **ShippedBack** állapotba kerül, amikor az adatközpontból a visszaküldési címnek visszaszállították. |

Ez a rendszerkép a Azure Portal egy példaként szolgáló feladatokhoz tartozó meghajtó állapotát jeleníti meg:

![Meghajtó állapotának megtekintése](./media/storage-import-export-view-drive-status/drive-state.png)

Az alábbi táblázat a meghajtó meghibásodási állapotait és az egyes állapotokhoz végrehajtott műveleteket ismerteti.

| Meghajtó állapota | Esemény | Megoldás/következő lépés |
|:--- |:--- |:--- |
| Soha nem érkezett meg | Egy **NeverReceived** jelölésű meghajtó (mivel a feladatok szállításának részeként nem érkezett meg) egy másik szállítmányban érkezik. | Az operatív csapat áthelyezi a meghajtót a **fogadásba**. |
| N/A | Egy olyan meghajtó, amely nem része a feladatoknak, egy másik feladattípus részeként az adatközpontba kerül. | A meghajtó külön meghajtóként van megjelölve. Az eredeti csomaghoz társított feladatot a rendszer visszaküldi Önnek. |

## <a name="time-to-process-job"></a>A feladatok feldolgozásának ideje
Az importálási/exportálási feladatok feldolgozásához szükséges idő számos tényezőtől függ, például:

-  Szállítási idő
-  Betöltés az adatközpontban
-  A másolandó adatok feladattípusa és mérete
-  Egy adott feladatokban található lemezek száma. 

Az import/export szolgáltatás nem rendelkezik SLA-val, de a szolgáltatás arra törekszik, hogy a másolást 7 – 10 nappal a lemezek fogadása után végezze el. A Azure Portal közzétett állapot mellett a REST API-k segítségével nyomon követheti a feladatok előrehaladását. A százalékos másolási folyamat megtekintéséhez használja a [feladatok megjelenítése](/previous-versions/azure/dn529083(v=azure.100)) művelet API-hívásának százalékos készültségi paraméterét.


## <a name="next-steps"></a>Következő lépések

* [Adatok átvitele a AzCopy parancssori segédprogrammal](../storage/common/storage-use-azcopy-v10.md)
* [Azure importálási REST API minta](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)