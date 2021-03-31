---
title: Azure szerepköralapú hozzáférés-vezérlés használata a StorSimple-hez | Microsoft Docs
description: Ismerteti, hogyan használható az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) a StorSimple környezetében.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 49c38e23ddbbfe983ff82ad25363c744292d4d69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518976"
---
# <a name="azure-role-based-access-control-for-storsimple"></a>Azure szerepköralapú hozzáférés-vezérlés a StorSimple-hez

Ez a cikk röviden ismerteti, hogyan használható az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) a StorSimple-eszközhöz. Az Azure RBAC részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az Azure RBAC használatával biztosíthatja, hogy csak a megfelelő mennyiségű hozzáférés legyen elérhető a StorSimple-felhasználók számára a feladatok elvégzéséhez ahelyett, hogy mindenki számára korlátlan hozzáférést adna. További információ az Azure-beli hozzáférés-kezelés alapjairól: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md).

Ez a cikk a 3,0-es vagy újabb verzióját futtató StorSimple 8000 sorozatú eszközökre vonatkozik a Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-roles-for-storsimple"></a>Azure-szerepkörök a StorSimple

Az Azure RBAC a szerepkörök alapján rendelhető hozzá. A szerepkörök bizonyos jogosultsági szinteket biztosítanak a környezetben elérhető erőforrások alapján. A StorSimple-felhasználók két típusú szerepkört választhatnak: beépített vagy egyéni.

* **Beépített szerepkörök** – a beépített szerepkörök tulajdonosa, közreműködője, olvasója vagy felhasználói hozzáférési rendszergazdája lehet. További információ: [beépített szerepkörök az Azure szerepköralapú Access Controlhoz](../role-based-access-control/built-in-roles.md).

* **Egyéni szerepkörök** – ha a beépített szerepkörök nem felelnek meg az igényeinek, létrehozhat egyéni Azure-szerepköröket a StorSimple. Egyéni Azure-szerepkör létrehozásához hozzon létre egy beépített szerepkört, szerkessze, majd importálja újra a környezetbe. A szerepkör letöltése és feltöltése Azure PowerShell vagy az Azure CLI használatával történik. További információ: [Egyéni szerepkörök létrehozása szerepköralapú Access Controlhoz](../role-based-access-control/custom-roles.md).

Ha szeretné megtekinteni a StorSimple-eszköz felhasználóinak számára elérhető különböző szerepköröket a Azure Portalban, lépjen a StorSimple Eszközkezelő szolgáltatáshoz, és lépjen a **hozzáférés-vezérlés (iam) > szerepkörök** elemre.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Egyéni szerepkör létrehozása a StorSimple-infrastruktúra rendszergazdájához

Az alábbi példában a beépített szerepkör- **olvasóval** kezdjük, amellyel a felhasználók megtekinthetik az összes erőforrás-hatókört, de nem szerkeszthetik őket, és újakat hozhatnak létre. Ezt követően kiterjesztjük ezt a szerepkört egy új egyéni szerepkör-StorSimple infrastruktúra-rendszergazda létrehozásához. Ez a szerepkör olyan felhasználókhoz van hozzárendelve, akik felügyelhetik a StorSimple-eszközök infrastruktúráját.

1. Futtassa a Windows PowerShellt rendszergazdaként.

2. Bejelentkezik az Azure-ba.

    `Connect-AzAccount`

3. Exportálja az olvasó szerepkört JSON-sablonként a számítógépén.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Nyissa meg a JSON-fájlt a Visual Studióban. Láthatja, hogy egy tipikus Azure-szerepkör három fő szakaszból, **műveletből**, nem **tapintatokból** és **AssignableScopes** áll.

    A **művelet** szakaszban megjelenik a szerepkörre vonatkozó összes engedélyezett művelet. Minden művelet hozzá van rendelve egy erőforrás-szolgáltatóhoz. StorSimple-infrastruktúra rendszergazdája használja az erőforrás- `Microsoft.StorSimple` szolgáltatót.

    A PowerShell használatával megtekintheti az előfizetésében elérhető és regisztrált összes erőforrás-szolgáltatót.

    `Get-AzResourceProvider`

    Az összes elérhető PowerShell-parancsmagot is megtekintheti az erőforrás-szolgáltatók kezeléséhez.

    A **tapintatok** szakaszban az adott Azure-szerepkörre vonatkozó összes korlátozott művelet szerepel a felsorolásban. Ebben a példában egyetlen művelet sincs korlátozva.
    
    A **AssignableScopes** alatt az előfizetési azonosítók szerepelnek a felsorolásban. Győződjön meg arról, hogy az Azure-szerepkör tartalmazza a explicit előfizetés-azonosítót, ahol a használatban van. Ha nincs megadva a megfelelő előfizetés-azonosító, nem importálhatja a szerepkört az előfizetésében.

    Szerkessze a fájlt, és vegye figyelembe az előző szempontokat.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importálja újra az Azure-beli egyéni szerepkört a környezetbe.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


A szerepkörnek ekkor a **hozzáférés-vezérlés** panelen kell megjelennie a szerepkörök listájában.

![Azure-szerepkörök megtekintése](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

További információért lépjen az [Egyéni szerepkörök](../role-based-access-control/custom-roles.md)elemre.

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Minta kimenete egyéni szerepkörök létrehozásához a PowerShell használatával

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Felhasználók hozzáadása az egyéni szerepkörhöz

A hozzáférés a szerepkör-hozzárendelés hatókörébe tartozó erőforrásból, erőforráscsoportból vagy előfizetésből biztosítható. A hozzáférés megadásakor vegye figyelembe, hogy a szülő csomóponton megadott hozzáférést a gyermek örökli. További információért látogasson el az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)című témakörre.

1. Nyissa meg a **hozzáférés-vezérlés (iam)** lehetőséget. Kattintson a **+ Hozzáadás** elemre a hozzáférés-vezérlés panelen.

    ![Hozzáférés hozzáadása az Azure-szerepkörhöz](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Válassza ki a hozzárendelni kívánt szerepkört, ebben az esetben ez a **StorSimple-infrastruktúra rendszergazdája**.

3. Válassza ki azt a felhasználót, csoportot vagy alkalmazást a címtárában, amely számára hozzáférést kíván biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.

4. A hozzárendelés létrehozásához válassza a **Mentés** lehetőséget.

    ![Engedélyek hozzáadása az Azure-szerepkörhöz](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

A **felhasználói értesítések hozzáadásával** nyomon követheti a folyamat előrehaladását. A felhasználó sikeres hozzáadása után a rendszer frissíti a hozzáférés-vezérlésben lévő felhasználók listáját.

## <a name="view-permissions-for-the-custom-role"></a>Az egyéni szerepkör engedélyeinek megtekintése

A szerepkör létrehozása után megtekintheti a szerepkörhöz társított engedélyeket a Azure Portal.

1. A szerepkörhöz társított engedélyek megtekintéséhez lépjen a **hozzáférés-vezérlés (iam) > szerepkörök > StorSimple-infrastruktúra rendszergazdája** elemre. Megjelenik a szerepkörben lévő felhasználók listája.

2. Válasszon ki egy StorSimple-infrastruktúra rendszergazdai felhasználót, és kattintson az **engedélyek** elemre.

    ![Az StorSimple infra rendszergazdai szerepkör engedélyeinek megtekintése](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Megjelenik az ehhez a szerepkörhöz társított engedélyek.

    ![Felhasználók megtekintése a StorSimple infra rendszergazdai szerepkörben](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [rendelhet hozzá egyéni szerepköröket belső és külső felhasználók](../role-based-access-control/role-assignments-external-users.md)számára.
