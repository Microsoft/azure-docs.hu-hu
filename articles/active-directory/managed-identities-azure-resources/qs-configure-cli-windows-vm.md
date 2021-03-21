---
title: Felügyelt identitások konfigurálása az Azure-beli virtuális gépen az Azure CLI-vel – Azure AD
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálásához Azure-beli virtuális gépen az Azure CLI használatával.
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
ms.openlocfilehash: 90b18c2556796f23be0c1135b0bad0d53368a46d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590953"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Felügyelt identitások konfigurálása Azure-beli virtuális gépen lévő Azure-erőforrásokhoz az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt identitással. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebben a cikkben az Azure CLI használatával megtudhatja, hogyan hajthatja végre a következő felügyelt identitásokat az Azure-erőforrások műveleteihez egy Azure-beli virtuális gépen:

- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure-beli virtuális gépen
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása egy Azure-beli virtuális gépen

Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](overview.md)című témakört. A rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitási típusok megismeréséhez lásd: [felügyelt identitások típusai](overview.md#managed-identity-types).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan engedélyezheti és tilthatja le a rendszerhez rendelt felügyelt identitást egy Azure-beli virtuális gépen az Azure CLI használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure-beli virtuális gép létrehozásakor

Ha olyan Azure virtuális gépet szeretne létrehozni, amelyen engedélyezve van a rendszerhez rendelt felügyelt identitás, a fióknak szüksége van a [virtuálisgép-közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Az [az group create](/cli/azure/group/#az-group-create) paranccsal hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) a virtuális gép és az ahhoz kapcsolódó erőforrások elkülönítéséhez és üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha inkább egy meglévő erőforráscsoportot kíván használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az-vm-create) paranccsal. Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre egy rendszer által hozzárendelt felügyelt identitással, a paraméter által kért módon `--assign-identity` . Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Rendszerhez rendelt felügyelt identitás engedélyezése meglévő Azure-beli virtuális gépen

Ha engedélyezni szeretné a rendszer által hozzárendelt felügyelt identitást egy virtuális gépen, a fióknak szüksége van a [virtuálisgép-közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login](/cli/azure/reference-index#az-login) paranccsal jelentkezzen be az Azure-ba. Használjon olyan fiókot, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

   ```azurecli-interactive
   az login
   ```

2. Használja az [az VM Identity assign](/cli/azure/vm/identity/) `identity assign` parancsot a paranccsal a rendszer által hozzárendelt identitás engedélyezése meglévő virtuális géphez:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Rendszer által hozzárendelt identitás letiltása egy Azure-beli virtuális gépről

Ha le szeretné tiltani a rendszerhez rendelt felügyelt identitást egy virtuális gépen, a fióknak szüksége van a [virtuálisgép-közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

Ha olyan virtuális géppel rendelkezik, amelynek már nincs szüksége a rendszer által hozzárendelt identitásra, de továbbra is felhasználó által hozzárendelt identitásokra van szüksége, használja a következő parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Ha olyan virtuális géppel rendelkezik, amelyhez már nincs szükség a rendszerhez rendelt identitásra, és nincs felhasználó által hozzárendelt identitása, használja a következő parancsot:

> [!NOTE]
> Az érték megkülönbözteti a kis-és nagybetűket `none` . Kisbetűnek kell lennie. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználóhoz rendelt felügyelt identitást egy Azure-beli virtuális gépről az Azure CLI használatával. Ha a felhasználó által hozzárendelt felügyelt identitást más RG-ban hozza létre, mint a virtuális gép. A felügyelt identitás URL-címét kell használnia ahhoz, hogy hozzárendelje a virtuális géphez.
például--identitások "/Subscriptions/ <SUBID> /Resourcegroups/ <RESROURCEGROUP> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER_ASSIGNED_ID_NAME>"

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy Azure-beli virtuális gép létrehozásakor

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális GÉPHEZ a létrehozása során, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) és [felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Ezt a lépést kihagyhatja, ha már rendelkezik egy használni kívánt erőforráscsoport-csoporttal. Hozzon létre egy [erőforráscsoportot](~/articles/azure-resource-manager/management/overview.md#terminology) a felhasználó által hozzárendelt felügyelt identitás tárolásához és üzembe helyezéséhez az [az Group Create](/cli/azure/group/#az-group-create)paranccsal. Ne felejtse el a `<RESOURCE GROUP>` és `<LOCATION>` paraméterek értékeit a saját értékeire cserélni. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   A válasz a következőhöz hasonló, felhasználó által hozzárendelt felügyelt identitás részleteit tartalmazza. A felhasználó által hozzárendelt felügyelt identitáshoz rendelt erőforrás-azonosító érték a következő lépésben használatos.

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

3. Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm/#az-vm-create) paranccsal. A következő példa létrehoz egy virtuális gépet, amely az új, felhasználó által hozzárendelt identitáshoz van társítva a `--assign-identity` paraméterben megadott módon. A `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuális géphez

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) és [felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Hozzon létre egy felhasználóhoz rendelt identitást az [az identity create](/cli/azure/identity#az-identity-create) paranccsal.  A `-g` paraméter határozza meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt identitás létrejött, és a `-n` paraméter a nevét adja meg. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

    > [!IMPORTANT]
    > A felhasználó által hozzárendelt, speciális karakterekkel (például aláhúzással) rendelkező felügyelt identitások létrehozása jelenleg nem támogatott. Alfanumerikus karaktereket használjon. Térjen vissza frissítésekért.  További információ: [Gyakori kérdések és ismert problémák](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A válasz a következőhöz hasonló, felhasználó által hozzárendelt felügyelt identitás részleteit tartalmazza. 

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

2. Rendelje hozzá a felhasználó által hozzárendelt identitást a virtuális géphez az [az VM Identity assign](/cli/azure/vm)paranccsal. Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A a `<USER ASSIGNED IDENTITY NAME>` felhasználó által hozzárendelt felügyelt identitás erőforrás `name` -tulajdonsága az előző lépésben létrehozott módon. Ha a felhasználó által hozzárendelt felügyelt identitást más RG-ban hozta létre, mint a virtuális gép. A felügyelt identitás URL-címét kell használnia.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása Azure-beli virtuális gépről

A felhasználó által hozzárendelt identitás egy virtuális géphez való eltávolításához a fióknak szüksége van a [virtuálisgép-közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelésre. 

Ha ez az egyetlen, a virtuális géphez hozzárendelt, felhasználó által hozzárendelt felügyelt identitás, a `UserAssigned` rendszer eltávolítja az azonosító típusú értékből.  Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A a `<USER ASSIGNED IDENTITY>` felhasználó által hozzárendelt identitás `name` tulajdonsága lesz, amely a virtuális gép identitás szakaszában található a következő használatával `az vm identity show` :

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Ha a virtuális gépnek nincs rendszerhez rendelt felügyelt identitása, és el szeretné távolítani az összes felhasználó által hozzárendelt identitást, használja a következő parancsot:

> [!NOTE]
> Az érték megkülönbözteti a kis-és nagybetűket `none` . Kisbetűnek kell lennie.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Ha a virtuális gépen a rendszerhez hozzárendelt és felhasználó által hozzárendelt identitások is vannak, akkor az összes felhasználó által hozzárendelt identitást eltávolíthatja úgy, hogy csak a rendszer által hozzárendelt használatára vált. Használja az alábbi parancsot:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Következő lépések
- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- A teljes körű Azure-beli virtuális gépek létrehozásához a következő témakörben talál további információt: 
  - [Windows rendszerű virtuális gép létrehozása a parancssori felülettel](../../virtual-machines/windows/quick-create-cli.md)  
  - [Linux rendszerű virtuális gép létrehozása a parancssori felülettel](../../virtual-machines/linux/quick-create-cli.md)
