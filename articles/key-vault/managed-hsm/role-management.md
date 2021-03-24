---
title: Felügyelt HSM adatsík szerepkör-kezelés – Azure Key Vault | Microsoft Docs
description: Ez a cikk a felügyelt HSM szerepkör-hozzárendeléseinek kezelésére használható.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 4d36b2c2178c7205246cd7c59aefedef3358e473
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951742"
---
# <a name="managed-hsm-role-management"></a>A Managed HSM szerepkörkezelése

> [!NOTE]
> Key Vault két típusú erőforrást támogat: a tárolókat és a felügyelt HSM. Ez a cikk a **felügyelt HSM**-ről szól. Ha szeretné megismerni a tárolók kezelését, tekintse meg [a Key Vault kezelése az Azure CLI használatával](../general/manage-with-cli2.md)című témakört.

A Managed HSM áttekintését lásd: [Mi a felügyelt HSM?](overview.md). Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Ez a cikk bemutatja, hogyan kezelheti a felügyelt HSM-adatsíkok szerepköreit. A felügyelt HSM hozzáférés-vezérlési modelljével kapcsolatos további tudnivalókért lásd: [felügyelt HSM hozzáférés-vezérlése](access-control.md).

Ahhoz, hogy egy rendszerbiztonsági tag (például egy felhasználó, egy szolgáltatásnév, egy csoport vagy egy felügyelt identitás) lehetővé váljon a felügyelt HSM adatsík műveleteinek elvégzése, hozzá kell rendelni egy olyan szerepkört, amely lehetővé teszi ezeknek a műveleteknek a végrehajtását. Ha például egy kulcs használatával szeretné engedélyezni az alkalmazásnak a bejelentkezési műveletet, hozzá kell rendelnie egy olyan szerepkört, amely a "Microsoft. kulcstartó/managedHSM/kulcsok/aláírás/művelet" kifejezést tartalmazza az adatműveletek egyike. Egy szerepkör hozzárendelhető egy adott hatókörhöz. A felügyelt HSM helyi RBAC két hatókört támogat: HSM-Wide ( `/` vagy `/keys` ) és/Key ( `/keys/<keyname>` ).

Az összes felügyelt HSM beépített szerepkör és az általuk engedélyezett műveletek listáját lásd: [felügyelt HSM beépített szerepkörei](built-in-roles.md).

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő Azure CLI-parancsok használatához a következő elemek szükségesek:

* Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
* Az Azure CLI verziója 2.21.0 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.
* Felügyelt HSM az előfizetésében. Lásd [: gyors útmutató: felügyelt HSM kiépítése és aktiválása az Azure CLI használatával](quick-create-cli.md) a FELÜGYELt HSM üzembe helyezéséhez és aktiválásához.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha a parancssori felülettel szeretne bejelentkezni az Azure-ba, írja be a következőt:

```azurecli
az login
```

A parancssori felületről való bejelentkezéssel kapcsolatos további információkért lásd: [Bejelentkezés az Azure CLI](/cli/azure/authenticate-azure-cli) használatával

## <a name="create-a-new-role-assignment"></a>Új szerepkör-hozzárendelés létrehozása

### <a name="assign-roles-for-all-keys"></a>Szerepkörök társítása az összes kulcshoz

A (z `az keyvault role assignment create` ) parancs használatával rendeljen hozzá egy **felügyelt HSM kriptográfiai** vezető szerepkört a felhasználóhoz az **Felhasználó2 \@ contoso.com** nevű felhasználó által azonosított, a ContosoHSM összes  **kulcsához** (hatóköréhez `/keys` ).

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Szerepkör társítása egy adott kulcshoz

A `az keyvault role assignment create` parancs használatával rendeljen hozzá egy **felügyelt HSM kriptográfiai** vezető szerepkört a felhasználóhoz az egyszerű felhasználónév **Felhasználó2 \@ contoso.com** egy **myrsakey** nevű adott kulcshoz.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Meglévő szerepkör-hozzárendelések listázása

`az keyvault role assignment list`A szerepkör-hozzárendelések listázására használható.

Összes szerepkör-hozzárendelés a hatókör/(alapértelmezés szerint, ha nincs megadva hatókör) az összes felhasználó számára (alapértelmezés szerint, ha nincs megadva a--megbízott)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Egy adott felhasználó HSM-szintjén található összes szerepkör-hozzárendelés **user1@contoso.com** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> Ha a hatókör/(vagy a/Keys) a LIST parancs csak a legfelső szinten lévő összes szerepkör-hozzárendelést listázza, és nem jeleníti meg a szerepkör-hozzárendeléseket az egyes kulcsok szintjén.

