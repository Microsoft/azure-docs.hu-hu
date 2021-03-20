---
title: 'Gyors útmutató: Azure-beli hatáskörébe tartozó fiók létrehozása Azure PowerShell/Azure CLI használatával (előzetes verzió)'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre Azure-beli hatáskörébe tartozó fiókot Azure PowerShell/Azure CLI használatával.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: 0698295688a4587a704e8cdba0a4796e8d1e6fcd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879999"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Gyors útmutató: Azure-beli hatáskörébe tartozó fiók létrehozása Azure PowerShell/Azure CLI használatával

> [!IMPORTANT]
> Az Azure-beli hatáskörébe jelenleg előzetes verzió érhető el. A [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak azok a további jogi feltételek, amelyek az Azure olyan szolgáltatásaira vonatkoznak, amelyek béta-, előzetes verziójú vagy egyéb módon még nem lettek nyilvánosan elérhetők.

Ebben a rövid útmutatóban létrehoz egy Azure-beli hatáskörébe tartozó fiókot Azure PowerShell/Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Az Azure-ba való bejelentkezéshez használt felhasználói fióknak a közreműködő vagy tulajdonos szerepkör tagjának, vagy az Azure-előfizetés rendszergazdájának kell lennie.

* Saját [Azure Active Directory-bérlő](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Telepítse a Azure PowerShell vagy az Azure CLI-t az ügyfélszámítógépen a következő sablon üzembe helyezéséhez: [parancssori telepítés](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="configure-your-subscription"></a>Az előfizetés konfigurálása

Ha szükséges, kövesse az alábbi lépéseket az előfizetés konfigurálásához, hogy az Azure-beli hatáskörébe az előfizetésében fusson:

   1. A Azure Portal keresse meg és válassza ki az **előfizetések** elemet.

   1. Az előfizetések listájából válassza ki a használni kívánt előfizetést. Az előfizetéshez rendszergazdai hozzáférési engedély szükséges.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="A Azure Portal-előfizetés kiválasztását bemutató képernyőkép.":::

   1. Az előfizetéséhez válassza az **erőforrás-szolgáltatók** lehetőséget. Az **erőforrás-szolgáltatók** ablaktáblán keresse meg és regisztrálja mind a három erőforrás-szolgáltatót: 
       1. **Microsoft. hatáskörébe**
       1. **Microsoft.Storage**
       1. **Microsoft. EventHub** 
      
      Ha nincsenek regisztrálva, regisztrálja azt a **regisztráció** lehetőség kiválasztásával.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Képernyőfelvétel: a Microsoft dot Azure-beli hatáskörébe tartozó erőforrás-szolgáltató regisztrálása a Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Azure-beli hatáskörébe tartozó fiók példányának létrehozása

1. Jelentkezzen be az Azure-beli hitelesítő adataival

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

1. Hozzon létre egy erőforráscsoportot a hatáskörébe tartozó fiókhoz. Ha már rendelkezik ilyennel, kihagyhatja ezt a lépést:

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

1. Hozzon létre egy hatáskörébe tartozó sablonfájlt, például: `purviewtemplate.json` . Frissítheti `name` , `location` és `capacity` ( `4` vagy `16` ):

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

1. A hatáskörébe tartozó sablon üzembe helyezése

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

1. Az üzembehelyezési parancs az eredményeket adja vissza. Ellenőrizze, `ProvisioningState` hogy az üzembe helyezés sikeres volt-e.
    
## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure-beli hatáskörébe tartozó fiókot.

A következő cikkből megtudhatja, hogyan engedélyezheti a felhasználók számára az Azure hatáskörébe tartozó fiók elérését. 

> [!div class="nextstepaction"]
> [Felhasználók hozzáadása az Azure-beli hatáskörébe-fiókhoz](catalog-permissions.md)