---
title: Felügyelt identitások konfigurálása az Azure-alkalmazás konfigurációjával
description: Ismerje meg, hogyan működnek a felügyelt identitások az Azure-alkalmazások konfigurációjában, és hogyan konfigurálhatja a felügyelt identitást
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: e6ced30e8c8c5eb60eea3806d9c8080df7c2a12d
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979589"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Felügyelt identitások használata az Azure-alkalmazások konfigurálásához

Ez a témakör bemutatja, hogyan hozhat létre felügyelt identitást az Azure app Configuration szolgáltatáshoz. Azure Active Directory (HRE) felügyelt identitása lehetővé teszi, hogy az Azure app Configuration könnyedén hozzáférhessen más HRE-védelemmel ellátott erőforrásokhoz, például a Azure Key Vaulthoz. Az identitást az Azure platform kezeli. Nincs szükség titkok kiépítésére vagy elforgatására. További információ a HRE felügyelt identitásokról: [felügyelt identitások az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).

Az alkalmazás két típusú identitást biztosíthat:

- A **rendszer által hozzárendelt identitás** a konfigurációs tárolóhoz van kötve. A rendszer törli, ha a konfigurációs tárolót törölték. A konfigurációs tárolónak csak egy rendszerhez rendelt identitása lehet.
- A **felhasználó által hozzárendelt identitás** egy önálló Azure-erőforrás, amelyet a konfigurációs tárolóhoz rendelhet hozzá. A konfigurációs tároló több felhasználó által hozzárendelt identitással is rendelkezhet.

## <a name="adding-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása

A rendszer által hozzárendelt identitással rendelkező alkalmazás-konfigurációs tárolók létrehozásához további tulajdonságot kell beállítani az áruházban.

### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

Felügyelt identitás Azure CLI használatával történő beállításához használja az az [appconfig Identity assign] parancsot egy meglévő konfigurációs tárolón. Ebben a szakaszban három lehetőség van a példák futtatására:

- Használja [Azure Cloud Shell](../cloud-shell/overview.md) a Azure Portal.
- A beágyazott Azure Cloud Shell az alábbi, az egyes kódrészletek jobb felső sarkában található "kipróbálás" gomb segítségével érheti el.
- [Telepítse az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli) (2,1 vagy újabb), ha inkább egy helyi CLI-konzolt szeretne használni.

Az alábbi lépések végigvezetik az alkalmazás konfigurációs tárolójának létrehozásán és identitás hozzárendelésének lépésein a parancssori felület használatával:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login] paranccsal jelentkezzen be az Azure-ba. Használja az Azure-előfizetéséhez társított fiókot:

    ```azurecli-interactive
    az login
    ```

1. Hozzon létre egy alkalmazás-konfigurációs tárolót a parancssori felület használatával. A CLI és az Azure-alkalmazások konfigurációjának használatával kapcsolatos további Példákért lásd az [app CONFIGURATION CLI-minták](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Futtassa az az [appconfig Identity assign] parancsot a rendszer által hozzárendelt identitás létrehozásához ehhez a konfigurációs tárolóhoz:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás hozzáadása

A felhasználó által hozzárendelt identitással rendelkező alkalmazás-konfigurációs tároló létrehozásához létre kell hoznia az identitást, majd hozzá kell rendelnie az erőforrás-azonosítót a tárolóhoz.

### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

Felügyelt identitás Azure CLI használatával történő beállításához használja az az [appconfig Identity assign] parancsot egy meglévő konfigurációs tárolón. Ebben a szakaszban három lehetőség van a példák futtatására:

- Használja [Azure Cloud Shell](../cloud-shell/overview.md) a Azure Portal.
- A beágyazott Azure Cloud Shell az alábbi, az egyes kódrészletek jobb felső sarkában található "kipróbálás" gomb segítségével érheti el.
- [Telepítse az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli) (2.0.31 vagy újabb), ha helyi CLI-konzolt szeretne használni.

A következő lépések végigvezetik a felhasználó által hozzárendelt identitás és az alkalmazás konfigurációs tárolójának létrehozásán, majd az identitásnak az áruházba való hozzárendelésével a parancssori felület használatával:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login] paranccsal jelentkezzen be az Azure-ba. Használja az Azure-előfizetéséhez társított fiókot:

    ```azurecli-interactive
    az login
    ```

1. Hozzon létre egy alkalmazás-konfigurációs tárolót a parancssori felület használatával. A CLI és az Azure-alkalmazások konfigurációjának használatával kapcsolatos további Példákért lásd az [app CONFIGURATION CLI-minták](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Hozzon létre egy felhasználó által hozzárendelt identitást `myUserAssignedIdentity` a parancssori felület használatával.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    A parancs kimenetében jegyezze fel a `id` tulajdonság értékét.

1. Futtassa az az [appconfig Identity assign] parancsot az új felhasználó által hozzárendelt identitás hozzárendeléséhez ehhez a konfigurációs tárolóhoz. Használja az `id` előző lépésben feljegyzett tulajdonság értékét.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Identitás eltávolítása

A rendszer által hozzárendelt identitás eltávolításához tiltsa le a szolgáltatást az Azure CLI az [appconfig Identity Remove](/cli/azure/appconfig/identity#az-appconfig-identity-remove) paranccsal. A felhasználó által hozzárendelt identitások egyenként eltávolíthatók. A rendszer által hozzárendelt identitások eltávolítása a HRE-ből is törölve lesz. A rendszer által hozzárendelt identitások is automatikusan törlődnek a HRE-ből az alkalmazás-erőforrás törlésekor.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [ASP.NET Core-alkalmazás létrehozása az Azure app Configurationvel](quickstart-aspnet-core-app.md)

[az appconfig Identity assign]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
