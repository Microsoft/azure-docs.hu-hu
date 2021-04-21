---
title: Felügyelt identitások konfigurálása Azure-beli virtuális gépen REST használatával – Azure AD
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálásához egy Azure-beli virtuális gépen a CURL használatával a REST API hívásokhoz.
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
ms.openlocfilehash: 9b74e7d312133c24daad448e029a3c3d4cbdce79
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773080"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának konfigurálása REST API hívásokkal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt rendszeridentitást biztosít az Azure-szolgáltatások számára a Azure Active Directory. Ezzel az identitással bármely Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet anélkül, hogy hitelesítő adatokat ad meg a kódban. 

Ebből a cikkből megtudhatja, hogyan hajtható végre a következő felügyelt identitások azure-erőforrásokkal kapcsolatos műveleteihez az Azure Resource Manager REST-végpontra a CURL használatával:

- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure-beli virtuális gépen
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása Azure-beli virtuális gépen

Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, lásd: Mik azok az [Azure-erőforrások felügyelt identitások?](overview.md). A rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitástípusokkal kapcsolatos további információkért lásd: [Felügyelt identitástípusok.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitást egy Azure-beli virtuális gépen a CURL használatával, hogy hívásokat kezdeményezjen a Azure Resource Manager REST-végpontra.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése Azure-beli virtuális gép létrehozása során

Ahhoz, hogy olyan Azure-beli virtuális gépet hozzon létre, amely számára engedélyezve van a rendszer által hozzárendelt felügyelt identitás, a fiókjának a Virtuális gépek közreműködője [szerepkör-hozzárendelésre](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) van szüksége.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Az [az group create](/cli/azure/group/#az_group_create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre [egy hálózati adaptert](/cli/azure/network/nic#az_network_nic_create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuális gép rendszer által hozzárendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. A Azure Cloud Shell hozzon létre egy virtuális gépet a CURL használatával a Azure Resource Manager REST-végpont hívása érdekében. Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre egy rendszer által hozzárendelt felügyelt identitással, amelyet a kérelem törzsében a érték `"identity":{"type":"SystemAssigned"}` azonosít. Cserélje le az helyére az előző lépésben kapott értéket, amikor tulajdonosi hozzáférési jogkivonatot kért, és az értéket a `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` környezetének megfelelően.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Kérelemfejlécek**
   
   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra.        | 
   
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

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Rendszer által hozzárendelt identitás engedélyezése meglévő Azure-beli virtuális gépen

Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitást olyan virtuális gépen engedélyezze, amely eredetileg nem lett kiépítve, a fiókjának rendelkeznie kell a [Virtuális](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) gépek közreműködője szerepkör-hozzárendeléssel.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuális gép rendszer által hozzárendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. A következő CURL-paranccsal hívja meg a Azure Resource Manager REST-végpontot, hogy engedélyezze a rendszer által hozzárendelt felügyelt identitást a virtuális gépen a myVM nevű virtuális gép értéke által azonosított kérelem `{"identity":{"type":"SystemAssigned"}` *törzsében.*  Cserélje le az helyére az előző lépésben kapott értéket, amikor tulajdonosi hozzáférési jogkivonatot kért, és az értéket a `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` környezetének megfelelően.
   
   > [!IMPORTANT]
   > Annak érdekében, hogy ne töröljön a virtuális géphez hozzárendelt meglévő, felhasználó által hozzárendelt felügyelt identitásokat, a következő CURL-paranccsal listába kell sorolni a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Ha van felhasználó által hozzárendelt felügyelt identitása a virtuális géphez a válaszban megadott érték alapján, ugorjon a 3. lépésre, amely bemutatja, hogyan őrizze meg a felhasználó által hozzárendelt felügyelt identitásokat, miközben engedélyezi a rendszer által hozzárendelt felügyelt identitásokat a virtuális `identity` gépen.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra.        | 
   
   **Kérelem törzse**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Ha engedélyezni szeretné a rendszer által hozzárendelt felügyelt identitást egy olyan virtuális gépen, amely meglévő, felhasználó által hozzárendelt felügyelt identitásokkal bír, hozzá kell adni a `SystemAssigned` `type` értéket.  
   
   Ha például a virtuális gép rendelkezik a felhasználó által hozzárendelt felügyelt identitásokkal, és hozzá van rendelve, és rendszer által hozzárendelt felügyelt identitást szeretne hozzáadni a virtuális géphez, használja a `ID1` `ID2` következő CURL-hívást. Cserélje le `<ACCESS TOKEN>` a és `<SUBSCRIPTION ID>` a értékeket a környezetének megfelelő értékekre.

   Az API-verzió a felhasználó által hozzárendelt felügyelt identitásokat szótár formátumban tárolja, nem pedig az API-verzióban használt `2018-06-01` `userAssignedIdentities` `identityIds` tömbformátumban. `2017-12-01`
   
   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra.        | 

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

   **API VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra.        | 

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

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás letiltása Azure-beli virtuális gépről

Ha le szeretné tiltani a rendszer által hozzárendelt felügyelt identitást egy virtuális gépen, a fiókjának a Virtuális gépek közreműködője [szerepkör-hozzárendelésre van](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szüksége.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuális gép rendszer által hozzárendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Frissítse a virtuális gépet a CURL használatával, hogy Azure Resource Manager REST-végpontot a rendszer által hozzárendelt felügyelt identitás letiltásához.  A következő példa letiltja a rendszer által hozzárendelt felügyelt identitást, amelyet a myVM nevű virtuális gép értéke azonosított a kérelem `{"identity":{"type":"None"}}` *törzsében.*  Cserélje le a helyére azt az értéket, amit az előző lépésben kapott, amikor tulajdonosi hozzáférési jogkivonatot kért, és az értéket a `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` környezetének megfelelően.

   > [!IMPORTANT]
   > Annak érdekében, hogy ne töröljön a virtuális géphez hozzárendelt meglévő, felhasználó által hozzárendelt felügyelt identitásokat, a következő CURL-paranccsal listába kell sorolni a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Ha van olyan felhasználó által hozzárendelt felügyelt identitása, amely a válaszban megadott értékben van hozzárendelve a virtuális géphez, ugorjon a 3. lépésre, amely bemutatja, hogyan őrizze meg a felhasználó által hozzárendelt felügyelt identitásokat, miközben letiltja a rendszer által hozzárendelt felügyelt identitást a virtuális `identity` gépen.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra.        | 

   **Kérelem törzse**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Ha el szeretné távolítani a rendszer által hozzárendelt felügyelt identitást egy felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik virtuális gépről, távolítsa el az értéket, miközben megtartja az értéket és a szótár értékeit, ha a `SystemAssigned` `{"identity":{"type:" "}}` `UserAssigned` `userAssignedIdentities` **2018-06-01 API-verziót használja.** Ha a **2017-12-01-es** vagy korábbi API-verziót használja, tartsa meg a `identityIds` tömböt.

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el felhasználó által hozzárendelt felügyelt identitást egy Azure-beli virtuális gépen a CURL használatával az Azure Resource Manager REST-végpontra.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése Azure-beli virtuális gép létrehozása során

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fiókjának a [Virtuális](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) gépek közreműködője és a [Felügyelt](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitáskezelő szerepkör-hozzárendelésre van szüksége. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuális gép rendszer által hozzárendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre [egy hálózati adaptert](/cli/azure/network/nic#az_network_nic_create) a virtuális géphez:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuális gép rendszer által hozzárendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található utasítások alapján: [Felhasználó által hozzárendelt felügyelt identitás létrehozása.](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)

5. Hozzon létre egy virtuális gépet a CURL használatával a Azure Resource Manager REST-végpont hívása érdekében. Az alábbi példa létrehoz egy *myVM* nevű virtuális gépet a *myResourceGroup erőforráscsoportban* egy felhasználó által hozzárendelt felügyelt identitással, amelyet a kérelem törzsében a érték `ID1` `"identity":{"type":"UserAssigned"}` azonosít. Cserélje le a helyére azt az értéket, amit az előző lépésben kapott, amikor tulajdonosi hozzáférési jogkivonatot kért, és az értéket a `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` környezetének megfelelően.
 
   **API VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra.        | 

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
  
   **API VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra.        | 

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

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuális géphez

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fiókjának a Virtuális gépek közreműködője és a [Felügyelt](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitáskezelő szerepkör-hozzárendelésre van szüksége. [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuális gép rendszer által hozzárendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található, Felhasználó által hozzárendelt [felügyelt identitás létrehozása útmutatás alapján.](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)

3. Annak érdekében, hogy ne törölje a virtuális géphez hozzárendelt meglévő felhasználó- vagy rendszer által hozzárendelt felügyelt identitásokat, az alábbi CURL-paranccsal listába kell sorolni a virtuális géphez rendelt identitástípusokat. Ha felügyelt identitásokkal van hozzárendelve a virtuálisgép-méretezési csoporthoz, azok az érték alatt megjelenik `identity` a listában.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra.

    Ha van olyan felhasználó vagy rendszer által hozzárendelt felügyelt identitása, amely a válaszban megadott értékben van hozzárendelve a virtuális géphez, ugorjon az 5. lépésre, amely bemutatja, hogyan őrizhető meg a rendszer által hozzárendelt felügyelt identitás, miközben felhasználó által hozzárendelt felügyelt identitást ad hozzá a virtuális `identity` géphez.

4. Ha nem rendel hozzá felhasználó által hozzárendelt felügyelt identitásokat a virtuális géphez, a következő CURL-paranccsal hívhatja meg a Azure Resource Manager REST-végpontot az első felhasználó által hozzárendelt felügyelt identitás hozzárendelése a virtuális géphez.

   Az alábbi példa egy felhasználó által hozzárendelt felügyelt identitást rendel hozzá egy myVM nevű virtuális géphez `ID1` a  *myResourceGroup erőforráscsoportban.*  Cserélje le az helyére az előző lépésben kapott értéket, amikor tulajdonosi hozzáférési jogkivonatot kért, és az értéket a `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` környezetének megfelelően.

   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra.        |
 
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

   **API VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra.        | 

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

5. Ha már van felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitása a virtuális géphez rendelve:
   
   **API VERSION 2018-06-01**

   Adja hozzá a felhasználó által hozzárendelt felügyelt identitást a `userAssignedIdentities` szótár értékhez.
    
   Ha például rendszer által hozzárendelt felügyelt identitással és a felhasználó által hozzárendelt felügyelt identitással van hozzárendelve a virtuális géphez, és hozzá szeretné adni a felhasználó által hozzárendelt felügyelt `ID1` `ID2` identitást:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra.        | 

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

   **API VERSION 2017-12-01**

   Az új, felhasználó által hozzárendelt felügyelt identitás hozzáadása közben őrizze meg a felhasználó által hozzárendelt felügyelt identitásokat, amelyek értékét meg `identityIds` szeretné tartani a tömbben.

   Ha például rendszer által hozzárendelt felügyelt identitással és a felhasználó által hozzárendelt felügyelt identitással van jelenleg hozzárendelve a virtuális géphez, és hozzá szeretné adni a felhasználó által hozzárendelt felügyelt `ID1` `ID2` identitást: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra.        | 

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása azure-beli virtuális gépről

Ha el szeretne távolítani egy felhasználó által hozzárendelt identitást egy virtuális gépről, a fiókjának a Virtuális gépek közreműködője [szerepkör-hozzárendelésre van](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szüksége.

1. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuális gép rendszer által hozzárendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak érdekében, hogy ne töröljön olyan meglévő, felhasználó által hozzárendelt felügyelt identitásokat, amelyek továbbra is hozzá vannak rendelve a virtuális géphez, vagy ne távolítsa el a rendszer által hozzárendelt felügyelt identitást, a következő CURL-paranccsal listába kell sorolni a felügyelt identitásokat: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra.
 
   Ha felügyelt identitásokat rendelt a virtuális géphez, azok a válaszban megjelenik az `identity` értékben.

   Ha például felhasználó által hozzárendelt felügyelt identitásokkal és a virtuális géphez van hozzárendelve, és csak a rendszer által hozzárendelt identitást szeretné hozzárendelni és `ID1` `ID2` `ID1` megőrizni:
   
   **API VERSION 2018-06-01**

   Adja hozzá a következőt a felhasználó által hozzárendelt `null` felügyelt identitáshoz, amit el szeretne távolítani:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra.        | 

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

   **API VERSION 2017-12-01**

   Csak a felhasználó által hozzárendelt felügyelt identitás(okat) őrizze meg a `identityIds` tömbben:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra.        | 

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

Ha a virtuális gép rendszer által hozzárendelt és felhasználó által hozzárendelt felügyelt identitásokkal is rendelkezik, az alábbi paranccsal eltávolíthatja az összes felhasználó által hozzárendelt felügyelt identitást.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H "Authorization:Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Kérelemfejlécek**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra. | 

**Kérelem törzse**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Ha a virtuális gép csak felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik, és mindegyiket el szeretné távolítani, használja a következő parancsot:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Kérelemfejlécek**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra.| 

**Kérelem törzse**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Következő lépések

A felhasználó által hozzárendelt felügyelt identitások REST használatával való létrehozásáról, listáról vagy törléséről a következőt lásd:

- [Felhasználó által hozzárendelt felügyelt identitások létrehozása, felsorolása vagy törlése REST API hívásokkal](how-to-manage-ua-identity-rest.md)
