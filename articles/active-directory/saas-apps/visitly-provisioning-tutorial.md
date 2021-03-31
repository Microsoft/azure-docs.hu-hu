---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés meglátogatásának konfigurálása a Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a látogatáshoz.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: ff3f3ab65df2d801b7c962de7cce645e9fc00b30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358609"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználói üzembe helyezéshez való felkeresés konfigurálása

Ennek az oktatóanyagnak a célja, hogy bemutassa a felkeresett és Azure Active Directory (Azure AD) lépéseit az Azure AD konfigurálásához, hogy a felhasználók vagy csoportok számára automatikusan kiépítse és kiépítse a felkeresett feladatokat.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók kiépítésének [automatizálása és a szolgáltatott szoftveres (SaaS) alkalmazások kiépítése a Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Felkeresett bérlő](https://www.visitly.io/pricing/)
* Felhasználói fiók meglátogatva rendszergazdai engedélyekkel

## <a name="assign-users-to-visitly"></a>Felhasználók kiosztása a látogatáshoz 

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak vagy csoportjai számára szükséges a látogatás. Ezután a következő utasításokat követve rendelje hozzá ezeket a felhasználókat vagy csoportokat a látogatáshoz:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Fontos Tippek a felhasználók a látogatáshoz való hozzárendeléséhez 

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen hozzá az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. További felhasználók vagy csoportok később is hozzárendelhetők.

* Amikor felhasználókat rendel a látogatáshoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-visitly-for-provisioning"></a>A kiépítés meglátogatásának beállítása

Az Azure AD-vel való automatikus felhasználó-kiépítés előtt érdemes engedélyezni a rendszert a tartományok közötti Identitáskezelés (SCIM) üzembe helyezéséhez a látogatás során.

1. A [látogatáshoz](https://app.visitly.io/login)jelentkezzen be. Válassza az **integrációk**  >  **gazdagép-szinkronizálás** lehetőséget.

    ![Gazdagép szinkronizálása](media/Visitly-provisioning-tutorial/login.png)

2. Válassza ki az **Azure ad** szakaszt.

    ![Azure AD-szakasz](media/Visitly-provisioning-tutorial/integration.png)

3. Másolja az **API-kulcsot**. Ezek az értékek a Azure Portalban a felkeresett alkalmazás **üzembe** helyezés lapjának **titkos jogkivonat** mezőjében szerepelnek.

    ![API-kulcs](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Látogatások hozzáadása a katalógusból

Az Azure AD-vel való automatikus felhasználói kiépítés meglátogatásának konfigurálásához az Azure AD-alkalmazás-katalógusban keresse fel a következőt: a felügyelt SaaS-alkalmazások listája.

Az Azure AD Application Galleryből való felkereséshez kövesse az alábbi lépéseket.

1. A [Azure Portal](https://portal.azure.com)bal oldali navigációs ablaktábláján válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A **keresőmezőbe írja be a felkeresett** elemet, válassza a **látogatás** lehetőséget az eredmények panelen, majd válassza a **Hozzáadás** lehetőséget az alkalmazás hozzáadásához.

    ![Látogatás az eredmények listájában](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Automatikus felhasználó-kiépítés beállítása a látogatáshoz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy a felhasználók vagy csoportok látogatását, frissítését és letiltását az Azure AD-ben, felhasználói vagy csoportos hozzárendelések alapján hozza létre.

> [!TIP]
> Az SAML-alapú egyszeri bejelentkezés felkereséséhez kövesse a [felkeresett egyszeri bejelentkezési oktatóanyag](Visitly-tutorial.md)utasításait. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása az Azure AD-ben való felkereséshez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![Minden alkalmazás](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **látogatás** lehetőséget.

    ![A felkeresett hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![A kiépítési mód beállítása automatikus](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg a `https://api.visitly.io/v1/usersync/SCIM` **bérlői URL-címben** és a **titkos jogkivonatban** korábban lekért és az **API-kulcs** értékeit. Válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen a látogatásra. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a felkeresett fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibajelentési értesítéseket. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók látogatása** lehetőséget.

    ![Felkeresett felhasználói leképezések](media/visitly-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelések** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok használatával a rendszer megkeresi a felhasználói fiókokat a frissítési műveletekhez. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    ![Felkeresett felhasználói attribútumok](media/visitly-provisioning-tutorial/userattribute.png)

10. A hatóköri szűrők konfigurálásához kövesse a hatókör- [szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

11. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a látogatáshoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és csoportokat, amelyeket fel szeretne venni a látogatáshoz, és válassza ki a kívánt értékeket a **hatókör** területen a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók vagy csoportok üzembe helyezése: meddig tart [a felhasználók kiépítése?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a látogatás során végrehajtott összes műveletet ismertetik. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../app-provisioning/check-status-user-account-provisioning.md)során.

## <a name="connector-limitations"></a>Összekötő korlátozásai

A látogatás nem támogatja a merevlemezek törlését. Minden, ami csak a törlést törli.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
