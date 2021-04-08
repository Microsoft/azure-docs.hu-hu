---
title: 'Oktatóanyag: a Figma automatikus felhasználó-kiépítés konfigurálása a Azure Active Directoryrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Figma.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: ec509cc04db87602b3e29230253d9b363a5e30d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353918"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Figma konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Figma és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Figma.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy Figma-bérlő](https://www.figma.com/pricing/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Figma-ben.

## <a name="assign-users-to-figma"></a>Felhasználók kiosztása a Figma.
Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Figma való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Figma az alábbi utasításokat követve:
 
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Fontos Tippek a felhasználók Figma való hozzárendeléséhez

 * Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Figma-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Figma rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-figma-for-provisioning"></a>Figma beállítása a kiépítés számára

Az Azure AD-vel való automatikus Figma konfigurálása előtt le kell kérnie néhány kiépítési információt a Figma-ből.

1. Jelentkezzen be a [Figma felügyeleti konzolra](https://www.Figma.com/). Kattintson a bérlő melletti fogaskerék ikonra.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Képernyőkép a Figma felügyeleti konzolról. A D scim-teszt nevű bérlő látható. A bérlő mellett egy fogaskerék ikon van kiemelve." border="false":::

2. Lépjen az **általános > frissítési napló beállításai elemre**.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="A Figma felügyeleti konzol általános lapjának képernyőképe. A bejelentkezés és kiépítés alatt a frissítési napló beállításai elem ki van emelve." border="false":::

3. Másolja a **bérlő azonosítóját**. Ezt az értéket fogja használni a SCIM-végpont URL-címének megadásához **a Figma** alkalmazás létesítés lapján a Azure Portal.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Képernyőkép a Figma felügyeleti konzoljának S L S O szakaszáról. A bérlői azonosító címkéje és a másolást kiemelő szomszédos hivatkozás." border="false":::

4. Görgessen le, és kattintson az **API-jogkivonat előállítása** elemre.

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Képernyőkép a Figma felügyeleti konzoljának S C I M kiépítési szakaszáról. A P I token létrehozásakor megjelenő hivatkozás ki van emelve." border="false":::

5. Másolja az  **API-jogkivonat** értékét. Ez az érték a Figma alkalmazás üzembe helyezés lapjának **titkos jogkivonat** mezőjében jelenik meg a Azure Portal. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Képernyőkép a Figma felügyeleti konzolján található oldalról. A P I token kiépítés alatt a jogkivonat helyőrzője ki van emelve." border="false":::

## <a name="add-figma-from-the-gallery"></a>Figma hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Figma konfigurálásához hozzá kell adnia a Figma az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Figma** kifejezést, válassza az **Figma** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Figma az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Automatikus felhasználó-kiépítés beállítása a Figma 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Figma alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Figma számára, az [Figma egyszeri bejelentkezés oktatóanyagában](figma-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Figma az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Figma** lehetőséget.

    ![Az Figma hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban `https://www.figma.com/scim/v2/<TenantID>` adja meg a **bérlői URL-címet** , ahol a **TenantID** az az érték, amelyet korábban a Figma adott vissza. Adja meg az **API-jogkivonat** értékét a **titkos jogkivonatban**. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Figma. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Figma-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Figma** lehetőséget.

    ![Figma felhasználói leképezések](media/Figma-provisioning-tutorial/figma05.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Figma. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Figma felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Figma felhasználói attribútumai](media/Figma-provisioning-tutorial/figma06.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás Figma való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a Figma kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Figma-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)