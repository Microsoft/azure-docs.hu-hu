---
title: Tesztkörnyezet létrehozása Azure DevTest Labs és Azure Resource Manager sablon használatával
description: Ebben a rövid útmutatóban egy labort hoz létre Azure DevTest Labs egy Azure Resource Manager sablon (ARM-sablon) használatával. A tesztkörnyezet rendszergazdája beállítja a labort, virtuális gépeket hoz létre a laborban, és konfigurálja a házirendeket.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 2b825b4d4485f401199556b6faaef0017f583cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91461189"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>Gyors útmutató: tesztkörnyezet beállítása Azure DevTest Labs ARM-sablon használatával
Ebben a rövid útmutatóban létrehoz egy labort egy Windows Server 2019 Datacenter virtuális géppel egy Azure Resource Manager sablon (ARM-sablon) használatával. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ebben a rövid útmutatóban a következő műveleteket hajtja végre:

> [!div class="checklist"]
> * A sablon áttekintése 
> * A sablon üzembe helyezése
> * A sablon ellenőrzése
> * Az erőforrások eltávolítása

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/) közül származik.

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

A sablonban definiált erőforrások a következők:

- [**Microsoft. segédösszetevője/Labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft. segédösszetevője Labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft. segédösszetevője Labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

További sablon-példákat az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)talál.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése
Az üzembe helyezés automatikus futtatásához kattintson a következő gombra. 

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Hozzon létre egy **új erőforráscsoportot** , hogy a későbbiekben könnyen tisztítható legyen.
1. Válassza ki az erőforráscsoport **helyét** . 
1. Adja meg **a labor nevét**. 
1. Adja meg a **virtuális gép nevét**. 
1. Adja meg a virtuális gépet elérő **felhasználónevet** . 
1. Adja meg a felhasználóhoz tartozó **jelszót**. 
1. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket**. 
1. Ezután válassza a **vásárlás** lehetőséget.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Sablon üzembe helyezése lap":::

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése
1. Válassza a felül található **értesítések** elemet a központi telepítés állapotának megtekintéséhez, majd kattintson a **telepítés folyamatban van** hivatkozásra.

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Üzembe helyezési értesítés":::
2. A **központi telepítés – áttekintés** oldalon várjon, amíg a telepítés befejeződik. Ez a művelet (különösen a virtuális gép létrehozása) elvégzése hosszabb időt vesz igénybe. Ezután válassza ki az **Erőforrás-csoport** vagy az **erőforráscsoport nevét** az alábbi képen látható módon: 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Erőforrás-csoport megkeresése":::
3. Az **erőforráscsoport** lapon megtekintheti az erőforráscsoport erőforrásainak listáját. Győződjön meg arról, hogy a következő típusú labor látható: az `DevTest Lab` erőforrásokban. A függő erőforrásokat, például a virtuális hálózatot és a virtuális gépet az erőforráscsoporthoz is megtekintheti. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Erőforráscsoport kezdőlapja":::
4. Válassza ki a labort az erőforrások listájából, és tekintse meg a labor kezdőlapját. Győződjön meg arról, hogy a **virtuális gépek** listájában a Windows Server 2019 Datacenter virtuális gép jelenik meg. Az alábbi ábrán az **Essentials (alapvető** erőforrások) szakasz a lehető legkisebbre van kialakítva. 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="A labor kezdőlapja":::

    > [!IMPORTANT] 
    > Tartsa megnyitva ezt a lapot, és kövesse a következő szakaszban található utasításokat az erőforrások törléséhez, hogy elkerülje a tesztkörnyezet és a virtuális gép Azure-beli futtatásának költségeit. Ha a következő oktatóanyag segítségével szeretné tesztelni a virtuális gépet a laborban, törölje az erőforrásokat az oktatóanyag átlépése után. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Először törölje a labort, hogy törölhető legyen az erőforráscsoport. Nem fogja tudni törölni az erőforráscsoportot egy laborban. A labor törléséhez válassza a **Törlés** lehetőséget az eszköztáron. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Tesztkörnyezet törlése gomb":::
 2. A jóváhagyás lapon írja be a **labor nevét**, majd válassza a **Törlés** lehetőséget. 
 3. Várjon, amíg a tesztkörnyezet törölve lesz. Válassza a **harang** ikont a törlési művelet értesítésének megtekintéséhez. Ez a folyamat hosszabb időt vesz igénybe. Erősítse meg a labor törlését, majd válassza ki az **erőforráscsoportot** a navigációs menüben. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="A virtuális gép törlésének megerősítése az értesítésekben":::
 1. Az **erőforráscsoport** lapon válassza az **erőforráscsoport törlése** elemet az eszköztárból. A jóváhagyás lapon írja be az **erőforráscsoport nevét**, majd válassza a **Törlés** lehetőséget. Ellenőrizze az értesítéseket, és győződjön meg arról, hogy az erőforráscsoport törölve lett.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Erőforráscsoport gomb törlése":::

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban létrehozott egy labort egy virtuális géppel. A labor elérésének megismeréséhez folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Oktatóanyag: Hozzáférés a tesztkörnyezethez](tutorial-use-custom-lab.md)
