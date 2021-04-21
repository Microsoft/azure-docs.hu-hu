---
title: Az Azure Resource Manager elérése Linux VM-beli, felhasználó által hozzárendelt felügyelt identitással
description: Oktatóanyag, amely végigvezeti az Azure Resource Manager Linux VM-beli, felhasználó által hozzárendelt felügyelt identitással való elérésének folyamatán.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2020
ms.author: barclayn
ROBOTS: NOINDEX,NOFOLLOW
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9c7555235283e892741234b74ddb80ce3a13051
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784712"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Oktatóanyag: Az Azure Resource Manager elérése Linux VM-beli, felhasználó által hozzárendelt felügyelt identitással

Ez az oktatóanyag ismerteti, hogyan hozhat létre felhasználó által hozzárendelt felügyelt identitást, és hogyan csatolhatja azt Linux rendszerű virtuális géphez, majd hogyan használhatja ezt az identitást az Azure Resource Manager API eléréséhez. Az Azure-erőforrások felügyelt identitásainak kezelését az Azure automatikusan végzi. Ezek segítségével anélkül végezhet hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban, hogy be kellene ágyaznia a hitelesítő adatokat a kódba. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt felügyelt identitás létrehozása
> * Felhasználó által hozzárendelt felügyelt identitás társítása Linux rendszerű virtuális géphez 
> * Hozzáférés engedélyezése a felhasználó által hozzárendelt felügyelt identitás számára az Azure Resource Manager erőforráscsoportjához 
> * Hozzáférési jogkivonat lekérése a felhasználó által hozzárendelt felügyelt identitás használatával, majd az Azure Resource Manager meghívása a jogkivonat használatával 

## <a name="prerequisites"></a>Előfeltételek

