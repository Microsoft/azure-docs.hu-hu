---
title: Blender-jelenet renderelése az Azure Batch és a Batch Explorer használatával
description: Oktatóanyag – Több képkocka renderelése egy Blender-jelenetből az Azure Batch és a Batch Explorer ügyfélalkalmazással
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ms.openlocfilehash: 8a512676ab0e56f51c0fb9c59f2e530cfcf73333
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791426"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>Oktatóanyag: A Blender használata Batch Explorer jelenet renderelése

Ez az oktatóanyag bemutatja, hogyan renderelheti egy Blender-bemutatójelenet több képkockáját. Az oktatóanyaghoz a Blendert használjuk, mert a használata ingyenes az ügyfél és a renderelő virtuális gépek számára, de a folyamat más alkalmazások, például a Maya vagy a 3ds Max esetén is nagyon hasonló.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Blender-jelenet feltöltése egy Azure-tárolóba
> * Batch-készlet létrehozása több csomóponttal a renderelés elvégzéséhez
> * Több képkocka renderelése
> * A renderelt képkockafájlok megtekintése és letöltése

## <a name="prerequisites"></a>Előfeltételek

Ha használatalapú fizetést szeretne alkalmazni a Batch renderelő alkalmazásaira, használatalapú előfizetésre vagy egy egyéb Azure vásárlási opcióra van szüksége. A használatalapú fizetés licencelése nem támogatott, ha olyan ingyenes Azure-ajánlatot használ, amely kreditfeltöltést kínál.

