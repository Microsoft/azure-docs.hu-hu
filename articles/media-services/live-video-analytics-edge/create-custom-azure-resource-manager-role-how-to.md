---
title: Hozzon létre egyéni Azure Resource Manager szerepkört, és rendelje hozzá az egyszerű szolgáltatáshoz – Azure
description: Ez a cikk bemutatja, hogyan hozhat létre egyéni Azure Resource Manager szerepkört, és hogyan rendelhető hozzá az élő video Analytics szolgáltatáshoz az Azure CLI használatával IoT Edge.
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: 80974c111dd451314635d06334766322bc68e437
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210444"
---
# <a name="create-custom-azure-resource-manager-role-and-assign-to-service-principal"></a>Egyéni Azure Resource Manager szerepkör létrehozása és hozzárendelés az egyszerű szolgáltatáshoz

Az IoT Edge Module-példány élő videós elemzéséhez aktív Azure Media Services fiókra van szükség ahhoz, hogy megfelelően működjön. A IoT Edge modul és az Azure Media Service-fiók élő videós elemzésének kapcsolata a modul Twin tulajdonságain keresztül jön létre. Ezen Twin tulajdonságok egyike egy [egyszerű szolgáltatásnév](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) , amely lehetővé teszi, hogy a modul-példány kommunikáljon a Media Services fiókján, és elindítsa a szükséges műveleteket. Ennek a szolgáltatásnak a lehető legkevesebb jogosultsággal kell rendelkeznie a lehetséges visszaélések és/vagy az Edge-eszközről való véletlen adatforgalom minimalizálásához.

Ebből a cikkből megtudhatja, hogyan hozhat létre egyéni Azure Resource Manager-szerepkört a Azure Cloud Shell, majd az egyszerű szolgáltatásnév létrehozásához használt lépéseket.

## <a name="prerequisites"></a>Előfeltételek  

A cikk előfeltételei a következők:

* Azure-előfizetés tulajdonosi előfizetéssel.
* Az alkalmazás létrehozásához és az egyszerű szolgáltatás szerepkörhöz rendeléséhez jogosultságokkal rendelkező Azure Active Directory.

