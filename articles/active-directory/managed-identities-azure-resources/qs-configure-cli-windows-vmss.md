---
title: Felügyelt identitások konfigurálása virtuálisgép-méretezési hálózaton – Azure CLI – Azure AD
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások Azure-beli virtuálisgép-méretezési csoporton való konfigurálásához az Azure CLI használatával.
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
ms.openlocfilehash: 0e6e7e1724247c0e6d2b9db2fdf6980e8ef553c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788186"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Azure-erőforrások felügyelt identitásának konfigurálása virtuálisgép-méretezési készleten az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt identitást biztosít az Azure-szolgáltatásoknak a Azure Active Directory. Ezzel az identitással bármely Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet anélkül, hogy hitelesítő adatokat ad meg a kódban. 

Ebből a cikkből megtudhatja, hogyan végezheti el a következő felügyelt identitásokat az Azure-erőforrások műveleteihez egy Azure-beli virtuálisgép-méretezési készleten az Azure CLI használatával:
- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure-beli virtuálisgép-méretezési csoporton
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása azure-beli virtuálisgép-méretezési csoporton

Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, lásd: Mik azok az [Azure-erőforrások felügyelt identitások?](overview.md). A rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitástípusokkal kapcsolatos további információkért lásd: [Felügyelt identitástípusok.](overview.md#managed-identity-types)

- A cikkben említett felügyeleti műveletek végrehajtásához a fiókjának a következő Azure-beli szerepköralapú hozzáférés-vezérlési hozzárendelésre van szüksége:

  - [Virtuális gépek közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) egy virtuálisgép-méretezési csoport létrehozásához, valamint a rendszer- és/vagy felhasználó által hozzárendelt felügyelt identitások engedélyezéséhez és eltávolításához egy virtuálisgép-méretezési csoportból.

  - [Felügyelt identitás közreműködője](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör felhasználó által hozzárendelt felügyelt identitás létrehozásához.

  - [Felügyelt identitáskezelői](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör a felhasználó által hozzárendelt felügyelt identitások virtuálisgép-méretezési csoportokhoz való hozzárendeléshez és eltávolításához.

  > [!NOTE]
  > Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le egy Azure-beli virtuálisgép-méretezési csoport rendszer által hozzárendelt felügyelt identitását az Azure CLI használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése Azure-beli virtuálisgép-méretezési csoport létrehozása során

Virtuálisgép-méretezési csoport létrehozása engedélyezett rendszer által hozzárendelt felügyelt identitással:

1. Az [](../../azure-resource-manager/management/overview.md#terminology) az group create használatával hozzon létre egy erőforráscsoportot a virtuálisgép-méretezési csoport és a kapcsolódó erőforrások el különüléséhez és üzembe [helyezéséhez.](/cli/azure/group/#az_group_create) Ezt a lépést kihagyhatja, ha már rendelkezik olyan erőforráscsoporttal, amely helyett a következőt szeretné használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. [](/cli/azure/vmss/#az_vmss_create) Virtuálisgép-méretezési csoport létrehozása. Az alábbi példa egy *myVMSS* nevű virtuálisgép-méretezési készletet hoz létre egy rendszer által hozzárendelt felügyelt identitással, a paraméter által `--assign-identity` kértek szerint. Az `--admin-username` és `--admin-password` paraméterek adják meg a virtuális gép bejelentkeztetéséhez tartozó rendszergazdanevet és -jelszót. A környezetnek megfelelően frissítse ezeket az értékeket: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése meglévő Azure-beli virtuálisgép-méretezési csoporton

Ha engedélyeznie [kell](/cli/azure/vmss/identity/#az_vmss_identity_assign) a rendszer által hozzárendelt felügyelt identitást egy meglévő Azure-beli virtuálisgép-méretezési csoporton:

```azurecli-interactive
az vmss identity assign -g myResourceGroup -n myVMSS
```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás letiltása Azure-beli virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési csoportja van, amely már nincs szüksége a rendszer által hozzárendelt felügyelt identitásra, de továbbra is felhasználó által hozzárendelt felügyelt identitásra van szüksége, használja a következő parancsot:

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Ha olyan virtuális géppel rendelkezik, amely már nem rendelkezik rendszer által hozzárendelt felügyelt identitással, és nem rendelkezik felhasználó által hozzárendelt felügyelt identitásokkal, használja a következő parancsot:

> [!NOTE]
> Az érték `none` megkülönbözteti a kis- és nagybetűket. Kisbetűsnek kell lennie. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és távolíthatja el a felhasználó által hozzárendelt felügyelt identitásokat az Azure CLI használatával.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoport létrehozása során

Ez a szakasz végigvezeti a virtuálisgép-méretezési csoport létrehozásának és a felhasználó által hozzárendelt felügyelt identitás virtuálisgép-méretezési csoporthoz való hozzárendelésének folyamatán. Ha már rendelkezik használni kívánt virtuálisgép-méretezési csoportokkal, hagyja ki ezt a szakaszt, és folytassa a következővel.

1. Ezt a lépést kihagyhatja, ha már rendelkezik olyan erőforráscsoporttal, amit használni szeretne. Hozzon [létre egy erőforráscsoportot](~/articles/azure-resource-manager/management/overview.md#terminology) a felhasználó által hozzárendelt felügyelt identitás el tartalmazottságához és üzembe helyezéséhez az az group [create használatával.](/cli/azure/group/#az_group_create) Ne felejtse el a `<RESOURCE GROUP>` és `<LOCATION>` paraméterek értékeit a saját értékeire cserélni. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az_identity_create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   A válasz a létrehozott, felhasználó által hozzárendelt felügyelt identitás adatait tartalmazza, az alábbihoz hasonlóan. A felhasználó által hozzárendelt felügyelt identitáshoz `id` rendelt erőforrásértéket a következő lépésben használjuk.

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

3. [](/cli/azure/vmss/#az_vmss_create) Virtuálisgép-méretezési csoport létrehozása. Az alábbi példa létrehoz egy virtuálisgép-méretezési készletet, amely az új, felhasználó által hozzárendelt felügyelt identitáshoz van társítva a `--assign-identity` paraméterben megadottak szerint. A `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` és `<USER ASSIGNED IDENTITY>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő virtuálisgép-méretezési csoporthoz

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [az identity create](/cli/azure/identity#az_identity_create) paranccsal.  A `-g` paraméter adja meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lesz hozva, a `-n` paraméter pedig annak nevét határozza meg. Ne felejtse el a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterek értékeit a saját értékeire cserélni:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   A válasz a létrehozott, felhasználó által hozzárendelt felügyelt identitás adatait tartalmazza, az alábbihoz hasonlóan.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [Rendelje](/cli/azure/vmss/identity) hozzá a felhasználó által hozzárendelt felügyelt identitást a virtuálisgép-méretezési csoporthoz. Ne felejtse el a `<RESOURCE GROUP>` és `<VIRTUAL MACHINE SCALE SET NAME>` paraméterek értékeit a saját értékeire cserélni. A a felhasználó által hozzárendelt identitás erőforrás-tulajdonsága az előző `<USER ASSIGNED IDENTITY>` `name` lépésben létrehozottak szerint:

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása azure-beli virtuálisgép-méretezési csoportból

Felhasználó [által](/cli/azure/vmss/identity#az_vmss_identity_remove) hozzárendelt felügyelt identitás virtuálisgép-méretezési csoportból való eltávolításához használja a következőt: `az vmss identity remove` . Ha ez az egyetlen felhasználó által hozzárendelt felügyelt identitás, amely hozzá van rendelve a virtuálisgép-méretezési csoporthoz, a rendszer eltávolítja az `UserAssigned` identitástípus értékből.  Ne felejtse el a `<RESOURCE GROUP>` és `<VIRTUAL MACHINE SCALE SET NAME>` paraméterek értékeit a saját értékeire cserélni. A a felhasználó által hozzárendelt felügyelt identitás tulajdonsága lesz, amely a virtuálisgép-méretezési csoport identitás szakaszában található a `<USER ASSIGNED IDENTITY>` `name` `az vmss identity show` használatával:

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

Ha a virtuálisgép-méretezési csoport nem rendelkezik rendszer által hozzárendelt felügyelt identitással, és el szeretné távolítani róla az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

> [!NOTE]
> Az érték `none` megkülönbözteti a kis- és nagybetűket. Kisbetűsnek kell lennie.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Ha a virtuálisgép-méretezési csoport rendszer által hozzárendelt és felhasználó által hozzárendelt felügyelt identitásokkal is rendelkezik, az összes felhasználó által hozzárendelt identitást eltávolíthatja úgy, hogy a rendszer által hozzárendelt felügyelt identitás használatára vált. Használja az alábbi parancsot:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrások felügyelt identitásának áttekintése](overview.md)
- Az Azure-beli virtuálisgép-méretezési csoport létrehozásának teljes rövid útmutatója: Virtuálisgép-méretezési csoport [létrehozása a CLI használatával](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)
