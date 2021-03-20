---
title: Azure rövid útmutató – Azure Key Vault és titkos kód létrehozása Azure Resource Manager sablon használatával | Microsoft Docs
description: Gyors útmutató, amely bemutatja, hogyan hozhatók létre Azure Key vaultok, és hogyan adhat hozzá titkokat a tárakhoz Azure Resource Manager sablon használatával.
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
ms.openlocfilehash: 1cbe5f986ca36ecc3b45cf4bb7ecffa7067a27bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936617"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Gyors útmutató: Azure Key Vault titkos kód beállítása és beolvasása ARM-sablon használatával

A [Azure Key Vault](../general/overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a titkok számára, például kulcsokat, jelszavakat, tanúsítványokat és egyéb titkokat. Ez a rövid útmutató egy Azure Resource Manager-sablon (ARM-sablon) üzembe helyezésének folyamatát tárgyalja egy kulcstartó és egy titkos kulcs létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez:

* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* A sablonnak szüksége van az ÖN Azure AD-felhasználói objektumazonosítójára az engedélyek konfigurálásához. Az alábbi eljárás az objektumazonosító (GUID) beolvasása.

    1. Futtassa a következő Azure PowerShell vagy Azure CLI-parancsot a **kipróbálás** lehetőség kiválasztásával, majd illessze be a szkriptet a rendszerhéj ablaktáblába. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés** lehetőséget.

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

    2. Jegyezze fel az objektum AZONOSÍTÓját. Ennek a rövid útmutatónak a következő szakaszában kell megadnia.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-key-vault-create/) közül származik.

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

Két Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. kulcstartó/**](/azure/templates/microsoft.keyvault/vaults)tárolók: hozzon létre egy Azure Key vaultot.
* [**Microsoft. kulcstartó/tárolók/titkok**](/azure/templates/microsoft.keyvault/vaults/secrets): hozzon létre egy Key Vault-titkot.

További Azure Key Vault-sablonok találhatók az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és egy titkos kulcsot.

    [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    ![ARM-sablon, Key Vault integráció, portál üzembe helyezése](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Ha meg van adva, az alapértelmezett érték használatával hozza létre a kulcstartót és a titkos kulcsot.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK** gombra.
    * **Hely**: válasszon ki egy helyet. Például: **USA középső régiója**.
    * **Key Vault neve**: adja meg a Key Vault nevét, amelynek globálisan egyedinek kell lennie a. Vault.Azure.net névtérben. A telepítés ellenőrzésekor a következő szakaszban kell megadnia a nevet.
    * **Bérlő azonosítója**: a sablon függvény automatikusan lekéri a bérlő azonosítóját. Ne módosítsa az alapértelmezett értéket.
    * **Ad felhasználói azonosító**: adja meg az [előfeltételekből](#prerequisites)beolvasott Azure ad felhasználói objektum azonosítóját.
    * **Titkos kód neve**: adja meg a Key vaultban tárolt titok nevét. Például: **AdminPassword**.
    * **Titkos érték**: adja meg a titkos értéket. Ha jelszót tárol, azt javasoljuk, hogy használja az előfeltételekben létrehozott generált jelszót.
    * **Elfogadom a fenti használati feltételeket**: Válassza ezt.
3. Válassza a **Beszerzés** lehetőséget. A Key Vault sikeres üzembe helyezését követően értesítést kap:

    ![ARM-sablon, Key Vault integráció, portál-értesítés üzembe helyezése](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Az Azure Portalon helyezhető üzembe a sablon. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal segítségével megtekintheti a kulcstartót és a titkos kulcsot, vagy a következő Azure CLI-vagy Azure PowerShell-szkripttel listázhatja a létrehozott titkos kulcsot.

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

A kimenet a következőhöz hasonlóan néz ki:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

![Képernyőkép: a portál érvényesítési kimenetének üzembe helyezése a CLI-ben.](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![ARM-sablon, Key Vault integráció, portál-ellenőrzési kimenet üzembe helyezése](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell használatával:

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

Ebben a rövid útmutatóban létrehozott egy kulcstartót és egy titkos kulcsot egy ARM-sablonnal, és ellenőrizte az üzembe helyezést. Ha többet szeretne megtudni a Key Vault és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- További információ az [Azure Resource Managerről](../../azure-resource-manager/management/overview.md)
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)