A legegyszerűbben a portálon ellenőrizheti, hogy rendelkezik-e megfelelő jogosultságokkal. Lásd: [Szükséges engedélyek ellenőrzése](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="overview"></a>Áttekintés  

Egy egyéni szerepkör létrehozásához és egy egyszerű szolgáltatáshoz való csatolásához szükséges lépéseket a következő sorrendben fogjuk megtenni:

1. Hozzon létre egy Media Service-fiókot, ha még nem rendelkezik ilyennel.
1. Egyszerű szolgáltatásnév létrehozása.
1. Hozzon létre egy egyéni Azure Resource Manager szerepkört korlátozott jogosultságokkal.
1. "Korlátozza" a szolgáltatás egyszerű jogosultságait a létrehozott egyéni szerepkör használatával.
1. Futtasson egy egyszerű tesztet, és ellenőrizze, hogy sikerült-e korlátozni a szolgáltatásnevet.
1. Rögzítse a IoT Edge üzembe helyezési jegyzékekben használt paramétereket.

### <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása  

Ha nem rendelkezik Media Service-fiókkal, akkor a következő lépésekkel hozhat létre egyet.

1. Tallózással keresse meg a [Cloud Shell](https://shell.azure.com/).
1. A rendszerhéj ablak bal oldalán található legördülő menüből válassza a "bash" lehetőséget a környezetében.

    ![A képernyő capturs a rendszerhéj ablakában kiválasztott bash-t jeleníti meg.](./media/create-custom-azure-resource-manager-role-how-to/bash.png)
1. Állítsa be az Azure-előfizetést alapértelmezett fiókként a következő parancs sablonjának használatával:
    
    ```
    az account set --subscription " <yourSubscriptionName or yourSubscriptionId>"
    ```
1. Hozzon létre egy [erőforráscsoportot](/cli/azure/group#az-group-create) és egy [Storage-fiókot](/cli/azure/storage/account#az-storage-account-create).
1. Most hozzon létre egy Azure Media Service-fiókot a következő parancs használatával Cloud Shellban:

    ```
    az ams account create --name <yourAMSAccountName>  --resource-group <yourResouceGroup>  --storage-account <yourStorageAccountName>
    ```

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása  

Most létrehozunk egy új szolgáltatásnevet, és összekapcsoljuk a Media Service-fiókkal.

A hitelesítési paraméterek nélkül a jelszó-alapú hitelesítés az egyszerű szolgáltatásnév véletlenszerű jelszavával együtt használható. A Cloud Shellban használja a következő parancsot:

```
az ams account sp create --account-name < yourAMSAccountName > --resource-group < yourResouceGroup >
```

Ez a parancs a következőhöz hasonló választ hoz létre:

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
1. A jelszó-hitelesítéssel rendelkező szolgáltatásnév kimenete tartalmazza azt a jelszót, amely ebben az esetben a "AadSecret" paraméter. 

    Győződjön meg róla, hogy ezt az értéket másolja – nem lehet lekérni. Ha elfelejti a jelszót, [állítsa alaphelyzetbe az egyszerű szolgáltatásnév hitelesítő adatait](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
1. A appId és a bérlői kulcs a kimenetben "AadClientId" és "AadTenantId" néven jelenik meg. Ezek az egyszerű szolgáltatás hitelesítésében használatosak. Jegyezze fel az értékeket, de az az [ad SP listával](/cli/azure/ad/sp#az-ad-sp-list)bármely pontról lekérhető.

### <a name="create-a-custom-role-definition"></a>Egyéni szerepkör-definíció létrehozása  

Egyéni szerepkör létrehozásához kövesse az alábbi lépéseket:

1. Hozzon létre egy szerepkör-definíciós JSON-fájlt a helyi rendszeren, és mentse a következő szöveget a fájlba. 
    1. Cserélje le < yourSubscriptionId> az Azure-előfizetés azonosítójával
    1. Az ehhez a szerepkörhöz csak a következő műveletek engedélyezettek:
        * listContainerSas – segítséget nyújt a modulnak a Storage-tároló URL-címeinek a közös hozzáférési aláírásokkal (SAS) való feltöltéséhez és az eszközök tartalmának letöltéséhez.
        * Eszközök írása – segítséget nyújt a modulnak bármilyen eszköz létrehozásában vagy frissítésében
        * listEdgePolicies – felsorolja a peremhálózati eszközre alkalmazott házirendeket  
        
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
          
1. A létrehozást követően futtassa az alábbi parancsot az új szerepkör-definíció létrehozásához az előfizetésben:
    
    ```
    az role definition create --role-definition "<location of the Role Definition JSON file >"
    ```

    A parancs sikeres végrehajtása után a következő kimenet jelenik meg:
    
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

Szerepkör-hozzárendelés hozzáadásához szüksége lesz annak az egyszerű szolgáltatásnak a objectId, amelyhez az imént létrehozott egyéni szerepkört hozzá szeretné rendelni.

A objectId lekéréséhez használja a következő parancsot a Cloud Shellban:

```
az ad sp show --id "<appId>" | Select-String "objectId"
```

> [!NOTE]
> `<appId>` az [egyszerű szolgáltatásnév létrehozása](#create-service-principal) lépés kimenetéről kérhető le.

A fenti parancs kinyomtatja az egyszerű szolgáltatásnév objectId. 

```
“objectId” : “<yourObjectId>”,
```

Használja az az [szerepkör-hozzárendelés létrehozása parancsot](/cli/azure/role/assignment#az-role-assignment-create) az egyéni szerepkör az egyszerű szolgáltatással való összekapcsolásához:

```
az role assignment create --role “LVAEdge User” --assignee-object-id < objectId>    
```

Paraméterek:

|Paraméterek|Description| 
|---|---|
|--role |Egyéni szerepkör neve vagy azonosítója. A mi esetünkben: "LVAEdge user".|
|--megbízott-Object-ID|A használni kívánt szolgáltatásnév objektumazonosító.|

Az eredmény az alábbihoz hasonló lesz:

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

### <a name="confirm-that-role-assignment-happened"></a>A szerepkör-hozzárendelés megtörténtének ellenőrzése

A következő parancs futtatásával ellenőrizheti, hogy az egyszerű szolgáltatás kapcsolódik-e az imént létrehozott egyéni szerepkörhöz:

```
az role assignment list  --assignee < objectId>
```

Az eredménynek a következőhöz hasonlóan kell kinéznie:

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
 
Keresse meg a "roleDefinitionName" kifejezést, és figyelje meg, hogy az értéke "LVAEdge user". 

Ezzel megerősíti, hogy az egyéni felhasználói szerepkört az alkalmazáshoz használt egyszerű szolgáltatáshoz csatoltuk.

### <a name="test-the-service-principal-access-control"></a>Egyszerű szolgáltatásnév hozzáférés-vezérlésének tesztelése

1. Jelentkezzen be az egyszerű szolgáltatásnév használatával. Ehhez 3 információra van szükségünk ahhoz, hogy a Azure Active Directory megadjon nekünk a megfelelő hozzáférési jogkivonatot, amelyet az [egyszerű szolgáltatás létrehozása](#create-service-principal) lépés kimenetéről kaphatunk:
    1. AadClientID 
    1. AadSecret
    1. AadTenantId
1. Most próbálja meg bejelentkezni az alábbi parancs használatával:
    
    ```
    az login --service-principal --username "< AadClientID>" --password " <AadSecret>" --tenant "<AadTenantId>"
    ```
3.  Most nézzük meg, hogy a bejelentkezés csak az "LVAEdge user" szerepkörrel rendelkező egyszerű szolgáltatásnév számára van-e kialakítva, hogy a művelet sikertelen legyen. Futtassa a következő parancsot a Cloud Shellban:

    ```
    az group create --location "central us" --name "testresourcegroup"
    ```

    A parancsnak sikertelennek kell lennie, és a következőképpen fog kinézni:
    
    ```
    The client '<AadClientId>' with object id '<AadClientId>' does not have authorization to perform action 'Microsoft.Resources/subscriptions/resourcegroups/write' over scope '/subscriptions/<yourSubscriptionId>/resourcegroups/testresourcegroup' or the scope is invalid. If access was recently granted, please refresh your credentials.
    ```

## <a name="next-steps"></a>Következő lépések  

Jegyezze fel a cikk következő értékeit. Ezek az értékek szükségesek ahhoz, hogy konfigurálni tudja az élő videó Analytics IoT Edge moduljának Twin tulajdonságait, lásd: [modul Twin JSON-séma](module-twin-configuration-schema.md).

| A cikk változója|A IoT Edge élő videó-elemzések Twin tulajdonságának neve|
|---|---|
|AadSecret |    aadServicePrincipalPassword|
|AadTenantId |  aadTenantId|
|AadClientId    |aadServicePrincipalAppId|
