---
title: Felhasználók tömeges törlése a Azure Active Directory portálon | Microsoft Docs
description: A felhasználók tömeges törlése az Azure felügyeleti központban Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574463"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Felhasználók tömeges törlése Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával számos tagot eltávolíthat egy csoportba egy vesszővel tagolt (CSV) fájl használatával a felhasználók tömeges törléséhez.

## <a name="understand-the-csv-template"></a>A CSV-sablon megismerése

Töltse le és töltse ki a CSV-sablont, hogy segítsen az Azure AD-felhasználók tömeges törlésében. A letöltött CSV-sablon a következő példához hasonló lehet:

![Táblázat a feltöltéshez és a híváshoz – az egyes sorok és oszlopok céljának és értékeinek ismertetése](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>CSV-sablon szerkezete

A letöltött CSV-sablon sorai a következők:

- **Verziószám**: a verziószámot tartalmazó első sornak szerepelnie kell a CSV-fájl feltöltésekor.
- **Oszlopfejlécek**: az oszlopfejlécek formátuma a (z &lt;  &gt; ) [PropertyName] elemnév &lt; *kötelező vagy üres* &gt; . Például: `User name [userPrincipalName] Required`. Előfordulhat, hogy a sablon néhány régebbi verziója némileg eltérő változatot tartalmaz.
- **Példák sora**: a sablonban szerepel egy sor, amely tartalmazza az egyes oszlopok elfogadható értékeit. El kell távolítania a példákat tartalmazó sort, és le kell cserélnie a saját bejegyzéseire.

### <a name="additional-guidance"></a>További útmutatás

- A feltöltési sablon első két sora nem távolítható el és nem módosítható, vagy a feltöltés nem dolgozható fel.
- Először a szükséges oszlopok szerepelnek.
- Nem ajánlott új oszlopokat hozzáadni a sablonhoz. A hozzáadott további oszlopokat a rendszer figyelmen kívül hagyja, és nem dolgozza fel.
- Javasoljuk, hogy a lehető legtöbbször töltse le a CSV-sablon legújabb verzióját.

## <a name="to-bulk-delete-users"></a>Felhasználók tömeges törlése

1. [Jelentkezzen be az Azure ad-szervezetbe](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. Az Azure ad-ben válassza a **felhasználók**  >  **tömeges törlés** lehetőséget.
1. A **felhasználó tömeges törlése** lapon válassza a **Letöltés** lehetőséget a felhasználói tulajdonságok érvényes CSV-fájljának fogadásához.

   ![Válassza ki azt a helyi CSV-fájlt, amelyben meg szeretné jeleníteni a törölni kívánt felhasználókat](./media/users-bulk-delete/bulk-delete.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden törölni kívánt felhasználóhoz. Az egyetlen szükséges érték a **felhasználó egyszerű neve**. Ezután mentse a fájlt.

   ![A CSV-fájl a törölni kívánt felhasználók nevét és azonosítóit tartalmazza](./media/users-bulk-delete/delete-csv-file.png)

1. A **felhasználó tömeges törlése** lapon a CSV- **fájl feltöltése** területen keresse meg a fájlt. Ha kijelöli a fájlt, majd a Küldés gombra kattint, a CSV-fájl érvényesítése elindul.
1. A fájl tartalmának ellenőrzésekor a **fájl feltöltése sikeresen** megtörténik. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
1. Amikor a fájl átadja az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget az Azure tömeges művelet elindításához, amely törli a felhasználókat.
1. Ha a törlési művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeresen befejeződött.

Ha hibák léptek fel, letöltheti és megtekintheti az eredményeket tartalmazó fájlt a **tömeges művelet eredményei** lapon. A fájl az egyes hibák okát is tartalmazza.

## <a name="check-status"></a>Állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei** lapon tekintheti meg.

   [![A tömeges műveletek eredményei lapon található törlés állapotának ellenőrzését.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

Ezután ellenőrizze, hogy a törölt felhasználók szerepelnek-e az Azure AD-szervezetben vagy a Azure Portal vagy a PowerShell használatával.

## <a name="verify-deleted-users-in-the-azure-portal"></a>A törölt felhasználók ellenőrzése a Azure Portal

1. Jelentkezzen be a Azure Portalba egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. A navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.
1. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
1. A **Megjelenítés** területen jelölje ki a csak az **összes felhasználó** elemet, és ellenőrizze, hogy a törölt felhasználók már nem szerepelnek-e a listáján.

### <a name="verify-deleted-users-with-powershell"></a>Törölt felhasználók ellenőrzése a PowerShell-lel

Futtassa az alábbi parancsot:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Győződjön meg arról, hogy a törölt felhasználók már nem szerepelnek a felsorolásban.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók tömeges hozzáadása](users-bulk-add.md)
- [Felhasználók listájának letöltése](users-bulk-download.md)
- [Felhasználók tömeges visszaállítása](users-bulk-restore.md)
