---
title: Megbízható tároló Azure Media Services
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a megbízható tárolást a Azure Media Services számára, a megbízható tároláshoz a védett tárolók használatával, valamint tűzfal vagy VPN használata esetén biztosíthatja az Azure-szolgáltatások elérését egy Storage-fiókhoz.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 2/8/2021
ms.openlocfilehash: ee57a743d28dcb80eeff5e2060ec9fff436e888c
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963308"
---
# <a name="tutorial-media-services-trusted-storage"></a>Oktatóanyag: Media Services megbízható tároló

Ezen oktatóanyag segítségével elsajátíthatja a következőket:

> [!div class="checklist"]
> - A megbízható tár engedélyezése Azure Media Services számára
> - Felügyelt identitások használata a megbízható tároláshoz
> - Az Azure-szolgáltatások hozzáférésének biztosítása egy Storage-fiókhoz hálózati hozzáférés-vezérlés, például tűzfal vagy VPN használata esetén

A 2020-05-01 API-val engedélyezheti a megbízható tárterületet egy felügyelt identitás Media Services-fiókkal való társításával.

>[!NOTE]
>A megbízható tárterület csak az API-ban érhető el, és jelenleg nincs engedélyezve a Azure Portal.

A Media Services a rendszerhitelesítés használatával automatikusan hozzáférhet a Storage-fiókhoz. Media Services ellenőrzi, hogy a Media Services fiók és a Storage-fiók ugyanahhoz az előfizetéshez tartoznak-e. Azt is ellenőrzi, hogy a társítást hozzáadó felhasználó hozzáfér-e a Storage-fiókhoz Azure Resource Manager RBAC.

Ha azonban hálózati hozzáférés-vezérléssel szeretné biztonságossá tenni a Storage-fiókot, és engedélyezi a megbízható tárolást, a [felügyelt identitások](concept-managed-identities.md) hitelesítésére van szükség. Lehetővé teszi Media Services számára, hogy hozzáférjen a megbízható tároló-hozzáférésen keresztül tűzfallal vagy VNet-korlátozással konfigurált Storage-fiókhoz.

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Használja az 2020-05-01 API-t a Media Services összes kéréséhez.

A Media Services megbízható tárolójának létrehozásának általános lépései:

1. Hozzon létre egy erőforráscsoportot.
1. Tárfiók létrehozása.
1. Lekérdezi a Storage-fiókot, amíg készen nem áll. Ha a Storage-fiók elkészült, a kérelem visszaküldi az egyszerű szolgáltatásnév AZONOSÍTÓját.
1. A *tárolási blob adatközreműködői* szerepkör azonosítójának megkeresése.
1. Hívja meg az engedélyezési szolgáltatót, és adjon hozzá egy szerepkör-hozzárendelést.
1. Frissítse a Media Services-fiókot úgy, hogy a felügyelt identitás használatával hitelesítse magát a Storage-fiókban.
1. Törölje az erőforrásokat, ha nem szeretné tovább használni őket, és nem kell fizetnie rájuk.

<!-- Link to storage role contributor role access differences information -->

## <a name="prerequisites"></a>Előfeltételek

Az első lépésekhez Azure-előfizetés szükséges.  Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free/).

### <a name="get-your-tenant-id-and-subscription-id"></a>Bérlői azonosító és előfizetés-azonosító beszerzése

Ha nem tudja, hogyan kérheti le a bérlői azonosítót és az előfizetés-azonosítót, olvassa el a [bérlői azonosító megkeresése](how-to-set-azure-tenant.md) és [a bérlői azonosító megkeresése](how-to-set-azure-tenant.md)című témakört.

### <a name="create-a-service-principal-and-secret"></a>Egyszerű szolgáltatásnév és titkos kód létrehozása

Ha nem tudja, hogyan hozhat létre egy egyszerű szolgáltatást és titkos kulcsot, tekintse [meg a hitelesítő adatok Beszerzése Media Services API eléréséhez](access-api-howto.md)című témakört.

