---
title: 'Gyors útmutató: az Azure API üzembe helyezése ARM-sablon használatával FHIR'
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe az Azure API-t a gyors egészségügyi együttműködési erőforrások (FHIR®) számára egy Azure Resource Manager sablon (ARM-sablon) használatával.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: cavoeg
ms.date: 10/06/2020
ms.openlocfilehash: 8d7f71477e50e4771bbc7670312943eadf1a8657
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018654"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>Gyors útmutató: ARM-sablon használata az Azure API FHIR való üzembe helyezéséhez

Ebből a rövid útmutatóból megtudhatja, hogyan használhat Azure Resource Manager sablont (ARM-sablont) az Azure API gyors egészségügyi együttműködési erőforrásokhoz való üzembe helyezéséhez (FHIR®). Az Azure API-t a Azure Portal, a PowerShell vagy a CLI használatával telepítheti a FHIR.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon ekkor megnyílik a Azure Portal a bejelentkezés után.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban Azure API a FHIR szolgáltatáshoz a Azure Portal ARM-sablon használatával.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* Ha a kódot helyileg szeretné futtatni, [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* Ha helyileg szeretné futtatni a kódot:
    * Egy bash-rendszerhéj (például git bash, amely a [git for Windows](https://gitforwindows.org)részét képezi).
    * [Azure CLI](/cli/azure/install-azure-cli)-vel.

---

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/) közül származik.

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

A sablon egy Azure-erőforrást definiál:

* **Microsoft. HealthcareApis/szolgáltatások**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

# <a name="portal"></a>[Portál](#tab/azure-portal)

A következő hivatkozásra kattintva telepítheti a FHIR készült Azure API-t a Azure Portal ARM sablonnal:

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Telepítse az Azure-ba egy Azure API-t a FHIR szolgáltatáshoz a Azure Portal ARM sablonjának használatával.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

Az **Azure API üzembe helyezése FHIR** oldalon:

1. Ha szeretné, módosítsa az **előfizetést** az alapértelmezettről egy másik előfizetésre.

2. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget, adja meg az új erőforráscsoport nevét, majd kattintson **az OK gombra**.

3. Ha létrehozott egy új erőforráscsoportot, válasszon ki egy **régiót** az erőforráscsoport számára.

4. Adja meg az új **szolgáltatásnév nevét** , és válassza ki a FHIR készült Azure API **helyét** . A hely az erőforráscsoport régiójától eltérő lehet.

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="Telepítse a FHIR készült Azure API-t a Azure Portal ARM sablonnal.":::

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Olvassa el a használati feltételeket, majd válassza a **Létrehozás** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Ha helyileg szeretné futtatni a PowerShell-parancsfájlokat, első lépésként adja meg az `Connect-AzAccount` Azure-beli hitelesítő adatait.

Ha az `Microsoft.HealthcareApis` erőforrás-szolgáltató még nincs regisztrálva az előfizetéséhez, a következő interaktív kóddal regisztrálhatja. A kód Azure Cloud Shellban való futtatásához válassza a **kipróbálás** a kód felső sarkában lehetőséget.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

A következő kód használatával telepítheti az Azure API-t a FHIR szolgáltatáshoz az ARM sablonnal. A kód megkéri az új FHIR-szolgáltatás nevét, az új erőforráscsoport nevét, valamint az egyes nevek helyét.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

Ha az `Microsoft.HealthcareApis` erőforrás-szolgáltató még nincs regisztrálva az előfizetéséhez, a következő interaktív kóddal regisztrálhatja. A kód Azure Cloud Shellban való futtatásához válassza a **kipróbálás** a kód felső sarkában lehetőséget.

```azurecli-interactive
az extension add --name healthcareapis
```

A következő kód használatával telepítheti az Azure API-t a FHIR szolgáltatáshoz az ARM sablonnal. A kód megkéri az új FHIR-szolgáltatás nevét, az új erőforráscsoport nevét, valamint az egyes nevek helyét.

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> A telepítés elvégzése néhány percet vesz igénybe. Jegyezze fel a FHIR szolgáltatáshoz tartozó Azure API nevét és az erőforráscsoportot, amelyet később az üzembe helyezett erőforrások áttekintésére használ.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépéseket követve áttekintheti az új Azure API-t a FHIR szolgáltatáshoz:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **Azure API**-t a FHIR.

2. A FHIR listából válassza ki az új szolgáltatást. Megjelenik az új Azure API for FHIR szolgáltatás **áttekintő** lapja.

3. Annak ellenőrzéséhez, hogy az új FHIR API-fiók ki van-e választva, kattintson a **FHIR metaadat-végpont** melletti HIVATKOZÁSRA a FHIR API-képesség utasításának beolvasásához. A hivatkozás formátuma: `https://<service-name>.azurehealthcareapis.com/metadata` . Ha a fiók van kiépítve, egy nagyméretű JSON-fájl jelenik meg.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

A következő interaktív kód futtatásával tekintheti meg az Azure API FHIR-szolgáltatással kapcsolatos részleteit. Meg kell adnia az új szolgáltatás nevét és az erőforráscsoportot.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

A következő interaktív kód futtatásával tekintheti meg az Azure API FHIR-szolgáltatással kapcsolatos részleteit. Meg kell adnia az új szolgáltatás nevét és az erőforráscsoportot.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki az **erőforráscsoportok** elemet.

2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.

3. Az erőforráscsoport **Áttekintés** lapján válassza az **erőforráscsoport törlése** elemet.

4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

Az ARM-sablonok létrehozásának folyamatát ismertető lépésenkénti oktatóanyagért lásd az [első ARM-sablon létrehozásával és üzembe helyezésével kapcsolatos oktatóanyagot](../../azure-resource-manager/templates/template-tutorial-create-first-template.md) .

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezte a FHIR készült Azure API-t az előfizetésében. Ha további beállításokat szeretne megadni a FHIR készült Azure API-ban, folytassa a további beállítások útmutatóval. Ha készen áll a FHIR készült Azure API használatára, olvassa el az alkalmazások regisztrálásával foglalkozó témakört.

>[!div class="nextstepaction"]
>[További beállítások a FHIR készült Azure API-ban](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Alkalmazások regisztrálása – áttekintés](fhir-app-registration.md)
