---
title: Tömeges feltöltés egy csoport tagjainak hozzáadásához vagy létrehozásához – Azure Active Directory | Microsoft Docs
description: Vegyen fel tömegesen a csoporttagokat a Azure Active Directory felügyeleti központban.
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
ms.openlocfilehash: 22d39a2ee66f2c63612ad2cb3cf9ae61f1660de3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547746"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Csoporttagok tömeges hozzáadása Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával nagyszámú tagot adhat hozzá egy csoporthoz egy vesszővel tagolt (CSV) fájl használatával a csoporttagok tömeges importálásához.

## <a name="understand-the-csv-template"></a>A CSV-sablon megismerése

Töltse le és töltse ki a CSV-fájl tömeges feltöltése sablont, hogy az Azure AD-csoport tagjait tömegesen adja hozzá. A CSV-sablon a következő példához hasonló lehet:

![Táblázat a feltöltéshez és a híváshoz – az egyes sorok és oszlopok céljának és értékeinek ismertetése](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>CSV-sablon szerkezete

A letöltött CSV-sablon sorai a következők:

- **Verziószám**: a verziószámot tartalmazó első sornak szerepelnie kell a CSV-fájl feltöltésekor.
- **Oszlopfejlécek**: az oszlopfejlécek formátuma a (z &lt;  &gt; ) [PropertyName] elemnév &lt; *kötelező vagy üres* &gt; . Például: `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Előfordulhat, hogy a sablon néhány régebbi verziója némileg eltérő változatot tartalmaz. A csoporttagság változásaihoz a következőt kell használni: tag objektumazonosító vagy egyszerű felhasználónév.
- **Példák sora**: a sablonban szerepel egy sor, amely tartalmazza az egyes oszlopok elfogadható értékeit. El kell távolítania a példákat tartalmazó sort, és le kell cserélnie a saját bejegyzéseire.

### <a name="additional-guidance"></a>További útmutatás

- A feltöltési sablon első két sora nem távolítható el és nem módosítható, vagy a feltöltés nem dolgozható fel.
- Először a szükséges oszlopok szerepelnek.
- Nem ajánlott új oszlopokat hozzáadni a sablonhoz. A hozzáadott további oszlopokat a rendszer figyelmen kívül hagyja, és nem dolgozza fel.
- Javasoljuk, hogy a lehető legtöbbször töltse le a CSV-sablon legújabb verzióját.
- A fájl sikeres feltöltéséhez adjon hozzá legalább két felhasználó UPN-vagy objektumazonosítók-azonosítóját.

## <a name="to-bulk-import-group-members"></a>Csoporttagok tömeges importálása

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) a szervezet felhasználói rendszergazdai fiókjával. A csoport tulajdonosai a saját csoportok tagjainak tömeges importálását is elvégezheti.
1. Az Azure ad-ben válassza a **csoportok**  >  **minden csoport** elemet.
1. Nyissa meg azt a csoportot, amelyhez tagokat vesz fel, majd válassza a **tagok** lehetőséget.
1. A **tagok** lapon válassza a **tagok importálása** lehetőséget.
1. A **csoportos importálás csoport tagjai** lapon válassza a **Letöltés** lehetőséget a CSV-sablonfájl a szükséges csoporttagok tulajdonságaival való beszerzéséhez.

    ![A tagok importálása parancs a csoport profil lapján található.](./media/groups-bulk-import-members/import-panel.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden olyan csoporttag számára, amelyet importálni szeretne a csoportba (a kötelező értékek vagy **tag-azonosítók** vagy **egyszerű felhasználónév**). Ezután mentse a fájlt.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="A CSV-fájl az importálandó tagok nevét és azonosítóit tartalmazza.":::

1. A **csoportos importálás csoport tagjai** lapon, a **CSV-fájl feltöltése** területen keresse meg a fájlt. A fájl kiválasztásakor elindul a CSV-fájl érvényesítése.
1. A fájl tartalmának ellenőrzésekor a tömeges importálás lap megjeleníti a **fájl feltöltésének sikerességét**. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
1. Ha a fájl érvényesíti az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget az Azure tömeges művelet elindításához, amely importálja a csoporttagokat a csoportba.
1. Amikor az importálási művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeresen befejeződött.

## <a name="check-import-status"></a>Importálás állapotának keresése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei** lapon tekintheti meg.

[![A tömeges műveletek eredményei lapon található állapot keresése.](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

A tömeges műveletben szereplő egyes sorokra vonatkozó részletekért válassza ki a **# sikeres**, **# sikertelen** vagy az **összes kérelem** oszlopban szereplő értékeket. Ha hiba történt, a hiba okai lesznek felsorolva.

## <a name="bulk-import-service-limits"></a>Tömeges importálási szolgáltatás korlátai

A csoporttagok listájának importálására szolgáló tömeges tevékenységek akár egy óráig is futhatnak. Ez lehetővé teszi a legfeljebb 40 000 tagot tartalmazó lista importálását.

## <a name="next-steps"></a>Következő lépések

- [Csoporttagok tömeges eltávolítása](groups-bulk-remove-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
- [Az összes csoport listájának letöltése](groups-bulk-download.md)