## <a name="use-a-rest-client"></a>REST-ügyfél használata

Ez a szkript egy REST-ügyféllel használható, például a Visual Studio Code Extensions szolgáltatásban elérhetővel.  A fejlesztési környezethez igazíthatja.

## <a name="set-initial-variables"></a>Kezdeti változók beállítása

A szkript ezen része egy REST-ügyfélben használható. A fejlesztési környezetben eltérő változók is használhatók.

```rest
### AAD details
@tenantId = your tenant ID
@servicePrincipalId = the service principal ID
@servicePrincipalSecret = the service principal secret

### AAD resources
@armResource = https%3A%2F%2Fmanagement.core.windows.net%2F
@graphResource = https%3A%2F%2Fgraph.windows.net%2F
@storageResource = https%3A%2F%2Fstorage.azure.com%2F

### Service endpoints
@armEndpoint = management.azure.com
@graphEndpoint = graph.windows.net
@aadEndpoint = login.microsoftonline.com

### ARM details
@subscription = your subscription id
@resourceGroup = the resource group you'll be creating
@storageName = the name of the storage you'll be creating
@accountName = the name of the account you'll be creating
@resourceLocation = East US (or the location that works best for your region)
```

## <a name="get-a-token-for-azure-resource-manager"></a>Azure Resource Manager jogkivonat beszerzése

```rest
// @name getArmToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{armResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-a-token-for-the-graph-api"></a>Token beszerzése a Graph APIhoz

A szkript ezen része egy REST-ügyfélben használható. A fejlesztési környezetben eltérő változók is használhatók.

```rest
// @name getGraphToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{graphResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="get-the-service-principal-details"></a>Az egyszerű szolgáltatás részleteinek beolvasása

```rest
// @name getServicePrincipals
GET https://{{graphEndpoint}}/{{tenantId}}/servicePrincipals?$filter=appId%20eq%20'{{servicePrincipalId}}'&api-version=1.6
x-ms-client-request-id: cae3e4f7-17a0-476a-a05a-0dab934ba959
Authorization:  Bearer {{getGraphToken.response.body.access_token}}
```

## <a name="store-the-service-principal-id"></a>Az egyszerű szolgáltatás AZONOSÍTÓjának tárolása

```rest
@servicePrincipalObjectId = {{getServicePrincipals.response.body.value[0].objectId}}
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

```rest
// @name createResourceGroup
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}
    ?api-version=2016-09-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "location": "{{resourceLocation}}"
}
```

## <a name="create-storage-account"></a>Storage-fiók létrehozása

```rest
// @name createStorageAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
    }
}
```

## <a name="get-the-storage-account-status"></a>A Storage-fiók állapotának beolvasása

A Storage-fiók eltarthat egy darabig, így a kérés lekérdezi az állapotát.  Ismételje meg ezt a kérelmet, amíg a Storage-fiók készen nem áll.

```rest
// @name getStorageAccountStatus
GET {{createStorageAccount.response.headers.Location}}
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-the-storage-account-details"></a>A Storage-fiók adatainak beolvasása

Ha a Storage-fiók elkészült, szerezze be a Storage-fiók tulajdonságait.

```rest
// @name getStorageAccount
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="get-a-token-for-arm"></a>Az ARM-token beszerzése

```rest
// @name getStorageToken
POST https://{{aadEndpoint}}/{{tenantId}}/oauth2/token
Accept: application/json
Content-Type: application/x-www-form-urlencoded

resource={{storageResource}}&client_id={{servicePrincipalId}}&client_secret={{servicePrincipalSecret}}&grant_type=client_credentials
```

## <a name="create-a-media-services-account-with-a-system-assigned-managed-identity"></a>Media Services fiók létrehozása rendszer által hozzárendelt felügyelt identitással

```rest
// @name createMediaServicesAccount
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="get-the-storage-storage-blob-data-role-definition"></a>A Storage-tár blob-adatszerepkör-definíciójának beolvasása

