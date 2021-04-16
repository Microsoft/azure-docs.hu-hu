---
title: Új Azure Blockchain Service létrehozása – Azure PowerShell
description: Hozzon létre Azure Blockchain Service egy tag a blokklánc konzorciumhoz a Azure PowerShell.
ms.reviewer: ravastra
ms.date: 9/22/2020
ms.topic: quickstart
ms.custom:
- references_regions
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9087bda9c1979a98f4bbf9f8343d012c4cf3098c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529624"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Rövid útmutató: Blokklánctag Azure Blockchain Service létrehozása a Azure PowerShell

Ebben a rövid útmutatóban egy új blokklánctagot és konzorciumot helyez üzembe a Azure Blockchain Service a Azure PowerShell.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az Az PowerShell modult, és csatlakoznia kell az Azure-fiókjához a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal. További információ az Az PowerShell-modul telepítéséről: [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Bár **az Az.Blockchain** PowerShell-modul előzetes verzióban érhető el, az Az PowerShell-modultól külön kell telepítenie a `Install-Module` parancsmag használatával. Amint ez a PowerShell-modul általánosan elérhetővé válik, a későbbi Az PowerShell-modulok kiadásának része lesz, és natív módon érhető el a Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Erőforrás-szolgáltató regisztrálása

Ha most használja először a Azure Blockchain szolgáltatást, regisztrálnia kell a **Microsoft.Blockchain** erőforrás-szolgáltatót.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Adott Azure-előfizetés kiválasztása

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat ki kell számlázni. Válasszon ki egy adott előfizetést a [Set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Változók meghatározása

Többször is több információt fog használni. Hozzon létre változókat az információk tárolására.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre [egy Azure-erőforráscsoportot](../../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

Az alábbi példa egy erőforráscsoportot hoz létre a változóban megadott régióban található változó `$resourceGroupName` neve `$location` alapján.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Blokklánctag létrehozása

A Azure Blockchain Service tagja egy blokklánccsomópont egy privát konzorciumi blokklánchálózatban.
Tag kiépítésekor konzorciumi hálózatot hozhat létre vagy csatlakozhat hozzá. Egy konzorciumhálózatnak legalább egy tagra van szüksége. A résztvevők számára szükséges blokklánctagok száma a forgatókönyvtől függ. A konzorciumi résztvevők egy vagy több blokklánctaggal is lehetnek, vagy megoszthatnak tagokat más résztvevőkkel. További információ a consortia projektről: Azure Blockchain Service [konzorcium.](consortium.md)

Számos paramétert és tulajdonságot kell átadnia. Cserélje le a példaparamétereket a saját értékeire.

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
| **ResourceGroupName** | Az erőforráscsoport neve, Azure Blockchain Service létre az erőforrásokat. Használja az előző szakaszban létrehozott erőforráscsoportot.
| **Név** | Egy egyedi név, amely azonosítja Azure Blockchain Service blokklánctagját. A nyilvános végpont címe a név. Például: `myblockchainmember.blockchain.azure.com`.
| **Hely** | Az Azure-régió, ahol a blokklánctag létrejön. Például: `westus2`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. Előfordulhat, hogy egyes régiókban a funkciók nem érhetők el. Azure Blockchain Data Manager azure-régiókban érhető el: az USA keleti régiója és Nyugat-Európa.
| **Jelszó** | A tag alapértelmezett tranzakciós csomópontjának jelszava. Alapszintű hitelesítéshez használja a jelszót, amikor a blokklánctag alapértelmezett tranzakciós csomópontjának nyilvános végpontjára csatlakozik.
| **Protokoll** | Blokklánc protokoll. A kvórum _protokoll_ jelenleg támogatott.
| **Konzorcium** | A csatlakozásra vagy létrehozásra használt konzorcium neve. További információ a consortia projektről: Azure Blockchain Service [konzorcium.](consortium.md)
| **ConsortiumManagementAccountPassword** | A konzorciumfiók jelszavát tagfiókjelszónak is nevezik. A tagfiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására használható. A konzorciumkezeléshez a tagfiókot és a tagfiók jelszavát használhatja.
| **Sku** | Réteg típusa. **Az S0** a standard, a **B0 pedig** az alapszintű. Az _Alapszintű csomag_ fejlesztési, tesztelési és koncepció igazolási célokra használható. Éles _környezetben a Standard_ szintet használja. Akkor is érdemes a _Standard_ szintet használni, ha Blockchain Data Manager nagy mennyiségű privát tranzakciót küld. A tarifacsomag alapszintű és standard szint közötti módosítása a tag létrehozása után nem támogatott.

A blokklánctag és a támogató erőforrások létrehozása körülbelül 10 percet vesz igénybe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használhatja a következő rövid útmutatóhoz vagy oktatóanyaghoz létrehozott blokklánctagot. Ha már nincs rá szükség, törölheti az erőforrásokat a rövid útmutatóhoz `myResourceGroup` létrehozott erőforráscsoport törlésével.

> [!CAUTION]
> Az alábbi példa törli a megadott erőforráscsoportot és az összes benne lévő erőforrást.
> Ha a cikk hatókörében nem található erőforrások a megadott erőforráscsoportban vannak, akkor azok is törlődnek.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Azure Blockchain Service egy új konzorciumot. Próbálja ki a következő rövid útmutatót, amely Azure Blockchain Ethereumhoz való fejlesztői készletet használja egy Azure Blockchain Service csatlakoztatáshoz.

> [!div class="nextstepaction"]
> [Az Visual Studio Code használata a Azure Blockchain Service](connect-vscode.md)
