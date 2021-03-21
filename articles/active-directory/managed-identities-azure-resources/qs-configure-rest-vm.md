---
title: Felügyelt identitások konfigurálása az Azure-beli virtuális gépen a REST-Azure AD használatával
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálásához egy Azure-beli virtuális gépen a CURL használatával REST API-hívások létrehozásához.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70229fabe4331adb7740cf09cf2bf8f3e3e4617
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587519"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Felügyelt identitások konfigurálása Azure-beli virtuális gépeken lévő Azure-erőforrásokhoz REST API hívások használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt rendszeridentitással. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebből a cikkből megtudhatja, hogyan hajthatja végre a következő felügyelt identitásokat az Azure-beli virtuális gépeken az Azure-erőforrások műveleteihez a CURL használatával a Azure Resource Manager REST-végpont hívásához:

- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure-beli virtuális gépen
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása egy Azure-beli virtuális gépen

Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](overview.md)című témakört. A rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitási típusok megismeréséhez lásd: [felügyelt identitások típusai](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan engedélyezheti és tilthatja le a rendszerhez rendelt felügyelt identitást egy Azure virtuális gépen a CURL használatával a Azure Resource Manager REST-végpontra irányuló hívások kezdeményezéséhez.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure-beli virtuális gép létrehozásakor

Ha olyan Azure virtuális gépet szeretne létrehozni, amelyen engedélyezve van a rendszerhez rendelt felügyelt identitás, a fióknak szüksége van a [virtuálisgép-közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Az [az group create](/cli/azure/group/#az-group-create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre egy [hálózati adaptert](/cli/azure/network/nic#az-network-nic-create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Azure Cloud Shell használatával hozzon létre egy virtuális gépet a CURL használatával a Azure Resource Manager REST-végpont meghívásához. Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre egy rendszerhez rendelt felügyelt identitással, amelyet a kérelem törzse az érték alapján azonosított `"identity":{"type":"SystemAssigned"}` . Cserélje le az elemet az `<ACCESS TOKEN>` előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és az `<SUBSCRIPTION ID>` adott környezetnek megfelelő értéket kért.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Kérésfejlécek**
   
   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 
   
   **Kérelem törzse**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Rendszerhez rendelt identitás engedélyezése meglévő Azure-beli virtuális gépen

Ha olyan virtuális gépen szeretné engedélyezni a rendszer által hozzárendelt felügyelt identitást, amelyet eredetileg anélkül osztott ki, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkörének hozzárendelésére.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. A következő CURL-paranccsal hívhatja meg a Azure Resource Manager REST-végpontot, hogy engedélyezze a rendszerhez rendelt felügyelt identitást a virtuális gépen a `{"identity":{"type":"SystemAssigned"}` *myVM* nevű virtuális gép értéke alapján.  Cserélje le az elemet az `<ACCESS TOKEN>` előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és az `<SUBSCRIPTION ID>` adott környezetnek megfelelő értéket kért.
   
   > [!IMPORTANT]
   > Annak biztosítása érdekében, hogy ne törölje a virtuális géphez hozzárendelt meglévő, felhasználóhoz rendelt felügyelt identitásokat, a következő CURL-parancs használatával fel kell sorolnia a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Ha a válaszban szereplő értékben azonosított, felhasználó által hozzárendelt felügyelt identitások vannak hozzárendelve a virtuális géphez `identity` , ugorjon a 3. lépésre, amely bemutatja, hogyan őrzi meg a felhasználó által hozzárendelt felügyelt identitásokat, miközben engedélyezheti a rendszerhez rendelt felügyelt identitást a virtuális gépen.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 
   
   **Kérelem törzse**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Ha a rendszer által hozzárendelt felügyelt identitást olyan virtuális gépen szeretné engedélyezni, amelyen már van felhasználó által hozzárendelt felügyelt identitás, hozzá kell adnia `SystemAssigned` az `type` értéket.  
   
   Ha például a virtuális gépen a felhasználó által hozzárendelt felügyelt identitások `ID1` vannak `ID2` társítva, és hozzá van rendelve a virtuális géphez, akkor a következő curl-hívást kell használnia. Cserélje le a `<ACCESS TOKEN>` és `<SUBSCRIPTION ID>` a értéket a környezetének megfelelő értékekre.

   Az API-verzió a `2018-06-01` felhasználó által hozzárendelt felügyelt identitásokat a `userAssignedIdentities` szótár formátumában tárolja az értékben, az `identityIds` API-verzióban használt tömb formátuma helyett `2017-12-01` .
   
   **API-VERZIÓ 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 

   **Kérelem törzse**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **API-VERZIÓ 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 

   **Kérelem törzse**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás letiltása egy Azure-beli virtuális gépről

Ha le szeretné tiltani a rendszerhez rendelt felügyelt identitást egy virtuális gépen, a fióknak szüksége van a [virtuálisgép-közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Frissítse a virtuális gépet a CURL használatával, hogy meghívja a Azure Resource Manager REST-végpontot a rendszer által hozzárendelt felügyelt identitás letiltásához.  A következő példa letiltja a rendszer által hozzárendelt felügyelt identitást a `{"identity":{"type":"None"}}` *myVM* nevű virtuális gép értéke alapján a kérelem törzsében azonosított módon.  Cserélje le az elemet az `<ACCESS TOKEN>` előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és az `<SUBSCRIPTION ID>` adott környezetnek megfelelő értéket kért.

   > [!IMPORTANT]
   > Annak biztosítása érdekében, hogy ne törölje a virtuális géphez hozzárendelt meglévő, felhasználóhoz rendelt felügyelt identitásokat, a következő CURL-parancs használatával fel kell sorolnia a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Ha a válaszban szereplő értékben azonosított, felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik `identity` , ugorjon a 3. lépésre, amely bemutatja, hogyan őrizze meg a felhasználó által hozzárendelt felügyelt identitásokat, miközben letiltja a rendszerhez rendelt felügyelt identitást a virtuális gépen.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 

   **Kérelem törzse**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Ha a rendszer által hozzárendelt felügyelt identitást egy olyan virtuális gépről szeretné eltávolítani, amely felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik, távolítsa el `SystemAssigned` az értéket az érték `{"identity":{"type:" "}}` `UserAssigned` és a szótár értékének megtartásával, `userAssignedIdentities` Ha az **API 2018-06-01**-es verzióját használja. Ha a 2017-12-01-es vagy korábbi **API-verziót** használja, tartsa meg a `identityIds` tömböt.

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan adhat hozzá és távolíthat el felhasználó által hozzárendelt felügyelt identitást egy Azure virtuális gépen a CURL használatával a Azure Resource Manager REST-végpontra irányuló hívások kezdeményezéséhez.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy Azure-beli virtuális gép létrehozásakor

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) és [felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre egy [hálózati adaptert](/cli/azure/network/nic#az-network-nic-create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található utasítások segítségével: [hozzon létre egy felhasználó által hozzárendelt felügyelt identitást](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Hozzon létre egy virtuális gépet a CURL használatával a Azure Resource Manager REST-végpont meghívásához. A következő példa egy *myVM* nevű virtuális gépet hoz létre az erőforráscsoport *myResourceGroup* egy felhasználó által hozzárendelt felügyelt identitással `ID1` , a kérelem törzsében az érték szerint azonosítva `"identity":{"type":"UserAssigned"}` . Cserélje le az elemet az `<ACCESS TOKEN>` előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és az `<SUBSCRIPTION ID>` adott környezetnek megfelelő értéket kért.
 
   **API-VERZIÓ 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 

   **Kérelem törzse**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **API-VERZIÓ 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuális géphez

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) és [felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található utasítások alapján, [hozzon létre egy felhasználó által hozzárendelt felügyelt identitást](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Annak biztosítása érdekében, hogy ne törölje a virtuális géphez hozzárendelt meglévő felhasználó vagy rendszer által hozzárendelt felügyelt identitásokat, a következő CURL-parancs használatával fel kell sorolnia a virtuális géphez rendelt identitási típusokat. Ha felügyelt identitások vannak hozzárendelve a virtuálisgép-méretezési csoporthoz, azok az érték alatt vannak felsorolva `identity` .

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.

    Ha a virtuális géphez a válaszban megadott módon azonosított felhasználó-vagy rendszerszintű felügyelt identitásokkal rendelkezik `identity` , ugorjon az 5. lépésre, amely bemutatja, hogyan őrizze meg a rendszer által hozzárendelt felügyelt identitást, miközben hozzáad egy felhasználóhoz rendelt felügyelt identitást a virtuális géphez.

4. Ha nem rendelkezik a virtuális géphez hozzárendelt, felhasználóhoz rendelt felügyelt identitásokkal, a következő CURL-paranccsal hívja meg az Azure Resource Manager REST-végpontot, hogy az első felhasználóhoz rendelt felügyelt identitást hozzárendelje a virtuális géphez.

   A következő példa egy felhasználó által hozzárendelt felügyelt identitást rendel hozzá `ID1` egy *myVM* nevű virtuális géphez az erőforráscsoport *myResourceGroup*.  Cserélje le az elemet az `<ACCESS TOKEN>` előző lépésben kapott értékre, amikor a tulajdonos hozzáférési jogkivonatát és az `<SUBSCRIPTION ID>` adott környezetnek megfelelő értéket kért.

   **API-VERZIÓ 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        |
 
   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **API-VERZIÓ 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. Ha rendelkezik egy meglévő, felhasználó által hozzárendelt vagy rendszerhez rendelt felügyelt identitással a virtuális géphez:
   
   **API-VERZIÓ 2018-06-01**

   Adja hozzá a felhasználó által hozzárendelt felügyelt identitást a `userAssignedIdentities` szótár értékéhez.
    
   Ha például a rendszer által hozzárendelt felügyelt identitással rendelkezik, és a felhasználó által hozzárendelt felügyelt identitás `ID1` jelenleg a virtuális géphez van rendelve, és hozzá szeretné adni a felhasználó által hozzárendelt felügyelt identitást `ID2` :

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **API-VERZIÓ 2017-12-01**

   Őrizze meg a felhasználó által hozzárendelt felügyelt identitásokat, amelyeket meg szeretne őrizni a `identityIds` tömb értékében az új felhasználó által hozzárendelt felügyelt identitás hozzáadásakor.

   Ha például a rendszer által hozzárendelt felügyelt identitással rendelkezik, és a felhasználó által hozzárendelt felügyelt identitás `ID1` jelenleg a virtuális géphez van rendelve, és hozzá szeretné adni a felhasználó által hozzárendelt felügyelt identitást `ID2` : 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása Azure-beli virtuális gépről

A felhasználó által hozzárendelt identitás egy virtuális géphez való eltávolításához a fióknak szüksége van a [virtuálisgép-közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelésre.

1. Kérje le a tulajdonos hozzáférési jogkivonatát, amelyet az engedélyezési fejléc következő lépésében fog használni a virtuális gép rendszerhez rendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak biztosítása érdekében, hogy ne töröljön olyan meglévő, felhasználó által hozzárendelt felügyelt identitásokat, amelyeket meg szeretne őrizni a virtuális géphez, vagy távolítsa el a rendszer által hozzárendelt felügyelt identitást, a következő CURL-paranccsal kell kilistáznia a felügyelt identitásokat: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.
 
   Ha felügyelt identitások vannak hozzárendelve a virtuális géphez, azok szerepelnek az érték válaszában `identity` .

   Ha például a felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik `ID1` `ID2` , és hozzá van rendelve a virtuális géphez, és csak a `ID1` hozzárendelt és a rendszer által hozzárendelt identitást szeretné megőrizni:
   
   **API-VERZIÓ 2018-06-01**

   Adja hozzá `null` az eltávolítani kívánt felhasználóhoz rendelt felügyelt identitást:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **API-VERZIÓ 2017-12-01**

   Csak azokat a felhasználó által hozzárendelt felügyelt identitásokat őrizze meg, amelyeket meg szeretne őrizni a `identityIds` tömbben:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérésfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.        | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Ha a virtuális gépen a rendszerhez hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás is van, akkor a következő paranccsal távolíthatja el az összes felhasználó által hozzárendelt felügyelt identitást úgy, hogy csak a rendszerhez rendelt felügyelt identitás használatára váltson:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Kérésfejlécek**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva. | 

**Kérelem törzse**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Ha a virtuális gépen csak a felhasználó által hozzárendelt felügyelt identitások vannak, és az összeset el szeretné távolítani, használja a következő parancsot:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Kérésfejlécek**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Content-Type*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezés*     | Kötelező. Érvényes `Bearer` hozzáférési jogkivonatra van beállítva.| 

**Kérelem törzse**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Következő lépések

A felhasználó által hozzárendelt felügyelt identitások REST használatával történő létrehozásával, listázásával vagy törlésével kapcsolatos információkért lásd:

- [Felhasználó által hozzárendelt felügyelt identitások létrehozása, listázása és törlése REST API hívásokkal](how-to-manage-ua-identity-rest.md)
