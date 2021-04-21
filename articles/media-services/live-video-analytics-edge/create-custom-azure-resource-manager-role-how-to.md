---
title: Egyéni szerepkör Azure Resource Manager és hozzárendelése szolgáltatásnévhez – Azure
description: Ez a cikk bemutatja, hogyan hozhat létre egyéni Azure Resource Manager szerepkört, és hogyan rendelhet hozzá szolgáltatásnévhez Live Video Analytics a IoT Edge Azure CLI használatával.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 6c33f6703522fc0b28237e22c16c96587467df40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788510"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Egyéni szerepkör Azure Resource Manager és hozzárendelése szolgáltatásnévhez

Live Video Analytics modulpéldányon IoT Edge egy aktív Azure Media Services, hogy megfelelően működjön. A modulon Live Video Analytics és IoT Edge Azure Media Service-fiók közötti kapcsolat az ikermodul tulajdonságainak egy készletével jött létre. Az ikertulajdonságok egyike egy szolgáltatásnév, amely lehetővé teszi [a](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) modulpéldány számára a kommunikációt, és aktiválja a szükséges műveleteket a Media Services fiókon. A peremeszközről való esetleges visszaélés és/vagy véletlen adatexpozíció minimalizálása érdekében a szolgáltatásnévnek a lehető legkevesebb jogosultsággal kell rendelkezik.

Ez a cikk bemutatja, hogyan hozhat létre egyéni Azure Resource Manager szerepkört a Azure Cloud Shell, amelyet aztán szolgáltatásnév létrehozására használ.

## <a name="prerequisites"></a>Előfeltételek  

A cikk előfeltételei a következők:

* Azure-előfizetés tulajdonosi előfizetéssel.
* Egy Azure Active Directory jogosultsággal rendelkezik egy alkalmazás létrehozásához és szolgáltatásnév szerepkörhöz való hozzárendeléséhez.

