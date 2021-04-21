---
title: Felhasználó által hozzárendelt felügyelt identitás kezelése – Azure CLI – Azure AD
description: Részletes útmutató felhasználó által hozzárendelt felügyelt identitások létrehozásához, listához és törléséhez az Azure CLI használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: a26f13b71ae96f4d6593cb4a4d9107f8ef6c207c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784874"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listának vagy törlése az Azure CLI használatával


Az Azure-erőforrások felügyelt identitása felügyelt identitásokat biztosít az Azure-szolgáltatások számára a Azure Active Directory. Ezzel az identitással anélkül hitelesítheti az Azure AD-hitelesítést támogató szolgáltatásokat, hogy hitelesítő adatokat kellene megadnia a kódban. 

Ebből a cikkből megtudhatja, hogyan hozhat létre, listelhet és törölhet felhasználó által hozzárendelt felügyelt identitásokat az Azure CLI használatával.

Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, lásd: Mik azok az [Azure-erőforrások felügyelt identitások?](overview.md). A rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitástípusokkal kapcsolatos további információkért lásd: [Felügyelt identitástípusok.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!NOTE]   
> Ha módosítani szeretné a felhasználói engedélyeket az App Service Principal cli használatával való használata esetén, további engedélyeket kell adnia a szolgáltatásnévnek az Azure AD Graph API-ban, mivel a CLI egy része GET-kéréseket hajt végre a Graph API. Ellenkező esetben "A művelet végrehajtásához nem megfelelő jogosultságok" üzenet jelenik meg. Ehhez be kell lépnie az alkalmazásregisztrációba a Azure Active Directory, ki kell választania az alkalmazást, kattintson az API-engedélyek elemre, görgessen le, és válassza a Azure Active Directory Graph lehetőséget. Itt válassza az Alkalmazásengedélyek lehetőséget, majd adja hozzá a megfelelő engedélyeket. 

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

Felhasználó által hozzárendelt felügyelt identitás létrehozásához [](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) a fiókjának a Felügyelt identitás közreműködője szerepkör-hozzárendelésre van szüksége.

Az [az identity create paranccsal](/cli/azure/identity#az_identity_create) hozzon létre egy felhasználó által hozzárendelt felügyelt identitást. A `-g` paraméter megadja azt az erőforráscsoportot, ahol a felhasználó által hozzárendelt felügyelt identitást létre kell hozni, a paraméter pedig a nevét adja `-n` meg. Cserélje le a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterértékeket a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások felsorolása

A felhasználó által hozzárendelt felügyelt identitások listához/olvasásához a fiókjának a Felügyelt identitás kezelője vagy a Felügyelt identitás közreműködője szerepkör-hozzárendelésre [van](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szüksége. [](../../role-based-access-control/built-in-roles.md#managed-identity-operator)

A felhasználó által hozzárendelt felügyelt identitások listához használja az [az identity list](/cli/azure/identity#az_identity_list) parancsot. Cserélje le `<RESOURCE GROUP>` a értéket a saját értékére:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

A JSON-válaszban a felhasználó által hozzárendelt felügyelt identitások a kulcs értékét `"Microsoft.ManagedIdentity/userAssignedIdentities"` kapják `type` vissza.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

Felhasználó által hozzárendelt felügyelt identitás törléséhez a fiókjának a Felügyelt identitás közreműködője szerepkör-hozzárendelésre [van](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szüksége.

Felhasználó által hozzárendelt felügyelt identitás törléséhez használja [az az identity delete](/cli/azure/identity#az_identity_delete) parancsot.  Az -n paraméter a nevét, a -g paraméter pedig azt az erőforráscsoportot adja meg, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lett hozva. Cserélje le a `<USER ASSIGNED IDENTITY NAME>` és a paraméter értékét a saját `<RESOURCE GROUP>` értékeire:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás törlésével nem távolítja el a hivatkozást a hozzárendelt erőforrásokból. Távolítsa el ezeket a virtuális gépről/VMSS-ről az `az vm/vmss identity remove` paranccsal.

## <a name="next-steps"></a>Következő lépések

Az Azure CLI identitásparancsok teljes listájáért lásd: [az identity](/cli/azure/identity).

A felhasználó által hozzárendelt felügyelt identitás Azure-beli virtuális gépekhez való hozzárendelését lásd: Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának konfigurálása az [Azure CLI használatával](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)
