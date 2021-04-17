---
title: 'Oktatóanyag: A Dropbox for Business konfigurálása a felhasználók automatikus üzembe Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja úgy a Azure Active Directory, hogy automatikusan kiépítse és megszenné a felhasználói fiókokat a Dropbox vállalati verzióban.
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
ms.openlocfilehash: 4e718ef30d029995c49efe36f19a6efcf7de0616
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589601"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Oktatóanyag: A Dropbox vállalati verzió konfigurálása automatikus felhasználóátépítéshez

Az oktatóanyag célja, hogy bemutatja a Dropbox for Business and Azure Active Directory (Azure AD) által az Azure AD-nek a felhasználók és/vagy csoportok a Dropbox for Business szolgáltatásba való automatikus építésére és de-kiépítésére való konfigurálására vonatkozó lépéseket.

> [!IMPORTANT]
> A jövőben a Microsoft és a Dropbox ki fogja elavultni a régi Dropbox-integrációt. Ezt eredetileg 2021.04. 04.00-ra tervezték, de határozatlan időre elhalasztották. A szolgáltatáskimaradás elkerülése érdekében azonban javasoljuk, hogy migráljon az új SCIM 2.0 Dropbox-integrációra, amely támogatja a csoportokat. Az új Dropbox-integrációra való migrálhoz adja hozzá és konfigurálja a Dropbox egy új példányát a kiépítéshez az Azure AD-bérlőben az alábbi lépésekkel. Miután konfigurálta az új Dropbox-integrációt, a kiépítési ütközések elkerülése érdekében tiltsa le a kiépítést a régi Dropbox-integráción. Az új Dropbox-integrációra való migrálás részletes lépéseiért lásd: Frissítés a legújabb [Vállalati Dropbox-alkalmazásra az Azure AD használatával.](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector)

> [!NOTE]
> Ez az oktatóanyag az Azure AD User Provisioning Service-t használó összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek valamelyikével:

