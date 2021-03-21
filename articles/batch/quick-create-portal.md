---
title: Azure rövid útmutató – az első batch-feladatot az Azure Portal futtathatja
description: Ez a rövid útmutató bemutatja, hogyan használható a Azure Portal egy batch-fiók, egy számítási csomópontok készlete és egy, a készleten alapszintű feladatokat futtató feladat létrehozásához.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: mvc
ms.openlocfilehash: 1234a932a732cdb6fda1c412a423ae0b1ea089e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184015"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Rövid útmutató: Az első Batch-feladat futtatása az Azure Portalon

Az Azure Batch használatának első lépései a Azure Portal használatával létrehozhat egy batch-fiókot, egy számítási csomópontok (virtuális gépek) készletét, valamint egy olyan feladatot, amely a készleten feladatokat futtat. A rövid útmutató elvégzése után megismerheti a Batch szolgáltatás legfontosabb fogalmait, és készen áll arra, hogy a Batch több reális számítási feladattal próbálkozzon nagyobb méretekben.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-batch-account"></a>Batch-fiók létrehozása

Kövesse az alábbi lépéseket egy minta Batch-fiók tesztelési céllal történő létrehozásához. Készletek és feladatok létrehozásához Batch-fiók szükséges. Ahogyan az az alábbi ábrán is látható, a Batch-fiók összekapcsolható egy Azure Storage-fiókkal. Bár a rövid útmutatóhoz nem kötelező, a Storage-fiók hasznos az alkalmazások üzembe helyezéséhez, valamint a legtöbb valós számítási feladat be- és kimeneti adatainak tárolásához.

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása**  >  **számítási**  >  **Batch szolgáltatás** elemet. 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Képernyőkép a Batch szolgáltatásról az Azure piactéren.":::

1. Az **erőforráscsoport** mezőben válassza az **új létrehozása** elemet, és adja meg az erőforráscsoport nevét.

1. Adja meg a **fióknév** értékét. Ennek a névnek egyedinek kell lennie a kiválasztott Azure- **helyen** belül. Csak kisbetűket és számokat tartalmazhat, és 3-24 karakter közöttinek kell lennie.

1. A **Storage-fiók** területen válasszon ki egy meglévő Storage-fiókot, vagy hozzon létre egy újat.

1. Ne módosítsa a többi beállítást. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd válassza a **Létrehozás** lehetőséget a Batch-fiók létrehozásához.

Amikor megjelenik az **üzembe helyezés sikeres** üzenet, lépjen a létrehozott batch-fiókra.

## <a name="create-a-pool-of-compute-nodes"></a>Számításicsomópont-készlet létrehozása

Most, hogy már rendelkezik Batch-fiókkal, hozzon létre egy windowsos számítási csomópontokból álló mintakészletet tesztelési célra. A gyors példa készlete két, Windows Server 2019 rendszerképet futtató csomópontból áll az Azure Marketplace-ről.

1. A Batch-fiókban válassza a **készletek**  >  **Hozzáadás** lehetőséget.

1. Adja meg a következő **készletazonosítót**: *mypool*.

1. Az **Operációs rendszer** területen válassza ki az alábbi beállításokat (más beállításokat is kipróbálhat).
  
   |Beállítás  |Érték  |
   |---------|---------|
   |**Rendszerkép típusa**|Piactér|
   |**Publisher**     |microsoftwindowsserver|
   |**Ajánlat**     |windowsserver|
   |**SKU**     |2019 – Datacenter-Core – smalldisk|

1. Görgessen le a **Csomópontméret** és a **Méretezés** beállítás megadásához. A javasolt csomópontméret jó teljesítmény/költség arányt kínál a jelen rövid példában.
  
   |Beállítás  |Érték  |
   |---------|---------|
   |**Csomópont tarifacsomagja**     |Szabványos a1|
   |**Dedikált célcsomópontok**     |2|

1. Hagyja meg az alapértelmezett értéket a többi beállításnál, és válassza az **OK** lehetőséget a készlet létrehozásához.

A Batch azonnal létrehozza a készletet, de a számítási csomópontok lefoglalása és elindítása igénybe vesz néhány percet. Eközben a készlet **Lefoglalási állapota****Átméretezés**. A készlet átméretezése közben létrehozhat egy feladatot és tevékenységeket.

Néhány perc elteltével a kiosztási állapot **állandóra** változik, és a csomópontok megkezdődnek. A csomópontok állapotának megtekintéséhez válassza ki a készletet, majd válassza a **csomópontok** lehetőséget. Ha egy csomópont állapota **Tétlen**, készen áll a tevékenységek futtatására.

## <a name="create-a-job"></a>Feladat létrehozása

Most, hogy már rendelkezik készlettel, hozzon létre egy feladatot, amelyet azon futtat. A Batch-feladatok egy vagy több feladat logikai csoportjai. Egy Batch-feladat magában foglalja a tevékenységek közös beállításait, mint a prioritást, illetve a készletet, amelyeken a tevékenységeket futtatni szeretné. A feladat kezdetben nem tartalmaz tevékenységeket.

1. A Batch-fiók nézetben válassza a **feladatok**  >  **Hozzáadás** lehetőséget.

1. Adja meg a következő **feladatazonosítót**: *myjob*. A **Készlet** mezőben válassza a *mypool* lehetőséget. Tartsa meg az alapértelmezett értékeket a többi beállításnál, és válassza az **OK** lehetőséget.

## <a name="create-tasks"></a>Tevékenységek létrehozása

Most válassza ki a feladatot a **feladatok** lap megnyitásához. Itt hozhat létre a feladatban futtatandó mintavételi feladatokat. Általában több feladatot is létre kell hoznia, amelyek a Batch-várólistákat és a kiosztásokat a számítási csomópontokon futtatják. Ebben a példában két azonos tevékenységet hoz létre. Minden tevékenység egy parancssort futtat, és megjeleníti a Batch környezeti változóit a számítási csomóponton, majd vár 90 másodpercet.

Batch használata esetén a parancssorban adhatja meg az alkalmazást vagy szkriptet. A Batch számos módszert kínál az alkalmazások és szkriptek számítási csomópontokon történő üzembe helyezésére.

Az első tevékenység létrehozása:

1. Válassza a **Hozzáadás** lehetőséget.

1. Adja meg a következő **tevékenységazonosítót**: *mytask*.

1. A **parancssorban** adja meg a következő parancsot: `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`. Tartsa meg az alapértelmezett értékeket a többi beállításnál, és válassza a **Küldés** lehetőséget.

Tevékenység létrehozása után a Batch várólistára helyezi azt a készleten való futtatáshoz. Amint egy csomópont készen áll a futtatásra, a rendszer futtatja a tevékenységet.

Egy második feladat létrehozásához ismételje meg a fenti lépéseket. Adjon meg egy másik **tevékenység-azonosítót**, de a parancssor legyen ugyanaz. Ha az első tevékenység még fut, a Batch a készlet egy másik csomópontján indítja el a második tevékenységet.

## <a name="view-task-output"></a>A tevékenység kimenetének megtekintése

A példaként létrehozott feladatok elvégzése néhány percen belül megtörténik. Egy befejezett feladat kimenetének megtekintéséhez jelölje ki a feladatot, majd válassza a **fájlok csomóponton** lehetőséget. Válassza ki a fájlt a `stdout.txt` feladat normál kimenetének megtekintéséhez. A tartalma a következőhöz hasonló lesz:

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