A legegyszerűbben a portálon ellenőrizheti, hogy rendelkezik-e megfelelő jogosultságokkal. Lásd: [Szükséges engedélyek ellenőrzése](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Áttekintés  

Az egyéni szerepkör létrehozásának és szolgáltatásnévvel való csatolásának lépéseit a következő sorrendben fogjuk átveszni:

1. Ha még nem rendelkezik Media Service-fiókkal, hozzon létre egyet.
1. Hozzon létre egy szolgáltatásnév.
1. Hozzon létre egy Azure Resource Manager szerepkört korlátozott jogosultságokkal.
1. "Korlátozza" a szolgáltatásnév jogosultságait a létrehozott egyéni szerepkör használatával.
1. Egy egyszerű teszt futtatásával ellenőrizze, hogy sikerül-e korlátozni a szolgáltatásnév korlátozását.
1. Rögzítse a telepítési jegyzékben használni IoT Edge paramétereket.

### <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása  

Ha még nincs Media Service-fiókja, a következő lépésekkel hozhat létre egyet.

1. Keresse meg a [Cloud Shell.](https://shell.azure.com/)
1. A rendszerhéj ablakának bal oldalán található legördülő menüben válassza a "Bash" környezetet

    ![A képernyőfelvételek a rendszerhéjablakból kiválasztott Basht jelenítik meg.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Állítsa be az Azure-előfizetését alapértelmezett fiókként a következő parancssablon használatával:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Hozzon [létre egy erőforráscsoportot és](/cli/azure/group#az_group_create) egy [tárfiókot.](/cli/azure/storage/account#az_storage_account_create)
1. Most hozzon létre egy Azure Media Service-fiókot a következő parancssablonnal a Cloud Shell:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása  

Most létrehozunk egy új szolgáltatásnév, és csatoljuk azt a Media Service-fiókjához.

Hitelesítési paraméterek nélkül a rendszer jelszóalapú hitelesítést használ a szolgáltatásnév véletlenszerű jelszavával. A Cloud Shell használja a következő parancssablont:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Ez a parancs a következő válaszhoz hasonló eredményt ad:

```
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "<yourServicePrincipalPassword>",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": " <yourAMSAccountName >",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "Central US",
  "ResourceGroup": " <yourResouceGroup >",
  "SubscriptionId": "<yourSubscriptionId>"
}

```
1. A jelszó-hitelesítéssel megadott szolgáltatásnév kimenete tartalmazza azt a jelszókulcsot, amely ebben az esetben az "AadSecret" paraméter. 

    Ügyeljen rá, hogy ezt az értéket másolja – nem olvasható be. Ha elfelejtette a jelszót, állítsa [alaphelyzetbe a szolgáltatásnév hitelesítő adatait.](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials)
1. Az appId és a bérlőkulcs "AadClientId" és "AadTenantId" értékként jelenik meg a kimenetben. Ezeket a rendszer szolgáltatásnév-hitelesítéshez használja. Jegyezheti fel az értékeiket, de bármikor lekérhetők [az az ad sp list listában.](/cli/azure/ad/sp#az_ad_sp_list)

### <a name="create-a-custom-role-definition"></a>Egyéni szerepkör-definíció létrehozása  

Egyéni szerepkör létrehozásához az alábbi lépéseket kell követnie:

1. Hozzon létre egy szerepkör-definíciós JSON-fájlt a helyi rendszeren, és mentse az alábbi szöveget a fájlba. 
    1. Cserélje < yourSubscriptionId> az Azure-előfizetés azonosítójára
    1. Ehhez a szerepkörhöz csak a következő műveletek engedélyezettek:
        * listContainerSas – segít a modulnak listába sorolni a tárolók közös hozzáférésű jogosultsági jogosultságokkal (SAS) megadott URL-címeit az adategység tartalmának feltöltéséhez és letöltéséhez.
        * Adategység írása – segít a modulnak bármilyen adategység létrehozásában vagy frissítésében
        * listEdgePolicies – felsorolja a peremeszközre alkalmazott szabályzatokat  
        
        ```
        {
          "Name": "LVAEdge User",
          "IsCustom": true,
          "Description": "Can create assets, view list of containers and view Edge policies",
          "Actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action"
          ],
          "NotActions": [],
          "DataActions": [],
          "NotDataActions": [],
          "AssignableScopes": [
            "/subscriptions/<yourSubscriptionId>"
          ]
        }
        ```  
          
1. A létrehozás után futtassa a következő parancssablont az új szerepkör-definíció létrehozásához az előfizetésben:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    A parancs sikeres végrehajtásakor a következő kimenetet fogja látni:
    
    ```
    {
      "assignableScopes": [
      "/subscriptions/<yourSubscriptionId>"
      ],
      "description": "Can create assets, view list of containers and view Edge policies",
      "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<unique name>",
      "name": "<unique name>",
      "permissions": [
        {
          "actions": [
            "Microsoft.Media/mediaservices/assets/listContainerSas/action",
            "Microsoft.Media/mediaservices/assets/write",
            "Microsoft.Media/mediaservices/listEdgePolicies/action",
          ],
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": " LVAEdge User ",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

### <a name="create-role-assignment"></a>Szerepkör-hozzárendelés létrehozása  

Szerepkör-hozzárendelés hozzáadásához szüksége lesz annak a szolgáltatásnévnek az objectId-ére, amely az előbb létrehozott egyéni szerepkört szeretné hozzárendelni.

Használja a következő parancsot a Cloud Shell az objectId lekért adatokat:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` A a Szolgáltatásnév létrehozása lépés [kimenetében olvasható](#create-service-principal) be.

A fenti parancs kinyomtatja a szolgáltatásnév objectId-ját. 

```
“objectId” : “<yourObjectId>”,
```

Az [az role assignment create parancssablon](/cli/azure/role/assignment#az_role_assignment_create) használatával csatolja az egyéni szerepkört a szolgáltatásnévhez:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Paraméterek:

|Paraméterek|Description| 
|---|---|
|--role (szerepkör) |Egyéni szerepkör neve vagy azonosítója. Ebben az esetben: "LVAEdge User".|
|--assignee-object-id|A használni fog szolgáltatásnév objektumazonosítója.|

Az eredmény a következő lesz:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleAssignments/<yourCustomRoleId>",
  "name": "<yourCustomRoleId>",
  "principalId": "<yourServicePrincipalId>",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<yourSubscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<yourRoleDefinitionId>",
  "scope": "/subscriptions/<yourSubscriptionId>",
  "type": "Microsoft.Authorization/roleAssignments"
} 
```

### <a name="confirm-that-role-assignment-happened"></a>Szerepkör-hozzárendelés megtörténtének ellenőrzése

Annak megerősítéséhez, hogy a szolgáltatásnév most már kapcsolódik az újonnan létrehozott egyéni szerepkörhöz, futtassa a következő parancsot:

```
az role assignment list  --assignee < objectId>
```

Az eredménynek így kell kinéznie:

```
[
  {
    "canDelegate": null,
    "id": "/subscriptions/xxx/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "principalId": "<yourServicePrincipalID>",
    "principalName": "<yourServicePrincipalName>",
    "principalType": "ServicePrincipal",
    "roleDefinitionId": "/subscriptions/xxx/providers/Microsoft.Authorization/roleDefinitions/zzz",
    "roleDefinitionName": "LVAEdge User",
    "scope": "/subscriptions/<yourSubscription ID>",
    "type": "Microsoft.Authorization/roleAssignments
  }
]  
```
 
Keresse meg a "roleDefinitionName" értéket, és figyelje meg, hogy az értéke "LVAEdge User" (LVAEdge-felhasználó) értékre van állítva. 

Ez megerősíti, hogy az egyéni felhasználói szerepkört az alkalmazáshoz használt szolgáltatásnévvel kapcsoltuk össze.

### <a name="test-the-service-principal-access-control"></a>A szolgáltatásnév hozzáférés-vezérlésének tesztelése

1. Jelentkezzen be a szolgáltatásnévvel. Ehhez 3 adatra lesz szükségünk a Azure Active Directory, hogy megfelelő hozzáférési jogkivonatot biztosítsunk, amely a Szolgáltatásnév létrehozása lépés [kimenetében található:](#create-service-principal)
    1. AadClientID 
    1. AadSecret (AadSecret)
    1. AadTenantId (AadTenantId)
1. Most próbáljon meg bejelentkezni az alábbi parancssablon használatával:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Most hozzon létre egy erőforráscsoportot, hogy ellenőrizze, hogy a bejelentkezés az "LVAEdge User" szerepkörrel használt szolgáltatásnévre korlátozódik-e. Futtassa a következő parancsot a Cloud Shell:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    Ennek a parancsnak sikertelennek kell lennie, és így fog kinézni:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Következő lépések  

Jegyezze fel a cikk alábbi értékeit. Ezekre az értékekre a modulban található ikertulajdonságok konfigurálására lesz szükség, Live Video Analytics modulban IoT Edge ikermodul [JSON-sémáját.](module-twin-configuration-schema.md)

| A cikk változója|A Live Video Analytics ikertulajdonságának IoT Edge|
|---|---|
|AadSecret (AadSecret) |    aadServicePrincipalPassword|
|AadTenantId (AadTenantId) |  aadTenantId (aadTenantId)|
|AadClientId (AadClientId)    |aadServicePrincipalAppId|
