---
title: Felügyelt identitások konfigurálása Azure-beli virtuális gépen az Azure CLI használatával – Azure AD
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások Azure-beli virtuális gépen, az Azure CLI használatával való konfigurálásához.
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
ms.openlocfilehash: a8dbe9665612e888b7e7afe95a472ba6b0de8d48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762514"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának konfigurálása az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt identitást biztosít az Azure-szolgáltatásoknak a Azure Active Directory. Ezzel az identitással bármely Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet anélkül, hogy hitelesítő adatokat ad meg a kódban. 

Ebből a cikkből megtudhatja, hogyan végezheti el az Azure-erőforrások alábbi felügyelt identitásokkal kapcsolatos műveleteit egy Azure-beli virtuális gépen az Azure CLI használatával:

- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure-beli virtuális gépen
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása Azure-beli virtuális gépen

Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, lásd: Mik azok az [Azure-erőforrások felügyelt identitások?](overview.md). A rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitástípusokkal kapcsolatos további információkért lásd: [Felügyelt identitástípusok.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitást egy Azure-beli virtuális gépen az Azure CLI használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése Azure-beli virtuális gép létrehozása során

Ahhoz, hogy olyan Azure-beli virtuális gépet hozzon létre, amely számára engedélyezve van a rendszer által hozzárendelt felügyelt identitás, a fiókjának a Virtuális gépek közreműködője [szerepkör-hozzárendelésre](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) van szüksége.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Az [az group create](/cli/azure/group/#az_group_create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az_vm_create) paranccsal. Az alábbi példa létrehoz egy *myVM* nevű virtuális gépet egy rendszer által hozzárendelt felügyelt identitással, a paraméter által `--assign-identity` kértek szerint. Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése meglévő Azure-beli virtuális gépen

A rendszer által hozzárendelt felügyelt identitás virtuális gépen való engedélyezéséhez a fióknak a Virtuális gépek közreműködője [szerepkör-hozzárendelésre van](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szüksége.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az_login) paranccsal jelentkezzen be az Azure-ba. Olyan fiókot használjon, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

   ```azurecli-interactive
   az login
   ```

2. Az [az vm identity assign paranccsal](/cli/azure/vm/identity/) engedélyezze a rendszer által hozzárendelt `identity assign` identitást egy meglévő virtuális géphez:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Rendszer által hozzárendelt identitás letiltása Azure-beli virtuális gépről

Ha le szeretné tiltani a rendszer által hozzárendelt felügyelt identitást egy virtuális gépen, a fiókjának szüksége van a Virtuális gépek közreműködője [szerepkör-hozzárendelésre.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

Ha olyan virtuális géppel van, amely már nem szükséges a rendszer által hozzárendelt identitáshoz, de továbbra is felhasználó által hozzárendelt identitásra van szüksége, használja a következő parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Ha olyan virtuális géppel rendelkezik, amely már nem rendelkezik rendszer által hozzárendelt identitással, és nem rendelkezik felhasználó által hozzárendelt identitásokkal, használja a következő parancsot:

> [!NOTE]
> Az érték `none` megkülönbözteti a kis- és nagybetűket. Kisbetűsnek kell lennie. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el felhasználó által hozzárendelt felügyelt identitást egy Azure-beli virtuális gépről az Azure CLI használatával. Ha a felhasználó által hozzárendelt felügyelt identitást a virtuális géptől eltérő erőforráscsoportban hozza létre. A virtuális géphez való hozzárendeléshez a felügyelt identitás URL-címét kell használnia.
i.e. --identities "/subscriptions/ <SUBID> /resourcegroups/ <RESROURCEGROUP> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER_ASSIGNED_ID_NAME>"

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése Azure-beli virtuális gép létrehozása során

Ha a létrehozás során felhasználó által hozzárendelt identitást [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szeretne hozzárendelni egy virtuális géphez, a fiókjának a Virtuális gépek közreműködője és a [Felügyelt](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitáskezelő szerepkör-hozzárendelésre van szüksége. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Ezt a lépést kihagyhatja, ha már rendelkezik olyan erőforráscsoporttal, amit használni szeretne. Hozzon [létre egy erőforráscsoportot](~/articles/azure-resource-manager/management/overview.md#terminology) a felhasználó által hozzárendelt felügyelt identitás el tartalmazottságához és üzembe helyezéséhez az az group [create használatával.](/cli/azure/group/#az_group_create) Ne felejtse el a `<RESOURCE GROUP>` és `<LOCATION>` paraméterek értékeit a saját értékeire cserélni. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az_identity_create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   A válasz a létrehozott, felhasználó által hozzárendelt felügyelt identitás adatait tartalmazza, az alábbihoz hasonlóan. A felhasználó által hozzárendelt felügyelt identitáshoz rendelt erőforrás-azonosító értékét a következő lépésben használjuk.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az_vm_create) paranccsal. Az alábbi példa egy, az új, felhasználó által hozzárendelt identitáshoz társított virtuális gépet hoz létre a `--assign-identity` paraméterben megadottak szerint. A `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuális géphez

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fiókjának a Virtuális gépek közreműködője és a [Felügyelt](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitáskezelő szerepkör-hozzárendelésre van szüksége. [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Hozzon létre egy felhasználóhoz rendelt identitást az [az identity create](/cli/azure/identity#az_identity_create) paranccsal.  A `-g` paraméter megadja azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt identitás létrejön, és a paraméter adja meg a `-n` nevét. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

    > [!IMPORTANT]
    > A felhasználó által hozzárendelt felügyelt identitások speciális karakterekkel (például aláhúzásjel) a névben való létrehozása jelenleg nem támogatott. Használjon alfanumerikus karaktereket. Térjen vissza frissítésekért.  További információért tekintse meg a gyakori kérdéseket [és az ismert problémákat.](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A válasz a létrehozott, felhasználó által hozzárendelt felügyelt identitás adatait tartalmazza, az alábbihoz hasonlóan. 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Rendelje hozzá a felhasználó által hozzárendelt identitást a virtuális géphez [az az vm identity assign használatával.](/cli/azure/vm) Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A az előző lépésben létrehozott, felhasználó által hozzárendelt felügyelt identitás `<USER ASSIGNED IDENTITY NAME>` `name` erőforrás-tulajdonsága. Ha a felhasználó által hozzárendelt felügyelt identitást a virtuális géptől eltérő erőforráscsoportban hozta létre. A felügyelt identitás URL-címét kell használnia.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása azure-beli virtuális gépről

Ha el szeretne távolítani egy felhasználó által hozzárendelt identitást egy virtuális gépről, a fiókjának a Virtuális gépek közreműködője [szerepkör-hozzárendelésre van](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szüksége. 

Ha ez az egyetlen felhasználó által hozzárendelt felügyelt identitás, amely hozzá van rendelve a virtuális géphez, a rendszer eltávolítja az identitástípus `UserAssigned` értékből.  Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY>` a felhasználó által hozzárendelt identitás tulajdonsága lesz, amely a virtuális gép identitás szakaszában található a `name` `az vm identity show` használatával:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Ha a virtuális gép nem rendelkezik rendszer által hozzárendelt felügyelt identitással, és el szeretné távolítani róla az összes felhasználó által hozzárendelt identitást, használja a következő parancsot:

> [!NOTE]
> Az érték `none` megkülönbözteti a kis- és nagybetűket. Csak kisbetűkből lehet.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Ha a virtuális gép rendszer által hozzárendelt és felhasználó által hozzárendelt identitásokkal is rendelkezik, az összes felhasználó által hozzárendelt identitást eltávolíthatja úgy, hogy a csak a rendszer által hozzárendelt identitás használatára vált. Használja az alábbi parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Következő lépések
- [Az Azure-erőforrások felügyelt identitásának áttekintése](overview.md)
- Az Azure-beli virtuális gépek létrehozásának teljes rövid útmutatóiért lásd: 
  - [Windows rendszerű virtuális gép létrehozása a CLI-val](../../virtual-machines/windows/quick-create-cli.md)  
  - [Linux rendszerű virtuális gép létrehozása a CLI-val](../../virtual-machines/linux/quick-create-cli.md)
