---
title: Logikaialkalmazás-sablonok üzembe helyezése
description: Megtudhatja, hogyan telepítheti a Azure Logic Appshoz létrehozott Azure Resource Manager sablonokat
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5f5db3fd88f04e7fe569cd675936445dcf730288
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705336"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure Resource Manager-sablonok üzembe helyezése Azure Logic Apps-alkalmazásokhoz

Miután létrehozott egy Azure Resource Manager sablont a logikai alkalmazáshoz, a következő módon telepítheti a sablont:

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API-k](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Üzembe helyezés Azure Portal

A Logic app-sablonok Azure-ba történő automatikus üzembe helyezéséhez válassza a következő **üzembe helyezés az Azure** -ban gombot, amely bejelentkezik a Azure Portalba, és megkéri a logikai alkalmazással kapcsolatos információk megadására. Ezután elvégezheti a logikai alkalmazás sablonjának vagy paramétereinek a szükséges módosításait.

[![Üzembe helyezés az Azure-ban](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ha például a Azure Portalba való bejelentkezés után kéri a következő információkat:

* Azure-előfizetés neve
* Használni kívánt erőforráscsoport
* Logikai alkalmazás helye
* A logikai alkalmazás neve
* Egy teszt URI
* A megadott feltételek és Kikötések elfogadása

További információt az alábbi témakörökben talál:

* [Áttekintés: a Logic apps üzembe helyezésének automatizálása Azure Resource Manager-sablonokkal](logic-apps-azure-resource-manager-templates-overview.md)
* [Erőforrások üzembe helyezése Azure Resource Manager-sablonokkal és a Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Üzembe helyezés a Visual Studióval

Ha a Visual Studióval létrehozott Azure erőforráscsoport-projektből szeretne logikai alkalmazást telepíteni, kövesse az alábbi [lépéseket a logikai alkalmazás manuális üzembe helyezéséhez](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) az Azure-ban.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

Egy adott *Azure-erőforráscsoport* üzembe helyezéséhez használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

További információt az alábbi témakörökben talál:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül

Egy adott *Azure-erőforráscsoport* üzembe helyezéséhez használja a következő parancsot:

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

További információt az alábbi témakörökben talál:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Üzembe helyezés az Azure DevOps

A logikai alkalmazások sablonjainak üzembe helyezéséhez és a környezetek kezeléséhez a csapatok általában olyan eszközt használnak, mint például az Azure- [DevOps](/azure/devops/user-guide/what-is-azure-devops-services)az Azure- [folyamatok](/azure/devops/pipelines/get-started/what-is-azure-pipelines) . Az Azure-folyamatok egy [Azure erőforráscsoport-telepítési feladatot](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) biztosítanak, amelyet bármely Build-vagy kiadási folyamathoz hozzáadhat. A kiadási folyamat üzembe helyezésének és létrehozásának engedélyezéséhez szükség van egy Azure Active Directory (AD) [egyszerű szolgáltatásra](../active-directory/develop/app-objects-and-service-principals.md)is. További információ az [egyszerű szolgáltatások Azure-folyamatokkal való használatáról](/azure/devops/pipelines/library/connect-to-azure).

További információ a folyamatos integrációról és a folyamatos üzembe helyezésről (CI/CD) az Azure-folyamatokkal rendelkező Azure Resource Manager-sablonokkal kapcsolatban: a következő témakörökben és példákban:

* [Resource Manager-sablonok integrálása az Azure-folyamatokkal](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Oktatóanyag: Azure Resource Manager-sablonok folyamatos integrálása az Azure-folyamatokkal](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Minta: Kapcsolódás Azure Service Bus várólistákhoz Azure Logic Apps és üzembe helyezése Azure-folyamatokkal az Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Kapcsolódás Azure Storage-fiókokhoz Azure Logic Apps és üzembe helyezés az Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Function app-művelet beállítása az Azure DevOps Azure-folyamatokkal való üzembe helyezéséhez és üzembe helyezéséhez Azure Logic Apps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: csatlakozás egy integrációs fiókhoz Azure Logic Apps és üzembe helyezése Azure-folyamatokkal az Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Példa: az Azure-folyamatok összehangolása Azure Logic Apps használatával](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Az Azure-folyamatok használatának általános magas szintű lépései:

1. Hozzon létre egy üres folyamatot az Azure-folyamatokban.

1. Válassza ki a folyamathoz szükséges erőforrásokat, például a logikai alkalmazás sablonját és a sablon paramétereit tartalmazó fájlokat, amelyeket manuálisan vagy a létrehozási folyamat részeként hoz létre.

1. Az ügynök feladatához keresse meg és adja hozzá az **Azure-erőforráscsoport telepítési** feladatát.

   !["Azure Resource Group Deployment" feladat hozzáadása](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Konfigurálás egy [egyszerű szolgáltatással](/azure/devops/pipelines/library/connect-to-azure).

1. Adjon hozzá hivatkozásokat a logikai alkalmazás sablonja és a sablon paramétereinek fájljaihoz.

1. Szükség szerint folytassa a kiadási folyamat lépéseit bármilyen más környezet, automatizált teszt vagy jóváhagyó esetében.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth-kapcsolatok engedélyezése

Az üzembe helyezést követően a logikai alkalmazás teljes körűen használható paraméterekkel, de érvényes hozzáférési jogkivonatokat hoz létre a [hitelesítő adatok hitelesítéséhez](../active-directory/develop/authentication-vs-authorization.md), továbbra is engedélyeznie kell vagy használnia kell az előkészítő OAuth-kapcsolatokat. Azonban csak egyszer kell telepítenie és hitelesítenie az API-kapcsolatok erőforrásait, ami azt jelenti, hogy a későbbi központi telepítések során nem kell felvennie ezeket a kapcsolódási erőforrásokat, kivéve, ha frissítenie kell a kapcsolódási adatokat. Ha folyamatos integrációs és folyamatos üzembe helyezési folyamatot használ, csak a frissített Logic Apps erőforrásokat kell telepítenie, és nem kell minden alkalommal újra engedélyezni a kapcsolatokat.

Íme néhány javaslat az engedélyezett kapcsolatok kezeléséhez:

* Engedélyezze és ossza meg az API-kapcsolatok erőforrásait az azonos régióban található logikai alkalmazások között. Az API-kapcsolatok az Azure-erőforrásoktól függetlenül működnek a Logic apps szolgáltatásban. Míg a Logic apps függőségekkel rendelkezik az API-kapcsolatok erőforrásainál, az API-kapcsolatok erőforrásai nem rendelkeznek a logikai alkalmazásokkal kapcsolatos függőségekkel, és a függő logikai alkalmazások törlése után is megmaradnak. Emellett a Logic apps más erőforráscsoportok is használhat API-kapcsolatokat. A Logic app Designer azonban csak a logikai alkalmazásokkal azonos erőforráscsoporthoz támogatja az API-kapcsolatok létrehozását.

  > [!NOTE]
  > Ha az API-kapcsolatok megosztását fontolgatja, győződjön meg arról, hogy a megoldás képes [kezelni a lehetséges szabályozási problémákat](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling). A szabályozás a kapcsolódási szinten történik, ezért a több logikai alkalmazás közötti kapcsolatok újrafelhasználásával növelheti a problémák szabályozásának lehetőségét.

* Ha a forgatókönyv olyan szolgáltatásokat és rendszereket foglal magába, amelyek többtényezős hitelesítést igényelnek, egy PowerShell-parancsfájl használatával biztosíthatja az egyes OAuth-kapcsolatok jóváhagyását azáltal, hogy egy folyamatos integrációs feldolgozót futtat egy olyan virtuális gépen, amelyen aktív böngésző-munkamenetek vannak megadva a már megadott engedélyekkel és hozzájárulásokkal. Áthelyezheti például a LogicAppConnectionAuth projekt által biztosított parancsfájlt [a GitHub-tárház Logic apps](https://github.com/logicappsio/LogicAppConnectionAuth).

* Az OAuth-kapcsolatok manuális engedélyezéséhez nyissa meg a logikai alkalmazást a Logic app Designerben, vagy a Azure Portal vagy a Visual Studióban.

* Ha a kapcsolatok engedélyezése helyett egy Azure Active Directory (Azure AD [) szolgáltatásnevet használ](../active-directory/develop/app-objects-and-service-principals.md) , megtudhatja, hogyan [adhat meg egyszerű szolgáltatásnév-paramétereket a logikai alkalmazás sablonjában](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Logikai alkalmazások figyelése](../logic-apps/monitor-logic-apps.md)
