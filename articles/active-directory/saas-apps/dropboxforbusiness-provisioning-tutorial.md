---
title: 'Oktatóanyag: a Dropbox for Business konfigurálása a felhasználók automatikus üzembe helyezéséhez a Azure Active Directory segítségével | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus kiépítéséhez és üzembe helyezéséhez a Dropbox for Business szolgáltatásban.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 04d17e17ef11696efd52f04ea83639f2a9b81fea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96938749"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Oktatóanyag: a Dropbox for Business konfigurálása a felhasználók automatikus üzembe helyezéséhez

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Dropboxban a vállalati és Azure Active Directory (Azure AD) számára az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Dropbox vállalatoknak.

> [!IMPORTANT]
> A Microsoft és a Dropbox a régi Dropbox-integráció érvényes 04/01/2021-es időpontját fogja érvényteleníteni. A szolgáltatás megszakadásának elkerülése érdekében ajánlott áttelepíteni az új Dropbox-integrációt, amely támogatja a csoportokat. Az új Dropbox-integrációra való áttéréshez adja hozzá és konfigurálja a Dropbox új példányát az Azure AD-bérlőben az alábbi lépésekkel való üzembe helyezéshez. Miután konfigurálta az új Dropbox-integrációt, tiltsa le a kiépítés beállítást a régi Dropbox-integráción, hogy elkerülje a kiépítési ütközéseket. Az új Dropbox-integrációra való áttérés részletes lépéseiért lásd: [frissítés a legújabb Dropbox for Business alkalmazásra az Azure ad használatával](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector).

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [A Dropbox vállalati bérlő](https://www.dropbox.com/business/pricing)
* Felhasználói fiók a Dropbox vállalatnál rendszergazdai engedélyekkel.

## <a name="add-dropbox-for-business-from-the-gallery"></a>A Dropbox for Business hozzáadása a katalógusból

Az Azure AD-vel való automatikus felhasználó-kiépítés előtt hozzá kell adnia a Dropbox for businesst az Azure AD Application Galleryből a felügyelt SaaS-alkalmazások listájára.

**Ha az Azure AD alkalmazás-katalógusból szeretné hozzáadni a Dropbox for businesst, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Dropbox for Business** kifejezést, válassza a **Dropbox vállalat számára** lehetőséget az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![A Dropbox for Business az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Felhasználók kiosztása a Dropbox vállalati verzióhoz

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD-beli felhasználók és/vagy csoportok hozzáférhessenek-e a Dropbox vállalati verzióhoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Dropbox for Business szolgáltatáshoz az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Fontos Tippek a felhasználóknak a Dropbox vállalati verzióhoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Dropbox for Business szolgáltatáshoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a Dropbox for Business szolgáltatáshoz, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Automatikus felhasználó-kiépítés beállítása a Dropbox vállalati verzióra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozzon létre, frissítsen és tiltsa le a Dropboxban lévő felhasználókat és/vagy csoportokat.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Dropbox vállalati verziójában, a [Dropbox for Business egyszeri bejelentkezés oktatóanyagában](dropboxforbusiness-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Automatikus felhasználó-kiépítés beállítása a Dropbox for Business szolgáltatáshoz az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Dropbox vállalatoknak** elemet.

    ![A Dropbox for Business hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés** elemre. Megnyílik egy új böngészőablakban a Dropbox for Business bejelentkezési párbeszédpanelje.

    ![Kiépítés ](common/provisioning-oauth.png)

6. A **vállalati dropboxba való bejelentkezéshez az Azure ad-vel való kapcsolódáshoz** jelentkezzen be a Dropbox for Business-bérlőbe, és ellenőrizze az identitását.

    ![Dropbox vállalati bejelentkezéshez](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Az 5. és a 6. lépés elvégzése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a vállalati dropboxhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Dropbox for Business-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-oauth.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók a dropboxhoz** lehetőséget.

    ![Dropbox felhasználói leképezések](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Tekintse át az Azure AD-ből a Dropboxba szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. A **megfelelő** tulajdonságokként kiválasztott attribútumok a Dropbox felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Dropbox felhasználói attribútumai](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Dropbox-hoz** lehetőséget.

    ![Dropbox-csoport leképezései](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Tekintse át az Azure AD-ből a Dropboxba szinkronizált csoportosítási attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a dropboxban lévő csoportok egyeztetésére használhatók a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Dropbox-csoport attribútumai](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

15. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Dropboxhoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

16. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni a Dropbox számára a **Settings (beállítások** ) szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

17. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik a dropboxban.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai
 
* A Dropbox nem támogatja a meghívott felhasználók felfüggesztését. Ha egy meghívott felhasználó fel van függesztve, a felhasználó törölve lesz.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