- A felügyelt identitások ismerete. Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](overview.md). 
- Egy Azure-fiók, [regisztráljon egy ingyenes fiókra.](https://azure.microsoft.com/free/)
- Szüksége lesz egy Linux rendszerű virtuális gépre is. Ha létre kell hoznia egy virtuális gépet ehhez az oktatóanyaghoz, kövesse [a Linux](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine) rendszerű virtuális gép létrehozása a következő Azure Portal
- A példaszk szkriptek futtatásához két lehetőség áll rendelkezésre:
    - Használja [a Azure Cloud Shell,](../../cloud-shell/overview.md)amelyet a kódblokkok jobb felső sarkában található **Try It (Próbálja** ki) gombbal nyithat meg.
    - Futtatassa helyileg a szkripteket az [Azure CLI](/cli/azure/install-azure-cli)legújabb verziójának telepítésével, majd jelentkezzen be az Azure-ba az az [login használatával.](/cli/azure/reference-index#az_login)

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az_identity_create) paranccsal. A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg. Ne felejtse el a `<RESOURCE GROUP>` és `<UAMI NAME>` paraméterek értékeit a saját értékeire cserélni:
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

A válasz tartalmazza az imént létrehozott, felhasználó által hozzárendelt felügyelt identitás részleteit, az alábbi példához hasonlóan. Jegyezze fel a felhasználó által hozzárendelt felügyelt identitás `id` értékét, mivel azt a következő lépésben használni fogja:

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-an-identity-to-your-linux-vm"></a>Identitás hozzárendelése Linux rendszerű virtuális géphez

A felhasználó által hozzárendelt felügyelt identitást az ügyfelek több Azure-erőforrás esetében is használhatják. Az alábbi parancsokkal rendelhet felhasználó által hozzárendelt felügyelt identitást egyetlen virtuális géphez. Ehhez használja az előző lépésben az `-IdentityID` paraméter esetében visszaadott `Id` tulajdonságot.

Rendelje hozzá a felhasználó által hozzárendelt felügyelt identitást a Linux rendszerű virtuális géphez [az az vm identity assign használatával.](/cli/azure/vm) Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. Ehhez használja az előző lépésben az `--identities` paraméterérték esetében visszaadott `id` tulajdonságot.

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-access-to-a-resource-group-in-azure-resource-manager"></a>Hozzáférés megadása egy erőforráscsoporthoz a Azure Resource Manager

Az Azure-erőforrások felügyelt identitásai segítségével a kód hozzáférési jogkivonatokat tud lekérni az olyan erőforrás API-k hitelesítéséhez, amelyek támogatják az Azure AD-hitelesítést. Ebben az oktatóanyagban a kódot az Azure Resource Manager API-jának eléréséhez használjuk.  

Ehhez azonban még engedélyeznie kell az identitás számára a hozzáférést az Azure Resource Manager erőforrásaihoz. Ebben az esetben arról az erőforráscsoportról van szó, amelyben a virtuális gép megtalálható. A környezetnek megfelelően frissítse a `<SUBSCRIPTION ID>` és `<RESOURCE GROUP>` paraméterek értékeit. Ezen kívül cserélje le a `<UAMI PRINCIPALID>` értékét az `az identity create` parancs által a [felhasználó által hozzárendelt felügyelt identitás létrehozását ismertető](#create-a-user-assigned-managed-identity) részben visszaadott `principalId` tulajdonságra:

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

A válasz tartalmazza az imént létrehozott szerepkör-hozzárendelés részleteit, az alábbi példához hasonlóan:

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Hozzáférési jogkivonat lekérése a VM identitásával, majd a Resource Manager meghívása a használatával 

Az oktatóanyag további részében a korábban létrehozott virtuális gépről dolgozunk.

A lépések elvégzéséhez szüksége lesz egy SSH-ügyfélre. Windows használata esetén használhatja a [Linux Windows alrendszerében](/windows/wsl/about) elérhető SSH-ügyfelet. 

1. Jelentkezzen be az Azure [Portalra.](https://portal.azure.com)
2. A portálon lépjen a **Virtuális gépek** szakaszra, lépjen a Linux rendszerű virtuális géphez, és az **Áttekintés** területen kattintson a **Csatlakozás** lehetőségre. Másolja ki sztringet a virtuális géphez való csatlakozáshoz.
3. Csatlakozzon a virtuális géphez a választott SSH-ügyféllel. Windows használata esetén használhatja a [Linux Windows alrendszerében](/windows/wsl/about) elérhető SSH-ügyfelet. Amennyiben segítségre van szüksége az SSH-ügyfél kulcsának konfigurálásához, [Az SSH-kulcsok és a Windows együttes használata az Azure-ban](~/articles/virtual-machines/linux/ssh-from-windows.md) vagy [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](~/articles/virtual-machines/linux/mac-create-ssh-keys.md) című cikkekben talál további információt.
4. A terminálablakban a CURL használatával küldjön kérést az Azure Instance Metadata szolgáltatás (IMDS) identitásvégpontjára egy hozzáférési jogkivonat lekérésére az Azure Resource Managerhez.  

   Az alábbi példában a hozzáférési jogkivonat beszerzésére irányuló CURL-kérés látható. Mindenképpen cserélje le a `<CLIENT ID>` értékét az `az identity create` parancs által a [felhasználó által hozzárendelt felügyelt identitás létrehozását ismertető](#create-a-user-assigned-managed-identity) részben visszaadott `clientId` tulajdonságra: 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Az `resource` paraméter értékének pontosan egyeznie kell az Azure AD által várt értékkel. A Resource Manager erőforrás-azonosítójának használatakor a záró perjelet is szerepeltetni kell az URI-ban. 
    
    A válasz tartalmazza az Azure Resource Manager eléréséhez szükséges hozzáférési jogkivonatot. 
    
    Példa a válaszra:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Az Azure Resource Managert a hozzáférési jogkivonattal érheti el, illetve azzal olvashatja annak az erőforráscsoportnak a tartalmát, amelyhez a felhasználó által hozzárendelt felügyelt identitás számára korábban hozzáférést adott. Cserélje le a `<SUBSCRIPTION ID>` és a `<RESOURCE GROUP>` értékét a korábban megadott értékekre, az `<ACCESS TOKEN>` értékét pedig az előző lépésben visszaadott jogkivonatra.

    > [!NOTE]
    > Az URL-cím megkülönbözteti a kis- és nagybetűket, ezért pontosan ugyanúgy adja meg, mint ahogy az erőforráscsoportot elnevezésekor, illetve ügyeljen a `resourceGroups` sztringben a nagy G betű használatára is.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    A válasz tartalmazza az adott erőforráscsoport adatait, az alábbi példához hasonlóan: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte, hogy hogyan hozhat létre felhasználó által hozzárendelt felügyelt identitást, majd csatolhatja egy Linux rendszerű virtuális géphez az Azure Resource Manager API elérése érdekében.  További információ az Azure Resource Managerről:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
