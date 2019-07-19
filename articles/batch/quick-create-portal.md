---
title: Azure rövid útmutató – Batch-feladat futtatása – portál
description: Rövid áttekintést kaphat arról, hogyan futtathat Batch-feladatokat az Azure Portalon.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: na
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 33455a95dff946676e3dfffbd2737ed36be59c64
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322361"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Gyors útmutató: Futtassa az első batch-feladatot a Azure Portal

Ez a rövid útmutató ismerteti, hogyan lehet létrehozni az Azure Portalon Batch-fiókot, számítási csomópontok (virtuális gépek) *készletét*, valamint egy olyan *feladatot*, amely alapszintű *tevékenységeket* futtat a készleten. A rövid útmutatóból megismerheti a Batch szolgáltatás fő fogalmait, és készen áll majd a Batch szolgáltatás használatára realisztikusabb számítási feladatokkal, nagyobb léptékben.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

Kövesse az alábbi lépéseket egy minta Batch-fiók tesztelési céllal történő létrehozásához. Készletek és feladatok létrehozásához Batch-fiók szükséges. Ahogyan az az alábbi ábrán is látható, a Batch-fiók összekapcsolható egy Azure Storage-fiókkal. Bár a rövid útmutatóhoz nem kötelező, a Storage-fiók hasznos az alkalmazások üzembe helyezéséhez, valamint a legtöbb valós számítási feladat be- és kimeneti adatainak tárolásához.


1. Válassza az **Erőforrás létrehozása** > **Számítás** > **Batch szolgáltatás** lehetőséget. 

   ![Batch a Piactéren][marketplace_portal]

2. Adjon meg értéket a **Fiók neve** és az **Erőforráscsoport** mezőben. A fiók nevének egyedinek kell lennie a kiválasztott Azure-**helyen**, csak kisbetűket vagy számokat, illetve 3–24 karaktert tartalmazhat. 

3. A **Tárfiókok** területen válasszon egy meglévő tárfiókot, vagy hozzon létre egy újat.

4. Hagyja meg az alapértelmezett értékeket a többi beállításnál, és válassza a **Létrehozás** gombot a fiók létrehozásához.

   ![Batch-fiók létrehozása][account_portal]  

Amikor megjelenik az **Üzembe helyezés sikeres** üzenet, lépjen a Batch-fiókba a portálon.

## <a name="create-a-pool-of-compute-nodes"></a>Számításicsomópont-készlet létrehozása

Most, hogy már rendelkezik Batch-fiókkal, hozzon létre egy windowsos számítási csomópontokból álló mintakészletet tesztelési célra. A jelen rövid példában a készlet 2 csomópontból áll, amelyek Windows Server 2012 R2-rendszerképet futtatnak az Azure Marketplace-ről.


1. A Batch-fiókban válassza a **Készletek** > **Hozzáadás** lehetőséget.

2. Adja meg a következő **készletazonosítót**: *mypool*. 

3. Az **Operációs rendszer** területen válassza ki az alábbi beállításokat (más beállításokat is kipróbálhat).
  
   |Beállítás  |Value  |
   |---------|---------|
   |**Rendszerkép típusa**|Marketplace (Linux/Windows)|
   |**Publisher**     |MicrosoftWindowsServer|
   |**Ajánlat**     |WindowsServer|
   |**Termékváltozat**     |2012-R2-Datacenter-smalldisk|

   ![Készlet operációs rendszerének kiválasztása][pool_os] 

4. Görgessen le a **Csomópontméret** és a **Méretezés** beállítás megadásához. A javasolt csomópontméret jó teljesítmény/költség arányt kínál a jelen rövid példában.
  
   |Beállítás  |Value  |
   |---------|---------|
   |**Csomópont tarifacsomagja**     |Standard_A1|
   |**Dedikált célcsomópontok**     |2|

   ![Készletméret kiválasztása][pool_size] 

5. Hagyja meg az alapértelmezett értéket a többi beállításnál, és válassza az **OK** lehetőséget a készlet létrehozásához.

A Batch azonnal létrehozza a készletet, de a számítási csomópontok lefoglalása és elindítása igénybe vesz néhány percet. Eközben a készlet **Lefoglalási állapota** **Átméretezés**. A készlet átméretezése közben létrehozhat egy feladatot és tevékenységeket. 

![A készlet átméretezés állapotban van][pool_resizing]

Néhány perc elteltével a készlet állapota **Stabil** értékre változik, és elindulnak a csomópontok. Válassza a **Csomópontok** lehetőséget a csomópontok állapotának megtekintéséhez. Ha egy csomópont állapota **Tétlen**, készen áll a tevékenységek futtatására. 