Egy adott felhasználóhoz tartozó összes szerepkör-hozzárendelés **user2@contoso.com** egy adott kulcs **myrsakey**.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Meghatározott szerepkör-hozzárendelés a szerepkör által **felügyelt HSM titkosítási tisztje** számára egy adott felhasználó számára egy adott **user2@contoso.com** kulcs **myrsakey**


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Szerepkör-hozzárendelés törlése

A `az keyvault role assignment delete` paranccsal törölheti a **felügyelt HSM kriptográfiai főigazgatói** szerepkört, amely a **myrsakey2** **\@ contoso.com felhasználói Felhasználó2** van hozzárendelve.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Az összes elérhető szerepkör-definíció listázása

`az keyvault role definition list`A parancs használatával listázhatja az összes szerepkör-definíciót.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="create-a-new-role-definition"></a>Új szerepkör-definíció létrehozása

A felügyelt HSM számos beépített (előre definiált) szerepkörrel rendelkezik, amelyek a leggyakoribb felhasználási helyzetekben hasznosak. Megadhatja a saját szerepkörét a szerepkör által elvégezhető konkrét műveletek listájával. Ezt követően ezt a szerepkört hozzárendelheti a résztvevőhöz, hogy az engedélyt adjon nekik a megadott műveletekhez. 

Használja a `az keyvault role definition create` parancsot egy **Egyéni szerepkört** használó SZEREPKÖRhöz egy JSON-karakterlánc használatával.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
    "roleName": "My Custom Role",
    "description": "The description of the custom rule.",
    "actions": [],
    "notActions": [],
    "dataActions": [
        "Microsoft.KeyVault/managedHsm/keys/read/action"
    ],
    "notDataActions": []
}'
```

Használja a `az keyvault role definition create` parancsot egy **my-custom-role-definition.js** nevű fájl egyik szerepköréhez, amely egy SZEREPKÖR-definíció JSON-karakterláncát tartalmazza. Lásd a fenti példát.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition @my-custom-role-definition.json
```

## <a name="show-details-of-a-role-definition"></a>Szerepkör-definíció részleteinek megjelenítése

A `az keyvault role definition show` parancs használatával megtekintheti egy adott szerepkör-definíció részleteit a név (GUID) használatával.

```azurecli-interactive
az keyvault role definition show --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## <a name="update-a-custom-role-definition"></a>Egyéni szerepkör-definíció frissítése

A `az keyvault role definition update` paranccsal egy **Egyéni szerepkörrel** rendelkező szerepkört használhat egy JSON-karakterlánc használatával.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
            "roleName": "My Custom Role",
            "name": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "id": "Microsoft.KeyVault/providers/Microsoft.Authorization/roleDefinitions/xxxxxxxx-
        xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "description": "The description of the custom rule.",
            "actions": [],
            "notActions": [],
            "dataActions": [
                "Microsoft.KeyVault/managedHsm/keys/read/action",
                "Microsoft.KeyVault/managedHsm/keys/write/action",
                "Microsoft.KeyVault/managedHsm/keys/backup/action",
                "Microsoft.KeyVault/managedHsm/keys/create"
            ],
            "notDataActions": []
        }'
```

## <a name="delete-custom-role-definition"></a>Egyéni szerepkör-definíció törlése

A `az keyvault role definition delete` parancs használatával megtekintheti egy adott szerepkör-definíció részleteit a név (GUID) használatával. 
```azurecli-interactive
az keyvault role definition delete --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

> [!NOTE]
> A beépített szerepkörök nem törölhetők. Az egyéni szerepkörök törlésekor az adott egyéni szerepkört használó összes szerepkör-hozzárendelés megszűnik.


## <a name="next-steps"></a>Következő lépések

- Tekintse át az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)áttekintését.
- A [felügyelt HSM szerepkör-kezelésről](role-management.md) szóló oktatóanyag megtekintése
- További információ a [felügyelt HSM hozzáférés-vezérlési modelljéről](access-control.md)
- A [felügyelt HSM helyi RBAC összes beépített szerepkörének](built-in-roles.md) megjelenítése
