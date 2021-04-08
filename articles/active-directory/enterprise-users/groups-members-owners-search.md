---
title: Csoportok tagjai és tulajdonosainak keresése és szűrése (előzetes verzió) – Azure Active Directory | Microsoft Docs
description: Csoportok tagjai és tulajdonosainak keresése és szűrése a Azure Portalban.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d78a2a4e0f14d99a7a1ecada915857f59422bb58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547372"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Csoportok és tagok keresése (előzetes verzió) Azure Active Directory

Ebből a cikkből megtudhatja, hogyan keresheti meg egy csoport tagjait és tulajdonosait, és hogyan használhatja a keresési szűrőket a csoportok fejlesztésének előzetes verziójának részeként a Azure Active Directory (Azure AD) portálon. A csoportok számos olyan fejlesztéssel rendelkeznek, amelyek segítségével gyorsan és egyszerűen kezelheti a csoportokat, beleértve a tagokat és a tulajdonosokat is. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az előzetes verzió változásai a következők:

- Új csoportok keresési képességek, például alsztring keresés a csoportok neveiben
- Új szűrési és rendezési beállítások a tag és a tulajdonos listán
- Új keresési funkciók a tagok és a tulajdonosok listájához
- A nagyméretű csoportok pontosabb csoportosítási száma

## <a name="enabling-and-managing-the-preview"></a>Az előnézet engedélyezése és kezelése

Könnyedén csatlakozhat az előzetes verzióhoz:

  1. Jelentkezzen be az [Azure ad-portálra](https://portal.azure.com), és válassza a **csoportok** lehetőséget.
  2. A groups – all groups (csoportok – minden csoport) lapon válassza a lap tetején található szalagcímet az előzetes verzióhoz való csatlakozáshoz.

A legújabb funkciókat és a tökéletesítéseket is megtekintheti, ha az **összes csoport** lapon az **előnézet információ** lehetőségre kattint. Az előzetes verzióhoz való csatlakozást követően az előnézeti címkét az összes olyan csoportnál láthatja, amely az előzetes verzió részét képezi. Nem minden csoport lap frissült az előzetes verzió részeként.

Ha problémák merülnek fel, a **minden csoport** lap tetején található szalagcím kiválasztásával visszaválthat a régi élményre. Nagyra értékeljük visszajelzését, hogy fejlesszük a tapasztalatainkat.

## <a name="group-search-and-sorting"></a>Csoportos keresés és rendezés

A csoportok listájának keresését továbbfejlesztettük, így amikor beírhat egy keresési karakterláncot, a keresés automatikusan végrehajt egy `startswith` és alkarakterlánc-keresést a csoportok neveinek listáján. Az alkarakterlánc-keresés csak egész szavakon hajtható végre, és nem tartalmaz speciális karaktereket. A karakterlánc-keresés a kis-és nagybetűk megkülönböztetésére szolgál.

![új alkarakterlánc-keresések a minden csoport lapon](./media/groups-members-owners-search/groups-search-preview.png)

Például a "Policy" kifejezésre való keresés most a "MDM Policy – West" és a "Policy Group" értéket fogja visszaadni. A rendszer nem ad vissza egy "New_policy" nevű csoportot.

- Ugyanezt a keresést is végrehajthatja a csoporttagság-listán.
- Mostantól a név oszlop fejlécének jobb oldalán található nyilak alapján rendezheti a csoportok listáját a lista növekvő vagy csökkenő sorrendbe rendezéséhez.

## <a name="group-member-search-and-filtering"></a>Csoporttagok keresése és szűrése

### <a name="search-group-member-and-owner-lists"></a>Keresési csoport tagjai és tulajdonosi listája

Mostantól megkeresheti egy adott csoport tagjait név szerint, és ugyanezt a keresést is végrehajthatja a csoport tulajdonosainak listáján. Ha az új funkcióban karakterláncot ad meg a keresőmezőbe, a rendszer automatikusan elvégzi a startswith keresését. Például a "Scott" kifejezés a Scott Wilkinson-t fogja visszaadni.

![az új alsztring a csoporttagok és a tulajdonosok listája alapján keres](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Tag és tulajdonosok listájának szűrése

A keresésen kívül a tag és a tulajdonos listáját is szűrheti felhasználói típus szerint. Ez a lista felhasználó típusa oszlopában található információ. Így szűrheti a listát a tagok és a vendégek között annak megállapításához, hogy vannak-e a csoportban lévő vendégek.

### <a name="view-and-manage-membership"></a>Tagság megtekintése és kezelése

Egy adott csoport közvetlen tagjainak megtekintése mellett most már megtekintheti a csoport tagjainak listáját a tagok oldalon. A tagok listán a csoport minden egyedi tagja szerepel, beleértve a tranzitív tagokat is.

A közvetlen tagok listáját és az összes tag listát is megkeresheti és szűrheti egyenként. A minden tag lista szűrése nem befolyásolja a közvetlen tagok listáján alkalmazott szűrőket.

## <a name="improved-group-member-counts"></a>Továbbfejlesztett csoporttagok száma

Javítottuk a csoport **áttekintő** oldalát, hogy a csoportok tagjainak száma minden méretben elérhető legyen. A tagok számát még a több mint 1 000 taggal rendelkező csoportok esetében is megtekintheti. Most már megtekintheti a csoport közvetlen tagjainak teljes számát és a tagságok teljes számát (a csoport minden egyedi tagja, beleértve a tranzitív tagokat is) az **Áttekintés** oldalon.

![Nagyobb pontosság a csoporttagság száma szerint](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Következő lépések

Ezek a cikkek további információkat nyújtanak a csoportok Azure AD-beli használatáról.

- [Csoportok és tagok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Csoporttagság kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-create-rule.md)
- [A csoport beállításainak szerkesztése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Az erőforrásokhoz való hozzáférés kezelése csoportokkal](../fundamentals/active-directory-manage-groups.md)
- [Az SaaS-alkalmazásokhoz való hozzáférés kezelése csoportokkal](groups-saasapps.md)
- [Csoportok kezelése PowerShell-parancsokkal](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Azure-előfizetés hozzáadása a Azure Active Directoryhoz](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
