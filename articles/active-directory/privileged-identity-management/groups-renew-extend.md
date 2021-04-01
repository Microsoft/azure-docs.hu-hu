---
title: A tagok hozzárendeléseinek lejárt csoportjának megújítása Privileged Identity Management-Azure AD-ben | Microsoft Docs
description: Ismerje meg, hogyan bővítheti vagy újíthatja meg a szerepkörhöz hozzárendelhető csoportok hozzárendeléseit Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87505990"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Kiemelt jogosultságú hozzáférési csoport hozzárendeléseinek (előzetes verzió) kiterjesztése vagy megújítása Privileged Identity Management

A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) a jogosultsági szintű hozzáférési csoportok hozzáférési és hozzárendelési életciklusának kezeléséhez biztosít vezérlőket. A rendszergazdák a kezdő és a befejező dátum-idő tulajdonságokkal is hozzárendelhetők a szerepkörökhöz. A hozzárendelési végpont megközelítése esetén a Privileged Identity Management e-mail-értesítéseket küld az érintett felhasználóknak vagy csoportoknak. E-mail-értesítéseket is küld az erőforrás rendszergazdái számára a megfelelő hozzáférés fenntartásának biztosítása érdekében. Előfordulhat, hogy a hozzárendelések megújítva maradnak, és a lejárt állapotban maradhatnak, akár 30 napig, még akkor is, ha a hozzáférés nincs kiterjesztve.

## <a name="who-can-extend-and-renew"></a>Kiterjeszthető és megújítható

Csak az erőforrás rendszergazdája bővítheti vagy újíthatja meg a Kiemelt jogosultságú hozzáférési csoport hozzárendeléseit. Az érintett felhasználó vagy csoport kérheti a lejárt hozzárendelések kiterjesztését, és a már lejárt hozzárendelések megújítását kéri.

## <a name="when-notifications-are-sent"></a>Értesítések küldésekor

Privileged Identity Management e-mail-értesítéseket küld a rendszergazdáknak és az érintett felhasználóktól a Privileged Access Group lejárati hozzárendeléseiről:

- A lejárat előtti 14 napon belül
- Egy nappal a lejárat előtt
- Hozzárendelés lejáratakor

A rendszergazdák értesítést kapnak, ha egy felhasználó vagy csoport lejárati vagy lejárt hozzárendelését szeretné meghosszabbítani vagy megújítani. Amikor egy rendszergazda feloldja a kérést, az összes rendszergazda és a kérelmező felhasználó értesítést kap a jóváhagyásról vagy elutasításról.

## <a name="extend-group-assignments"></a>Csoport hozzárendeléseinek kiterjesztése

A következő lépések a csoport-hozzárendelések kiterjesztésének vagy megújításának kérelmezésének, feloldásának vagy felügyeletének folyamatát ismertetik.

### <a name="self-extend-expiring-assignments"></a>Lejáró hozzárendelések önálló kiterjesztése

A Kiemelt hozzáférési csoportba tartozó felhasználók a csoport **hozzárendelések** lapján közvetlenül a **jogosult** vagy az **aktív** lapról is kiterjeszthetik a csoportos hozzárendelések lejáratát. A felhasználók vagy csoportok kérhetik a következő 14 napban lejáró jogosult és aktív hozzárendelések kiterjesztését.

![A My roles (szerepkörök) lap, amely egy műveleti oszloppal rendelkező jogosult assgnments listáz](media/groups-renew-extend/self-extend-group-assignment.png)

Ha a hozzárendelés befejezési dátuma és időpontja 14 napon belül van, a **kiterjesztés** parancs elérhető. A csoport-hozzárendelés kiterjesztésének megadásához válassza a **kiterjesztés** lehetőséget a kérelem űrlapjának megnyitásához.

