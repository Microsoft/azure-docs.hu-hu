---
title: Fürt létrehozása Service Fabric sablon Azure Resource Manager használatával
description: Ebben a rövid útmutatóban egy Azure Service Fabric-tesztfürtöt fog létrehozni egy Azure Resource Manager használatával.
author: erikadoyle
ms.author: edoyle
ms.date: 07/29/2020
ms.topic: quickstart
ms.service: service-fabric
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: cb3758ae837dc03789371d00f768b257a9ef295d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535306"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-arm-template"></a>Rövid útmutató: Service Fabric fürt létrehozása ARM-sablonnal

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások és tárolók csomagolását, üzembe helyezését és kezelését. A Service Fabric *virtuális* gépek hálózatra csatlakozó készletei, amelyekben a mikroszolgáltatások üzembe vannak állítva és kezelve vannak. Ez a cikk azt ismerteti, hogyan helyezhet üzembe Service Fabric tesztfürtöt az Azure-ban egy Azure Resource Manager sablon (ARM-sablon) használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ezt az ötcsomópontos Windows-fürtöt önaírt tanúsítvánnyal biztosítjuk, így csak utasítási célokra szolgál (nem éles számítási feladatokhoz). A sablon üzembe helyezéséhez Azure PowerShell a sablont. A Azure PowerShell mellett használhatja a Azure Portal, az Azure CLI és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-portal.md)

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-secure-cluster-5-node-1-nodetype%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Az Service Fabric SDK- és PowerShell-modulok telepítése

A rövid útmutató befejezéséhez a következőt kell végrehajtania:

* Telepítse a [Service Fabric SDK-t és a PowerShell-modult.](service-fabric-get-started.md)

* Telepítse [a Azure PowerShell.](/powershell/azure/install-az-ps)

### <a name="download-the-sample-template-and-certificate-helper-script"></a>A mintasablon és a tanúsítvány-segítő szkript letöltése

Klónozza vagy töltse le [Azure Resource Manager gyorsindítási sablonokhoz](https://github.com/Azure/azure-quickstart-templates) való hozzáférést. Másik lehetőségként másolja le helyileg a következő fájlokat, amelyek a *service-fabric-secure-cluster-5-node-1-nodetype* mappából lesznek használva:

* [New-ServiceFabricClusterCertificate.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/scripts/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy.jsbe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.jsbe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és jelölje ki azt az előfizetést, amely a Service Fabric létrehozásához szükséges.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Hozzon létre egy önaírt tanúsítványt, amely a Key Vault

Service Fabric X.509-tanúsítványokat használ [a](./service-fabric-cluster-security.md) fürtök biztonságának és az alkalmazásbiztonsági szolgáltatások biztosítanak, [Key Vault](../key-vault/general/overview.md) a tanúsítványok kezeléséhez. A fürt sikeres létrehozásához szükség van egy fürt tanúsítványra a csomópontok közötti kommunikáció engedélyezéséhez. A gyorsindítási tesztfürt létrehozásához egy önaírt tanúsítványt hozunk létre a fürthitelesítéshez. Az éles számítási feladatokhoz megfelelően konfigurált Windows Server-tanúsítványszolgáltatás vagy jóváhagyott hitelesítésszolgáltató (CA) által létrehozott tanúsítványokra van szükség.

```powershell
# Designate unique (within cloudapp.azure.com) names for your resources
$resourceGroupName = "SFQuickstartRG"
$keyVaultName = "SFQuickstartKV"

# Create a new resource group for your Key Vault and Service Fabric cluster
New-AzResourceGroup -Name $resourceGroupName -Location SouthCentralUS

# Create a Key Vault enabled for deployment
New-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -Location SouthCentralUS -EnabledForDeployment

# Generate a certificate and upload it to Key Vault
.\scripts\New-ServiceFabricClusterCertificate.ps1
```

A szkript kérni fogja a következőket (mindenképpen módosítsa a *CertDNSName* és *a KeyVaultName* értékeket az alábbi példaértékek alapján):

* **Jelszó:** Jelszó!1
* **CertDNSName:** *sfquickstart*.southcentralus.cloudapp.azure.com
* **KeyVaultName:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

A befejezést követően a szkript meg fogja adni a sablon üzembe helyezéséhez szükséges paraméterértékeket. Ezeket a következő változókban tárolja, mivel a fürtsablon üzembe helyezéséhez szükség lesz rájuk:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/) közül származik. A cikk sablonja túl hosszú ahhoz, hogy itt meg is mutassa. A sablon megtekintéséhez tekintse meg aazuredeploy.js[fájlt.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)

A sablonban több Azure-erőforrás is definiálva van:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)

Az Azure-beli virtuális Service Fabric további sablonokat az [Azure gyorsindítási sablonokban találja.](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric)

### <a name="customize-the-parameters-file"></a>A paraméterfájl testreszabása

Nyissa *azuredeploy.parameters.jsés* szerkessze a paraméterértékeket a következőre:

* **A clusterName** megegyezik a fürt tanúsítványának létrehozásakor megadott *CertDNSName* értékkel
* **Az adminUserName** érték nem az alapértelmezett *GEN-UNIQUE jogkivonat,*
* **Az adminPassword** az alapértelmezett *GEN-PASSWORD jogkivonattól* más érték
* **certificateThumbprint**, **sourceVaultResourceId**, és **certificateUrlValue** mind üres sztring ( `""` )

Például:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "sfquickstart"
    },
    "adminUsername": {
      "value": "testadm"
    },
    "adminPassword": {
      "value": "Password#1234"
    },
    "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultResourceId": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    }
  }
}
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Tárolja az ARM-sablon és a paraméterfájlok elérési útját változókban, majd telepítse a sablont.

```powershell
$templateFilePath = "<full path to azuredeploy.json>"
$parameterFilePath = "<full path to azuredeploy.parameters.json>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $certThumbprint `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultResourceId $sourceVaultId `
    -Verbose
```

## <a name="review-deployed-resources"></a>Az üzembe helyezett erőforrások áttekintése

Az üzembe helyezés befejezése után keresse meg az értéket a kimenetben, és nyissa meg a címet egy webböngészőben, hogy megtekintse a fürtöt a `managementEndpoint` [Service Fabric Explorer.](./service-fabric-visualizing-your-cluster.md)

![Service Fabric Explorer új fürt megjelenítése](./media/quickstart-cluster-template/service-fabric-explorer.png)

A végpontot a Service Explorer Service Fabric Explorer panelen is megkeresheti a Azure Portal.

![Service Fabric erőforrás panelje a Service Fabric Explorer megjelenítésével](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Ezután távolítsa el a fürt tanúsítványát a helyi tárolóból. Listába sorolja a telepített tanúsítványokat a fürt ujjlenyomatának megkereséhez:

```powershell
Get-ChildItem Cert:\CurrentUser\My\
```

Ezután távolítsa el a tanúsítványt:

```powershell
Get-ChildItem Cert:\CurrentUser\My\{THUMBPRINT} | Remove-Item
```

## <a name="next-steps"></a>Következő lépések

Az egyéni Azure-fürtsablonok Service Fabric lásd:

> [!div class="nextstepaction"]
> [Fürtsablon Service Fabric Resource Manager létrehozása](service-fabric-cluster-creation-create-template.md)
