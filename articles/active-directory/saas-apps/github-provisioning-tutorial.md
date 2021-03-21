---
title: 'Oktatóanyag: felhasználói kiépítés a GitHubhoz – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a GitHubba.
services: active-directory
author: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: Zhchia
ms.openlocfilehash: f1600dfc5705ca97f16e8966a796b54fc556d216
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359262"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Oktatóanyag: a GitHub konfigurálása automatikus felhasználói üzembe helyezéshez

Ennek az oktatóanyagnak a célja, hogy megmutassa a GitHubon és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a GitHubba.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Azure Active Directory-bérlő
* GitHub [vállalati felhőben](https://help.github.com/articles/github-s-products/#github-enterprise)létrehozott GitHub-szervezet, amelyhez a [GitHub Enterprise számlázási csomag](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) szükséges
* Felhasználói fiók a GitHubban rendszergazdai engedélyekkel a szervezethez
* [SAML-konfiguráció a GitHub Enterprise Cloud organizationhoz](./github-tutorial.md)
* Győződjön meg arról, hogy az [itt](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) leírtak szerint a OAuth hozzáférése meg lett biztosítva a szervezet számára
* Az SCIM-kiépítés egyetlen szervezet számára csak akkor támogatott, ha a szervezeti szinten engedélyezve van az egyszeri bejelentkezés.

> [!NOTE]
> Az Azure AD-kiépítés integrációja a GitHub [scim API](https://developer.github.com/v3/scim/)-ra támaszkodik, amely a GitHub Enterprise [Számlázási csomagjában](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)elérhető [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) -ügyfelek számára érhető el.

## <a name="assigning-users-to-github"></a>Felhasználók kiosztása a GitHubhoz

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek. 

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználók és/vagy csoportok szerepelnek az Azure AD-ben azon felhasználók számára, akiknek hozzáférést kell biztosítani a GitHub-alkalmazáshoz. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a GitHub-alkalmazáshoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Fontos Tippek a felhasználók GitHubhoz való hozzárendeléséhez

* Azt javasoljuk, hogy a kiépítési konfiguráció teszteléséhez egyetlen Azure AD-felhasználó legyen hozzárendelve a GitHubhoz. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a GitHubhoz, ki kell választania a **felhasználói** szerepkört vagy egy másik érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkör nem működik a kiépítés során, és ezek a felhasználók kimaradnak.

## <a name="configuring-user-provisioning-to-github"></a>A felhasználók üzembe helyezésének beállítása a GitHubra

Ez a szakasz végigvezeti az Azure AD-nek a GitHub felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználók és csoportok hozzárendelése alapján a GitHubhoz rendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Automatikus felhasználói fiók üzembe helyezésének beállítása a GitHubhoz az Azure AD-ben

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás**  szakaszt.

2. Ha már konfigurálta a GitHubot az egyszeri bejelentkezéshez, keressen rá a GitHub-példányra a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **githubot** az alkalmazás-katalógusban. Válassza a GitHub elemet a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a GitHub-példányt, majd válassza a **kiépítés** lapot.

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![GitHub-kiépítés](./media/github-provisioning-tutorial/GitHub1.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés** elemre. A művelet megnyit egy GitHub-engedélyezési párbeszédpanelt egy új böngészőablakban. Ügyeljen arra, hogy a hozzáférés engedélyezéséhez jóvá kell hagynia a jóváhagyást. Kövesse az [itt](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)ismertetett útmutatást.

6. Az új ablakban jelentkezzen be a GitHubba a rendszergazdai fiókjával. Az eredményül kapott hitelesítés párbeszédpanelen válassza ki azt a GitHub-csoportot, amely számára engedélyezni szeretné a kiépítés beállítást, majd válassza az **Engedélyezés** lehetőséget. Miután végzett, lépjen vissza az Azure Portalra az átadás konfigurációjának befejezéséhez.

    ![A képernyőfelvételen a GitHub bejelentkezési oldala látható.](./media/github-provisioning-tutorial/GitHub2.png)

7. A Azure Portal írja be a **bérlői URL-címet** , majd kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a GitHub-alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a GitHub-fiókja rendszergazdai jogosultságokkal rendelkezik, és a **bérlői URL-cím** helyesen van megadva, majd próbálja megismételni az "engedélyezés" lépést (a **bérlői URL-cím** a szabály `https://api.github.com/scim/v2/organizations/<Organization_name>` szerint megtalálhatja a szervezeteket a GitHub-fiókjában: **Settings**  >  **Organizations**).

    ![Képernyőfelvétel: a GitHubon a szervezetek lapja látható.](./media/github-provisioning-tutorial/GitHub3.png)

8. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az értesítő e-mail-értesítéseket az **értesítési e-mail** mezőben, és jelölje be az "e-mail-értesítés küldése hiba esetén" jelölőnégyzetet.

9. Kattintson a **Mentés** gombra.

10. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a githubra** lehetőséget.

11. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből a githubra szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a GitHub felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások véglegesítéséhez válassza a Mentés gombot.

12. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a GitHubhoz, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

13. Kattintson a **Mentés** gombra.

Ez a művelet elindítja a felhasználók és csoportok szakaszban a GitHubhoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz használatával figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által végrehajtott összes műveletet leírják.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)