![A csoport hozzárendelési paneljének kiterjesztése az OK mezőre és a részletekre](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>Azt javasoljuk, hogy a bővítmény miért szükséges, és hogy mennyi ideig kell megadni a bővítményt (ha rendelkezik ezekkel az információkkal).

Pillanatok alatt a rendszergazdák egy e-mail-értesítést kapnak arról, hogy áttekintik a bővítmény kérelmét. Ha egy kiterjesztésre vonatkozó kérelmet már elküldtek, egy Azure-értesítés jelenik meg a portálon.

A kérelem állapotának megtekintéséhez vagy megszakításához nyissa meg a **függőben lévő kérések** lapot a csoport-hozzárendeléshez.

![Kiemelt jogosultságú hozzáférési csoport hozzárendelései – függőben lévő kérelmek lap, amely a Mégse hivatkozásra mutat](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>Rendszergazda által jóváhagyott bővítmény

Amikor egy felhasználó vagy csoport egy csoport-hozzárendelés kiterjesztésére vonatkozó kérelmet küld, a rendszergazdák e-mailben értesítést kapnak, amely tartalmazza az eredeti hozzárendelés részleteit és a kérelem okát. Az értesítés közvetlen hivatkozást tartalmaz a rendszergazda jóváhagyására vagy elutasítására vonatkozó kérésre.

Amellett, hogy az e-mail-hivatkozás követése mellett a rendszergazdák jóváhagyják vagy megtagadják a kéréseket, a Privileged Identity Management felügyeleti portálra, majd a bal oldali ablaktáblán a **kérések jóváhagyása** lehetőségre kattintva.

![Kiemelt jogosultságú hozzáférési csoport hozzárendelései – a kérelmek jóváhagyása lap a kérelmek és a jóváhagyásra vagy elutasításra mutató hivatkozások listázása](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

Ha egy rendszergazda kiválasztja a **jóváhagyás** vagy a **Megtagadás** lehetőséget, a rendszer megjeleníti a kérés részleteit, valamint egy mezőt, amely üzleti indoklást biztosít a naplók számára.

![Csoport-hozzárendelési kérelem jóváhagyása a kérelmező okának, a hozzárendelés típusa, a kezdési idő, a Befejezés időpontja és az ok alapján](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

A csoport-hozzárendelés kiterjesztésére vonatkozó kérelem jóváhagyásakor az erőforrás-rendszergazdák választhatnak új kezdő dátumot, záró dátumot és hozzárendelési típust. Szükség lehet a hozzárendelés típusának módosítására, ha a rendszergazda korlátozott hozzáférést szeretne biztosítani egy adott feladat végrehajtásához (például egy nap). Ebben a példában a rendszergazda módosíthatja a hozzárendelést a **jogosultról** **aktívra**. Ez azt jelenti, hogy hozzáférést biztosíthatnak a kérelmezőnek anélkül, hogy aktiválni kellene őket.

### <a name="admin-initiated-extension"></a>Rendszergazda által kezdeményezett bővítmény

Ha egy csoporthoz rendelt felhasználó nem kér bővítményt a csoport-hozzárendeléshez, a rendszergazda kiterjesztheti a hozzárendelést a felhasználó nevében. A csoport-hozzárendelés felügyeleti bővítményei nem igényelnek jóváhagyást, de a hozzárendelés kiterjesztése után az összes többi rendszergazdának értesítést küld a rendszer.

A csoport-hozzárendelés kiterjesztéséhez keresse meg Privileged Identity Management a hozzárendelés nézetét. A kiterjesztést igénylő hozzárendelés megkeresése. Ezután válassza a **kiterjesztés** elemet a művelet oszlopban.

![Hozzárendelések lap a kiterjeszthető hivatkozásokkal rendelkező jogosult csoport-hozzárendelések listázása](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>Csoportos hozzárendelések megújítása

Habár a bővítmény igénylésének folyamata elméletileg hasonló, a lejárt csoportos hozzárendelés megújításának folyamata eltérő. A következő lépések segítségével a hozzárendelések és a rendszergazdák szükség esetén megújítják a lejárt hozzárendelésekhez való hozzáférést.

### <a name="self-renew"></a>Önkiszolgáló megújítás

Azok a felhasználók, akik már nem férnek hozzá az erőforrásokhoz, akár 30 napig is hozzáférhetnek a lejárt hozzárendelési előzményekhez. Ehhez keresse meg a **saját szerepköröket** a bal oldali ablaktáblán, majd válassza a **Lejárt hozzárendelések** fület.

![Saját szerepkörök lap – lejárt hozzárendelések lap](media/groups-renew-extend/groups-renew-from-my-roles.png)

A hozzárendelések listája a **jogosult hozzárendelések** alapértelmezett értékeit jeleníti meg. Használja a legördülő menüt a jogosult és az aktív hozzárendelések közötti váltáshoz.

Ha a listában szereplő bármelyik csoport-hozzárendelésre vonatkozó megújítást szeretne kérni, válassza a **megújítási** műveletet. Ezután adja meg a kérelem okát. Hasznos lehet időtartamot biztosítani az olyan további kontextusok vagy üzleti indoklások mellett, amelyek segítségével az erőforrás-rendszergazda jóváhagyhatja vagy megtagadhatja a jóváhagyást.

![A csoport-hozzárendelés ablaktábla megújítása az OK mezőben](media/groups-renew-extend/groups-renew-request-form.png)

A kérés elküldése után az erőforrás-rendszergazdák értesítést kapnak egy függő kérelemről egy csoport-hozzárendelés megújításához.

### <a name="admin-approves"></a>A rendszergazda jóváhagyja

Az erőforrás-rendszergazdák hozzáférhetnek a megújítási kérelemhez az e-mail-értesítésben szereplő hivatkozáson Privileged Identity Management keresztül, vagy a Azure Portal, és a bal oldali ablaktáblán a **kérések jóváhagyása** lehetőség kiválasztásával

Ha egy rendszergazda kiválasztja a **jóváhagyás** vagy a **Megtagadás** lehetőséget, a rendszer a kérés részleteit jeleníti meg egy mezővel együtt, amely üzleti indoklást biztosít a naplók számára.

A csoport-hozzárendelés megújítására vonatkozó kérelem jóváhagyásakor az erőforrás-rendszergazdáknak új kezdési dátumot, záró dátumot és hozzárendelési típust kell megadniuk.

## <a name="next-steps"></a>Következő lépések

- [Jogosultsági szintű hozzáférési csoport hozzárendeléseire vonatkozó kérelmek jóváhagyása vagy elutasítása Privileged Identity Management](groups-approval-workflow.md)
- [Privilegizált hozzáférési csoport beállításainak konfigurálása Privileged Identity Management](groups-role-settings.md)
