---
title: 'Oktatóanyag: A Elium konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Elium.
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
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a4ddcf27869ea7484f98329d14d01bfad83af219
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709535"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Oktatóanyag: Elium konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja a Elium és a Azure Active Directory (Azure AD) a felhasználók vagy csoportok automatikus kiépítésére és kiépítésére a Elium.

> [!NOTE]
> Ez az oktatóanyag egy, az Azure AD-felhasználó kiépítési szolgáltatására épülő összekötőt ismertet. A szolgáltatás működéséről és működéséről, valamint a gyakori kérdésekről a következő témakörben talál további információt: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory használatával](../manage-apps/user-provisioning.md).
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az előzetes verzióban elérhető Azure-funkciók általános használati feltételeiért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)feltételeit.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy Elium-bérlő](https://www.elium.com/pricing/)
* Felhasználói fiók a Elium-ben rendszergazdai engedélyekkel

## <a name="assigning-users-to-elium"></a>Felhasználók kiosztása a Elium

Az Azure AD a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak azok a felhasználók és csoportok lesznek szinkronizálva, amelyek az Azure AD-alkalmazáshoz vannak rendelve.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználói és csoportjai férhetnek hozzá a Elium. Ezután rendelje hozzá ezeket a felhasználókat és csoportokat a Elium-hez a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című témakör lépéseit követve.

## <a name="important-tips-for-assigning-users-to-elium"></a>Fontos Tippek a felhasználók Elium való hozzárendeléséhez 

Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen hozzá a Elium-hez az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. További felhasználók és csoportok később is hozzárendelhetők.

Amikor Elium rendel hozzá egy felhasználóhoz, ki kell választania egy érvényes, alkalmazásspecifikus szerepkört (ha vannak ilyenek) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-elium-for-provisioning"></a>Elium beállítása a kiépítés számára

Az Azure AD-vel való automatikus Elium konfigurálása előtt engedélyeznie kell a rendszer számára a tartományok közötti Identitáskezelés-felügyelet (SCIM) létesítését a Elium-on. Kövesse az alábbi lépéseket:

1. Jelentkezzen be a Elium-ba, és lépjen a **saját profil** > **Beállítások menüpontra**.

    ![Beállítások menüelem a Elium](media/Elium-provisioning-tutorial/setting.png)

1. A bal alsó sarokban, a **speciális**alatt válassza a **Biztonság**elemet.

    ![Biztonsági hivatkozás a Elium](media/Elium-provisioning-tutorial/security.png)

1. Másolja a **bérlői URL-címet** és a **titkos jogkivonat** értékeit. Ezeket az értékeket később, a Elium alkalmazás **üzembe** helyezés lapjának megfelelő mezőiben fogja használni a Azure Portal.

    ![Bérlői URL-cím és titkos jogkivonat mezői a Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Elium hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Elium konfigurálásához az Azure AD Elium is hozzá kell adnia a felügyelt szolgáltatott szoftveres (SaaS) alkalmazások listájához. Kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

    ![Azure Active Directory menüelem](common/select-azuread.png)

1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

     ![Azure AD Enterprise-alkalmazások panel](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza a panel tetején található **új alkalmazás** lehetőséget.

    ![Új alkalmazás hivatkozása](common/add-new-app.png)

1. A keresőmezőbe írja be a **Elium**kifejezést, válassza az **Elium** elemet az eredmények listájában, majd válassza a **Hozzáadás** lehetőséget az alkalmazás hozzáadásához.

    ![Katalógus – keresőmező](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Automatikus felhasználó-kiépítés beállítása a Elium

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és csoportos hozzárendeléseken alapuló felhasználókat és csoportokat hozzon létre, frissítsen és tiltsa le az Elium-ben.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az egyszeri bejelentkezést a Elium Security Assertion Markup Language (SAML) alapján, az [Elium egyszeri bejelentkezési oktatóanyag](Elium-tutorial.md)utasításait követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés lehetőségtől függetlenül is konfigurálhatja, bár a két szolgáltatás kiegészíti egymást.

Az Azure AD-beli Elium automatikus felhasználó-kiépítés konfigurálásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.

    ![Azure AD Enterprise-alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **Elium**lehetőséget.

    ![Alkalmazások listája a vállalati alkalmazások panelen](common/all-applications.png)

1. Válassza ki a kiépítés lapot.

    ![Kiépítés lap a vállalati alkalmazások panelen](common/provisioning.png)

1. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Automatikus beállítás a kiépítési mód esetében](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban írja be a következőt: **\<tenantURL @ no__t-3/scim/v2** a **bérlői URL-cím** mezőben. (A **tenantURL** a Elium felügyeleti konzolról korábban beolvasott érték.) Írja be a Elium **titkos jogkivonat** értékét is a **titkos jogkivonat** mezőbe. Végül kattintson a **kapcsolat tesztelése** elemre annak ellenőrzéséhez, hogy az Azure ad tud-e csatlakozni a Elium. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Elium-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![A bérlői URL-cím és a titkos jogkivonat mezői a rendszergazdai hitelesítő adatokban](common/provisioning-testconnection-tenanturltoken.png)

1. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, aki megkapja a kiépítési hibákra vonatkozó értesítéseket. Ezután jelölje be az **e-mailes értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő e-mail](common/provisioning-notification-email.png)

1. Kattintson a **Save** (Mentés) gombra.

1. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Elium**lehetőséget.

    ![Az Azure AD-felhasználók Elium való leképezésére szolgáló hivatkozás szinkronizálása](media/Elium-provisioning-tutorial/usermapping.png)

1. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelések** szakasz Elium. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Elium felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    ![Attribútumok hozzárendelése az Azure AD és a Elium között](media/Elium-provisioning-tutorial/userattribute.png)

1. A hatóköri szűrők konfigurálásához kövesse a hatókör- [szűrő oktatóanyagának](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

1. Az Azure AD-kiépítési szolgáltatás Elium való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot beállítása be](common/provisioning-toggle-on.png)

1. Adja meg a Elium kiépíteni kívánt felhasználókat és csoportokat a **Beállítások** szakasz **hatókör** legördülő listájában a kívánt értékek kiválasztásával.

    ![Kiépítés hatóköre lista](common/provisioning-scope.png)

1. Ha készen áll a létesítésre, válassza a **Mentés**lehetőséget.

    ![Mentés gomb a kiépítési konfigurációhoz](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálását. Ez a kezdeti szinkronizálási folyamat hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ a kiépítés szükséges időpontjáról: meddig tart a [felhasználók kiépítése?](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat. A kiépítési tevékenység jelentés az Azure AD-kiépítési szolgáltatás által a Elium-on végrehajtott összes műveletet ismerteti. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../manage-apps/check-status-user-account-provisioning.md)során.

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókok üzembe](../manage-apps/configure-automatic-user-provisioning-portal.md)helyezésének kezelése.
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
