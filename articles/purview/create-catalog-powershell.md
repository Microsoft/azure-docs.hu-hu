---
title: 'Rövid útmutató: Azure Purview-fiók létrehozása a Azure PowerShell/Azure CLI (előzetes verzió) használatával'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Azure Purview-fiókot a Azure PowerShell/Azure CLI használatával.
author: hophanms
ms.author: hophan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-api
ms.openlocfilehash: 6266aedaec8f171a1a6ff3e0d15abdad0263767a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530875"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Rövid útmutató: Azure Purview-fiók létrehozása a Azure PowerShell/Azure CLI használatával

> [!IMPORTANT]
> Az Azure Purview jelenleg előzetes verzióban érhető el. A [Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziók kiegészítő használati feltételei további jogi feltételeket tartalmaznak, amelyek a bétaverzióban, előzetes verzióban vagy egyéb módon még nem elérhető Azure-funkciókra vonatkoznak.

Ebben a rövid útmutatóban egy Azure Purview-fiókot hoz létre a Azure PowerShell/Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Az Azure-ba való bejelentkezéshez használt felhasználói fióknak a közreműködői vagy tulajdonosi szerepkör vagy az Azure-előfizetés rendszergazdájának kell lennie.

* Saját [Azure Active Directory-bérlő](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Telepítse az Azure PowerShell vagy az Azure CLI-t az ügyfélszámítógépen a sablon üzembe [helyezéséhez: Parancssori üzembe helyezés](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="configure-your-subscription"></a>Az előfizetés konfigurálása

Ha szükséges, az alábbi lépésekkel konfigurálhatja előfizetését úgy, hogy engedélyezze az Azure Purview futtatását az előfizetésében:

   1. A Azure Portal keresse meg és válassza az **Előfizetések lehetőséget.**

   1. Az előfizetések listájából válassza ki a használni kívánt előfizetést. Rendszergazdai hozzáférési engedély szükséges az előfizetéshez.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Képernyőkép az előfizetés kiválasztásáról a Azure Portal.":::

   1. Az előfizetéséhez válassza az **Erőforrás-szolgáltatók lehetőséget.** Az **Erőforrás-szolgáltatók panelen** keresse meg és regisztrálja mindhárom erőforrás-szolgáltatót: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Ha nincsenek regisztrálva, regisztrálja a Regisztráció **lehetőség kiválasztásával.**

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="A Microsoft Dot Azure Purview erőforrás-szolgáltató regisztrálását bemutató képernyőkép a Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Azure Purview-fiókpéldány létrehozása

1. Bejelentkezés Azure-beli hitelesítő adataival

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Ha több Azure-előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Hozzon létre egy erőforráscsoportot a Purview-fiókjához. Ha már rendelkezik ilyen lépéssel, kihagyhatja ezt a lépést:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Hozzon létre egy Purview sablonfájlt, `purviewtemplate.json` például: . Frissítheti a `name` , , és ( vagy ) `location` `capacity` `4` `16` et:

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. A Purview sablon üzembe helyezése

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. Az üzembe helyezési parancs visszaadja az eredményeket. Nézze meg, `ProvisioningState` hogy sikeres volt-e az üzembe helyezés.
    
## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Purview-fiókot.

A következő cikk azt is bemutatja, hogyan engedélyezheti a felhasználóknak az Azure Purview-fiókhoz való hozzáférést. 

> [!div class="nextstepaction"]
> [Felhasználók hozzáadása az Azure Purview-fiókhoz](catalog-permissions.md)