```rest
// @name getStorageBlobDataContributorRoleDefinition
GET https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions?$filter=roleName%20eq%20%27Storage%20Blob%20Data%20Contributor%27&api-version=2015-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

### <a name="set-the-storage-role-assignment"></a>A tárolási szerepkör hozzárendelésének beállítása

A szerepkör-hozzárendelés azt mondja, hogy az Media Services-fiókhoz tartozó szolgáltatásnév rendelkezik a Storage szerepkör *Storage blob Adatközreműködőivel*.  Ez eltarthat egy darabig, és fontos, hogy várjon, vagy a Media Services fiók nem lesz megfelelően beállítva.

```rest
PUT https://management.azure.com/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleAssignments/{{$guid}}?api-version=2020-04-01-preview
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "roleDefinitionId": "/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}/providers/Microsoft.Authorization/roleDefinitions/{{getStorageBlobDataContributorRoleDefinition.response.body.value[0].name}}",
    "principalId": "{{createMediaServicesAccount.response.body.identity.principalId}}"
  }
}
```

## <a name="give-managed-identity-bypass-access-to-the-storage-account"></a>Felügyelt identitás megkerülési hozzáférésének biztosítása a Storage-fiókhoz

Ez a művelet megváltoztatja a rendszer által felügyelt identitásról a felügyelt identitásra való hozzáférést. Így a Storage-fiók tűzfalon keresztül férhet hozzá a Storage-fiókhoz, mivel az Azure-szolgáltatások az IP-hozzáférési szabályoktól (ACL) függetlenül hozzáférhetnek a Storage-fiókhoz is.

Ismét várjon, amíg a szerepkör hozzá lett rendelve a Storage-fiókhoz, vagy a Media Services fiók helytelenül lesz beállítva.

```rest
// @name setStorageAccountFirewall
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
    "sku": {
    "name": "Standard_GRS"
    },
    "kind": "StorageV2",
    "location": "{{resourceLocation}}",
    "properties": {
      "minimumTlsVersion": "TLS1_2",
      "networkAcls": {
        "bypass": "AzureServices",
        "virtualNetworkRules": [],
        "ipRules": [],
        "defaultAction": "Deny"
      }
    }
}
```

## <a name="update-the-media-services-account-to-use-the-managed-identity"></a>A Media Services fiók frissítése a felügyelt identitás használatára

Előfordulhat, hogy a kérést többször is el kell végezni, mert a tárolási szerepkör-hozzárendelés eltarthat néhány percig.

```rest
// @name updateMediaServicesAccountWithManagedStorageAuth
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
  "identity": {
      "type": "SystemAssigned"
  },
  "properties": {
    "storageAccounts": [
      {
        "id": "{{getStorageAccountStatus.response.body.id}}"
      }
    ],
    "storageAuthentication": "ManagedIdentity",
    "encryption": {
      "type": "SystemKey"
    }
  },
  "location": "{{resourceLocation}}"
}
```

## <a name="test-access"></a>Hozzáférés tesztelése

A hozzáférés teszteléséhez hozzon létre egy eszközt a Storage-fiókban.

```rest
// @name createAsset
PUT https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}/assets/testasset{{index}}withoutmi?api-version=2018-07-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
Content-Type: application/json; charset=utf-8

{
}
```

## <a name="delete-resources"></a>Erőforrások törlése

Ha nem szeretné megőrizni a létrehozott erőforrásokat, és továbbra is fizetnie kell rájuk, törölje őket.

```rest
### Clean up the Storage account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Storage/storageAccounts/{{storageName}}
    ?api-version=2019-06-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
DELETE https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}

### Clean up the Media Services account
GET https://{{armEndpoint}}/subscriptions/{{subscription}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaservices/{{accountName}}?api-version=2020-05-01
Authorization: Bearer {{getArmToken.response.body.access_token}}
```

## <a name="next-steps"></a>Következő lépések

[Eszköz létrehozása](asset-create-asset-how-to.md)