## <a name="create-a-job"></a>Feladat létrehozása

Most, hogy már rendelkezik készlettel, hozzon létre egy feladatot, amelyet azon futtat. A Batch-feladat egy vagy több tevékenység logikai csoportja. Egy Batch-feladat magában foglalja a tevékenységek közös beállításait, mint a prioritást, illetve a készletet, amelyeken a tevékenységeket futtatni szeretné. A feladat kezdetben nem tartalmaz tevékenységeket. 

1. A Batch-fiók nézetben kattintson a **Feladatok** > **Hozzáadás** lehetőségre. 

2. Adja meg a következő **feladatazonosítót**: *myjob*. A **Készlet** mezőben válassza a *mypool* lehetőséget. Tartsa meg az alapértelmezett értékeket a többi beállításnál, és válassza az **OK** lehetőséget.

   ![Feladat létrehozása][job_create]

A feladat létrehozása után megnyílik a **Tevékenységek** lap.

## <a name="create-tasks"></a>Tevékenységek létrehozása

Most hozzon létre a feladatban futtatni kívánt mintatevékenységeket. Általában több tevékenységet hozunk létre, amelyeket a Batch sorba állít és szétoszt a számítási csomópontokra. Ebben a példában két azonos tevékenységet hoz létre. Minden tevékenység egy parancssort futtat, és megjeleníti a Batch környezeti változóit a számítási csomóponton, majd vár 90 másodpercet. 

Batch használata esetén a parancssorban adhatja meg az alkalmazást vagy szkriptet. A Batch számos módszert kínál az alkalmazások és szkriptek számítási csomópontokon történő üzembe helyezésére. 

Az első tevékenység létrehozása:

1. Válassza a **Hozzáadás** lehetőséget.

2. Adja meg a következő **tevékenységazonosítót**: *mytask*. 

3. A **parancssorban** adja meg a következő parancsot: `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Tartsa meg az alapértelmezett értékeket a többi beállításnál, és válassza az **OK** lehetőséget.

   ![Feladat létrehozása][task_create]

Tevékenység létrehozása után a Batch várólistára helyezi azt a készleten való futtatáshoz. Amint egy csomópont készen áll a futtatásra, a rendszer futtatja a tevékenységet.

Egy második tevékenység létrehozásához menjen vissza az 1. lépésre. Adjon meg egy másik **tevékenység-azonosítót**, de a parancssor legyen ugyanaz. Ha az első tevékenység még fut, a Batch a készlet egy másik csomópontján indítja el a második tevékenységet.

## <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

A fenti példatevékenységek néhány percen belül befejeződnek. A befejezett tevékenységek kimenetének megtekintéséhez válassza a **Fájlok a csomóponton** lehetőséget, és válassza az `stdout.txt` fájlt. A fájl a tevékenység szabványos kimenetét jeleníti meg. A tartalma a következőhöz hasonló lesz:

![A tevékenység kimenetének megtekintése][task_output]

A tartalom a csomóponton beállított Azure Batch környezeti változókat jeleníti meg. Saját Batch-feladatok és -tevékenységek létrehozása során hivatkozhat ezekre a környezeti változókra a tevékenységek parancssorában, illetve a parancssorok által futtatott alkalmazásokban és szkriptekben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatni szeretné a Batch-oktatóanyagok és -minták használatát, használja az ebben a rövid útmutatóban létrehozott Batch-fiókot és az ahhoz kapcsolt Storage-fiókot. A Batch-fiók használata díjmentes.

A készletért díjat számítunk fel, amíg a csomópontok futnak, még akkor is, ha nincsenek feladatok ütemezve. Ha már nincs szüksége a készletre, törölje azt. A fióknézetben válassza a **Készletek** lehetőséget, majd a készlet nevét. Ezután válassza a **Törlés** elemet.  A készlet törlésekor a rendszer a csomópont összes tevékenységének kimenetét is törli. 

Törölje az erőforráscsoportot, a Batch-fiókot és az összes kapcsolódó erőforrást, ha már nincs rá szüksége. Ehhez válassza ki a Batch-fiókhoz tartozó erőforráscsoportot, és válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Batch-fiókot, egy Batch-készletet és egy Batch-feladatot hozott létre. A feladat mintatevékenységeket futtatott, Ön pedig megtekintette az egyik csomóponton létrejött kimenetet. Most, hogy megismerkedett a Batch szolgáltatás fő fogalmaival, készen áll a Batch szolgáltatás realisztikusabb számítási feladatokkal, nagyobb léptékben történő kipróbálására. Az Azure Batchről további információt az Azure Batch-oktatóanyagokban találhat. 

> [!div class="nextstepaction"]
> [Azure Batch-oktatóanyagok](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png