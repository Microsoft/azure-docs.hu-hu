---
title: 'Oktatóanyag: a Symantec Web Security szolgáltatás (WSS) konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus üzembe helyezéséhez és kiépítéséhez a Symantec Web Security szolgáltatásban (WSS).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: fbd105ca1623512a3c16f3b609374f5c4055898b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063118"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Oktatóanyag: a Symantec Web Security szolgáltatás (WSS) konfigurálása a felhasználók automatikus üzembe helyezéséhez

Az oktatóanyag célja, hogy bemutassa a Symantec Web Security szolgáltatásban (WSS) és Azure Active Directory (Azure AD) végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat a Symantec Web Security Service (WSS) szolgáltatáshoz.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
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

1. Jelentkezzen be a [Symantec Web Security szolgáltatás felügyeleti konzolján](https://portal.threatpulse.com/login.jsp). Navigáljon a **megoldások** > **szolgáltatáshoz**.

    ![Symantec webes biztonsági szolgáltatás (WSS)](media/symantec-web-security-service/service.png)

2. Navigáljon a **fiók karbantartása** > **integrációk** > **új integráció**elemre.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Válassza ki a **harmadik féltől származó felhasználók & csoportok szinkronizálását**. 

    ![Symantec webes biztonsági szolgáltatás](media/symantec-web-security-service/third-party-users.png)

4.  Másolja a **scim URL-címét** és **tokenjét**. Ezeket az értékeket a rendszer a Symantec Web Security szolgáltatás (WSS) alkalmazás üzembe helyezés lapjának **bérlő URL-címe** és **titkos jogkivonat** mezőjébe írja be a Azure Portal.

    ![Symantec webes biztonsági szolgáltatás](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Symantec Web Security szolgáltatás (WSS) hozzáadása a katalógusból

Ha a Symantec Web Security szolgáltatást (WSS) szeretné konfigurálni az Azure AD-vel való automatikus felhasználó-kiépítési szolgáltatáshoz, hozzá kell adnia a Symantec Web Security szolgáltatást (WSS) az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**A Symantec Web Security szolgáltatás (WSS) Azure AD-alkalmazás-katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Symantec Web Security szolgáltatást**, válassza a **Symantec Web Security szolgáltatás** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Symantec Web Security szolgáltatás (WSS) az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Automatikus felhasználó-kiépítés konfigurálása a Symantec Web Security szolgáltatásban (WSS)

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein, amelyekkel felhasználókat és/vagy csoportokat hozhat létre, frissíthet és tilt le a Symantec Web Security szolgáltatásban (WSS) az Azure AD-ben a felhasználók és/vagy csoportok hozzárendelései alapján.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Symantec Web Security szolgáltatáshoz (WSS), a [Symantec Web Security szolgáltatás (WSS) egyszeri bejelentkezés oktatóanyagában](symantec-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Symantec Web Security szolgáltatáshoz (WSS) az Azure AD-ben:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Symantec Web Security szolgáltatás**elemet.

    ![A Symantec Web Security szolgáltatás (WSS) hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg a **bérlői URL-címben** és a **titkos jogkivonatban** korábban lekért **scim URL-címet** és **jogkivonat** -értékeket. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a Symantec Web Security szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Symantec Web Security szolgáltatás (WSS) fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba**esetén.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Save** (Mentés) gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Symantec Web Security szolgáltatással (WSS)** lehetőséget.

    ![A Symantec Web Security szolgáltatás (WSS) felhasználói hozzárendelései](media/symantec-web-security-service/usermapping.png)

9. Tekintse át az Azure AD-ről a Symantec Web Security szolgáltatásra (WSS) szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Symantec Web Security szolgáltatás (WSS) felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![A Symantec Web Security szolgáltatás (WSS) felhasználói hozzárendelései](media/symantec-web-security-service/userattribute.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Symantec Web Security szolgáltatáshoz**lehetőséget.

    ![A Symantec Web Security szolgáltatás (WSS) felhasználói hozzárendelései](media/symantec-web-security-service/groupmapping.png)

11. Tekintse át az Azure AD-ről a Symantec Web Security szolgáltatásra (WSS) szinkronizált csoport attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Symantec Web Security szolgáltatásban (WSS) lévő csoportok egyeztetésére használhatók frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![A Symantec Web Security szolgáltatás (WSS) felhasználói hozzárendelései](media/symantec-web-security-service/groupattribute.png)

12. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Symantec Web Security szolgáltatáshoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne a Symantec Web Security szolgáltatásba (WSS) kiépíteni a **Beállítások** szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, hogy [mennyi ideig tart a felhasználók kiépítése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik a Symantec Web Security szolgáltatásban (WSS). További információ: [a felhasználó kiépítési állapotának ellenõrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../app-provisioning/check-status-user-account-provisioning.md)során.

## <a name="additional-resources"></a>További háttéranyagok

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)