* Egy Azure AD-bérlő
* [Egy Dropbox for Business-bérlő](https://www.dropbox.com/business/pricing)
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók a Dropbox Vállalati verziójában.

## <a name="add-dropbox-for-business-from-the-gallery"></a>A Dropbox For Business hozzáadása a katalógusból

Mielőtt konfigurálja a Dropbox for Businesst az Azure AD automatikus felhasználóátépítéséhez, hozzá kell adni a Dropbox for Businesst az Azure AD alkalmazáskatatárából a felügyelt SaaS-alkalmazások listájához.

**Ha a Dropbox for Businesst az Azure AD alkalmazásgyűjteményből szeretne hozzáadni, hajtsa végre a következő lépéseket:**

1. A bal **[Azure Portal](https://portal.azure.com)** panelen válassza a Azure Active Directory **lehetőséget.**

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Válassza a **Vállalati alkalmazások lehetőséget,** majd válassza a **Minden alkalmazás lehetőséget.**

    ![A Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza  az Új alkalmazás gombot a panel tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Dropbox for Business (Dropbox vállalati** verzió) lehetőséget, válassza a **Dropbox for Business (Dropbox vállalati** verzió) lehetőséget az eredménypanelen, majd kattintson a Hozzáadás gombra az alkalmazás hozzáadásához. 

    ![A Dropbox for Business az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Felhasználók hozzárendelése a Dropbox vállalati verzióhoz

Azure Active Directory a hozzárendelések koncepciót használja annak meghatározására, hogy mely felhasználók kapják meg a hozzáférést a kiválasztott alkalmazásokhoz.  Az automatikus felhasználóátépítés kontextusában csak azok a felhasználók és/vagy csoportok szinkronizálódnak, akik hozzá vannak rendelve egy alkalmazáshoz az Azure AD-ban.

Az automatikus felhasználóátépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Dropbox for Business szolgáltatáshoz. A döntés után az itt található utasításokat követve hozzárendelheti ezeket a felhasználókat és/vagy csoportokat a Dropbox for Businesshez:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Fontos tippek a felhasználók a Dropbox vállalati verzióhoz való hozzárendelésére

* Az automatikus felhasználóátépítési konfiguráció tesztelése érdekében ajánlott egyetlen Azure AD-felhasználót hozzárendelni a Dropbox for Businesshöz. Később további felhasználók és/vagy csoportok is hozzárendelhetőek.

* Amikor felhasználót rendel a Dropbox Vállalati verzióhoz, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az alapértelmezett hozzáférési **szerepkörű** felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Automatikus felhasználóátépítés konfigurálása a Dropbox for Business szolgáltatásban 

Ez a szakasz végigvezeti azon lépéseken, amelyek segítségével konfigurálhatja az Azure AD kiépítési szolgáltatást felhasználók és/vagy csoportok létrehozására, frissítésére és letiltására a Dropbox vállalati verzióban felhasználói és/vagy csoport-hozzárendelések alapján az Azure AD-ban.

> [!TIP]
> Az SAML-alapú egyszeri bejelentkezést is engedélyezheti a Dropbox for Businesshez a Dropbox for Business egyszeri bejelentkezési oktatóanyagának [utasításait követve.](dropboxforbusiness-tutorial.md) Az egyszeri bejelentkezés az automatikus felhasználóátépítéstől függetlenül konfigurálható, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Automatikus felhasználóátépítés konfigurálása a Dropbox vállalati verzióhoz az Azure AD-ban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Dropbox vállalati verzió lehetőséget.**

    ![A Dropbox Vállalati verzió hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a Beállítások kezelése lehetőségről a Kiépítés lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a Kiépítési mód legördülő listáról, az Automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés** elemre. Megnyílik egy Dropbox Vállalati verzió bejelentkezési párbeszédpanelje egy új böngészőablakban.

    ![Kiépítés ](common/provisioning-oauth.png)

6. A Bejelentkezés a Dropbox vállalati verzióba az **Azure AD-val** való összekapcsolás érdekében párbeszédpanelen jelentkezzen be a Dropbox vállalati verzió bérlőjébe, és ellenőrizze az identitását.

    ![A Dropbox vállalati verzióba való bejelentkezése](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Az 5. és a 6. lépés elvégzése után kattintson a Kapcsolat tesztelése elemre annak biztosításához, hogy az Azure AD kapcsolódhat a Dropbox for Business szolgáltatáshoz.  Ha a kapcsolat nem sikerül, ellenőrizze, hogy a Dropbox for Business-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-oauth.png)

8. Az **Értesítési** e-mail-cím mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek a kiépítési hibákról szóló értesítéseket kell kapnia, és jelölje be az E-mail-értesítés küldése hiba **esetén jelölőnégyzetet.**

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **Leképezések szakaszban** válassza a **Felhasználók szinkronizálása Azure Active Directory Dropboxba lehetőséget.**

    ![Dropbox-felhasználóleképezések](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Tekintse át az Azure AD-ból a Dropboxba szinkronizált felhasználói attribútumokat az **Attribútumleképezés szakaszban.** Az Egyező tulajdonságokként **kiválasztott** attribútumok a Dropboxban található felhasználói fiókoknak megfelelő frissítési műveletekhez használhatók. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

    ![Dropbox felhasználói attribútumok](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. A **Leképezések szakaszban** válassza a Szinkronizálás a Azure Active Directory **a Dropboxba lehetőséget.**

    ![Dropbox-csoportleképezések](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Tekintse át az Azure AD-ről a Dropboxba szinkronizált csoportattribútumokat az **Attribútumleképezés szakaszban.** Az Egyező  tulajdonságokként kiválasztott attribútumok a Dropboxban található csoportokkal egyeznek meg a frissítési műveletekhez. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

    ![Dropbox-csoportattribútumok](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

15. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Dropboxhoz, módosítsa a **Kiépítési állapot** beállítást **Be** állapotra a **Beállítások szakaszban.**

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

16. Határozza meg a Dropboxban kiépítni kívánt felhasználókat és/vagy  csoportokat a Kívánt értékek kiválasztásával a Hatókör területen a **Beállítások szakaszban.**

    ![Átadási hatókör](common/provisioning-scope.png)

17. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz Hatókör szakaszában meghatározott összes felhasználó és/vagy csoport **kezdeti** **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint az ezt követő szinkronizálások, ami körülbelül 40 percenként történik, amíg az Azure AD kiépítési szolgáltatás fut. A Synchronization **Details** (Szinkronizálás részletei) szakaszban nyomon követheti a folyamat előrehaladását, és követheti a kiépítési tevékenységről szóló jelentésre mutató hivatkozásokat, amelyek az Azure AD kiépítési szolgáltatása által a Dropboxban végrehajtott összes műveletet ismertetik.

Az Azure AD kiépítési naplók olvasására vonatkozó további információkért lásd: Reporting on automatic user account provisioning (Jelentéskészítés a felhasználói fiókok automatikus [építésével kapcsolatban).](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Összekötőkre vonatkozó korlátozások
 
* A Dropbox nem támogatja a meghívott felhasználók felfüggesztését. Ha egy meghívott felhasználót felfüggesztenek, az adott felhasználó törlődik.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

