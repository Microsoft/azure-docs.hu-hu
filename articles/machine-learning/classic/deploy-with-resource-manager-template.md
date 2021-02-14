---
title: 'ML Studio (klasszikus): munkaterületek üzembe helyezése Azure Resource Manager-Azure-ban'
description: Azure Machine Learning Studio (klasszikus) munkaterületének üzembe helyezése Azure Resource Manager sablon használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-azurepowershell
ms.date: 02/05/2018
ms.openlocfilehash: ee0bee4857d410781561e6f7f6a7eae0509d1138
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520034"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Azure Machine Learning Studio (klasszikus) munkaterület üzembe helyezése Azure Resource Manager használatával

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Egy Azure Resource Manager telepítési sablon használatával időt takaríthat meg azáltal, hogy méretezhető módon telepíti az összekapcsolt összetevőket egy érvényesítési és újrapróbálkozási mechanizmussal. Azure Machine Learning Studio (klasszikus) munkaterületek beállításához például először be kell állítania egy Azure Storage-fiókot, majd telepítenie kell a munkaterületet. Képzelje el ezt manuálisan a több száz munkaterülethez. Egyszerűbb alternatíva egy Azure Resource Manager sablon használata Studio (klasszikus) munkaterület és annak összes függőségének üzembe helyezéséhez. Ez a cikk végigvezeti a folyamat lépésein. A Azure Resource Manager nagyszerű áttekintését az [Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md)című témakörben tekintheti meg.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Lépésről lépésre: Machine Learning-munkaterület létrehozása
Létre fogunk hozni egy Azure-erőforráscsoportot, majd üzembe kell helyezni egy új Azure Storage-fiókot és egy új Azure Machine Learning Studio (klasszikus) munkaterületet egy Resource Manager-sablon használatával. Az üzembe helyezés befejezését követően a rendszer kinyomtatja a létrehozott munkaterületekről (az elsődleges kulcsról, a munkaterület azonosítója és a munkaterület URL-címéről) fontos információkat.

### <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager sablon létrehozása

A Machine Learning-munkaterület egy Azure Storage-fiókra van szükség ahhoz, hogy tárolja a hozzá társított adatkészletet.
A következő sablon az erőforráscsoport nevét használja a Storage-fiók nevének és a munkaterület nevének a létrehozásához.  Emellett a Storage-fiók nevét is használja tulajdonságként a munkaterület létrehozásakor.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
A sablon mentése mlworkspace.jsfájlként a c:\temp\. alatt

### <a name="deploy-the-resource-group-based-on-the-template"></a>Az erőforráscsoport üzembe helyezése a sablon alapján

* A PowerShell megnyitása
* A Azure Resource Manager és az Azure Service Management moduljának telepítése

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Ezek a lépések letöltik és telepítik a további lépések végrehajtásához szükséges modulokat. Ezt csak egyszer kell elvégezni abban a környezetben, ahol a PowerShell-parancsokat végrehajtja.

* Hitelesítés az Azure-ban

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Ezt a lépést minden egyes munkamenetnél meg kell ismételni. A hitelesítés után az előfizetési adatokat meg kell jeleníteni.

![Azure-fiók](./media/deploy-with-resource-manager-template/azuresubscription.png)

Most, hogy elérjük az Azure-t, létrehozhatjuk az erőforráscsoportot.

* Erőforráscsoport létrehozása

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Ellenőrizze, hogy az erőforráscsoport megfelelően van-e kiépítve. A **ProvisioningState** "sikeres" értékűnek kell lennie.
Az erőforráscsoport nevét a sablon használja a Storage-fiók nevének létrehozásához. A Storage-fiók nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket használjon.

![Erőforráscsoport](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Az erőforráscsoport-telepítés használatával helyezzen üzembe egy új Machine Learning-munkaterület.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Az üzembe helyezés befejezése után egyszerűen hozzáférhet a telepített munkaterület tulajdonságaihoz. Elérheti például az elsődleges kulcs jogkivonatát.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

A meglévő munkaterület jogkivonatának beolvasásának másik módja az Invoke-AzResourceAction parancs használata. Például listázhatja az összes munkaterület elsődleges és másodlagos jogkivonatait.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
A munkaterület kiépítés után számos Azure Machine Learning Studio (klasszikus) feladatot is automatizálhat [Azure Machine learning Studio (klasszikus) PowerShell-modul](https://aka.ms/amlps)használatával.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Resource Manager-sablonok létrehozásáról](../../azure-resource-manager/templates/template-syntax.md).
* Tekintse meg az [Azure Gyorsindítás sablonok tárházát](https://github.com/Azure/azure-quickstart-templates).
* Tekintse meg ezt a videót a [Azure Resource Managerról](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Tekintse meg a [Resource Manager-sablon referenciájának súgóját](/azure/templates/microsoft.machinelearning/allversions) .

<!--Link references-->