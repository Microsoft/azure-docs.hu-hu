---
title: 'Oktatóanyag: a Symantec Web Security szolgáltatás (WSS) konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus üzembe helyezéséhez és kiépítéséhez a Symantec Web Security szolgáltatásban (WSS).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: d7e0db1b0bc1e7aef68ee06f3bdd5e5e0f83b73e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94354699"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Oktatóanyag: a Symantec Web Security szolgáltatás (WSS) konfigurálása a felhasználók automatikus üzembe helyezéséhez

Az oktatóanyag célja, hogy bemutassa a Symantec Web Security szolgáltatásban (WSS) és Azure Active Directory (Azure AD) végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat a Symantec Web Security Service (WSS) szolgáltatáshoz.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Symantec webes biztonsági szolgáltatás (WSS) bérlője](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Egy felhasználói fiók a Symantec Web Security szolgáltatásban (WSS) rendszergazdai engedélyekkel.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Felhasználók kiosztása a Symantec Web Security szolgáltatásban (WSS)

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok hozzáférhessenek a Symantec Web Security szolgáltatáshoz (WSS). Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Symantec Web Security szolgáltatáshoz (WSS) az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Fontos Tippek a felhasználók a Symantec Web Security szolgáltatáshoz (WSS) való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Symantec Web Security szolgáltatáshoz (WSS) a felhasználó automatikus kiépítési konfigurációjának teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a Symantec Web Security szolgáltatáshoz (WSS), ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>A Symantec Web Security szolgáltatás (WSS) beállítása a kiépítés számára

Mielőtt konfigurálja a Symantec Web Security szolgáltatást (WSS) az Azure AD-vel való automatikus felhasználó-kiépítés előtt, engedélyeznie kell a SCIM-létesítést a Symantec Web Security szolgáltatásban (WSS).

1. Jelentkezzen be a [Symantec Web Security szolgáltatás felügyeleti konzolján](https://portal.threatpulse.com/login.jsp). Navigáljon a **Solutions**  >  **szolgáltatáshoz**.

    ![Symantec webes biztonsági szolgáltatás (WSS)](media/symantec-web-security-service/service.png)

2. Navigáljon a **fiók-karbantartási**  >  **integrációk**  >  **új integrációhoz**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Válassza ki a **harmadik féltől származó felhasználók & csoportok szinkronizálását**. 

    ![Képernyőfelvétel a harmadik féltől származó felhasználók & csoportok szinkronizálása lehetőségről.](media/symantec-web-security-service/third-party-users.png)

4.  Másolja a **scim URL-címét** és **tokenjét**. Ezeket az értékeket a rendszer a Symantec Web Security szolgáltatás (WSS) alkalmazás üzembe helyezés lapjának **bérlő URL-címe** és **titkos jogkivonat** mezőjébe írja be a Azure Portal.

    ![Képernyőkép az új integráció párbeszédpanelről, amely az S C I M U R L és a jogkivonat szövegmezőt jelöli.](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Symantec Web Security szolgáltatás (WSS) hozzáadása a katalógusból

Ha a Symantec Web Security szolgáltatást (WSS) szeretné konfigurálni az Azure AD-vel való automatikus felhasználó-kiépítési szolgáltatáshoz, hozzá kell adnia a Symantec Web Security szolgáltatást (WSS) az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**A Symantec Web Security szolgáltatás (WSS) Azure AD-alkalmazás-katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Symantec Web Security szolgáltatást**, válassza a **Symantec Web Security szolgáltatás** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Symantec Web Security szolgáltatás (WSS) az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Automatikus felhasználó-kiépítés konfigurálása a Symantec Web Security szolgáltatásban (WSS)

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein, amelyekkel felhasználókat és/vagy csoportokat hozhat létre, frissíthet és tilt le a Symantec Web Security szolgáltatásban (WSS) az Azure AD-ben a felhasználók és/vagy csoportok hozzárendelései alapján.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Symantec Web Security szolgáltatáshoz (WSS), a [Symantec Web Security szolgáltatás (WSS) egyszeri bejelentkezés oktatóanyagában](symantec-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Symantec Web Security szolgáltatáshoz (WSS) az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Symantec Web Security szolgáltatás** elemet.

    ![A Symantec Web Security szolgáltatás (WSS) hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg a **bérlői URL-címben** és a **titkos jogkivonatban** korábban lekért **scim URL-címet** és **jogkivonat** -értékeket. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a Symantec Web Security szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Symantec Web Security szolgáltatás (WSS) fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Symantec Web Security szolgáltatással (WSS)** lehetőséget.

    ![Képernyőfelvétel a leképezésekről szakasz a Azure Active Directory felhasználók szinkronizálása a Symantec Web Security szolgáltatáshoz.](media/symantec-web-security-service/usermapping.png)

9. Tekintse át az Azure AD-ről a Symantec Web Security szolgáltatásra (WSS) szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Symantec Web Security szolgáltatás (WSS) felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Képernyőkép a hozzárendelés-leképezésről, amely 16 egyező tulajdonságot mutat be.](media/symantec-web-security-service/userattribute.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Symantec Web Security szolgáltatáshoz** lehetőséget.

    ![Képernyőfelvétel a leképezésekről szakasz a Azure Active Directory csoportok szinkronizálása a Symantec Web Security szolgáltatáshoz.](media/symantec-web-security-service/groupmapping.png)

11. Tekintse át az Azure AD-ről a Symantec Web Security szolgáltatásra (WSS) szinkronizált csoport attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Symantec Web Security szolgáltatásban (WSS) lévő csoportok egyeztetésére használhatók frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![A hozzárendelés-leképezési szakasz képernyőképe, amely három egyező tulajdonságot mutat be.](media/symantec-web-security-service/groupattribute.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Symantec Web Security szolgáltatáshoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne a Symantec Web Security szolgáltatásba (WSS) kiépíteni a **Beállítások** szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, hogy [mennyi ideig tart a felhasználók kiépítése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik a Symantec Web Security szolgáltatásban (WSS). További információ: [a felhasználó kiépítési állapotának ellenõrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../app-provisioning/check-status-user-account-provisioning.md)során.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
