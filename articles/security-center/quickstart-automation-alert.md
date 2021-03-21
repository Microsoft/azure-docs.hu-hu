---
title: Speciális biztonsági riasztások biztonsági automatizálásának létrehozása Azure Resource Manager sablon használatával (ARM-sablon)
description: Megtudhatja, hogyan hozhat létre Azure Security Center automationt egy logikai alkalmazás elindításához, amelyet egy Azure Resource Manager sablon (ARM-sablon) használatával adott Security Center riasztások indítanak el.
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: e53a5c4840cee0651090bec0b68cc53e13102299
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705489"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-arm-template"></a>Gyors útmutató: automatikus válasz létrehozása egy adott biztonsági riasztáshoz ARM-sablon használatával

Ez a rövid útmutató azt ismerteti, hogyan használható egy Azure Resource Manager-sablon (ARM-sablon) egy olyan munkafolyamat-automatizálás létrehozásához, amely egy logikai alkalmazást indít el, ha Azure Security Center adott biztonsági riasztásokat fogad.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

A Azure Security Center munkafolyamat-automatizálási funkciójával való együttműködéshez szükséges szerepkörök és engedélyek listáját lásd: munkafolyamat- [automatizálás](workflow-automation.md).

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/) közül származik.

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>Kapcsolódó erőforrások

- [**Microsoft. Security/automations**](/azure/templates/microsoft.security/automations): a logikai alkalmazást kiváltó automatizálás, ha egy Azure Security Center riasztást kap, amely egy adott karakterláncot tartalmaz.
- [**Microsoft. Logic/munkafolyamatok**](/azure/templates/microsoft.logic/workflows): üres aktiválható logikai alkalmazás.

Más Security Center a gyors üzembe helyezési sablonokkal kapcsolatban tekintse meg ezeket a [közösségi sablonokat](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

- **PowerShell**:

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **CLI**:

  ```azurecli-interactive
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **Portál**:

  [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

  További információ erről az üzembe helyezési lehetőségről: [telepítés használata gomb a sablonok a GitHub-tárházból történő telepítéséhez](../azure-resource-manager/templates/deploy-to-azure-button.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal használatával győződjön meg arról, hogy telepítve van a Munkafolyamat-automatizálás.

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Security Center**.
1. A felső menüsorban kattintson a szűrő ikonra, és válassza ki azt az előfizetést, amelyre az új munkafolyamat-automatizálást telepítette.
1. A Security Center oldalsávján nyissa meg a **munkafolyamat-automatizálást** , és keresse meg az új automatizálást.
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Konfigurált automatizálások listája" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > Ha az előfizetése számos munkafolyamat-automatizálással rendelkezik, használja a **szűrés név alapján** lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a munkafolyamat-automatizálást a Azure Portal használatával.

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Security Center**.
1. A felső menüsorban kattintson a szűrő ikonra, és válassza ki azt az előfizetést, amelyre az új munkafolyamat-automatizálást telepítette.
1. A Security Center oldalsávján nyissa meg a **munkafolyamatok automatizálását** , és keresse meg a törölni kívánt automatizálást.
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="A Munkafolyamat-automatizálás eltávolításának lépései" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. Jelölje be a törölni kívánt elem jelölőnégyzetét.
1. Az eszköztáron válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

A sablonok létrehozásának folyamatát ismertető, lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
