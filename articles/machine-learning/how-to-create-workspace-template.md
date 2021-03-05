---
title: Munkaterület létrehozása Azure Resource Manager sablonnal
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre egy új Azure Machine Learning-munkaterületet egy Azure Resource Manager sablon használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli, devx-track-azurepowershell
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.openlocfilehash: f8ceacf88968d884de666b764c1037db9e63450f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214915"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Munkaterületek létrehozása Azure Machine Learninghez Azure Resource Manager sablon használatával


<br>

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning munkaterületet Azure Resource Manager sablonok használatával. A Resource Manager-sablonok segítségével egyszerűen hozhat létre erőforrásokat egyetlen, koordinált műveletként. A sablon egy JSON-dokumentum, amely meghatározza a központi telepítéshez szükséges erőforrásokat. Emellett telepítési paramétereket is megadhat. A paraméterek a sablon használatakor a bemeneti értékek biztosítására szolgálnak.

További információ: [alkalmazások központi telepítése Azure Resource Manager sablonnal](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha még nem rendelkezik ilyennel, próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Ha a parancssori felületről szeretne sablont használni, [Azure PowerShell](/powershell/azure/?view=azps-1.2.0) vagy az [Azure CLI](/cli/azure/install-azure-cli)-t kell használnia.

* Egyes esetekben támogatási jegyet kell megnyitni. Ezek a forgatókönyvek a következők:

    * __Private Link-kompatibilis, felhasználó által felügyelt kulccsal rendelkező munkaterület__
    * __Azure Container Registry a virtuális hálózat mögötti munkaterülethez__

    További információ: a [kvóták kezelése és növelése](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Korlátozások

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="workspace-resource-manager-template"></a>Munkaterület Resource Manager-sablon

Az ebben a dokumentumban használt Azure Resource Manager sablon az Azure Gyorsindítás sablonok GitHub-tárházának [201-Machine-learning-Advanced](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json) könyvtárában található.

Ez a sablon a következő Azure-szolgáltatásokat hozza létre:

* Azure Storage-tárfiók neve
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-munkaterület

Az erőforráscsoport az a tároló, amely a szolgáltatásokat tárolja. A Azure Machine Learning munkaterület különböző szolgáltatásokat igényel.

A példa sablon két **kötelező** paraméterrel rendelkezik:

* A **hely** , ahol az erőforrások létre lesznek hozva.

    A sablon a legtöbb erőforráshoz kiválasztott helyet fogja használni. A kivétel a Application Insights szolgáltatás, amely nem érhető el a többi szolgáltatás összes helyén. Ha olyan helyet választ, ahol nem érhető el, a szolgáltatás az USA déli középső régiójában lesz létrehozva.

* A **workspaceName**, amely a Azure Machine learning munkaterület rövid neve.

    > [!NOTE]
    > A munkaterület neve megkülönbözteti a kis-és nagybetűket.

    A többi szolgáltatás neve véletlenszerűen jön létre.

> [!TIP]
> Míg a dokumentumhoz társított sablon új Azure Container Registry hoz létre, a tároló-beállításjegyzék létrehozása nélkül is létrehozhat egy új munkaterületet. A rendszer létrehoz egy tároló-beállításjegyzéket igénylő művelet végrehajtásakor. Például egy modell betanítása vagy üzembe helyezése.
>
> Egy meglévő tároló beállításjegyzék-vagy Storage-fiókra is hivatkozhat a Azure Resource Manager sablonban, ahelyett, hogy újat hozna létre. Ilyenkor [felügyelt identitást](how-to-use-managed-identities.md) (előzetes verzió) kell használnia, vagy engedélyeznie kell a tároló-beállításjegyzék [rendszergazdai fiókját](../container-registry/container-registry-authentication.md#admin-account) .

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

A sablonokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Azure Resource Manager sablonok szerzője](../azure-resource-manager/templates/template-syntax.md)
* [Alkalmazás üzembe helyezése Azure Resource Manager-sablonokkal](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft. MachineLearningServices erőforrástípusok](/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezéséhez létre kell hoznia egy erőforráscsoportot.

Ha inkább a grafikus felhasználói felületet használja, tekintse meg a [Azure Portal](#use-the-azure-portal) szakaszt.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

Az erőforráscsoport sikeres létrehozása után telepítse a sablont a következő paranccsal:

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

Alapértelmezés szerint a sablon részeként létrehozott összes erőforrás új. Lehetősége van azonban meglévő erőforrások használatára is. Ha további paramétereket biztosít a sablonhoz, használhatja a meglévő erőforrásokat. Ha például egy meglévő Storage-fiókot szeretne használni, állítsa a **storageAccountOption** értéket **meglévő** értékre, és adja meg a Storage-fiók nevét a **storageAccountName** paraméterben.

> [!IMPORTANT]
> Ha meglévő Azure Storage-fiókot szeretne használni, akkor nem lehet prémium szintű fiók (Premium_LRS és Premium_GRS). Emellett nem lehet hierarchikus névtér (Azure Data Lake Storage Gen2). Sem a Premium Storage, sem a hierarchikus névtér nem támogatott a munkaterület alapértelmezett Storage-fiókjával. A munkaterület _alapértelmezett_ Storage-fiókja nem támogatja a Premium Storage-t vagy a hierarchikus névtereket. A Premium Storage vagy a hierarchikus névtér _nem alapértelmezett Storage-_ fiókokkal használható.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>Titkosított munkaterület üzembe helyezése

Az alábbi példa bemutatja, hogyan hozhat létre egy munkaterületet három beállítással:

* Magas titkosítási beállítások engedélyezése a munkaterületen. Ez létrehoz egy új Cosmos DB példányt.
* A munkaterület titkosításának engedélyezése.
* Egy meglévő Azure Key Vault használ az ügyfél által felügyelt kulcsok lekéréséhez. Az ügyfél által felügyelt kulcsok használatával új Cosmos DB példány hozható létre a munkaterülethez.

    [!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

> [!IMPORTANT]
> Miután létrehozta a munkaterületet, nem módosíthatja a bizalmas adatok, a titkosítás, a kulcstároló-azonosító vagy a kulcs-azonosítók beállításait. Az értékek módosításához új munkaterületet kell létrehoznia az új értékekkel.

További információ: [titkosítás a REST](concept-data-encryption.md#encryption-at-rest)-ben.

> [!IMPORTANT]
> A sablon használata előtt néhány speciális követelménynek meg kell felelnie az előfizetésnek:
> * A titkosítási kulcsot tartalmazó meglévő Azure Key Vaultnak kell lennie.
> * A Azure Key Vaultnak ugyanabban a régióban kell lennie, ahol létre kívánja hozni a Azure Machine Learning munkaterületet.
> * Meg kell adnia a Azure Key Vault AZONOSÍTÓját és a titkosítási kulcs URI-JÁT.

 A `cmk_keyvault` sablonhoz szükséges (Key Vault) és a `resource_cmk_uri` (kulcs URI) paraméterek értékeinek lekéréséhez kövesse az alábbi lépéseket:    

1. A Key Vault-azonosító beszerzéséhez használja a következő parancsot:  

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault show --name <keyvault-name> --query 'id' --output tsv   
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'    
    ``` 
    --- 

    A parancs a következőhöz hasonló értéket ad vissza: `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>` .  

1. Az ügyfél által felügyelt kulcs URI azonosítójának megszerzéséhez használja a következő parancsot:    

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv  
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>' 
    ``` 
    --- 

    A parancs a következőhöz hasonló értéket ad vissza: `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` . 

> [!IMPORTANT]  
> Miután létrehozta a munkaterületet, nem módosíthatja a bizalmas adatok, a titkosítás, a kulcstároló-azonosító vagy a kulcs-azonosítók beállításait. Az értékek módosításához új munkaterületet kell létrehoznia az új értékekkel.

Az ügyfél által felügyelt kulcsok használatának engedélyezéséhez állítsa be a következő paramétereket a sablon telepítésekor:

* **Encryption_status** **engedélyezett**.
* **cmk_keyvault** az `cmk_keyvault` előző lépések során beszerzett értékre.
* **resource_cmk_uri** az `resource_cmk_uri` előző lépések során beszerzett értékre.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

Ügyfél által felügyelt kulcs használatakor Azure Machine Learning létrehoz egy másodlagos erőforráscsoportot, amely tartalmazza a Cosmos DB példányt. További információ: [titkosítás a REST-Cosmos DBon](concept-data-encryption.md#encryption-at-rest).

Az adatai számára további konfigurációt adhat meg, ha a **confidential_data** paramétert True ( **igaz**) értékre állítja. Ezzel a következő műveleteket hajtja végre:

* Elindítja a Azure Machine Learning számítási fürtök helyi lemezeinek titkosítását, így még nem hozott létre korábbi fürtöket az előfizetésében. Ha korábban már létrehozott egy fürtöt az előfizetésben, nyisson meg egy támogatási jegyet, hogy a számítási fürtökön engedélyezve legyen a lemez titkosítása.
* A helyi kaparós lemez tisztítása a futtatások között.
* A Key Vault használatával biztonságosan továbbíthatja a Storage-fiók, a Container Registry és az SSH-fiók hitelesítő adatait a végrehajtási rétegből a számítási fürtökbe.
* Engedélyezi az IP-szűrést annak érdekében, hogy a mögöttes batch-készletek ne legyenek meghívva a AzureMachineLearningService-től eltérő külső szolgáltatásokkal.

    > [!IMPORTANT]
    > Miután létrehozta a munkaterületet, nem módosíthatja a bizalmas adatok, a titkosítás, a kulcstároló-azonosító vagy a kulcs-azonosítók beállításait. Az értékek módosításához új munkaterületet kell létrehoznia az új értékekkel.

  További információ: [titkosítás a REST](concept-data-encryption.md#encryption-at-rest)-ben.

## <a name="deploy-workspace-behind-a-virtual-network"></a>Munkaterület üzembe helyezése virtuális hálózat mögött

`vnetOption`Ha a paraméter értékét a vagy a értékre állítja `new` `existing` , akkor létrehozhat egy virtuális hálózat mögötti munkaterület által használt erőforrásokat.

> [!IMPORTANT]
> A Container Registry esetében csak a prémium SKU támogatott.

> [!IMPORTANT]
> Application Insights nem támogatja a virtuális hálózat mögötti üzembe helyezést.

### <a name="only-deploy-workspace-behind-private-endpoint"></a>Csak a munkaterület üzembe helyezése privát végpont mögött

Ha a társított erőforrások nem egy virtuális hálózat mögött találhatók, a **privateEndpointType** paramétert beállíthatja úgy, hogy `AutoAproval` `ManualApproval` a munkaterületet egy privát végpont mögött helyezze üzembe. Ezt az új és a meglévő munkaterületek esetében is megteheti. Meglévő munkaterület frissítésekor adja meg a sablon paramétereit a meglévő munkaterületről származó információkkal.

> [!IMPORTANT]
> A privát hivatkozást tartalmazó Azure Machine Learning munkaterület nem érhető el a Azure Government-régiókban vagy az Azure China 21Vianet-régiókban.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>Új virtuális hálózat használata

Egy új virtuális hálózat mögötti erőforrás üzembe helyezéséhez állítsa a **vnetOption** **új** értékre a megfelelő erőforráshoz tartozó virtuális hálózati beállításokkal együtt. Az alábbi telepítés azt mutatja be, hogyan helyezhet üzembe egy munkaterületet a Storage-fiók erőforrásával egy új virtuális hálózat mögött.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

Azt is megteheti, hogy a virtuális hálózat mögött több vagy minden függő erőforrást is üzembe helyez.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>Meglévő virtuális hálózati & erőforrások használata

A meglévő társított erőforrásokkal rendelkező munkaterületek üzembe helyezéséhez a **vnetOption** paramétert a **meglévő** alhálózati paraméterekkel együtt kell beállítania. A központi telepítés **előtt** azonban minden erőforráshoz létre kell hoznia szolgáltatási végpontokat a virtuális hálózaton. Az új virtuális hálózati környezetekhez hasonlóan a virtuális hálózat mögött egy vagy több erőforrás is lehet.

> [!IMPORTANT]
> Az alhálózatnak `Microsoft.Storage` szolgáltatási végponttal kell rendelkeznie

> [!IMPORTANT]
> Az alhálózatok nem teszik lehetővé privát végpontok létrehozását. Tiltsa le a magánhálózati végpontot az alhálózat engedélyezéséhez.

1. Szolgáltatási végpontok engedélyezése az erőforrásokhoz.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. A munkaterület üzembe helyezése

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Kövesse az [erőforrások telepítése egyéni sablonból](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)című témakör lépéseit. Amikor megérkezik a __sablon kiválasztása__ képernyőre, válassza ki a **201-Machine-learning-Advanced** sablont a legördülő listából.
1. Válassza a sablon __kiválasztása__ lehetőséget a sablon használatához. A telepítési forgatókönyvtől függően adja meg a következő szükséges információkat és egyéb paramétereket.

   * Előfizetés: válassza ki az erőforrásokhoz használni kívánt Azure-előfizetést.
   * Erőforráscsoport: válasszon ki vagy hozzon létre egy erőforráscsoportot, amely tartalmazza a szolgáltatásokat.
   * Régió: válassza ki azt az Azure-régiót, ahol létre kívánja hozni az erőforrásokat.
   * Munkaterület neve: a létrehozandó Azure Machine Learning munkaterület nevét fogja használni. A munkaterület nevének 3 és 33 karakter közöttinek kell lennie. Csak alfanumerikus karaktereket és "-" karaktert tartalmazhat.
   * Hely: válassza ki azt a helyet, ahová létre kívánja hozni az erőforrásokat.
1. Válassza az __Áttekintés + létrehozás__ lehetőséget.
1. A __felülvizsgálat + létrehozás__ képernyőn fogadja el a felsorolt feltételeket és kikötéseket, majd válassza a __Létrehozás__ lehetőséget.

További információ: [erőforrások központi telepítése egyéni sablonból](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault hozzáférési szabályzat és Azure Resource Manager sablonok

Ha Azure Resource Manager sablonnal hozza létre a munkaterületet és a hozzá tartozó erőforrásokat (beleértve a Azure Key Vault), többször is. Például a sablon többszöri használata ugyanazzal a paraméterekkel, mint a folyamatos integráció és üzembe helyezési folyamat részeként.

A sablonokon keresztül a legtöbb erőforrás-létrehozási művelet idempotens, de Key Vault törli a hozzáférési házirendeket a sablon használatakor. A hozzáférési házirendek törlése megszakítja a hozzáférést a Key Vault az azt használó meglévő munkaterületekhez. Előfordulhat például, hogy a Azure Notebooks virtuális gép működőképességének leállítása/létrehozása sikertelen.  

A probléma elkerüléséhez a következő módszerek egyikét javasoljuk:

* A sablont ne telepítse többször ugyanarra a paraméterekre. Vagy törölje a meglévő erőforrásokat, mielőtt a sablon használatával újra létrehozza őket.

* Vizsgálja meg a Key Vault hozzáférési házirendeket, majd használja ezeket a házirendeket a `accessPolicies` sablon tulajdonságának beállításához. A hozzáférési szabályzatok megtekintéséhez használja az alábbi Azure CLI-parancsot:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    A sablon szakaszának használatával kapcsolatos további információkért `accessPolicies` tekintse meg a [AccessPolicyEntry objektum hivatkozását](/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Ellenőrizze, hogy a Key Vault erőforrás már létezik-e. Ha igen, ne hozza létre újra a sablonon keresztül. Ha például a meglévő Key Vault szeretné használni, ahelyett, hogy újat hozna létre, végezze el a következő módosításokat a sablonon:

    * **Adjon hozzá** egy olyan paramétert, amely egy meglévő Key Vault erőforrás azonosítóját fogadja el:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Távolítsa el** a Key Vault erőforrást létrehozó szakaszt:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Távolítsa el** a `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` sort a `dependsOn` munkaterület szakaszából. **Módosítsa** a `keyVault` munkaterület szakaszának bejegyzését is a `properties` paraméterre való hivatkozáshoz `keyVaultId` :

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    A módosítások után megadhatja a meglévő Key Vault erőforrás AZONOSÍTÓját a sablon futtatásakor. A sablon ezután újra felhasználja a Key Vault a `keyVault` munkaterület tulajdonságának az azonosítóra való beállításával.

    A Key Vault AZONOSÍTÓjának lekéréséhez hivatkozhat az eredeti sablon kimenetére, vagy használhatja az Azure CLI-t is. Az alábbi parancs egy példa arra, hogyan használhatja az Azure CLI-t a Key Vault erőforrás-azonosító lekéréséhez:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Ez a parancs az alábbi szöveghez hasonló értéket ad vissza:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

### <a name="virtual-network-not-linked-to-private-dns-zone"></a>A virtuális hálózat nem kapcsolódik a magánhálózati DNS-zónához

Ha privát végponttal hoz létre munkaterületet, a sablon létrehoz egy __privatelink.API.azureml.MS__ nevű saját DNS zónát. A rendszer automatikusan hozzáadja a __virtuális hálózati kapcsolatot__ ehhez a magánhálózati DNS-zónához. A hivatkozás csak az első munkaterülethez és egy erőforráscsoporthoz létrehozott privát végponthoz lesz hozzáadva. Ha egy másik virtuális hálózatot és munkaterületet hoz létre egy privát végponttal ugyanabban az erőforráscsoportban, előfordulhat, hogy a második virtuális hálózat nem kerül be a magánhálózati DNS-zónába.

A magánhálózati DNS-zónához már meglévő virtuális hálózati kapcsolatok megtekintéséhez használja az alábbi Azure CLI-parancsot:

```azurecli
az network private-dns link vnet list --zone-name privatelink.api.azureml.ms --resource-group myresourcegroup
```

Egy másik munkaterületet és privát végpontot tartalmazó virtuális hálózat hozzáadásához kövesse az alábbi lépéseket:

1. A hozzáadni kívánt hálózat virtuális hálózati AZONOSÍTÓjának megkereséséhez használja a következő parancsot:

    ```azurecli
    az network vnet show --name myvnet --resource-group myresourcegroup --query id
    ```
    
    Ez a parancs a "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet" értékhez hasonló értéket ad vissza. Mentse ezt az értéket, és használja a következő lépésben.

2. A privatelink.api.azureml.ms saját DNS zónához tartozó virtuális hálózati kapcsolat hozzáadásához használja az alábbi parancsot. A `--virtual-network` paraméterhez használja az előző parancs kimenetét:

    ```azurecli
    az network private-dns link vnet create --name mylinkname --registration-enabled true --resource-group myresourcegroup --virtual-network myvirtualnetworkid --zone-name privatelink.api.azureml.ms
    ```

## <a name="next-steps"></a>Következő lépések

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és Resource Manager-Rest APIokkal](../azure-resource-manager/templates/deploy-rest.md).
* [Azure-erőforráscsoportok létrehozása és üzembe helyezése a Visual Studióval](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
* [Az Azure Machine Learning kapcsolódó egyéb sablonokkal kapcsolatban lásd az Azure Gyorsindítás sablonok tárházát.](https://github.com/Azure/azure-quickstart-templates)