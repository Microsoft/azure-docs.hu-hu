---
title: Csoporttagok tömeges eltávolítása egy CSV-fájl feltöltésével – Azure Active Directory | Microsoft Docs
description: Az Azure felügyeleti központban tömeges műveletekkel távolíthatja el a csoporttagokat.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b905faa892cc338b5bdf4f998ad95f7b8a484e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95488946"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Csoporttagok tömeges eltávolítása Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával számos tagot eltávolíthat egy csoportból egy vesszővel tagolt (CSV) fájl használatával a csoporttagok tömeges eltávolításához.

## <a name="understand-the-csv-template"></a>A CSV-sablon megismerése

Töltse le és töltse ki a CSV-fájl tömeges feltöltése sablont, hogy az Azure AD-csoport tagjait tömegesen adja hozzá. A CSV-sablon a következő példához hasonló lehet:

![Táblázat a feltöltéshez és a híváshoz – az egyes sorok és oszlopok céljának és értékeinek ismertetése](./media/groups-bulk-remove-members/template-example.png)

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

## <a name="to-bulk-remove-group-members"></a>Csoporttagok tömeges eltávolítása

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) a szervezet felhasználói rendszergazdai fiókjával. A csoport tulajdonosai a saját csoportok tagjainak tömeges eltávolítására is jogosultak.
1. Az Azure ad-ben válassza a **csoportok**  >  **minden csoport** elemet.
1. Nyissa meg azt a csoportot, amelyből el kívánja távolítani a tagokat, majd válassza a **tagok** elemet.
1. A **tagok** lapon válassza a **tagok eltávolítása** lehetőséget.
1. A **csoporttagok eltávolítása** lapon válassza a **Letöltés** lehetőséget a CSV-sablonfájl a szükséges csoporttagok tulajdonságaival való beszerzéséhez.

   ![A tagok eltávolítása parancs a csoport profil lapján található.](./media/groups-bulk-remove-members/remove-panel.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden olyan csoporttag számára, amelyet el szeretne távolítani a csoportból (a kötelező értékek a tag objektum azonosítója vagy egyszerű felhasználónév). Ezután mentse a fájlt.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="A CSV-fájl az eltávolítandó csoporttagok nevét és azonosítóit tartalmazza.":::

1. A **csoporttagok eltávolítása** lapon a **CSV-fájl feltöltése** területen keresse meg a fájlt. A fájl kiválasztásakor elindul a CSV-fájl érvényesítése.
1. A fájl tartalmának ellenőrzésekor a tömeges importálás lap megjeleníti a **fájl feltöltésének sikerességét**. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
1. Ha a fájl átadja az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget az Azure tömeges művelet elindításához, amely eltávolítja a csoport tagjait a csoportból.
1. Amikor az eltávolítási művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeresen befejeződött.

## <a name="check-removal-status"></a>Eltávolítás állapotának ellenõrzése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei** lapon tekintheti meg.

[![Állapot keresése a tömeges műveletek eredményei lapon](./media/groups-bulk-remove-members/bulk-center.png)](./media/groups-bulk-remove-members/bulk-center.png#lightbox)

A tömeges műveletben szereplő egyes sorokra vonatkozó részletekért válassza ki a **# sikeres**, **# sikertelen** vagy az **összes kérelem** oszlopban szereplő értékeket. Ha hiba történt, a hiba okai lesznek felsorolva.

## <a name="bulk-removal-service-limits"></a>Tömeges eltávolítási szolgáltatás korlátai

Minden tömeges tevékenység, amelyből a csoporttagok listája akár egy óráig is futtatható. Ez lehetővé teszi, hogy legalább 40 000 tagot tartalmazó lista legyen eltávolítva.

## <a name="next-steps"></a>Következő lépések

- [Csoporttagok tömeges importálása](groups-bulk-import-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
- [Az összes csoport listájának letöltése](groups-bulk-download.md)
