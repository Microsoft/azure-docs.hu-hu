---
title: 'Oktatóanyag: A Zscaler konfigurálása a felhasználók automatikus üzembe Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directory, hogy automatikusan kiépítsen és kiépítsen felhasználói fiókokat a Zscalerben.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 92553e9d598823ee1b812a4f07480dec380b2490
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739421"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Oktatóanyag: A Zscaler konfigurálása automatikus felhasználóátépítéshez

Ennek az oktatóanyagnak a célja, hogy bemutatja a Zscaler és az Azure Active Directory (Azure AD) által az Azure AD-nek a felhasználók és/vagy csoportok Zscaler számára való automatikus építésre és delagálására való konfigurálását.

> [!NOTE]
> Ez az oktatóanyag az Azure AD User Provisioning Service-t használó összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következővel:

* Egy Azure AD-bérlő.
* Egy Zscaler-bérlő.
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók Zscalerben.

> [!NOTE]
> Az Azure AD kiépítési integrációja a Zscaler SCIM API-ra támaszkodik, amely Zscaler-fejlesztők számára érhető el az Enterprise csomaggal rendelkező fiókokhoz.

## <a name="adding-zscaler-from-the-gallery"></a>Zscaler hozzáadása a katalógusból

Mielőtt a Zscalert automatikus felhasználóátépítésre konfigurálja az Azure AD-val, hozzá kell adni a Zscalert az Azure AD alkalmazáskatatárából a felügyelt SaaS-alkalmazások listájához.

