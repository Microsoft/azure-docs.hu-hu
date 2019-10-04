---
title: A felhasználók üzembe helyezésének felügyelete a Azure Active Directory vállalati alkalmazásaiban | Microsoft Docs
description: Megtudhatja, hogyan kezelheti a vállalati alkalmazások felhasználói fiókjának kiépítési folyamatait a Azure Active Directory használatával
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26b00670ad93cceab8f570d3a5f56bd095fa80b5
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315270"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>A vállalati alkalmazások felhasználói fiókkal való üzembe helyezésének kezelése a Azure Portal

Ez a cikk azt ismerteti, hogyan használható a [Azure Portal](https://portal.azure.com) a felhasználói fiókok automatikus üzembe helyezésére és kiépítés kezelésére az azt támogató alkalmazások esetében. További információ a felhasználói fiókok automatikus kiépítéséről és működéséről: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory használatával](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Alkalmazások keresése a portálon

A Azure Active Directory portálon megtekintheti és kezelheti az egyszeri bejelentkezéshez konfigurált összes alkalmazást egy címtárban. A vállalati alkalmazások a szervezeten belül üzembe helyezett és használt alkalmazások. A vállalati alkalmazások megtekintéséhez és kezeléséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [Azure Active Directory portált](https://aad.portal.azure.com).
1. Válassza a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán. Megjelenik az összes konfigurált alkalmazás listája, beleértve a gyűjteményből hozzáadott alkalmazásokat is.
1. Válassza ki bármelyik alkalmazást az erőforrás-ablaktábla betöltéséhez, ahol megtekintheti a jelentéseket, és kezelheti az alkalmazás beállításait.
1. A **kiépítés** lehetőség kiválasztásával kezelheti a kiválasztott alkalmazás felhasználói fiókjának kiépítési beállításait.

   ![Kiépítési képernyő a felhasználói fiókok üzembe helyezési beállításainak kezeléséhez](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Üzembe helyezési módok

A **kiépítés** ablaktábla egy **üzemmód** menüvel kezdődik, amely a vállalati alkalmazások által támogatott üzembe helyezési módokat mutatja, és lehetővé teszi a konfigurálását. Az elérhető lehetőségek a következők:

* **Automatikus** – ez a beállítás akkor jelenik meg, ha az Azure ad támogatja az API-alapú üzembe helyezést vagy a felhasználói fiókok ezen alkalmazáshoz való kivonását. Válassza ezt a módot egy olyan felület megjelenítéséhez, amely segít a rendszergazdáknak:

  * Az Azure AD konfigurálása az alkalmazás felhasználói kezelési API-hoz való kapcsolódáshoz
  * Fiók-hozzárendelések és munkafolyamatok létrehozása, amelyek meghatározzák, hogy a felhasználói fiókadatok hogyan legyenek az Azure AD és az alkalmazás között
  * Az Azure AD-kiépítési szolgáltatás kezelése

* **Manuális** – ez a beállítás akkor jelenik meg, ha az Azure ad nem támogatja a felhasználói fiókok automatikus üzembe helyezését az alkalmazásban. Ebben az esetben az alkalmazásban tárolt felhasználói fiókok rekordjait külső folyamattal kell felügyelni, az adott alkalmazás által biztosított felhasználói felügyelet és kiépítési képességek alapján (amely magában foglalhatja az SAML igény szerinti üzembe helyezését).

## <a name="configuring-automatic-user-account-provisioning"></a>A felhasználói fiókok automatikus üzembe helyezésének konfigurálása

Válassza az **automatikus** lehetőséget a rendszergazdai hitelesítő adatok, hozzárendelések, Indítás és Leállítás, valamint a szinkronizálás beállításainak megadásához.

### <a name="admin-credentials"></a>Rendszergazdai hitelesítő adatok

A **rendszergazdai hitelesítő adatok** kibontásával adja meg azokat a hitelesítő adatokat, amelyek szükségesek ahhoz, hogy az Azure ad csatlakozhasson az alkalmazás felhasználói felügyeleti API-hoz. A bemenet megadása az alkalmazástól függően változhat. Az adott alkalmazások hitelesítő adataival és követelményeivel kapcsolatos további információkért tekintse meg az adott [alkalmazás konfigurációs oktatóanyagát](user-provisioning.md).

Válassza a **kapcsolat tesztelése** lehetőséget a hitelesítő adatok teszteléséhez azáltal, hogy az Azure ad megpróbál csatlakozni az alkalmazás kiépítési alkalmazásához a megadott hitelesítő adatok használatával.

### <a name="mappings"></a>Leképezések

A **leképezések** kibontásával megtekintheti és szerkesztheti az Azure ad és a célalkalmazás közötti felhasználói attribútumokat, amikor a felhasználói fiókokat kiépítik vagy frissítik.

Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között előre konfigurált leképezések vannak. Egyes alkalmazások más típusú objektumokat, például csoportokat vagy névjegyeket kezelnek. Válasszon ki egy leképezést a táblázatban a leképezési szerkesztő jobbra való megnyitásához, ahol megtekintheti és testreszabhatja azokat.

![Az attribútum-leképezési képernyő megjelenítése](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

A támogatott testreszabások a következők:

* Adott objektumok (például az Azure AD felhasználói objektum) leképezésének engedélyezése és letiltása a SaaS-alkalmazás felhasználói objektumához.
* Az Azure AD felhasználói objektumból az alkalmazás felhasználói objektumára áramló attribútumok szerkesztése. További információ az attribútumok hozzárendeléséről: az [attribútumok megfeleltetési típusainak ismertetése](customize-application-attributes.md#understanding-attribute-mapping-types).
* Az Azure AD által a célként megadott alkalmazáson futtatott kiépítési műveletek szűrése. Ahelyett, hogy az Azure AD teljes mértékben szinkronizálja az objektumokat, korlátozhatja a műveletek futtatását.

  Ha például csak a **frissítés** lehetőséget választja, és az Azure ad csak a meglévő felhasználói fiókokat frissíti egy alkalmazásban, de nem hoz létre újakat. Csak a **Létrehozás** és az Azure lehetőséget kell választania, csak új felhasználói fiókokat hoz létre, de nem frissíti a meglévőket. Ez a funkció lehetővé teszi, hogy a rendszergazdák különböző leképezéseket hozzanak létre a fiókok létrehozásához és a munkafolyamatokhoz.

* Új attribútum-hozzárendelés hozzáadása. Az **attribútum-hozzárendelési** ablaktábla alján kattintson az **Új leképezés hozzáadása** lehetőségre. Töltse ki az **attribútum szerkesztése** űrlapot, és kattintson az **OK** gombra az új hozzárendelésnek a listához való hozzáadásához.

### <a name="settings"></a>Beállítások

A **kiépítési** képernyő **Beállítások** területén elindíthatja és leállíthatja az Azure ad kiépítési szolgáltatást a kiválasztott alkalmazáshoz. Dönthet úgy is, hogy törli a kiépítési gyorsítótárat, és újraindítja a szolgáltatást.

Ha az üzembe helyezést az alkalmazás első alkalommal engedélyezi, kapcsolja be a szolgáltatást úgy, hogy a **kiépítési állapotot** **a be**értékre módosítja. Ez a módosítás azt eredményezi, hogy az Azure AD-kiépítési szolgáltatás kezdeti ciklust futtat. Beolvassa a **felhasználók és csoportok** szakaszban hozzárendelt felhasználókat, lekérdezi azokat, majd futtatja az Azure ad- **leképezések** szakaszban meghatározott üzembe helyezési műveleteket. A folyamat során a kiépítési szolgáltatás tárolja a gyorsítótárazott információkat arról, hogy milyen felhasználói fiókokat kezel, ezért a nem felügyelt fiókokat a nem a hozzárendelés hatókörébe tartozó alkalmazásokon belül nem érinti a kiépítési műveletek. A kezdeti ciklust követően a kiépítési szolgáltatás 40 percenként automatikusan szinkronizálja a felhasználók és a csoportok objektumait.

Állítsa a **kiépítési állapotot** **ki** értékre a kiépítési szolgáltatás szüneteltetéséhez. Ebben az állapotban az Azure nem hoz létre, nem frissít vagy távolít el semmilyen felhasználói vagy csoportos objektumot az alkalmazásban. Állítsa vissza az állapotot **a be** értékre, és a szolgáltatás felveszi a helyét, ahol abbahagyta.

Jelölje be a **jelenlegi állapot törlése és a szinkronizálás újraindítása** jelölőnégyzetet, és válassza a **Mentés** a következőre:

* A kiépítési szolgáltatás leállítása
* Indítsa újra a szolgáltatásokat, és futtassa újra a kezdeti ciklust

Ez a beállítás lehetővé teszi, hogy a rendszergazdák újra elindítsák a kiépítési telepítési folyamatot.
