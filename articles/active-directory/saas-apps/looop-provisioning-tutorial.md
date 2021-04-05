---
title: 'Oktatóanyag: a Looop konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Looop.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 528003ac482da6f254bf437321c70c389d23844b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835033"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Looop konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Looop és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Looop.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Looop-bérlő](https://www.looop.co/pricing/)
* Rendszergazdai jogosultságokkal rendelkező Looop felhasználói fiókja.

## <a name="assign-users-to-looop"></a>Felhasználók Looop rendelése

Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Looop való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Looop az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Fontos Tippek a felhasználók Looop való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Looop-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Looop rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-looop-for-provisioning"></a>Looop beállítása a kiépítés számára

Az Azure AD-vel való automatikus Looop konfigurálása előtt le kell kérnie néhány kiépítési információt a Looop-ből.

1. Jelentkezzen be a [Looop felügyeleti konzolra](https://app.looop.co/#/login) , és válassza a **fiók** lehetőséget. A **Fiókbeállítások** területen válassza a **hitelesítés** lehetőséget.

    :::image type="content" source="media/looop-provisioning-tutorial/admin.png" alt-text="Képernyőkép a Looop felügyeleti konzolról. A fiók lap ki van emelve és meg van nyitva. A Fiókbeállítások területen a hitelesítés ki van emelve." border="false":::

2. Hozzon létre egy új tokent a **scim-integráció** **elemre kattintva.**

    :::image type="content" source="media/looop-provisioning-tutorial/resettoken.png" alt-text="Képernyőkép a Looop felügyeleti konzolján található oldal C I M-integrációs szakaszáról. A jogkivonat alaphelyzetbe állítása gomb ki van emelve." border="false":::

3. Másolja a **scim-végpontot** és a **tokent**. Ezek az értékek a **bérlői URL-cím** és a **titkos jogkivonat** mezőiben lesznek megadva a Looop alkalmazás kiépítés lapján a Azure Portal. 

    ![Looop-létrehozási jogkivonat](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Looop hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Looop konfigurálásához hozzá kell adnia a Looop az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Looop** kifejezést, majd az eredmények panelen kattintson a **Looop** elemre. 

    ![Looop az eredmények listájában](common/search-new-app.png)

5. Válassza ki a **Looop (regisztráció)** gombot, amely átirányítja a Looop bejelentkezési oldalára. 

    ![Looop OIDC hozzáadása](media/looop-provisioning-tutorial/signup.png)

6. Mivel a Looop egy OpenIDConnect-alkalmazás, a Microsoft munkahelyi fiókjával való bejelentkezéshez válassza a Looop lehetőséget.

    ![Looop OIDC-bejelentkezés](media/looop-provisioning-tutorial/msftlogin.png)

7. Sikeres hitelesítés után fogadja el a jóváhagyást kérő lapot. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a Looop-fiókjába.

    ![Looop OIDc-beleegyezett](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Automatikus felhasználó-kiépítés beállítása a Looop 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Looop alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Looop az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Looop** lehetőséget.

    ![Az Looop hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://<organisation_domain>.looop.co/scim/v2` meg a **bérlői URL-címet**. Például: `https://demo.looop.co/scim/v2`. Adja meg a beolvasott és a korábban mentett értéket a **titkos token** Looop. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Looop. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Looop-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Looop** lehetőséget.

    ![Looop felhasználói leképezések](media/looop-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Looop. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Looop felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |emails[type eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |externalId|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Looop: 2.0: felhasználó: körzet|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Looop: 2.0: felhasználó: custom_1|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Looop: 2.0: felhasználó: custom_2|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Looop: 2.0: felhasználó: custom_3|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Looop: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Looop: 2.0: felhasználó: employee_id|Sztring|
   |urn: IETF: params: scim: sémák: kiterjesztés: Looop: 2.0: felhasználó: hely|Sztring|
   |urn: IETF: params: scim: sémák: kiterjesztés: Looop: 2.0: felhasználó: pozíció|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Looop: 2.0: felhasználó: startAt|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a meta Networks-összekötőhöz** lehetőséget.

    ![Looop csoport-hozzárendelések](media/looop-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ről a meta Networks-összekötőre szinkronizált csoportosítási attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a meta Networks connectorban lévő csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    |Attribútum|Típus|Szűréshez támogatott|
    |---|---|---|
    |displayName|Sztring|&check;|
    |tagok|Referencia|
    |externalId|Sztring|


10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Az Azure AD-kiépítési szolgáltatás Looop való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a Looop kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Looop-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)


