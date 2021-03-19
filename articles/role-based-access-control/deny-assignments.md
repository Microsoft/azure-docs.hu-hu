---
title: Azure-beli megtagadási hozzárendelések ismertetése – Azure RBAC
description: Tudnivalók az Azure-beli megtagadási hozzárendelésekről az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: a5f17f009caa9306631debf511f2c890f8f2a450
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "82733771"
---
# <a name="understand-azure-deny-assignments"></a>Az Azure deny-hozzárendelések ismertetése

A szerepkör-hozzárendeléshez hasonlóan a megtagadási *hozzárendelések* egy adott hatókörben egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz tartozó megtagadási műveletet csatolnak a hozzáférés megtagadása céljából. A hozzárendelések megtagadása esetén a felhasználók bizonyos Azure-erőforrás-műveleteket hajtanak végre, még akkor is, ha egy szerepkör-hozzárendelés hozzáférést biztosít

Ez a cikk a megtagadási hozzárendelések definiálásának módját ismerteti.

## <a name="how-deny-assignments-are-created"></a>A megtagadási hozzárendelések létrehozása

A megtagadási hozzárendeléseket az Azure hozza létre és kezeli az erőforrások elleni védelem érdekében. Az Azure-tervezetek és az Azure által felügyelt alkalmazások megtagadási hozzárendelésekkel védik a rendszerfelügyelt erőforrásokat. Az Azure-tervezetek és az Azure által felügyelt alkalmazások kizárólag a megtagadási hozzárendelések létrehozására használhatók. Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket. További információ arról, hogy a tervrajzok hogyan használják a megtagadási hozzárendeléseket az erőforrások zárolásához: [az erőforrások zárolásának megismerése az Azure-tervekben](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket.

## <a name="compare-role-assignments-and-deny-assignments"></a>Szerepkör-hozzárendelések összehasonlítása és a hozzárendelések megtagadása

A megtagadási hozzárendelések a szerepkör-hozzárendelések hasonló mintáját követik, de vannak eltérések is.

| Képesség | Szerepkör-hozzárendelés | Hozzárendelés megtagadása |
| --- | --- | --- |
| Hozzáférés biztosítása | :heavy_check_mark: |  |
| Hozzáférés megtagadása |  | :heavy_check_mark: |
| Közvetlenül is létrehozható | :heavy_check_mark: |  |
| Alkalmazás hatókörön | :heavy_check_mark: | :heavy_check_mark: |
| Rendszerbiztonsági tag kizárása |  | :heavy_check_mark: |
| Alárendelt hatókörök öröklésének megakadályozása |  | :heavy_check_mark: |
| Alkalmazás a [klasszikus előfizetés-rendszergazdai](rbac-and-directory-admin-roles.md) hozzárendelésekre |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Hozzárendelési tulajdonságok megtagadása

 Egy megtagadási hozzárendelés a következő tulajdonságokkal rendelkezik:

> [!div class="mx-tableFixed"]
> | Tulajdonság | Kötelező | Típus | Leírás |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Igen | Sztring | A megtagadási hozzárendelés megjelenített neve. A névnek egyedinek kell lennie egy adott hatókörhöz. |
> | `Description` | Nem | Sztring | A megtagadási hozzárendelés leírása. |
> | `Permissions.Actions` | Legalább egy művelet vagy egy DataActions | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza azokat a felügyeleti műveleteket, amelyekhez a megtagadási hozzárendelés blokkolja a hozzáférést. |
> | `Permissions.NotActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza a megtagadási hozzárendelésből kizárandó felügyeleti műveleteket. |
> | `Permissions.DataActions` | Legalább egy művelet vagy egy DataActions | Karakterlánc [] | Karakterláncok tömbje, amelyek meghatározzák azokat az adatműveleteket, amelyekhez a megtagadási hozzárendelés blokkolja a hozzáférést. |
> | `Permissions.NotDataActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza a megtagadási hozzárendelésből kizárandó adatműveleteket. |
> | `Scope` | Nem | Sztring | Egy karakterlánc, amely megadja azt a hatókört, amelyre a megtagadási hozzárendelés vonatkozik. |
> | `DoNotApplyToChildScopes` | No | Logikai | Meghatározza, hogy a megtagadási hozzárendelés alárendelt hatókörökre vonatkozzon-e. Az alapértelmezett érték false (hamis). |
> | `Principals[i].Id` | Yes | Karakterlánc [] | Az Azure AD rendszerbiztonsági tag-azonosítók (felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás) tömbje, amelyre a megtagadási hozzárendelés vonatkozik. Az összes résztvevőt jelölő üres GUID értékre állítva `00000000-0000-0000-0000-000000000000` . |
> | `Principals[i].Type` | No | Karakterlánc [] | Objektumtípusok tömbje, amelyet a rendszerbiztonsági tag [i]. id. az `SystemDefined` összes rendszerbiztonsági tag jelölésére van beállítva. |
> | `ExcludePrincipals[i].Id` | No | Karakterlánc [] | Az Azure AD Principal-objektumazonosítók (felhasználó, csoport, szolgáltatásnév vagy felügyelt identitás) tömbje, amelyre a megtagadási hozzárendelés nem vonatkozik. |
> | `ExcludePrincipals[i].Type` | No | Karakterlánc [] | Az ExcludePrincipals [i]. id által jelölt objektumtípusok tömbje. |
> | `IsSystemProtected` | No | Logikai | Megadja, hogy a megtagadási hozzárendelést az Azure hozta-e létre, és nem szerkeszthető és nem törölhető. Jelenleg minden megtagadási hozzárendelés a rendszer által védett. |

## <a name="the-all-principals-principal"></a>Az összes rendszerbiztonsági tag

A megtagadási hozzárendelések támogatásához a rendszer által definiált rendszerbiztonsági *tag lett* bevezetve. Ez a rendszerbiztonsági tag az összes felhasználót, csoportot, egyszerű szolgáltatást és felügyelt identitást jelképezi egy Azure AD-címtárban. Ha a résztvevő azonosítója nulla GUID `00000000-0000-0000-0000-000000000000` , és a rendszerbiztonsági tag, a rendszerbiztonsági tag `SystemDefined` az összes résztvevőt képviseli. Azure PowerShell kimenetben az összes rendszerbiztonsági tag a következőhöz hasonlóan néz ki:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Az összes rendszerbiztonsági tag egyesíthető az `ExcludePrincipals` összes rendszerbiztonsági tag letiltásával, kivéve egyes felhasználók esetén. Minden rendszerbiztonsági tag a következő korlátozásokkal rendelkezik:

- Csak a alkalmazásban használható, `Principals` és nem használható a alkalmazásban `ExcludePrincipals` .
- `Principals[i].Type` értékre kell állítani `SystemDefined` .

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: új erőforrások biztosítása az Azure BluePrints erőforrás-zárolásokkal](../governance/blueprints/tutorials/protect-new-resources.md)
* [Azure-beli megtagadási hozzárendelések listázása a Azure Portal használatával](deny-assignments-portal.md)
