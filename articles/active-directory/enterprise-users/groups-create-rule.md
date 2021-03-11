---
title: Dinamikus csoport létrehozása vagy szerkesztése és állapot beolvasása – Azure AD | Microsoft Docs
description: Csoporttagság-szabály létrehozása vagy frissítése a Azure Portalban, és a feldolgozási állapotának megkeresése.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8525e9a76fa2439692cdb26b36c0bb761d63177c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550229"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Dinamikus csoport létrehozása vagy frissítése Azure Active Directory

Azure Active Directory (Azure AD) esetében a szabályok segítségével meghatározhatja a csoporttagság alapján a felhasználók vagy az eszközök tulajdonságait. Ez a cikk azt ismerteti, hogyan lehet szabályt beállítani egy dinamikus csoporthoz a Azure Portalban.
A dinamikus tagság biztonsági csoportok vagy Microsoft 365 csoportok esetén támogatott. Csoporttagság-szabály alkalmazása esetén a rendszer kiértékeli a felhasználók és az eszközök attribútumait a tagsági szabállyal való egyezések alapján. Amikor egy attribútum megváltoztatja a felhasználót vagy az eszközt, a rendszer a szervezet összes dinamikus csoportjának szabályait dolgozza fel a tagság változásaihoz. A felhasználók és az eszközök akkor lesznek hozzáadva vagy eltávolítva, ha megfelelnek a csoport feltételeinek. A biztonsági csoportok bármely eszközhöz vagy felhasználóhoz használhatók, de Microsoft 365 csoportok csak felhasználói csoportok lehetnek. A dinamikus csoportok használatához az Azure AD Premium P1 licenc szükséges. További részletekért tekintse [meg a csoportok dinamikus tagsági szabályait](./groups-dynamic-membership.md) . 

## <a name="rule-builder-in-the-azure-portal"></a>Szabálykészlet a Azure Portalban

Az Azure AD egy olyan szabályt biztosít, amellyel gyorsabban hozhat létre és frissíthet fontos szabályokat. A szabály-szerkesztő legfeljebb öt kifejezést támogat. A szabály-szerkesztő megkönnyíti néhány egyszerű kifejezéssel rendelkező szabály kiépítését, azonban nem használható minden szabály újbóli létrehozásához. Ha a szabály-szerkesztő nem támogatja a létrehozni kívánt szabályt, használhatja a szövegmezőt.

Íme néhány példa a speciális szabályokra vagy szintaxisra, amelyekhez ajánlott a következő szövegmezővel létrehozni:

