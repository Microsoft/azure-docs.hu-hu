---
title: 'Oktatóanyag: a Wrike konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a Wrike felhasználói fiókjait.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 53b1db1a8c4da59055c0af5f448fa0c8a6933daf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95988127"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Wrike konfigurálása

Ennek az oktatóanyagnak a célja, hogy bemutassa a Wrike és a Azure Active Directory (Azure AD) által végrehajtott lépéseket az Azure AD konfigurálásához, hogy a felhasználók vagy csoportok automatikusan kiépítsék és felépítsék a Wrike.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók kiépítésének [automatizálása és a szolgáltatott szoftveres (SaaS) alkalmazások kiépítése a Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Wrike-bérlő](https://www.wrike.com/price/)
* Rendszergazdai jogosultságokkal rendelkező Wrike felhasználói fiók

## <a name="assign-users-to-wrike"></a>Felhasználók Wrike rendelése
Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználói vagy csoportjai férhetnek hozzá a Wrike. Ezután rendelje hozzá ezeket a felhasználókat vagy csoportokat a Wrike-hez az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Fontos Tippek a felhasználók Wrike való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen hozzá a Wrike-hez az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. További felhasználók vagy csoportok később is hozzárendelhetők.

* Amikor Wrike rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-wrike-for-provisioning"></a>Wrike beállítása a kiépítés számára

Mielőtt konfigurálja a Wrike-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, engedélyeznie kell a rendszer számára a tartományok közötti Identitáskezelés (SCIM) létesítését a Wrike-on.

1. Jelentkezzen be a [Wrike felügyeleti konzolra](https://www.Wrike.com/login/). Nyissa meg a bérlői AZONOSÍTÓját. Válassza az **alkalmazások & integrációk** lehetőséget.

    ![Alkalmazások & integrációk](media/Wrike-provisioning-tutorial/admin.png)

2.  Nyissa meg az **Azure ad** -t, és válassza ki.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Válassza a SCIM lehetőséget. Másolja ki az **alap URL-címet**.

    ![Kiindulási URL-cím](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Válassza az **API**  >  **Azure scim** lehetőséget.

    ![Azure-SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Megnyílik egy előugró ablak. Adja meg ugyanazt a jelszót, amelyet korábban hozott létre egy fiók létrehozásához.

    ![Wrike-létrehozási jogkivonat](media/Wrike-provisioning-tutorial/password.png)

6.  Másolja ki a **titkos jogkivonatot**, és illessze be az Azure ad-be. Válassza a **Mentés** lehetőséget a kiépítési beállítás befejezéséhez a Wrike.

    ![Állandó hozzáférési jogkivonat](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Wrike hozzáadása a gyűjteményből

Mielőtt konfigurálja a Wrike-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, adja hozzá a Wrike-t az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

Az alábbi lépéseket követve adhat hozzá Wrike az Azure AD Application Galleryből.

1. A [Azure Portal](https://portal.azure.com)bal oldali navigációs ablaktábláján válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Wrike** kifejezést, válassza az **Wrike** elemet az eredmények panelen, majd válassza a **Hozzáadás** lehetőséget az alkalmazás hozzáadásához.

    ![Wrike az eredmények listájában](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Automatikus felhasználó-kiépítés beállítása a Wrike 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, amelyek segítségével a felhasználók vagy csoportok Wrike hozhatók létre, frissíthetők és letilthatók az Azure AD-ben.

> [!TIP]
> Az SAML-alapú egyszeri bejelentkezés Wrike való engedélyezéséhez kövesse az [Wrike egyszeri bejelentkezés oktatóanyagának](wrike-tutorial.md)utasításait. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása a Wrike az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![Minden alkalmazás](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Wrike** lehetőséget.

    ![Az Wrike hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![A kiépítési mód beállítása automatikus](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg a **bérlői URL** -címben és a **titkos jogkivonatban** korábban lekért **alap URL-címet** és **állandó hozzáférési jogkivonat** -értékeket. Válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen csatlakozni a Wrike. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Wrike-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibajelentési értesítéseket. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

8. Kattintson a **Mentés** gombra.

9. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Wrike** lehetőséget.

    ![Wrike felhasználói leképezések](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelések** szakasz Wrike. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Wrike felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    ![Wrike felhasználói attribútumai](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. A hatóköri szűrők konfigurálásához kövesse a hatókör- [szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

12. Az Azure AD-kiépítési szolgáltatás Wrike való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

13. Adja meg a Wrike kiépíteni kívánt felhasználókat vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

14. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók vagy csoportok üzembe helyezése: meddig tart [a felhasználók kiépítése?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Wrike-on végrehajtott összes műveletet ismertetik. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../app-provisioning/check-status-user-account-provisioning.md)során.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)