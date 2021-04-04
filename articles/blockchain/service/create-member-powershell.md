---
title: Azure Blockchain szolgáltatásbeli tag létrehozása – Azure PowerShell
description: Hozzon létre egy Azure Blockchain-szolgáltatási tagot egy Blockchain Consortium számára a Azure PowerShell használatával.
ms.date: 9/22/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: b57c44e79d599ab41b2c3356ee337811acdf639d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91948357"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Rövid útmutató: Azure Blockchain Service Blockchain-tag létrehozása Azure PowerShell használatával

Ebben a rövid útmutatóban egy új blockchain-tagot és-konzorciumot helyez üzembe az Azure Blockchain szolgáltatásban Azure PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Habár az az **. Blockchain** PowerShell-modul előzetes verzióban érhető el, a parancsmag használatával külön kell telepítenie az az PowerShell-modulból `Install-Module` . Miután a PowerShell-modul általánosan elérhetővé válik, az a PowerShell-modul kiadásainak részévé válik, és natív módon elérhető lesz a Azure Cloud Shellon belülről.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Erőforrás-szolgáltató regisztrálása

Ha első alkalommal használja az Azure Blockchain szolgáltatást, regisztrálnia kell a **Microsoft. Blockchain** erőforrás-szolgáltatót.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Adott Azure-előfizetés kiválasztása

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon ki egy adott előfizetést a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Változók meghatározása

Az adatok többször is használhatók. Hozzon létre változókat az információk tárolásához.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy erőforráscsoportot a változóban `$resourceGroupName` megadott régióban található változó alapján `$location` .

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Blockchain-tag létrehozása

Az Azure Blockchain szolgáltatás tagja egy Blockchain-csomópont egy privát konzorcium Blockchain-hálózatában.
Egy tag kiépítés esetén létrehozhat vagy csatlakozhat konzorciumi hálózathoz. Legalább egy tagnak szüksége van egy konzorciumi hálózatra. A résztvevők által igényelt blockchain-tagok száma a forgatókönyvtől függ. A konzorcium résztvevői rendelkezhetnek egy vagy több blockchain-taggal, vagy megoszthatnak más résztvevőkkel rendelkező tagokat is. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md).

Több paramétert és tulajdonságot kell átadnia. Cserélje le a példában szereplő paramétereket az értékekre.

```azurepowershell-interactive
$passwd = Read-Host -Prompt 'Enter the members default transaction node password' -AsSecureString
$csPasswd = Read-Host -Prompt 'Enter  the consortium account password' -AsSecureString

$memberParams = @{
  Name = 'myblockchainmember'
  ResourceGroupName = $resourceGroupName
  Consortium = 'myconsortium'
  ConsortiumManagementAccountPassword = $csPasswd
  Location = $location
  Password = $passwd
  Protocol = 'Quorum'
  Sku = 'S0'
}
New-AzBlockchainMember @memberParams
```

| Paraméter | Leírás |
|---------|-------------|
| **ResourceGroupName** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai jönnek létre. Használja az előző szakaszban létrehozott erőforráscsoportot.
| **Név** | Egy egyedi név, amely az Azure Blockchain szolgáltatás Blockchain-tagját azonosítja. A nevet a rendszer a nyilvános végpont címeként használja. Például: `myblockchainmember.blockchain.azure.com`.
| **Hely** | Az az Azure-régió, ahol a blockchain-tag létrejött. Például: `westus2`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. Előfordulhat, hogy egyes régiókban nem érhetők el szolgáltatások. Az Azure Blockchain Data Manager a következő Azure-régiókban érhető el: USA keleti régiója és Nyugat-Európa.
| **Jelszó** | A tag alapértelmezett tranzakciós csomópontjának jelszava. Az alapszintű hitelesítéshez használja a jelszót az blockchain-tag alapértelmezett tranzakciós csomópontjának nyilvános végponthoz való csatlakozáskor.
| **Protokoll** | Blockchain protokoll. A _kvórum_ protokoll jelenleg támogatott.
| **Konzorcium** | A csatlakozáshoz vagy létrehozáshoz használandó konzorcium neve. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md).
| **ConsortiumManagementAccountPassword** | A Consortium-fiók jelszavát más néven a tag fiók jelszavaként kell megadni. A tag fiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A tag fiókja és a fiók jelszava a konzorciumok kezeléséhez.
| **SKU** | A rétegek típusa. A standard vagy a **B0** **S0** alapszintű. A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához _használja az alapszintű_ csomagot. Használja a _standard_ szintű üzemi szintű üzembe helyezést. A _standard_ szintet is érdemes használni, ha Blockchain Data Manager használ, vagy nagy mennyiségű privát tranzakciót küld. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott.

A blockchain-tag és a támogató erőforrások létrehozása körülbelül 10 percet vesz igénybe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használhatja a következő rövid útmutatóhoz vagy oktatóanyaghoz létrehozott blockchain tagot. Ha már nincs rá szükség, törölheti az erőforrásokat a gyors útmutatóhoz `myResourceGroup` létrehozott erőforráscsoport törlésével.

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a cikk hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain-szolgáltatási tagot és egy új konzorciumot. Próbálja ki a következő rövid útmutatót a Ethereum készült Azure Blockchain Development Kit használatával az Azure Blockchain-szolgáltatáshoz való csatlakoztatáshoz.

> [!div class="nextstepaction"]
> [A Visual Studio Code használata az Azure Blockchain szolgáltatáshoz való kapcsolódáshoz](connect-vscode.md)