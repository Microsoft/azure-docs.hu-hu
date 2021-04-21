---
title: Felügyelt identitások konfigurálása Azure App Configuration
description: Megtudhatja, hogyan működnek a felügyelt identitások a Azure App Configuration és hogyan konfigurálhatja a felügyelt identitásokat
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: e4fdff2515dde941b2e9037a21ad931ac27b6fef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764224"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Felügyelt identitások használata Azure App Configuration

Ez a témakör bemutatja, hogyan hozhat létre felügyelt identitást a Azure App Configuration. A felügyelt identitás Azure Active Directory (AAD) lehetővé teszi, Azure App Configuration más AAD által védett erőforrásokhoz, például a Azure Key Vault. Az identitást az Azure platform kezeli. Nem szükséges hozzá titkos kulcsok kiépítése vagy váltogatása. További információ az AAD-beli felügyelt identitásokkal kapcsolatban: [Azure-erőforrások felügyelt identitása.](../active-directory/managed-identities-azure-resources/overview.md)

Az alkalmazás kétféle identitástípust kaphat:

- A **rendszer által hozzárendelt identitás** a konfigurációs tárolóhoz van kötve. A rendszer törli a konfigurációs tároló törlésekor. Egy konfigurációs tároló csak egy rendszer által hozzárendelt identitással lehet.
- A **felhasználó által hozzárendelt identitás** egy különálló Azure-erőforrás, amely hozzárendelhető a konfigurációs tárolóhoz. A konfigurációs tárolók több felhasználó által hozzárendelt identitást is kaphatnak.

## <a name="adding-a-system-assigned-identity"></a>Rendszer által hozzárendelt identitás hozzáadása

Ha rendszer által App Configuration identitással hoz létre egy tárolót, további tulajdonságot kell beállítania az áruházon.

### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

Ha felügyelt identitást szeretne beállítani az Azure CLI használatával, használja az [az appconfig identity assign] parancsot egy meglévő konfigurációs tárolón. Az ebben a szakaszban található példák futtatására három lehetőség közül választhat:

- Használja [Azure Cloud Shell](../cloud-shell/overview.md) a Azure Portal.
- A beágyazott Azure Cloud Shell az egyes kódblokkok jobb felső sarkában található "Próbálja ki" gombbal.
- [Ha helyi CLI-konzolt](/cli/azure/install-azure-cli) szeretne használni, telepítse az Azure CLI legújabb verzióját (2.1-es vagy újabb verzió).

A következő lépések végigmennek egy App Configuration tároló létrehozásán és identitás hozzárendelésének folyamatán a CLI használatával:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login] paranccsal jelentkezzen be az Azure-ba. Használjon az Azure-előfizetéséhez társított fiókot:

    ```azurecli-interactive
    az login
    ```

1. Hozzon létre App Configuration tárolót a CLI használatával. További példák a CLI és a Azure App Configuration használatára: App Configuration [CLI-minták:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Futtassa [az az appconfig identity assign] parancsot a rendszer által hozzárendelt identitás létrehozásához ehhez a konfigurációs tárolóhoz:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás hozzáadása

Ha felhasználó App Configuration identitással szeretne létrehozni egy tárolót, létre kell hoznia az identitást, majd hozzá kell rendelnie az erőforrás-azonosítóját az áruházhoz.

### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

Ha felügyelt identitást szeretne beállítani az Azure CLI használatával, használja az [az appconfig identity assign] parancsot egy meglévő konfigurációs tárolón. Az ebben a szakaszban található példák futtatására három lehetőség közül választhat:

- Használja [Azure Cloud Shell](../cloud-shell/overview.md) a Azure Portal.
- A beágyazott Azure Cloud Shell a lenti kódblokkok jobb felső sarkában található "Próbálja ki" gombbal.
- [Ha helyi CLI-konzolt](/cli/azure/install-azure-cli) szeretne használni, telepítse az Azure CLI legújabb verzióját (2.0.31-es vagy újabb verzió).

A következő lépések végigkúton végigmennek egy felhasználó által hozzárendelt identitás és egy App Configuration tároló létrehozásán, majd az identitás tárolóhoz való hozzárendelésének folyamatán a CLI használatával:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login] paranccsal jelentkezzen be az Azure-ba. Használjon az Azure-előfizetéséhez társított fiókot:

    ```azurecli-interactive
    az login
    ```

1. Hozzon létre App Configuration tárolót a CLI használatával. További példák a CLI és a Azure App Configuration használatára: App Configuration [CLI-minták:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Hozzon létre egy nevű, felhasználó által hozzárendelt `myUserAssignedIdentity` identitást a CLI használatával.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    A parancs kimenetében jegyezze fel a tulajdonság `id` értékét.

1. Futtassa [az az appconfig identity assign] parancsot az új, felhasználó által hozzárendelt identitás ehhez a konfigurációs tárolóhoz való hozzárendeléséhez. Használja az előző lépésben feljegyzett tulajdonság `id` értékét.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Identitás eltávolítása

A rendszer által hozzárendelt identitás úgy távolítható el, ha letiltja a funkciót az [az appconfig identity remove](/cli/azure/appconfig/identity#az_appconfig_identity_remove) paranccsal az Azure CLI-ban. A felhasználó által hozzárendelt identitások egyenként távolíthatók el. A rendszer által hozzárendelt identitás ily módon való eltávolítása az AAD-ból is törli azt. Az alkalmazás-erőforrás törlésekor a rendszer automatikusan eltávolítja a rendszer által hozzárendelt identitásokat az AAD-ból.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [ASP.NET Core-alkalmazás létrehozása Azure App Configuration](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity#az_appconfig_identity_assign
[az login]: /cli/azure/reference-index#az_login
