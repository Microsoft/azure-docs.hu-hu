---
title: Törölt felhasználók tömeges visszaállítása a Azure Active Directory portálon | Microsoft Docs
description: A törölt felhasználók tömeges visszaállítása az Azure AD felügyeleti központban Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: be3947e3de18f8ccaf47382c4035e187521ac710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571504"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Törölt felhasználók tömeges visszaállítása Azure Active Directory

Azure Active Directory (Azure AD) támogatja a tömeges felhasználói visszaállítási műveleteket, és támogatja a felhasználók, csoportok és csoporttagok letöltési listáját.

## <a name="understand-the-csv-template"></a>A CSV-sablon megismerése

Töltse le és töltse ki a CSV-sablont, hogy az Azure AD-felhasználók tömeges visszaállítása sikeres legyen. A letöltött CSV-sablon a következő példához hasonló lehet:

![Táblázat a feltöltéshez és a híváshoz – az egyes sorok és oszlopok céljának és értékeinek ismertetése](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>CSV-sablon szerkezete

A letöltött CSV-sablon sorai a következők:

- **Verziószám**: a verziószámot tartalmazó első sornak szerepelnie kell a CSV-fájl feltöltésekor.
- **Oszlopfejlécek**: az oszlopfejlécek formátuma a (z &lt;  &gt; ) [PropertyName] elemnév &lt; *kötelező vagy üres* &gt; . Például: `Object ID [objectId] Required`. Előfordulhat, hogy a sablon néhány régebbi verziója némileg eltérő változatot tartalmaz.
- **Példák sora**: a sablonban szerepel egy sor, amely tartalmazza az egyes oszlopok elfogadható értékeit. El kell távolítania a példákat tartalmazó sort, és le kell cserélnie a saját bejegyzéseire.

### <a name="additional-guidance"></a>További útmutatás

- A feltöltési sablon első két sora nem távolítható el és nem módosítható, vagy a feltöltés nem dolgozható fel.
- Először a szükséges oszlopok szerepelnek.
- Nem ajánlott új oszlopokat hozzáadni a sablonhoz. A hozzáadott további oszlopokat a rendszer figyelmen kívül hagyja, és nem dolgozza fel.
- Javasoljuk, hogy a lehető legtöbbször töltse le a CSV-sablon legújabb verzióját.

## <a name="to-bulk-restore-users"></a>Felhasználók tömeges visszaállítása

1. [Jelentkezzen be az Azure ad-szervezetbe](https://aad.portal.azure.com) egy olyan fiókkal, amely felhasználói rendszergazda az Azure ad-szervezetben.
1. Az Azure ad-ben válassza a **felhasználók**  >  **törölve** lehetőséget.
1. A **törölt felhasználók** lapon a **tömeges visszaállítás** elemre kattintva töltse fel a visszaállítani kívánt felhasználók tulajdonságaihoz tartozó érvényes CSV-fájlt.

    ![A törölt felhasználók lapon válassza a tömeges visszaállítás parancsot.](./media/users-bulk-restore/bulk-restore.png)

1. Nyissa meg a CSV-sablont, és adjon hozzá egy sort minden visszaállítani kívánt felhasználóhoz. Az egyetlen szükséges érték a **ObjectId**. Ezután mentse a fájlt.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="Válassza ki azt a helyi CSV-fájlt, amelyben fel szeretné sorolni a hozzáadni kívánt felhasználókat":::

1. A **tömeges visszaállítás** lapon a CSV- **fájl feltöltése** területen keresse meg a fájlt. Ha kijelöli a fájlt, majd a **Küldés** gombra kattint, a CSV-fájl érvényesítése elindul.
1. A fájl tartalmának ellenőrzésekor a **fájl feltöltése sikeresen** megtörténik. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
1. Ha a fájl érvényesíti az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget az Azure tömeges művelet elindításához, amely visszaállítja a felhasználókat.
1. Ha a visszaállítási művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeresen befejeződött.

Ha hibák léptek fel, letöltheti és megtekintheti az eredményeket tartalmazó fájlt a **tömeges művelet eredményei** lapon. A fájl az egyes hibák okát is tartalmazza.

## <a name="check-status"></a>Állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei** lapon tekintheti meg.

[![A tömeges műveletek eredményei lapon található állapot keresése.](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

Ezután ellenőrizze, hogy a visszaállított felhasználók szerepelnek-e az Azure AD-szervezetben vagy a Azure Portal vagy a PowerShell használatával.

## <a name="view-restored-users-in-the-azure-portal"></a>Visszaállított felhasználók megtekintése a Azure Portal

1. [Jelentkezzen be az Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. A navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.
1. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
1. A **Megjelenítés** területen válassza a **minden felhasználó** elemet, és ellenőrizze, hogy a visszaállított felhasználók szerepelnek-e a listáján.

### <a name="view-users-with-powershell"></a>Felhasználók megtekintése a PowerShell-lel

Futtassa az alábbi parancsot:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Ekkor megjelenik a visszaállított felhasználók listája.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók tömeges importálása](users-bulk-add.md)
- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók listájának letöltése](users-bulk-download.md)
