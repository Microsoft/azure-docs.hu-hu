---
title: Felügyelt identitások konfigurálása Azure-beli virtuálisgép-méretezési készleten a REST használatával – Azure AD
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások Konfigurálásához egy Azure-beli virtuálisgép-méretezési csoporton a CURL használatával a REST API hívásokhoz.
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
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 585e31ab566cc990af2819fcf9cdde0506560208
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780172"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Azure-erőforrások felügyelt identitásának konfigurálása virtuálisgép-méretezési REST API használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt rendszeridentitást biztosít az Azure-szolgáltatások számára a Azure Active Directory. Ezzel az identitással bármely Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet anélkül, hogy hitelesítő adatokat ad meg a kódban. 

Ebben a cikkben a CURL használatával az Azure Resource Manager REST-végpontra való hívásokkal megtanulja, hogyan végezheti el a következő felügyelt identitásokat az Azure-erőforrások műveleteihez egy virtuálisgép-méretezési készleten:

- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure-beli virtuálisgép-méretezési csoporton
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása azure-beli virtuálisgép-méretezési csoporton

Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, lásd: Mik azok az [Azure-erőforrások felügyelt identitások?](overview.md). A rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitástípusokkal kapcsolatos további információkért lásd: [Felügyelt identitástípusok.](overview.md#managed-identity-types)

- A cikkben említett felügyeleti műveletek végrehajtásához a fiókjának a következő Azure-beli szerepkör-hozzárendeléseket kell elvégeznie:

  - [Virtuális gépek közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) egy virtuálisgép-méretezési csoport létrehozásához, valamint a rendszer- és/vagy felhasználó által hozzárendelt felügyelt identitások engedélyezéséhez és eltávolításához egy virtuálisgép-méretezési csoportból.

  - [Felügyelt identitás közreműködője](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör felhasználó által hozzárendelt felügyelt identitás létrehozásához.

  - [Felügyelt identitáskezelői](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör felhasználó által hozzárendelt identitások virtuálisgép-méretezési csoportokhoz való hozzárendelése és eltávolítása a virtuálisgép-méretezési csoportokban.

  > [!NOTE]
  > Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoporton a CURL használatával a Azure Resource Manager REST-végpontra.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése virtuálisgép-méretezési csoport létrehozása során

Ha olyan virtuálisgép-méretezési csoportokat hoz létre, amelyeken engedélyezve van a rendszer által hozzárendelt felügyelt identitás, létre kell hoznia egy virtuálisgép-méretezési készletet, és le kellkérni egy hozzáférési jogkivonatot, amely a CURL használatával hívja meg a Resource Manager-végpontot a rendszer által hozzárendelt felügyelt identitástípus értékével.

1. Az [](../../azure-resource-manager/management/overview.md#terminology) az group create használatával hozzon létre egy erőforráscsoportot a virtuálisgép-méretezési csoport és a kapcsolódó erőforrások el különüléséhez és üzembe [helyezéséhez.](/cli/azure/group/#az_group_create) Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Hozzon létre [egy hálózati adaptert](/cli/azure/network/nic#az_network_nic_create) a virtuálisgép-méretezési csoport számára:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához egy rendszer által hozzárendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. A Azure Cloud Shell hozzon létre egy virtuálisgép-méretezési készletet a CURL használatával a Azure Resource Manager REST-végpont hívása érdekében. Az alábbi példa egy *myVMSS* nevű virtuálisgép-méretezési csoportot hoz létre a *myResourceGroup* csoportban egy rendszer által hozzárendelt felügyelt identitással, amelyet a kérelem törzsében a érték `"identity":{"type":"SystemAssigned"}` azonosít. Cserélje le az helyére az előző lépésben kapott értéket, amikor tulajdonosi hozzáférési jogkivonatot kért, és az értéket a `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` környezetének megfelelően.

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra. | 

   **Kérelem törzse**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
   ```  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése meglévő virtuálisgép-méretezési csoporton

A rendszer által hozzárendelt felügyelt identitás meglévő virtuálisgép-méretezési csoporton való engedélyezéséhez be kell szereznie egy hozzáférési jogkivonatot, majd a CURL használatával meg kell hívnia az Resource Manager REST-végpontot az identitástípus frissítéséhez.

1. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához egy rendszer által hozzárendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ```

2. A következő CURL-paranccsal hívja meg a Azure Resource Manager REST-végpontot, hogy engedélyezze a rendszer által hozzárendelt felügyelt identitást a virtuálisgép-méretezési csoporton a kérelem törzsében `{"identity":{"type":"SystemAssigned"}` a *myVMSS* nevű virtuálisgép-méretezési csoport értéke alapján azonosítottak szerint.  Cserélje le a helyére azt az értéket, amit az előző lépésben kapott, amikor tulajdonosi hozzáférési jogkivonatot kért, és az értéket a `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` környezetének megfelelően.
   
   > [!IMPORTANT]
   > Annak érdekében, hogy ne töröljön a virtuálisgép-méretezési csoporthoz hozzárendelt, felhasználó által hozzárendelt felügyelt identitásokat, a következő CURL-paranccsal listába kell sorolni a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Ha van felhasználó által hozzárendelt felügyelt identitása a virtuálisgép-méretezési csoporthoz a válaszban meghatározott értékkel, ugorjon a 3. lépésre, amely bemutatja, hogyan őrizze meg a felhasználó által hozzárendelt felügyelt identitásokat, miközben engedélyezi a rendszer által hozzárendelt felügyelt identitásokat a `identity` virtuálisgép-méretezési csoporton.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra. | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned"
       }
    }
   ```

3. Ha engedélyezni szeretné a rendszer által hozzárendelt felügyelt identitást egy meglévő, felhasználó által hozzárendelt felügyelt identitásokkal egy virtuálisgép-méretezési csoporton, hozzá kell adni az `SystemAssigned` `type` értéket.  
   
   Ha például a virtuálisgép-méretezési csoport felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik, és hozzá van rendelve, és rendszer által hozzárendelt felügyelt identitást szeretne hozzáadni a virtuálisgép-méretezési csoporthoz, használja a következő `ID1` `ID2` CURL-hívást. Cserélje le `<ACCESS TOKEN>` a és `<SUBSCRIPTION ID>` a értékeket a környezetének megfelelő értékekre.

   Az API-verzió a felhasználó által hozzárendelt felügyelt identitásokat szótár formátumban tárolja, nem pedig az API-verzióban használt `2018-06-01` `userAssignedIdentities` `identityIds` tömbformátumban. `2017-12-01`
   
   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra. |
 
   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
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

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás letiltása virtuálisgép-méretezési csoportból

Ha le szeretné tiltani a rendszer által hozzárendelt identitást egy meglévő virtuálisgép-méretezési csoporton, be kell szereznie egy hozzáférési jogkivonatot, majd a CURL használatával meg kell hívnia az Resource Manager REST-végpontot az identitástípus frissítéséhez a következőre: `None` .

1. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához egy rendszer által hozzárendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Frissítse a virtuálisgép-méretezési készletet a CURL használatával, hogy Azure Resource Manager REST-végpontot a rendszer által hozzárendelt felügyelt identitás letiltásához.  Az alábbi példa letiltja a rendszer által hozzárendelt felügyelt identitást, amelyet a kérelem törzsében a `{"identity":{"type":"None"}}` *myVMSS nevű* virtuálisgép-méretezési csoport értéke azonosít.  Cserélje le az helyére az előző lépésben kapott értéket, amikor tulajdonosi hozzáférési jogkivonatot kért, és az értéket a `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` környezetének megfelelően.

   > [!IMPORTANT]
   > Annak érdekében, hogy ne töröljön a virtuálisgép-méretezési csoporthoz hozzárendelt meglévő, felhasználó által hozzárendelt felügyelt identitásokat, a következő CURL-paranccsal listába kell sorolni a felhasználó által hozzárendelt felügyelt identitásokat: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"` . Ha felhasználó által hozzárendelt felügyelt identitás van hozzárendelve a virtuálisgép-méretezési csoporthoz, ugorjon a 3. lépésre, amely bemutatja, hogyan őrizze meg a felhasználó által hozzárendelt felügyelt identitásokat, miközben eltávolítja a rendszer által hozzárendelt felügyelt identitást a virtuálisgép-méretezési csoportból.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
          "type":"None"
       }
    }
   ```

   Ha el szeretné távolítani a rendszer által hozzárendelt felügyelt identitást egy felhasználó által hozzárendelt felügyelt identitásokat használó virtuálisgép-méretezési csoportból, távolítsa el az értéket, miközben megtartja az értéket és a szótár értékeit, ha a `SystemAssigned` `{"identity":{"type:" "}}` `UserAssigned` `userAssignedIdentities` **2018-06-01 API-verziót használja.** Ha a **2017-12-01-es** vagy korábbi API-verziót használja, tartsa meg a `identityIds` tömböt.

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el felhasználó által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoporton a CURL használatával, hogy hívásokat kezdeményezjen a Azure Resource Manager REST-végpontra.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoport létrehozása során

1. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport rendszer által hozzárendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Hozzon létre [egy hálózati adaptert](/cli/azure/network/nic#az_network_nic_create) a virtuálisgép-méretezési csoport számára:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport rendszer által hozzárendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található utasítások alapján: [Felhasználó által hozzárendelt felügyelt identitás létrehozása.](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)

5. Hozzon létre egy virtuálisgép-méretezési csoport a CURL használatával a Azure Resource Manager REST-végpontot. Az alábbi példa létrehoz egy *myVMSS* nevű virtuálisgép-méretezési csoportot a *myResourceGroup erőforráscsoportban* egy felhasználó által hozzárendelt felügyelt identitással, amelyet a kérelem törzsében a érték `ID1` `"identity":{"type":"UserAssigned"}` azonosít. Cserélje le a helyére azt az értéket, amit az előző lépésben kapott, amikor tulajdonosi hozzáférési jogkivonatot kért, és az értéket a `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` környezetének megfelelően.
 
   **API VERSION 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra. | 

   **Kérelem törzse**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **API VERSION 2017-12-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra. |
 
   **Kérelem törzse**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuálisgép-méretezési csoporthoz

1. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport létrehozásához egy rendszer által hozzárendelt felügyelt identitással.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az itt található, Felhasználó által hozzárendelt [felügyelt identitás létrehozása útmutató alapján.](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity)

3. Annak érdekében, hogy ne törölje a virtuálisgép-méretezési csoporthoz hozzárendelt meglévő felhasználó- vagy rendszer által hozzárendelt felügyelt identitásokat, az alábbi CURL-paranccsal ki kell sorolni a virtuálisgép-méretezési csoporthoz rendelt identitástípusokat. Ha felügyelt identitásokat rendelt a virtuálisgép-méretezési csoporthoz, azok az értékben vannak `identity` felsorolva.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Engedélyezés*     | Kötelező. Állítsa érvényes hozzáférési `Bearer` jogkivonatra. |   
 

4. Ha nincs hozzárendelve felhasználó vagy rendszer által hozzárendelt felügyelt identitás a virtuálisgép-méretezési csoporthoz, a következő CURL-paranccsal hívhatja meg a Azure Resource Manager REST-végpontot az első felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez a virtuálisgép-méretezési csoporthoz.  Ha felhasználó vagy rendszer által hozzárendelt felügyelt identitás(k) van hozzárendelve a virtuálisgép-méretezési csoporthoz, ugorjon az 5. lépésre, amely bemutatja, hogyan adhat hozzá több felhasználó által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoporthoz a rendszer által hozzárendelt felügyelt identitások fenntartása közben.

   Az alábbi példa egy felhasználó által hozzárendelt felügyelt identitást rendel hozzá `ID1` egy *myVMSS* nevű virtuálisgép-méretezési csoporthoz a *myResourceGroup erőforráscsoportban.*  Cserélje le az helyére az előző lépésben kapott értéket, amikor tulajdonosi hozzáférési jogkivonatot kért, és az értéket a `<ACCESS TOKEN>` `<SUBSCRIPTION ID>` környezetének megfelelően.

   **API VERSION 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **API VERSION 2017-12-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```  

5. Ha már van felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitása a virtuálisgép-méretezési csoporthoz rendelve:
   
   **API VERSION 2018-06-01**

   Adja hozzá a felhasználó által hozzárendelt felügyelt identitást `userAssignedIdentities` a szótárértékhez.

   Ha például rendszer által hozzárendelt felügyelt identitással és a felhasználó által hozzárendelt felügyelt identitással van jelenleg hozzárendelve a virtuálisgép-méretezéshez, és hozzá szeretné adni a felhasználó által hozzárendelt felügyelt `ID1` `ID2` identitást:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra. | 

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

   Ha például rendszer által hozzárendelt identitással és a felhasználó által hozzárendelt felügyelt identitással van jelenleg hozzárendelve a virtuálisgép-méretezési csoporthoz, és hozzá szeretné adni a felhasználó által hozzárendelt felügyelt `ID1` `ID2` identitást:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra. | 

   **Kérelem törzse**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoportból

1. Lekér egy bearer hozzáférési jogkivonatot, amelyet az Authorization (Engedélyezés) fejléc következő lépésében fog használni a virtuálisgép-méretezési csoport rendszer által hozzárendelt felügyelt identitással való létrehozásához.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Annak érdekében, hogy ne töröljön olyan meglévő, felhasználó által hozzárendelt felügyelt identitásokat, amelyek továbbra is hozzá vannak rendelve a virtuálisgép-méretezési csoporthoz, vagy el szeretné távolítani a rendszer által hozzárendelt felügyelt identitást, ki kell sorolni a felügyelt identitásokat a következő CURL-paranccsal:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Kérelemfejlécek**

   |Kérelem fejléce  |Leírás  |
   |---------|---------|
   |*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra. |
   
   Ha felügyelt identitásokat rendelt a virtuális géphez, azok a válaszban megjelenik az `identity` értékben. 
    
   Ha például felhasználó által hozzárendelt felügyelt identitásokkal és a virtuálisgép-méretezési csoporthoz van hozzárendelve, és csak a rendszer által hozzárendelt felügyelt identitást szeretné hozzárendelni és `ID1` `ID2` `ID1` megőrizni:

   **API VERSION 2018-06-01**

   Adja hozzá a következőt a felhasználó által hozzárendelt `null` felügyelt identitáshoz, amit el szeretne távolítani:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
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
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Ha a virtuálisgép-méretezési csoport rendszer által hozzárendelt és felhasználó által hozzárendelt felügyelt identitásokkal is rendelkezik, az összes felhasználó által hozzárendelt felügyelt identitást eltávolíthatja úgy, hogy a következő paranccsal átvált a csak a rendszer által hozzárendelt használatára:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
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
    
Ha a virtuálisgép-méretezési csoport csak felhasználó által hozzárendelt felügyelt identitásokkal rendelkezik, és mindegyiket el szeretné távolítani, használja a következő parancsot:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Kérelemfejlécek**

|Kérelem fejléce  |Leírás  |
|---------|---------|
|*Tartalomtípus*     | Kötelező. Állítsa `application/json` értékre.        |
|*Engedélyezés*     | Kötelező. Állítsa egy érvényes hozzáférési `Bearer` jogkivonatra. | 

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

- [Felhasználó által hozzárendelt felügyelt identitás létrehozása, felsorolása vagy törlése REST API hívásokkal](how-to-manage-ua-identity-rest.md)
