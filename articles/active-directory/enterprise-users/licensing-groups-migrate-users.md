---
title: Közvetlen licenccel rendelkező felhasználók hozzáadása a licenceléshez – Azure AD | Microsoft Docs
description: Áttelepítés az egyes felhasználói licencekről a csoport alapú licencelésre Azure Active Directory használatával
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc80cf579ce3086abd4171b065f859acd0b9294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546505"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Egyéni licenccel rendelkező felhasználók áttelepítése licencelési csoportokba

Előfordulhat, hogy a szervezet felhasználói számára közvetlen hozzárendeléssel telepített meglévő licencek vannak telepítve. Ez a PowerShell-parancsfájlok vagy más eszközök használata az egyes felhasználói licencek hozzárendeléséhez. Mielőtt elkezdi a csoportos licencelés használatát a szervezeten belüli licencek kezeléséhez, az áttelepítési terv segítségével zökkenőmentesen lecserélheti a meglévő megoldásokat a csoport alapú licenceléssel.

A legfontosabb, hogy ne feledje, hogy el kell kerülnie azt a helyzetet, amikor a csoport alapú licencelésre való áttérés miatt a felhasználók átmenetileg elvesztik a jelenleg hozzárendelt licenceket. A licencek eltávolítását eredményező folyamatokat el kell kerülni, hogy el lehessen távolítani a felhasználókat a szolgáltatásokhoz és az azokhoz való hozzáférés elvesztésével járó kockázatokat.

## <a name="recommended-migration-process"></a>Ajánlott áttelepítési folyamat

1. Meglévő Automation (például PowerShell) segítségével kezeli a licencek hozzárendelését és eltávolítását a felhasználók számára. Ne futtassa a t.

1. Hozzon létre egy új licencelési csoportot (vagy döntse el, hogy mely meglévő csoportokat kívánja használni), és győződjön meg arról, hogy az összes szükséges felhasználó hozzá lesz adva tagként.

1. Rendelje hozzá a szükséges licenceket ezekhez a csoportokhoz; a cél az, hogy ugyanazt a licencelési állapotot tükrözze, amelyet a meglévő Automation (például a PowerShell) alkalmaz a felhasználókra.

1. Győződjön meg arról, hogy a licencek a csoportok összes felhasználójára lettek alkalmazva. Ez az alkalmazás úgy végezhető el, hogy ellenőrzi a feldolgozási állapotot az egyes csoportokon, és ellenőrzi a naplókat.

   - A licenc részleteinek megtekintésével az egyes felhasználókat is megvizsgálhatja. Látni fogja, hogy ugyanazok a licencek vannak hozzárendelve a "közvetlen" és "örökölt" csoportokhoz.

   - Egy PowerShell-szkript futtatásával ellenőrizheti, hogy a [licencek hogyan vannak hozzárendelve a felhasználókhoz](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Ha ugyanaz a licenc közvetlenül és egy csoporton keresztül van hozzárendelve a felhasználóhoz, akkor a felhasználó csak egy licencet használ fel. Ezért nincs szükség további licencekre az áttelepítés végrehajtásához.

1. Győződjön meg arról, hogy a licenc-hozzárendelések sikertelenek voltak. Ehhez ellenőrizze az egyes csoportokat a hibás állapotú felhasználók számára. További információ: [a csoport licencelési problémáinak azonosítása és megoldása](licensing-groups-resolve-problems.md).

Érdemes lehet eltávolítani az eredeti közvetlen hozzárendeléseket. Azt javasoljuk, hogy fokozatosan hajtsa végre, és figyelje meg az eredményt a felhasználók egy részhalmazán. Ha az eredeti közvetlen hozzárendeléseket el szeretné hagyni a felhasználók számára, de ha a felhasználók elhagyják a licenccel rendelkező csoportokat, megőrzik a közvetlenül hozzárendelt licenceket, amelyek esetleg nem a kívántak.

## <a name="an-example"></a>Példa

A szervezetnek 1 000 felhasználója van. Minden felhasználónak Office 365 Enterprise E3 licenc szükséges. Jelenleg a szervezet rendelkezik egy helyszíni PowerShell-szkripttel, amely a felhasználóktól érkező licencek hozzáadásával és eltávolításával jár. A szervezet azonban a parancsfájlt csoportos licenceléssel szeretné helyettesíteni, így a licenceket az Azure AD automatikusan tudja felügyelni.

Az áttelepítési folyamat így néz ki:

1. A Azure Portal használatával rendelje hozzá az Office 365 E3-licencet az Azure AD **összes felhasználó** csoportjához.

1. Győződjön meg arról, hogy az összes felhasználó számára befejeződött a licenc-hozzárendelés. Nyissa meg a csoport áttekintés lapját, válassza a **licencek** lehetőséget, majd a **licencek** panel felső részén tekintse meg a feldolgozás állapotát.

   - A feldolgozás befejezésének megerősítéséhez keresse meg a "legújabb licencelési módosítások alkalmazása az összes felhasználóra" lehetőséget.

   - Tekintse meg az azon felhasználókra vonatkozó értesítéseket, akik számára nem sikerült a licencek hozzárendelése. Kifogytak a licencek egyes felhasználók számára? Vannak-e olyan ütköző licenccel rendelkező felhasználók, amelyek megakadályozzák a csoportos licencek öröklését?

1. Ellenőrizze, hogy vannak-e olyan felhasználók, akik ellenőrzik, hogy mind a közvetlen, mind a csoportos licenc alkalmazva van-e. Lépjen a felhasználó profil lapjára, válassza a **licencek** lehetőséget, és vizsgálja meg a licencek állapotát.

   - Ez a várt felhasználói állapot az áttelepítés során:

      ![a várt felhasználói állapot az áttelepítés során](./media/licensing-groups-migrate-users/expected-user-state.png)

     Ezzel megerősíti, hogy a felhasználó közvetlen és örökölt licencekkel rendelkezik. Láthatjuk, hogy az Office 365 E3 van hozzárendelve.

   - Válassza ki az egyes licenceket, és ellenőrizze, hogy mely szolgáltatások engedélyezettek. Annak ellenőrzéséhez, hogy a közvetlen és a csoportos licenc pontosan ugyanazokat a szolgáltatásokat engedélyezi-e a felhasználó számára, válassza a **hozzárendelések** lehetőséget.

1. Miután megerősítette, hogy a közvetlen és a csoportos licencek is egyenértékűek, megkezdheti a közvetlen licencek eltávolítását a felhasználóktól. Ezt úgy tesztelheti, ha eltávolítja őket a portál egyes felhasználóival, majd automatizálja az Automation-parancsfájlokat, hogy azok tömeges eltávolításra kerülnek. Íme egy példa arra a felhasználóra, aki a közvetlen licenceket eltávolította a portálon keresztül. Figyelje meg, hogy a licenc állapota változatlan marad, de a közvetlen hozzárendelések már nem láthatók.

   ![Győződjön meg arról, hogy a közvetlen licencek el vannak távolítva](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Következő lépések

További információ a csoportos licencek kezelésével kapcsolatos egyéb forgatókönyvekről:

- [Mi a Azure Active Directory csoportos licencelése?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](licensing-groups-assign.md)
- [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](licensing-groups-resolve-problems.md)
- [Felhasználók áttelepítése licencek között a csoport alapú licencelés használatával Azure Active Directory](licensing-groups-change-licenses.md)
- [Az Azure Active Directory csoportalapú licencelésének további forgatókönyvei](licensing-group-advanced.md)
- [PowerShell-példák csoportházirend-alapú licenceléshez Azure Active Directory](licensing-ps-examples.md)
