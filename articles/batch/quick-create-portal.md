---
title: Azure rövid útmutató – Az első Batch-feladat futtatása a Azure Portal
description: Ez a rövid útmutató bemutatja, hogyan használható a Azure Portal Batch-fiók, számítási csomópontok készletének és a készleten alapszintű tevékenységeket futtató feladat létrehozására.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 3333097b4bd55173725aa33bc4bfbae318510cf1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538543"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Rövid útmutató: Az első Batch-feladat futtatása az Azure Portalon

A Azure Batch használatának első Azure Portal Batch-fiók, számítási csomópontok (virtuális gépek) készletének és a készleten tevékenységeket futtató feladat létrehozásához. A rövid útmutató elvégzése után megértheti a Batch szolgáltatás alapfogalmait, és készen áll arra, hogy realisztikusabb számítási feladatokkal próbálja ki a Batch szolgáltatást nagyobb léptékben.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

Kövesse az alábbi lépéseket egy minta Batch-fiók tesztelési céllal történő létrehozásához. Készletek és feladatok létrehozásához Batch-fiók szükséges. Ahogyan az az alábbi ábrán is látható, a Batch-fiók összekapcsolható egy Azure Storage-fiókkal. Bár a rövid útmutatóhoz nem kötelező, a Storage-fiók hasznos az alkalmazások üzembe helyezéséhez, valamint a legtöbb valós számítási feladat be- és kimeneti adatainak tárolásához.