Szüksége van egy Azure Batch-fiókra, társított tárfiókkal.  Batch-fiók létrehozásához tekintse meg bármelyik Batch rövid útmutatót, például a [parancssori felülettel kapcsolatos cikket](https://docs.microsoft.com/azure/batch/quick-create-cli).

Legalább 50 magos alacsony prioritású magkvótára van szükség a virtuális gép méretéhez és a virtuális gépek ebben az oktatóanyagban meghatározott számához; az alapértelmezett kvóta is használható, de ekkor kisebb méretű virtuális gépet kell használni, ami azt jelenti, hogy a képek renderelése hosszabb ideig tart. Ha nagyobb magkvótát szeretne kérni, [ezt a cikket](https://docs.microsoft.com/azure/batch/batch-quota-limit) tekintse át.

Telepítve kell lennie a [Batch Explorernek](https://azure.github.io/BatchExplorer/), amely Windows, OSX és Linux rendszerekhez érhető el. Nem kötelező, de ha a [Blender](https://www.blender.org/download/) telepítve van, akkor megtekinthető a mintául szolgáló modellfájl.

## <a name="download-the-demo-scene"></a>A bemutatójelenet letöltése

Töltse le a Blender „Class room” (Osztályterem) ZIP-fájlját a [Blender bemutatófájlok weboldaláról](https://www.blender.org/download/demo-files/), és bontsa ki egy helyi meghajtón.

## <a name="upload-a-scene-to-azure-storage"></a>jelenet feltöltése az Azure-tárolóba;

Hozzon létre egy tárfióktárolót a bemutatójelenet fájljaihoz:

* Indítsa el a Batch Explorert.
* A bal oldali főmenüben válassza a „Data” (Adatok) menüpontot.
* Ellenőrizze, hogy a „File groups” (Fájlcsoportok) elem van-e kiválasztva a legördülő menüben.
* Válassza a „+” gombot, és hozza létre a „blender-classroom” nevű új „üres fájlcsoportot”.
  * A fájlcsoportok egyszerűen egy „fgrp-” előtaggal rendelkező Azure Storage-blobtárolók; ez a tárfiókban lévő többi tároló kiszűrésére szolgáló elnevezés.

![Fájlcsoport jelenetfájlokhoz](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

Töltse fel a jelenetfájlokat:

* Válassza ki az új tárolót, és húzza a „classroom” mappa tartalmát a tárolóra a Batch Explorerben.

![Feltöltött jelenetfájlok](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>Azure Storage-tároló létrehozása kimeneti képekhez

Hozzon létre egy tárfióktárolót a bemutatójelenet kimeneti fájljaihoz:

* A bal oldali főmenüben válassza a „Data” (Adatok) menüpontot.
* Válassza a „+” gombot, és hozza létre a „render-output” nevű új „üres fájlcsoportot”.

![Fájlcsoport kimeneti fájlokhoz](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>Virtuális gépek készletének létrehozása rendereléshez

Hozzon létre egy Batch-készletet a Blender alkalmazást tartalmazó Azure Marketplace virtuálisgép-rendszerkép renderelésével:

* A bal oldali főmenüben válassza a „Gallery” (Katalógus) menüpontot.
* Válassza az alkalmazáselemek listájából a „Blender” elemet.
* Válassza ki a képkockák Windows Serveren történő renderelését beállító elemet.
* Az elem jobb oldalán lévő hivatkozás ikon is kiválasztható a készlet és feladat létrehozásához használandó sablonfájlok megtekintéséhez.

![Blender katalóguselemek](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* Válassza a készletek későbbi használatra történő létrehozását beállító (Create pool for later use) gombot.
  * Hagyja meg a „blender-windows” készletnevet.
  * Állítsa a dedikált virtuális gépek számát 0-ra.
  * Állítsa az alacsony prioritású virtuális gépek számát 3-ra.
  * Állítsa a csomópont méretét „Standard_F16” értékre – más méret is választható a virtuális gépekhez, de a képkockák renderelésének ideje főleg a magok számától függ.
* Válassza a zöld gombot a készlet létrehozásához.
  * A készlet szinte azonnal létrejön, de a virtuális gépek lefoglalása és elindítása eltarthat néhány percig.

![Készletsablon a Blenderhez](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> Vegye figyelembe, hogy amikor virtuális gépek vannak egy készletben, akkor azok díjával megterheljük Azure-előfizetését. Ha nem szeretne további költségekkel számolni, törölni kell a készletet vagy a virtuális gépeket. Az oktatóanyag végén törölje a készletet, nehogy továbbra is fizetnie kelljen érte.

A készlet és a virtuális gépek állapota figyelhető a "Gyűjtők" nézetben az alábbi példa bemutatja a három virtuális gép le lett foglalva, két elindítása megtörtént, és inaktív, továbbra is indítása: ![Készlet intenzitástérkép](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>Renderelési feladat létrehozása

Hozzon létre egy renderelési feladatot, hogy a létrehozott készlettel rendereljen néhány képkockát:
* A bal oldali főmenüben válassza a „Gallery” (Katalógus) menüpontot.
* Válassza az alkalmazáselemek listájából a „Blender” elemet.
* Válassza ki a képkockák Windows Serveren történő renderelését beállító elemet.
* Válassza a „Run job with existing pool” (Feladat futtatása meglévő készlettel) gombot.
* Válassza ki a „blender-windows” készletet.
* A feladat neve legyen „blender-render-tutorial1”.
* Válassza a „fgrp-blender-classroom” bemeneti adatokat.
* Válassza ki a „Blend file” (Blend-fájl) fájl ikonját, és válassza a „classroom.blend” elemet.
* Hagyja a képkocka kezdetét 1, a képkocka végét 5 értéken.
* Állítsa a kimeneteket „fgrp-render-output” értékre.
* Válassza a zöld gombot a feladat létrehozásához; létrejön egy feladat öt tevékenységgel (képkockánként eggyel).

![Feladatsablon a Blenderhez](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

A feladat, és minden feladat létrehozása után, a feladat feldolgozás feladatokat jelenik meg: ![Feladatok listája](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

Amikor egy tevékenység először indul el egy készletezett virtuális gépen, lefut egy Batch-feladatelőkészítési tevékenység, amely átmásolja a jelenetfájlokat a tárolófájlcsoportból a virtuális gépre, hogy a Blender hozzájuk férhessen.
A renderelés állapota a Blender által készített stdout.txt naplófájlban tekinthető meg.  Válasszon ki egy tevékenységet. Alapértelmezés szerint megjelennek a tevékenységkimenetek, és az „stdout.txt” kiválasztható és megtekinthető.
![stdout fájl](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

A blender-windows-készlet választásakor a készlet virtuális gépek futó állapotban lesz látható: ![A futó csomópontok a készlet intenzitástérkép](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

A renderelt képek elkészítése több percig is tarthat a kiválasztott virtuálisgép-mérettől függően.  A korábban meghatározott F16 virtuális géppel a képkockák renderelése körülbelül 16 percig tart.

## <a name="view-the-rendering-output"></a>A renderelés kimenetének megtekintése

Ha keretek végzett Renderelés, ezeket a feladatokat befejezettként jelenhetnek meg: ![A feladatok elvégzése](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

A renderelt kép íródik a virtuális Gépet először, és a "wd" mappa kiválasztásával tekinthet meg: ![Renderelt kép a készlet csomóponton](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

A feladatsablon azt is meghatározza, hogy a kimeneti képkockát és a naplófájlokat a rendszer kiírja a feladat létrehozásakor meghatározott Azure Storage-fiók fájlcsoportjába.  Az "adatok" felhasználói felület segítségével megtekintheti a kimeneti fájlokat és a naplók; azt is használható a fájlok letöltésére: ![Renderelt kép tárolócsoport fájl](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

Minden feladat befejeződött, ha a feladat befejezését feladatként lesznek megjelölve: ![Feladatok és minden feladat befejeződött](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

> [!WARNING]
> Törölni kell a készletet (vagy át kell méreteznie nulla csomópontra), hogy a virtuális gépek díjai ne terheljék Azure-előfizetését.

* Válassza a „Pools” (Készletek) lehetőséget.
* Válassza ki a „blender-windows” készletet.
* Kattintson a jobb gombbal, és válassza a „Delete” (Törlés) parancsot, vagy válassza ki a készlet fölött lévő kuka ikont.

## <a name="next-steps"></a>További lépések
* A „Gallery” (Katalógus) szakaszban megismerkedhet a Batch Exploreren keresztül elérhető renderelő alkalmazásokkal.
* Mindegyik alkalmazáshoz több sablon érhető el, amelyek száma idővel nőni fog.  A Blenderhez elérhetők például olyan sablonok, amelyek egyetlen képet osztanak fel csempékké, hogy egy kép részeit egymással párhuzamosan lehessen renderelni.
* A renderelési képességek átfogó ismertetését az [itt](https://docs.microsoft.com/azure/batch/batch-rendering-service) található cikkek tartalmazzák.
