---
title: Service Fabric-fürt létrehozása Azure Resource Manager sablon használatával
description: Ebben a rövid útmutatóban egy Azure Service Fabric test-fürtöt hoz létre Azure Resource Manager sablon használatával.
author: erikadoyle
ms.service: service-fabric
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: edoyle
ms.date: 07/29/2020
ms.openlocfilehash: 359b527733ee8eebf7e1e7d12c40a0c74ec1c9bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460303"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-arm-template"></a>Rövid útmutató: Service Fabric-fürt létrehozása ARM-sablonnal

Az Azure Service Fabric egy elosztott rendszerplatform, amely megkönnyíti a skálázható és megbízható mikroszolgáltatások és tárolók csomagolását, üzembe helyezését és kezelését. A Service Fabric- *fürt* olyan virtuális gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy Service Fabric tesztelési fürtöt az Azure-ban egy Azure Resource Manager sablon (ARM-sablon) használatával.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ez az öt csomópontos Windows-fürt egy önaláírt tanúsítvánnyal van ellátva, ezért csak az éles számítási feladatokhoz (nem pedig éles számítási feladatokhoz) készült. A sablon üzembe helyezéséhez Azure PowerShell fogunk használni. A Azure PowerShellon kívül használhatja a Azure Portal, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-portal.md).

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-secure-cluster-5-node-1-nodetype%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Service Fabric SDK és PowerShell-modulok telepítése

A rövid útmutató elvégzéséhez a következőket kell tennie:

* Telepítse a [Service FABRIC SDK-t és a PowerShell-modult](service-fabric-get-started.md).

* Telepítse a [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="download-the-sample-template-and-certificate-helper-script"></a>A sablon és a tanúsítvány segítő parancsfájl letöltése

A [Azure Resource Manager Gyorsindítás sablonok](https://github.com/Azure/azure-quickstart-templates) tárházának klónozása vagy letöltése. Másik lehetőségként másolja le helyileg a következő fájlokat, amelyeket a *Service-Fabric-Secure-cluster-5-Node-1-NodeType* mappából fogunk használni:

* [New-ServiceFabricClusterCertificate.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/scripts/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy.jsbekapcsolva](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.jsbekapcsolva](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és jelölje ki a Service Fabric-fürt létrehozásához használni kívánt előfizetést.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Key Vault tárolt önaláírt tanúsítvány létrehozása

A Service Fabric X. 509 tanúsítványokat használ a [fürt biztonságossá](./service-fabric-cluster-security.md) tételéhez és az alkalmazás biztonsági funkcióinak biztosításához, valamint [Key Vault](../key-vault/general/overview.md) a tanúsítványok kezeléséhez. A fürt sikeres létrehozásához a fürtnek a csomópontok közötti kommunikációt lehetővé tevő tanúsítványra van szüksége. A rövid útmutató tesztelési fürt létrehozásához hozzon létre egy önaláírt tanúsítványt a fürt hitelesítéséhez. Az éles számítási feladatokhoz olyan tanúsítványokra van szükség, amelyek megfelelően konfigurált Windows Server Certificate Service-szolgáltatással vagy egy jóváhagyott hitelesítésszolgáltatótól (CA) lettek létrehozva.

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

A szkript felszólítja az alábbiakra (Ügyeljen rá, hogy módosítsa a *CertDNSName* és a *KeyVaultName* az alábbi példák alapján):

* **Jelszó:** Jelszó! 1
* **CertDNSName:** *sfquickstart*. southcentralus.cloudapp.Azure.com
* **KeyVaultName:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

Befejezésekor a parancsfájl megadja a sablon üzembe helyezéséhez szükséges paramétereket. Ügyeljen arra, hogy ezeket a következő változókban tárolja, mivel ezek a fürt sablonjának telepítéséhez szükségesek:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/) közül származik. A cikk sablonja túl hosszú ahhoz, hogy megjelenjen itt. A sablon megtekintéséhez tekintse meg a [azuredeploy.js](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json) fájlt.

Több Azure-erőforrás van definiálva a sablonban:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/nyilvános IP](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Microsoft. ServiceFabric/fürtök](/azure/templates/microsoft.servicefabric/clusters)

Az Azure Service Fabric-hoz kapcsolódó további sablonokért tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric).

### <a name="customize-the-parameters-file"></a>A paraméterek fájljának testreszabása

Nyissa meg *azuredeploy.parameters.jsa* paraméter értékeit, és szerkessze a következőt:

* a **clusterName** megegyezik a *CertDNSName* megadott értékkel a fürt tanúsítványának létrehozásakor.
* a **adminUserName** az alapértelmezett *Gen-Unique* tokentől eltérő érték.
* a **adminPassword** értéke nem az alapértelmezett *Gen-Password* token
* a **certificateThumbprint**, a **SourceVaultResourceId** és a **certificateUrlValue** minden üres karakterlánc ( `""` )

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

Tárolja az ARM-sablon és a paraméter fájljainak elérési útját változókban, majd telepítse a sablont.

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

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Miután az üzembe helyezés befejeződött, keresse meg a `managementEndpoint` kimenetben található értéket, és nyissa meg a webböngészőben a következőt: a fürt [Service Fabric Explorerban](./service-fabric-visualizing-your-cluster.md)való megtekintéséhez.

![Új fürtöt megjelenítő Service Fabric Explorer](./media/quickstart-cluster-template/service-fabric-explorer.png)

A Service Fabric Explorer végpontot a Service Explorer erőforrás paneljéről is megkeresheti Azure Portalban.

![Service Fabric Explorer végpontot mutató Service Fabric erőforrás panel](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli az erőforráscsoport erőforrásait.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Ezután távolítsa el a fürt tanúsítványát a helyi tárolóból. Telepített tanúsítványok listázása a fürt ujjlenyomatának megkereséséhez:

```powershell
Get-ChildItem Cert:\CurrentUser\My\
```

Ezután távolítsa el a tanúsítványt:

```powershell
Get-ChildItem Cert:\CurrentUser\My\{THUMBPRINT} | Remove-Item
```

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan hozhat létre egyéni Azure Service Fabric-fürtöt, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Service Fabric fürterőforrás-kezelő sablon létrehozása](service-fabric-cluster-creation-create-template.md)
