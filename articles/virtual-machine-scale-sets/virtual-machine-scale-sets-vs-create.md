---
title: Virtuálisgép-méretezési csoport üzembe helyezése a Visual Studióval
description: Virtual Machine Scale Sets üzembe helyezése a Visual Studióval és egy Resource Manager-sablonnal
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: visual-studio
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 2db281f1b3278e37deca8486971a7f7a83f85ccf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86503081"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Virtuálisgép-méretezési csoport létrehozása a Visual Studióval

Ez a cikk bemutatja, hogyan helyezhet üzembe egy Azure-beli virtuálisgép-méretezési csoportot egy Visual Studio erőforráscsoport-telepítés használatával.

Az [azure Virtual Machine Scale sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) egy Azure-beli számítási erőforrás, amely hasonló virtuális gépek gyűjteményét helyezi üzembe és felügyeli, Automatikus méretezéssel és terheléselosztással. Virtual Machine Scale Sets üzembe helyezése és központi telepítése [Azure Resource Manager sablonok](https://github.com/Azure/azure-quickstart-templates)használatával. Azure Resource Manager sablonok az Azure CLI-vel, a PowerShell-lel, a REST-tel és közvetlenül a Visual studióból is üzembe helyezhetők. A Visual Studio egy példa sablonokat tartalmaz, amelyeket üzembe helyezhet egy Azure erőforráscsoport-telepítési projekt részeként.

Az Azure erőforráscsoport-telepítések lehetővé teszik a kapcsolódó Azure-erőforrások csoportosítását és közzétételét egyetlen telepítési művelet során. További információ: [Azure-erőforráscsoportok létrehozása és telepítése a Visual Studióval](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="prerequisites"></a>Előfeltételek

A Virtual Machine Scale Sets a Visual Studióban való üzembe helyezésének megkezdéséhez a következő előfeltételek szükségesek:

* Visual Studio 2013 vagy újabb
* Azure SDK 2,7, 2,8 vagy 2,9

>[!NOTE]
>Ez a cikk a Visual Studio 2019 és az [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)használatával készült.

## <a name="create-a-project"></a>Projekt létrehozása <a name="creating-a-project"></a> 

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása** lehetőséget.

1. Az **új projekt létrehozása** területen válassza a C# **Azure-erőforráscsoport** elemet, majd kattintson a **tovább** gombra.

1. Az **új projekt konfigurálása** lapon adjon meg egy nevet, és válassza a **Létrehozás** lehetőséget.

    ![A projekt neve és létrehozása](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. A sablonok listájában válassza a **Windows virtuálisgép-méretezési csoport** vagy a **Linux virtuálisgép** -méretezési csoport sablonját. Válassza az **OK** lehetőséget.

   ![Virtuálisgép-sablon kiválasztása](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

A projekt létrehozása után **megoldáskezelő** egy PowerShell telepítési parancsfájlt, egy Azure Resource Manager sablont és egy, a virtuálisgép-méretezési csoporthoz tartozó paraméter-fájlt tartalmaz.

## <a name="customize-your-project"></a>A projekt testreszabása

Most szerkesztheti a sablont az alkalmazás igényeinek megfelelően. Hozzáadhat virtuálisgép-bővítményi tulajdonságokat, vagy szerkesztheti a terheléselosztási szabályokat. Alapértelmezés szerint a virtuálisgép-méretezési csoport sablonjai a **AzureDiagnostics** -bővítmény üzembe helyezésére vannak konfigurálva, ami megkönnyíti az autoskálázási szabályok hozzáadását. A sablonok egy nyilvános IP-címmel rendelkező terheléselosztó is üzembe helyezhetők, amely bejövő NAT-szabályokkal van konfigurálva.

A terheléselosztó lehetővé teszi, hogy a virtuálisgép-példányokhoz SSH (Linux) vagy RDP (Windows) rendszerű virtuális gépekhez csatlakozzon. Az előtér-porttartomány 50000-kor kezdődik. Linux esetén, ha az 50000-es porton keresztül SSH-val rendelkezik, a terheléselosztás a méretezési csoport első virtuális gépe 22-es portjára irányítja át. Az 50001-es porthoz való csatlakozás a második virtuális gép 22-es portjára van irányítva, és így tovább.

 A sablonok Visual Studióval való szerkesztésének jó módja a **JSON-vázlat** használata. Rendszerezheti a paramétereket, a változókat és az erőforrásokat. A séma megismerése érdekében a Visual Studio az üzembe helyezése előtt rámutathat a hibákra a sablonban.

![JSON-tallózó](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>A projekt üzembe helyezése

Helyezze üzembe a Azure Resource Manager sablont a virtuálisgép-méretezési csoport erőforrásának létrehozásához:

1. **Megoldáskezelő** kattintson a jobb gombbal a projektre, és válassza az  >  **új** telepítése lehetőséget.

    ![A projekt üzembe helyezése](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. A **telepítés az erőforráscsoporthoz** területen válassza ki a használni kívánt előfizetést, és válasszon ki egy erőforráscsoportot. Szükség esetén létrehozhat egy erőforráscsoportot is.

1. Ezután válassza a **Paraméterek szerkesztése** lehetőséget a sablonnak átadott paraméterek megadásához.

   ![Előfizetés és erőforráscsoport megadása](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Adja meg az operációs rendszer felhasználónevét és jelszavát. Ezek az értékek szükségesek az üzemelő példány létrehozásához. Ha nincs telepítve a PowerShell-eszközök a Visual studióhoz, válassza a **jelszavak mentése** lehetőséget a rejtett PowerShell-parancssor elkerüléséhez, vagy használja [Key Vault támogatást](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). A folytatáshoz válassza a **Mentés** lehetőséget.

    ![Telepítési paraméterek szerkesztése](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. Az **üzembe helyezés az erőforráscsoporthoz** területen válassza a **telepítés** lehetőséget. A művelet a **Deploy-AzureResourceGroup.ps1** parancsfájlt futtatja. A **kimeneti** ablak megjeleníti a telepítési folyamatot.

   ![A kimenet az eredményeket jeleníti meg](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport megismerése <a name="exploring-your-virtual-machine-scale-set"></a>

Válassza   >  a **Cloud Explorer** megtekintése lehetőséget az új virtuálisgép-méretezési csoport megtekintéséhez. Ha szükséges, használja az **összes frissítése** parancsot.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

A **Cloud Explorer** lehetővé teszi az Azure-erőforrások kezelését a Visual Studióban az alkalmazások fejlesztése során. A virtuálisgép-méretezési csoport a [Azure Portal](https://portal.azure.com) és [Azure erőforrás-kezelő](https://resources.azure.com/)is megtekinthető.

 A portál lehetővé teszi az Azure-infrastruktúra webböngészővel való felügyeletének legmegfelelőbb módját. Azure Erőforrás-kezelő egyszerű módszert kínál az Azure-erőforrások feltárására és hibakeresésére. Azure Erőforrás-kezelő a példány nézetét, valamint a megtekintett erőforrásokhoz tartozó PowerShell-parancsokat is megjeleníti.

## <a name="next-steps"></a>Következő lépések

Miután sikeresen üzembe helyezte Virtual Machine Scale Sets a Visual studión keresztül, a projekt tovább szabható az alkalmazás követelményeinek megfelelően. Például úgy konfigurálhatja az autoskálázást, hogy hozzáad egy **bepillantást** az erőforráshoz. Hozzáadhat infrastruktúrát a sablonhoz, például az önálló virtuális gépekhez, vagy telepíthet alkalmazásokat az egyéni szkriptek bővítmény használatával. A jó példaként szolgáló sablonok az Azure rövid útmutató [sablonok](https://github.com/Azure/azure-quickstart-templates) GitHub-tárházában találhatók. Keressen a `vmss` kifejezésre.
