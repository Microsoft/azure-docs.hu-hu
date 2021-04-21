---
title: Azure rövid útmutató – Azure-kulcstartó és titkos kulcs létrehozása Azure Resource Manager sablon | Microsoft Docs
description: Rövid útmutató, amely bemutatja, hogyan hozhat létre Azure-kulcstartókat, és hogyan adhat hozzá titkos kulcsokat a Azure Resource Manager használatával.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 214c86eb7272c87e067b1d5f6df0b09ce9e7095c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814135"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Rövid útmutató: Titkos adat beállítása és lekérése Azure Key Vault arm-sablon használatával

[Azure Key Vault](../general/overview.md) egy felhőszolgáltatás, amely biztonságos tárolót biztosít a titkos kulcsok, például kulcsok, jelszavak, tanúsítványok és egyéb titkos kulcsok számára. Ez a rövid útmutató a kulcstartó és a titkos kulcs Azure Resource Manager sablon (ARM-sablon) üzembe helyezésének folyamatára összpontosít.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

A cikk befejezéséhez:

* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* A sablonnak szüksége van az ÖN Azure AD-felhasználói objektumazonosítójára az engedélyek konfigurálásához. Az alábbi eljárás az objektumazonosítót (GUID) kapja meg.

    1. Futtassa a Azure PowerShell vagy Azure CLI-parancsot a Kipróbálom gombra **kattintva,** majd illessze be a szkriptet a felület paneljére. A szkript beillesztéshez kattintson a jobb gombbal a rendszerhéjra, majd válassza a Beillesztés **lehetőséget.**

        # <a name="cli"></a>[Parancssori felület](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Írja le az objektumazonosítót. Erre a rövid útmutató következő szakaszában lesz szüksége.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-key-vault-create/) közül származik.

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

A sablonban két Azure-erőforrás van meghatározva:

* [**Microsoft.KeyVault/vaults:**](/azure/templates/microsoft.keyvault/vaults)Hozzon létre egy Azure-kulcstartót.
* [**Microsoft.KeyVault/vaults/secrets:**](/azure/templates/microsoft.keyvault/vaults/secrets)kulcstartókulcs létrehozása.

További Azure Key Vault mintákat az Azure gyorsindítási [sablonok között talál.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és egy titkos kulcsot.

    [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    ![ARM-sablon, Key Vault integráció, portál üzembe helyezése](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Ha nincs megadva, használja az alapértelmezett értéket a kulcstartó és a titkos kulcs létrehozásához.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport:** válassza az **Új létrehozása** lehetőséget, adjon egyedi nevet az erőforráscsoportnak, majd kattintson az **OK gombra.**
    * **Hely**: válasszon ki egy helyet. Például: **USA középső régiója**.
    * **Key Vault Név:** adja meg a kulcstartó nevét, amelynek globálisan egyedinek kell lennie a .vault.azure.net névtérben. Az üzembe helyezés ellenőrzésekor szüksége lesz a névre a következő szakaszban.
    * **Bérlőazonosító:** a sablon függvény automatikusan lekéri a bérlőazonosítót. Ne módosítsa az alapértelmezett értéket.
    * **Ad felhasználói azonosító:** adja meg az Előfeltételekből lekért Azure AD felhasználói [objektumazonosítót.](#prerequisites)
    * **Titkos kulcs neve:** adja meg a kulcstartóban tárolt titkos kulcs nevét. Például: **adminpassword.**
    * **Titkos érték:** adja meg a titkos értéket. Ha jelszót tárol, javasoljuk, hogy az Előfeltételek között létrehozott jelszót használja.
    * **Elfogadom a fenti használati feltételeket**: Válassza ezt.
3. Válassza a **Beszerzés** lehetőséget. A kulcstartó sikeres üzembe helyezése után értesítést kap:

    ![ARM-sablon, Key Vault integráció, portálértesítés üzembe helyezése](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Az Azure Portalon helyezhető üzembe a sablon. A Azure Portal mellett használhatja a Azure PowerShell, az Azure CLI-t és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../../azure-resource-manager/templates/deploy-powershell.md)

## <a name="review-deployed-resources"></a>Az üzembe helyezett erőforrások áttekintése

Használhatja a Azure Portal kulcstartó és a titkos kulcs ellenőrzésével, vagy használhatja a következő Azure CLI-t vagy Azure PowerShell szkriptet a létrehozott titkos kulcs listához.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

A kimenet a következőre hasonlít:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

![A portál üzembe helyezésének érvényesítési kimenetét a CLI-ről bemutató képernyőkép.](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![ARM-sablon, Key Vault integráció, portálérvényesítési kimenet üzembe helyezése](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót és egy titkos kulcsot egy ARM-sablonnal, és érvényesítette az üzembe helyezést. Ha többet szeretne megtudni a Key Vault és Azure Resource Manager, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](../general/overview.md)
- További információ az [Azure Resource Managerről](../../azure-resource-manager/management/overview.md)
- A biztonsági [Key Vault áttekintése](../general/security-features.md)