1. A [Azure Portal](https://portal.azure.com)válassza az **Erőforrás létrehozása** Számítási  >    >  **Batch-szolgáltatás lehetőséget.** 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Képernyőkép a Batch szolgáltatásról a Azure Marketplace.":::

1. Az **Erőforráscsoport mezőben** válassza az **Új létrehozása** lehetőséget, és adjon nevet az erőforráscsoportnak.

1. Adjon meg egy értéket az **Account name (Fiók neve) mezőben.** Ennek a névnek egyedinek kell lennie a kiválasztott **Azure-helyen** belül. Csak kisbetűket és számokat tartalmazhat, és 3–24 karakter hosszúságú lehet.

1. A **Tárfiók alatt** válasszon ki egy meglévő tárfiókot, vagy hozzon létre egy újat.

1. Ne módosítsa a többi beállítást. Válassza **az Áttekintés + létrehozás** lehetőséget, majd a **Létrehozás** lehetőséget a Batch-fiók létrehozásához.

Amikor **megjelenik az Üzembe helyezés sikeres** üzenet, kattintson a létrehozott Batch-fiókra.

## <a name="create-a-pool-of-compute-nodes"></a>Számításicsomópont-készlet létrehozása

Most, hogy már rendelkezik Batch-fiókkal, hozzon létre egy windowsos számítási csomópontokból álló mintakészletet tesztelési célra. A gyors példa készlete két csomópontból áll, amelyek a Windows Server 2019 rendszerképét futtatják a Azure Marketplace.

1. A Batch-fiókban válassza a **Készletek Hozzáadás**  >  **lehetőséget.**

1. Adja meg a következő **készletazonosítót**: *mypool*.

1. Az **Operációs rendszer** területen válassza ki az alábbi beállításokat (más beállításokat is kipróbálhat).
  
   |Beállítás  |Érték  |
   |---------|---------|
   |**Rendszerkép típusa**|Piactér|
   |**Publisher**     |microsoftwindowsserver|
   |**Ajánlat**     |windowsserver|
   |**Sku**     |2019-datacenter-core-smalldisk|

1. Görgessen le a **Csomópontméret** és a **Méretezés** beállítás megadásához. A javasolt csomópontméret jó teljesítmény/költség arányt kínál a jelen rövid példában.
  
   |Beállítás  |Érték  |
   |---------|---------|
   |**Csomópont tarifacsomagja**     |Standard A1|
   |**Dedikált célcsomópontok**     |2|

1. Hagyja meg az alapértelmezett értéket a többi beállításnál, és válassza az **OK** lehetőséget a készlet létrehozásához.

A Batch azonnal létrehozza a készletet, de a számítási csomópontok lefoglalása és elindítása igénybe vesz néhány percet. Eközben a készlet **Lefoglalási állapota****Átméretezés**. A készlet átméretezése közben létrehozhat egy feladatot és tevékenységeket.

Néhány perc múlva a lefoglalás állapota **Stabilra** változik, és a csomópontok elindulnak. A csomópontok állapotának ellenőrzéshez jelölje ki a készletet, majd válassza a **Csomópontok lehetőséget.** Ha egy csomópont állapota **Tétlen**, készen áll a tevékenységek futtatására.

## <a name="create-a-job"></a>Feladat létrehozása

Most, hogy már rendelkezik készlettel, hozzon létre egy feladatot, amelyet azon futtat. A Batch-feladatok egy vagy több tevékenység logikai csoportja. Egy Batch-feladat magában foglalja a tevékenységek közös beállításait, mint a prioritást, illetve a készletet, amelyeken a tevékenységeket futtatni szeretné. A feladat kezdetben nem tartalmaz tevékenységeket.

1. A Batch-fiók nézetben válassza a **Feladatok Hozzáadása**  >  **lehetőséget.**

1. Adja meg a következő **feladatazonosítót**: *myjob*. A **Készlet** mezőben válassza a *mypool* lehetőséget. Tartsa meg az alapértelmezett értékeket a többi beállításnál, és válassza az **OK** lehetőséget.

## <a name="create-tasks"></a>Tevékenységek létrehozása

Most válassza ki a feladatot a Tevékenységek **lap megnyitásához.** Itt hozhat létre a feladatban futtatható minta tevékenységeket. Általában több olyan feladatot hoz létre, amelyek a Batch várólistára kerülnek, és elosztják a számítási csomópontokon való futtatásra. Ebben a példában két azonos tevékenységet hoz létre. Minden tevékenység egy parancssort futtat, és megjeleníti a Batch környezeti változóit a számítási csomóponton, majd vár 90 másodpercet.

Batch használata esetén a parancssorban adhatja meg az alkalmazást vagy szkriptet. A Batch számos módszert kínál az alkalmazások és szkriptek számítási csomópontokon történő üzembe helyezésére.

Az első tevékenység létrehozása:

1. Válassza a **Hozzáadás** lehetőséget.

1. Adja meg a következő **tevékenységazonosítót**: *mytask*.

1. A **parancssorban** adja meg a következő parancsot: `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. A többi beállításnál tartsa meg az alapértelmezett értékeket, majd válassza a **Küldés lehetőséget.**

Tevékenység létrehozása után a Batch várólistára helyezi azt a készleten való futtatáshoz. Amint egy csomópont készen áll a futtatásra, a rendszer futtatja a tevékenységet.

Egy második feladat létrehozásához ismételje meg a fenti lépéseket. Adjon meg egy másik **tevékenység-azonosítót**, de a parancssor legyen ugyanaz. Ha az első tevékenység még fut, a Batch a készlet egy másik csomópontján indítja el a második tevékenységet.

## <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

A létrehozott példafeladatok néhány percen belül befejeződnek. Egy befejezett tevékenység kimenetének megtekintéséhez válassza ki a tevékenységet, majd válassza a **Fájlok a csomóponton lehetőséget.** Válassza ki a `stdout.txt` fájlt a feladat standard kimenetének megtekintéséhez. A tartalma a következőhöz hasonló lesz:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="A befejezett feladat kimenetének képernyőképe.":::

A tartalom a csomóponton beállított Azure Batch környezeti változókat jeleníti meg. Saját Batch-feladatok és -tevékenységek létrehozása során hivatkozhat ezekre a környezeti változókra a tevékenységek parancssorában, illetve a parancssorok által futtatott alkalmazásokban és szkriptekben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha folytatni szeretné a Batch-oktatóanyagok és -minták használatát, használja az ebben a rövid útmutatóban létrehozott Batch-fiókot és az ahhoz kapcsolt Storage-fiókot. A Batch-fiók használata díjmentes.

A készletért díjat számítunk fel, amíg a csomópontok futnak, még akkor is, ha nincsenek feladatok ütemezve. Ha már nincs szüksége a készletre, törölje azt. A fióknézetben válassza a **Készletek** lehetőséget, majd a készlet nevét. Ezután válassza a **Törlés** elemet.  A készlet törlésekor a rendszer a csomópont összes tevékenységének kimenetét is törli.

Törölje az erőforráscsoportot, a Batch-fiókot és az összes kapcsolódó erőforrást, ha már nincs rá szüksége. Ehhez válassza ki a Batch-fiókhoz tartozó erőforráscsoportot, és válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Batch-fiókot, egy Batch-készletet és egy Batch-feladatot hozott létre. A feladat mintatevékenységeket futtatott, Ön pedig megtekintette az egyik csomóponton létrejött kimenetet. Most, hogy megismerkedett a Batch szolgáltatás fő fogalmaival, készen áll a Batch szolgáltatás realisztikusabb számítási feladatokkal, nagyobb léptékben történő kipróbálására. Az Azure Batchről további információt az Azure Batch-oktatóanyagokban találhat.

> [!div class="nextstepaction"]
> [Azure Batch-oktatóanyagok](./tutorial-parallel-dotnet.md)
