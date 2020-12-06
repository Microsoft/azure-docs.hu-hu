---
title: Azure Portal irányítópult létrehozása Azure Resource Manager sablon használatával
description: Megtudhatja, hogyan hozhat létre Azure Portal irányítópultot egy Azure Resource Manager sablon használatával.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/15/2020
ms.openlocfilehash: 55ac24e48c1c976eca1e6e8e51cf6e88361ec630
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745723"
---
# <a name="quickstart-create-a-dashboard-in-the-azure-portal-by-using-an-arm-template"></a>Rövid útmutató: irányítópult létrehozása a Azure Portal ARM-sablon használatával

A Azure Portal egyik irányítópultja a Felhőbeli erőforrások célzott és rendezett nézete. Ez a rövid útmutató egy Azure Resource Manager-sablon (ARM-sablon) üzembe helyezésének folyamatát tárgyalja az irányítópult létrehozásához. Az irányítópulton egy virtuális gép (VM) teljesítménye, valamint néhány statikus információ és hivatkozás látható.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Egy meglévő virtuális gép.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A rövid útmutató következő részében létrehozott irányítópultnak egy meglévő virtuális gépet kell megadnia. Hozzon létre egy virtuális gépet a következő lépésekkel.

1. A Azure Portal válassza a Cloud Shell lehetőséget.

    ![Válassza ki a Cloud Shell elemet a Azure Portal menüszalagon](media/quick-create-template/cloud-shell.png)

1. Másolja az alábbi parancsot, és írja be a parancssorba egy erőforráscsoport létrehozásához.

    ```powershell
    New-AzResourceGroup -Name SimpleWinVmResourceGroup -Location EastUS
    ```

    ![Parancs másolása a parancssorba](media/quick-create-template/command-prompt.png)

1. Másolja a következő parancsot, és írja be a parancssorba, és hozzon létre egy virtuális gépet az erőforráscsoporthoz.

    ```powershell
    New-AzVm `
        -ResourceGroupName "SimpleWinVmResourceGroup" `
        -Name "SimpleWinVm" `
        -Location "East US" 
    ```

1. Adja meg a virtuális gép felhasználónevét és jelszavát. Ez egy új Felhasználónév és jelszó; nem például az Azure-ba való bejelentkezéshez használt fiók. További információ: a [felhasználónévre vonatkozó követelmények](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) és a [jelszóra vonatkozó követelmények](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

    A virtuális gép üzembe helyezése most elindul, és általában néhány percet vesz igénybe. Az üzembe helyezés befejezése után lépjen tovább a következő szakaszra.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azure-portal-dashboard/) közül származik. A cikk sablonja túl hosszú ahhoz, hogy megjelenjen itt. A sablon megtekintéséhez lásd: [azuredeploy.js](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-portal-dashboard/azuredeploy.json). Egy Azure-erőforrás van definiálva a sablonban, [Microsoft. Portal/irányítópultok](/azure/templates/microsoft.portal/dashboards) – irányítópult létrehozása a Azure Portal.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket, majd válassza a **felülvizsgálat + létrehozás** elemet.

    ![ARM-sablon, irányítópult létrehozása, portál üzembe helyezése](media/quick-create-template/create-dashboard-using-template-portal.png)

    Ha nincs megadva, az alapértelmezett értékeket használja az irányítópult létrehozásához.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza a **SimpleWinVmResourceGroup** lehetőséget.
    * Hely: válassza az **USA keleti** **régióját**.
    * **Virtuális gép neve**: adja meg a **SimpleWinVm**.
    * **Virtuális gép erőforráscsoport**: adja meg a **SimpleWinVmResourceGroup**.

1. Válassza a **Létrehozás** vagy a **vásárlás** lehetőséget. Az irányítópult sikeres üzembe helyezését követően értesítést kap:

    ![ARM-sablon, irányítópult létrehozása, portál üzembe helyezése – értesítés](media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

A Azure Portal a sablon üzembe helyezéséhez volt használva. Az Azure Portalon kívül használhatja a következőket is: Azure PowerShell, Azure CLI és REST API. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Ellenőrizze, hogy az irányítópult létrehozása sikeres volt-e, és hogy látható-e az adatok a virtuális gépről.

1. Az Azure Portalon válassza az **Irányítópult** lehetőséget.

    ![Navigálás az irányítópulton Azure Portal](media/quick-create-template/navigate-to-dashboards.png)

1. Az irányítópult lapon válassza az **egyszerű VM-irányítópult** lehetőséget.

    ![Navigáljon az egyszerű VM-irányítópultra](media/quick-create-template/select-simple-vm-dashboard.png)

1. Tekintse át az ARM-sablon által létrehozott irányítópultot. Láthatja, hogy a tartalom némelyike statikus, de vannak olyan diagramok is, amelyek az elején létrehozott virtuális gép teljesítményét mutatják.

    ![Egyszerű VM-irányítópult áttekintése](media/quick-create-template/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a virtuális gépet és a hozzá tartozó irányítópultot, törölje az azokat tartalmazó erőforráscsoportot.

1. A Azure Portal keresse meg a **SimpleWinVmResourceGroup**, majd válassza ki a keresési eredmények között.

1. A **SimpleWinVmResourceGroup** lapon válassza az **erőforráscsoport törlése** elemet, írja be az erőforráscsoport nevét a megerősítéshez, majd válassza a **Törlés** lehetőséget.

    ![Erőforráscsoport törlése](media/quick-create-template/delete-resource-group.png)

## <a name="next-steps"></a>További lépések

A Azure Portal irányítópultokkal kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Irányítópultok létrehozása és megosztása az Azure Portalon](azure-portal-dashboards.md)