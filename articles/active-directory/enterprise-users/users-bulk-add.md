---
title: Felhasználók tömeges létrehozása a Azure Active Directory portálon | Microsoft Docs
description: Felhasználók tömeges hozzáadása az Azure AD felügyeleti központban Azure Active Directory
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
ms.openlocfilehash: c653f3e8583ef3aadff26cb2b7a3266555d313a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547814"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Felhasználók tömeges létrehozása Azure Active Directory

Azure Active Directory (Azure AD) támogatja a tömeges felhasználói létrehozási és törlési műveleteket, és támogatja a felhasználók leletöltését. Csak töltse ki az Azure AD-portálról letölthető, vesszővel tagolt (CSV) sablont.

## <a name="required-permissions"></a>Szükséges engedélyek

A felhasználók tömeges létrehozásához a felügyeleti portálon globális rendszergazdaként vagy felhasználói rendszergazdaként kell bejelentkeznie.

## <a name="understand-the-csv-template"></a>A CSV-sablon megismerése

Töltse le és töltse ki a CSV-fájl tömeges feltöltése sablont, amely segít az Azure AD-felhasználók tömeges létrehozásában. A letöltött CSV-sablon a következő példához hasonló lehet:

![Táblázat a feltöltéshez és a híváshoz – az egyes sorok és oszlopok céljának és értékeinek ismertetése](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> Ha csak egy bejegyzést ad hozzá a CSV-sablonnal, meg kell őriznie a 3. sort, és fel kell vennie az új bejegyzést a 4. sorba.

### <a name="csv-template-structure"></a>CSV-sablon szerkezete

A letöltött CSV-sablon sorai a következők:

- **Verziószám**: a verziószámot tartalmazó első sornak szerepelnie kell a CSV-fájl feltöltésekor.
- **Oszlopfejlécek**: az oszlopfejlécek formátuma a (z &lt;  &gt; ) [PropertyName] elemnév &lt; *kötelező vagy üres* &gt; . Például: `Name [displayName] Required`. Előfordulhat, hogy a sablon néhány régebbi verziója némileg eltérő változatot tartalmaz.
- **Példák sora**: a sablonban szerepel egy sor, amely tartalmazza az egyes oszlopok elfogadható értékeit. El kell távolítania a példákat tartalmazó sort, és le kell cserélnie a saját bejegyzéseire.

### <a name="additional-guidance"></a>További útmutatás

- A feltöltési sablon első két sora nem távolítható el és nem módosítható, vagy a feltöltés nem dolgozható fel.
- Először a szükséges oszlopok szerepelnek.
- Nem ajánlott új oszlopokat hozzáadni a sablonhoz. A hozzáadott további oszlopokat a rendszer figyelmen kívül hagyja, és nem dolgozza fel.
- Javasoljuk, hogy a lehető legtöbbször töltse le a CSV-sablon legújabb verzióját.
- Győződjön meg arról, hogy a mező előtt/után nem található nem kívánt szóköz. Az **egyszerű felhasználónevek** esetében az ilyen szóközök az importálási hiba miatt meghiúsulnak.

## <a name="to-create-users-in-bulk"></a>Felhasználók tömeges létrehozása

1. [Jelentkezzen be az Azure ad-szervezetbe](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. Az Azure ad-ben válassza a  >  **tömegesen létrehozott** felhasználók elemet.
1. A felhasználó **tömeges létrehozása** lapon válassza a **Letöltés** lehetőséget a felhasználói tulajdonságok érvényes, VESSZŐVEL tagolt (CSV) fájljának megjelenítéséhez, majd adja hozzá a létrehozni kívánt felhasználókat.

   ![Válassza ki azt a helyi CSV-fájlt, amelyben fel szeretné sorolni a hozzáadni kívánt felhasználókat](./media/users-bulk-add/upload-button.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden létrehozni kívánt felhasználóhoz. Az egyetlen szükséges érték a **név**, a **egyszerű felhasználónév**, a **kezdeti jelszó** és a **blokkolási bejelentkezés (igen/nem)**. Ezután mentse a fájlt.

   [![A CSV-fájl a létrehozandó felhasználók nevét és azonosítóit tartalmazza](./media/users-bulk-add/add-csv-file.png)](./media/users-bulk-add/add-csv-file.png#lightbox)

1. A **felhasználó tömeges létrehozása** lapon a CSV-fájl feltöltése területen keresse meg a fájlt. Ha kijelöli a fájlt, majd a **Küldés** gombra kattint, a CSV-fájl érvényesítése elindul.
1. A fájl tartalmának ellenőrzése után a **fájl feltöltése sikeresen** megtörténik. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
1. Amikor a fájl átadja az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget, hogy elindítsa az új felhasználókat importáló Azure bulk műveletet.
1. Amikor az importálási művelet befejeződik, megjelenik egy értesítés a tömeges művelet állapotáról.

Ha hibák léptek fel, letöltheti és megtekintheti az eredményeket tartalmazó fájlt a **tömeges művelet eredményei** lapon. A fájl az egyes hibák okát is tartalmazza. A fájl küldésének meg kell egyeznie a megadott sablonnal, és tartalmaznia kell a pontos oszlopnevek nevét.

## <a name="check-status"></a>Állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei** lapon tekintheti meg.

   [![A tömeges műveletek eredményei lapon lévő létrehozási állapot megtekintése](./media/users-bulk-add/bulk-center.png)](./media/users-bulk-add/bulk-center.png#lightbox)

Ezután ellenőrizze, hogy a létrehozott felhasználók szerepelnek-e az Azure AD-szervezetben vagy a Azure Portal vagy a PowerShell használatával.

## <a name="verify-users-in-the-azure-portal"></a>Felhasználók ellenőrzése a Azure Portalban

1. [Jelentkezzen be az Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. A navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.
1. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
1. A **Megjelenítés** területen válassza a **minden felhasználó** lehetőséget, és győződjön meg arról, hogy a létrehozott felhasználók szerepelnek a felsorolásban.

### <a name="verify-users-with-powershell"></a>Felhasználók ellenőrzése a PowerShell-lel

Futtassa az alábbi parancsot:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Látnia kell, hogy a létrehozott felhasználók szerepelnek a listáján.

## <a name="bulk-import-service-limits"></a>Tömeges importálási szolgáltatás korlátai

A felhasználók létrehozásához minden tömeges tevékenység akár egy óráig is futhat. Ez legalább 50 000 felhasználó tömeges létrehozását teszi lehetővé.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók listájának letöltése](users-bulk-download.md)
- [Felhasználók tömeges visszaállítása](users-bulk-restore.md)