**Ha Zscalert szeretne hozzáadni az Azure AD-alkalmazásgyűjteményből, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** bal oldali navigációs panelen kattintson a Azure Active Directory **ikonra.**

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás** lehetőséget.

    ![A Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a **párbeszédpanel tetején** található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler** parancsot, válassza a **Zscaler** lehetőséget az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Zscaler az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Felhasználók hozzárendelése a Zscalerhez

Azure Active Directory a "hozzárendelések" koncepciót használja annak meghatározására, hogy mely felhasználók kaphatnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználóátépítés kontextusában a rendszer csak az Azure AD-beli alkalmazáshoz "hozzárendelt" felhasználókat és/vagy csoportokat szinkronizálja.

Az automatikus felhasználóátépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Zscalerhez. A döntés után ezeket a felhasználókat és/vagy csoportokat az itt található utasításokat követve rendelheti hozzá a Zscalerhez:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Fontos tippek felhasználók Zscalerhez való hozzárendelésére

* Az automatikus felhasználóátépítési konfiguráció tesztelése érdekében ajánlott egyetlen Azure AD-felhasználót hozzárendelni a Zscalerhez. Később további felhasználók és/vagy csoportok is hozzárendelhetőek.

* Amikor felhasználót rendel a Zscalerhez, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az alapértelmezett hozzáférési **szerepkörű** felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Automatikus felhasználóátépítés konfigurálása a Zscalerben

Ez a szakasz végigvezeti az Azure AD kiépítési szolgáltatás zscalerbeli felhasználók és/vagy csoportok létrehozására, frissítésére és letiltására való konfigurálásához szükséges lépéseken az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.


> [!NOTE]
> Hozzon [létre egy tartományt](https://help.zscaler.com/) a Zscalerben támogatási jegy megnyitásával.

> [!TIP]
> Az SAML-alapú egyszeri bejelentkezést is engedélyezheti a Zscalerhez a [Zscaler](zscaler-tutorial.md)egyszeri bejelentkezési oktatóanyagának utasításait követve. Az egyszeri bejelentkezés az automatikus felhasználóátépítéstől függetlenül konfigurálható, bár ez a két funkció kiegészíti egymást.

> [!NOTE]
> Azt javasoljuk, hogy a felhasználók és csoportok kiépítésekor vagy a csoport-kiépítésükkor rendszeres időközönként indítsa újra a kiépítést, hogy a csoporttagságokat megfelelően frissítve legyen. Újraindítás esetén a szolgáltatásnak újra kell értékelnie az összes csoportot, és frissítenie kell a tagságokat. 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>A Zscaler automatikus felhasználóátépítésének konfigurálása az Azure AD-ban:

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com) és válassza a Vállalati **alkalmazások** lehetőséget, válassza a **Minden alkalmazás** lehetőséget, majd válassza a **Zscaler lehetőséget.**

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zscaler lehetőséget.**

    ![A Zscaler hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a Zscaler – Provisioning Enterprise Application (Vállalati alkalmazás kiépítése) oldalsávról, kiemelt Kiépítés lehetőséggel.](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a Kiépítés lapról, az Automatikus kiépítési mód beállítással.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. A **Rendszergazdai hitelesítő adatok szakaszban** adja  meg Zscaler-fiókjának **bérlői URL-címét** és titkos jogkivonatát a 6. lépésben leírtak szerint.

6. A **Bérlői URL-cím** és a Titkos jogkivonat beszerzéséhez keresse meg az **Administration > Authentication Settings** (Adminisztráció és hitelesítési beállítások) lehetőséget a Zscaler portál felhasználói felületén, és kattintson az **SAML** elemre a **Hitelesítési típus alatt.** 

    ![A Hitelesítési beállítások oldal képernyőképe.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Kattintson az **SAML konfigurálása elemre** a **Konfigurációs SAML-beállítások megnyitásához.**

    ![Képernyőkép az S A M L konfigurálása párbeszédpanelről, az Alap U R L és a Bearer Token szövegmezők megjelenítésével.](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Válassza **az Enable SCIM-Based Provisioning** (Kiépítés engedélyezése) lehetőséget az alap **URL-cím** és a bearer token **lekéréséhez,** majd mentse a beállításokat. Másolja az **alap URL-címet** a **bérlői URL-címre,** a **Bearer Tokent** pedig a titkos jogkivonatba a Azure Portal. 

7. Az 5. lépésben látható mezők  kiugrása után kattintson a Kapcsolat tesztelése elemre annak biztosításához, hogy az Azure AD kapcsolódhat a Zscalerhez. Ha a kapcsolat sikertelen, ellenőrizze, hogy a Zscaler-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Képernyőkép a Rendszergazdai hitelesítő adatok szakaszról, a Kapcsolat tesztelése lehetőséggel.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Az **Értesítési** e-mail-cím mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek a kiépítési hibákról szóló értesítéseket kell kapnia, és jelölje be az E-mail-értesítés küldése hiba **esetén jelölőnégyzetet.**

    ![Az Értesítési e-mail szövegmező képernyőképe.](./media/zscaler-provisioning-tutorial/notification.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések szakaszban** válassza a **Felhasználók szinkronizálása Azure Active Directory Zscalerbe lehetőséget.**

    ![Képernyőkép a Leképezések szakaszról, kiemelt Synchronize Azure Active Directory Users to Zscaler (Felhasználók szinkronizálása a Zscalerbe) lehetőséggel.](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD-ről a Zscalerre szinkronizált felhasználói attribútumokat az **Attribútumleképezés szakaszban.** Az Egyező  tulajdonságokként kiválasztott attribútumok a Zscaler felhasználói fiókjaihoz illeszkednek a frissítési műveletekhez. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

    ![Képernyőkép az Attribútumleképezések szakaszról hét leképezés megjelenítésével.](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. A **Leképezések szakaszban** válassza a Szinkronizálás Azure Active Directory **a Zscalerbe lehetőséget.**

    ![Képernyőkép a Leképezések szakaszról, kiemelt Szinkronizálás Azure Active Directory Zscaler-csoportokkal lehetőséggel.](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Tekintse át az Azure AD-ről a Zscalerre szinkronizált csoportattribútumokat az Attribútumleképezés **szakaszban.** Az Egyező  tulajdonságokként kiválasztott attribútumok a Zscaler-csoportoknak megfelelő frissítési műveletekhez használhatók. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

    ![Képernyőkép az Attribútumleképezések szakaszról három leképezés megjelenítésével.](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

15. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Zscaler számára, módosítsa a **Kiépítési** állapot beállítást **Be** állapotra a **Beállítások szakaszban.**

    ![Képernyőkép a Kiépítési állapot beállítás Be beállításáról.](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Határozza meg a Zscaler számára kiépítni kívánt felhasználókat és/vagy  csoportokat a Kívánt értékek kiválasztásával a Hatókör területen a **Beállítások szakaszban.**

    ![Képernyőkép a Hatókör beállításról, kiemelt Csak a hozzárendelt felhasználók és csoportok szinkronizálása lehetőséggel.](./media/zscaler-provisioning-tutorial/scoping.png)

17. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Képernyőkép a Zscaler – Provisioning Enterprise Application (Vállalati alkalmazás kiépítése) oldalsávról a Save (Mentés) lehetőséggel.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör szakaszában meghatározott összes felhasználó és/vagy csoport **kezdeti** **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint az ezt követő szinkronizálások, ami körülbelül 40 percenként történik, amíg az Azure AD kiépítési szolgáltatás fut. A Szinkronizálás  részletei szakaszban figyelheti az előrehaladást, és a kiépítési tevékenységről szóló jelentésre mutató hivatkozásokat követhet, amelyek az Azure AD kiépítési szolgáltatás által a Zscalerben végrehajtott összes műveletet ismertetik.

Az Azure AD kiépítési naplók olvasására vonatkozó további információkért lásd: Reporting on automatic user account provisioning (Jelentéskészítés a felhasználói fiókok automatikus [építésével kapcsolatban).](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
