---
title: IdentitySelector FELHASZNÁLÓIFELÜLET-elem
description: A Azure Portal Microsoft. ManagedIdentity. IdentitySelector felhasználói felületi elemének ismertetése. Felügyelt identitások erőforráshoz való hozzárendelésére használható.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063382"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft. ManagedIdentity. IdentitySelector FELHASZNÁLÓIFELÜLET-elem

Egy, a központi telepítésben lévő erőforráshoz tartozó [felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md) hozzárendelésére szolgáló vezérlő.

## <a name="ui-sample"></a>Felhasználói felület mintája

A vezérlő a következő elemekből áll:

![Microsoft. ManagedIdentity. IdentitySelector – első lépés](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

Amikor a felhasználó kiválasztja a **Hozzáadás** lehetőséget, megnyílik a következő űrlap. A felhasználó kijelölhet egy vagy több felhasználó által hozzárendelt identitást az erőforráshoz.

![Microsoft. ManagedIdentity. IdentitySelector második lépés](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

A kiválasztott identitások a táblázatban jelennek meg. A felhasználó hozzáadhat vagy törölhet elemeket ebből a táblából.

![Microsoft. ManagedIdentity. IdentitySelector harmadik lépés](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

## <a name="schema"></a>Séma

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Példakimenet

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Megjegyzések

- **defaultValue.systemAssignedIdentity** használatával állítsa be a System Assigned Identity Options vezérlő kezdeti értékét. Az alapértelmezett érték **ki van kapcsolva**. A következő értékek engedélyezettek:
  - **On** – a rendszer hozzárendelt identitást rendel hozzá az erőforráshoz.
  - **Kikapcsolva** – a rendszerhez rendelt identitás nincs hozzárendelve az erőforráshoz.
  - **OnOnly** – a rendszer hozzárendelt identitást rendel hozzá az erőforráshoz. A felhasználók nem szerkeszthetik ezt az értéket az üzembe helyezés során.
  - **OffOnly** – a rendszerhez rendelt identitás nincs hozzárendelve az erőforráshoz. A felhasználók nem szerkeszthetik ezt az értéket az üzembe helyezés során.

- Ha a **Options. hideSystemAssignedIdentity** értéke **true (igaz**), akkor a rendszerhez rendelt identitás konfigurálására szolgáló felhasználói felület nem jelenik meg. A beállítás alapértelmezett értéke **hamis**.
- Ha a **Options. hideUserAssignedIdentity** értéke **true (igaz**), a felhasználó által hozzárendelt identitás konfigurálására szolgáló felhasználói felület nem jelenik meg. Az erőforráshoz nincs hozzárendelve felhasználóhoz rendelt identitás. A beállítás alapértelmezett értéke **hamis**.

## <a name="next-steps"></a>Következő lépések

- A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
- A felhasználói felületi elemek általános tulajdonságainak leírását lásd: [CreateUiDefinition-elemek](create-uidefinition-elements.md).