- Szabály ötnél több kifejezéssel
- A közvetlen jelentések szabálya
- [Operátor prioritásának](groups-dynamic-membership.md#operator-precedence) beállítása
- [Összetett kifejezésekkel rendelkező szabályok](groups-dynamic-membership.md#rules-with-complex-expressions); például: `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Előfordulhat, hogy a szabály-szerkesztő nem tudja megjeleníteni a szövegmezőben létrehozott egyes szabályokat. Előfordulhat, hogy egy üzenet jelenik meg, ha a szabály-szerkesztő nem tudja megjeleníteni a szabályt. A szabály-szerkesztő semmilyen módon nem módosítja a dinamikus csoport szabályainak támogatott szintaxisát, érvényesítését vagy feldolgozását.

![Képernyőkép a "dinamikus tagsági szabályok" lap "kifejezés hozzáadása" műveletével, amely a "szabályok konfigurálása" lapon van kiválasztva.](./media/groups-create-rule/update-dynamic-group-rule.png)

A tagsági szabályok szintaxisára, támogatott tulajdonságaira, operátorára és értékeire példákat a következő témakörben talál: [Azure Active Directory csoportok dinamikus tagsági szabályai](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Csoporttagság-szabály létrehozása

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a globális rendszergazda, az Intune-rendszergazda vagy a felhasználói rendszergazdai szerepkör tagja az Azure ad-szervezetben.
1. **Csoportok** keresése és kiválasztása.
1. Válassza ki **az összes csoportot**, és válassza az **új csoport** lehetőséget.

   ![Válassza ki az új csoport hozzáadására szolgáló parancsot](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. A **csoport** lapon adja meg az új csoport nevét és leírását. Válasszon **tagsági típust** a felhasználók vagy eszközök számára, majd válassza a **dinamikus lekérdezés hozzáadása** lehetőséget. A szabály-szerkesztő legfeljebb öt kifejezést támogat. Ötnél több kifejezés hozzáadásához a szövegmezőt kell használnia.

   ![Képernyőkép, amely a "minden csoport" lapot mutatja az "új csoport" művelet kiválasztásával.](./media/groups-create-rule/add-dynamic-group-rule.png)

1. A tagsági lekérdezéshez elérhető egyéni kiterjesztési tulajdonságok megtekintéséhez:
   1. Válassza az **Egyéni bővítmény tulajdonságainak beolvasása** elemet.
   1. Adja meg az alkalmazás AZONOSÍTÓját, majd kattintson a **Tulajdonságok frissítése** elemre.
1. A szabály létrehozása után válassza a **Mentés** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget az **új csoport** lapon a csoport létrehozásához.

Ha a beírt szabály nem érvényes, a portálon egy Azure-értesítésben láthatja, hogy a szabály miért nem dolgozható fel. Olvassa el figyelmesen, hogy megtudja, hogyan javíthatja a szabályt.

## <a name="to-update-an-existing-rule"></a>Meglévő szabály frissítése

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a globális rendszergazda, a csoport rendszergazdája, az Intune-rendszergazda vagy a felhasználói rendszergazdai szerepkör tagja az Azure ad-szervezetben.
1. Válassza   >  **a csoportok minden csoport** elemet.
1. Válasszon ki egy csoportot a profiljának megnyitásához.
1. A csoport profil lapján válassza a **dinamikus tagsági szabályok** lehetőséget. A szabály-szerkesztő legfeljebb öt kifejezést támogat. Ötnél több kifejezés hozzáadásához a szövegmezőt kell használnia.

   ![Tagsági szabály hozzáadása dinamikus csoporthoz](./media/groups-create-rule/update-dynamic-group-rule.png)

1. A tagsági szabályhoz elérhető egyéni kiterjesztési tulajdonságok megtekintéséhez:
   1. Válassza az **Egyéni bővítmény tulajdonságainak beolvasása** elemet.
   1. Adja meg az alkalmazás AZONOSÍTÓját, majd kattintson a **Tulajdonságok frissítése** elemre.
1. A szabály frissítése után válassza a **Mentés** lehetőséget.

## <a name="turn-on-or-off-welcome-email"></a>Üdvözlő e-mailek be-és kikapcsolása

Új Microsoft 365 csoport létrehozásakor a rendszer üdvözlő e-mail-értesítést küld a csoportba felvett felhasználóknak. Később, ha a felhasználó vagy az eszköz bármelyik attribútuma módosul, a rendszer a szervezet összes dinamikus csoportjának szabályait dolgozza fel a tagság változásaihoz. A hozzáadott felhasználók megkapják az üdvözlő értesítést is. Ezt a viselkedést az [Exchange PowerShellben](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup)is kikapcsolhatja.

## <a name="check-processing-status-for-a-rule"></a>Szabály feldolgozási állapotának keresése

A csoport **Áttekintés** lapján megtekintheti a tagság feldolgozási állapotát és a legutóbbi frissítés dátumát.
  
  ![a dinamikus csoport állapotának megjelenítése](./media/groups-create-rule/group-status.png)

A következő állapotüzenetek láthatók a **tagság feldolgozási** állapotához:

- **Értékelés**: a csoport módosítása megérkezett, és a frissítések kiértékelése folyamatban van.
- **Feldolgozás**: folyamatban van a frissítések feldolgozása.
- A **frissítés befejeződött**: a feldolgozás befejeződött, és a rendszer az összes vonatkozó frissítést elvégezte.
- **Feldolgozási hiba**: a feldolgozás nem fejeződött be, mert hiba történt a tagsági szabály kiértékelése során.
- A **frissítés szüneteltetve**: a dinamikus tagsági szabály frissítéseit a rendszergazda szünetelteti. A MembershipRuleProcessingState "szüneteltetve" értékre van állítva.

A következő állapotüzenetek jeleníthetők meg a **tagság utolsó frissítésének** állapotához:

- &lt;**Dátum és idő** &gt; : a tagság utolsó frissítésekor.
- **Folyamatban**: a frissítések jelenleg folyamatban vannak.
- **Ismeretlen**: az utolsó frissítés időpontja nem olvasható be. Lehet, hogy a csoport új.

Ha hiba lép fel egy adott csoport tagsági szabályának feldolgozása közben, a rendszer riasztást jelenít meg a csoport **Áttekintés oldalának** tetején. Ha 24 óránál hosszabb ideig nem lehet feldolgozni a szervezeten belüli összes csoportra vonatkozóan a függőben lévő dinamikus tagsági frissítéseket, a rendszer riasztást jelenít meg az **összes csoport** tetején.

![hibaüzenetek feldolgozása – riasztások](./media/groups-create-rule/processing-error.png)

Ezek a cikkek további információkat nyújtanak Azure Active Directory csoportjairól.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)