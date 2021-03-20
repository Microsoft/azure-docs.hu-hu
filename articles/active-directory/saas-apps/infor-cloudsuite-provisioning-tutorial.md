---
title: 'Oktatóanyag: az infor-CloudSuite konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az infor CloudSuite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 8fdd2c8a326fbdc68d1aec65377f4c465c5ee4c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353901"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Oktatóanyag: az infor-CloudSuite konfigurálása a felhasználók automatikus kiépítési felállításához

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani az infor CloudSuite és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat az infor CloudSuite.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy infor CloudSuite-bérlő](https://www.infor.com/products/infor-os)
* Az infor CloudSuite rendszergazdai engedélyekkel rendelkező felhasználói fiók.

## <a name="assigning-users-to-infor-cloudsuite"></a>Felhasználók kiosztása az infor CloudSuite

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra és/vagy csoportokra van szükség az Azure AD-ban az infor-CloudSuite. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az infor CloudSuite az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Fontos Tippek a felhasználók infor-CloudSuite való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve az infor CloudSuite az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha a felhasználót az infor CloudSuite rendeli hozzá, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Az infor-CloudSuite beállítása a kiépítés számára

1. Jelentkezzen be az [infor CloudSuite felügyeleti konzolra](https://www.infor.com/customer-center). Kattintson a felhasználó ikonra, majd navigáljon a **felhasználók felügyeletéhez**.

    ![Infor CloudSuite felügyeleti konzol](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Kattintson a képernyő bal felső sarkában található menü ikonra. Kattintson a **kezelés** gombra.

    ![Infor CloudSuite-SCIM hozzáadása](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navigáljon a **scim-fiókokhoz**.

    ![Infor CloudSuite SCIM-fiók](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Vegyen fel egy rendszergazda felhasználót a plusz ikonra kattintva. Adjon meg egy **scim-jelszót** , és írja be ugyanazt a jelszót a **Jelszó megerősítése** alatt. A jelszó mentéséhez kattintson a mappa ikonra. Ekkor megjelenik a rendszergazda felhasználó számára létrehozott **felhasználói azonosító** .

    ![Infor CloudSuite rendszergazdai felhasználó](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite jelszava](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Képernyőkép az infor CloudSuite felügyeleti konzolról, amely a Kiemelt táblázat sorait jeleníti meg. Ez a sor egy felhasználói azonosítót, jelszót és egy időbélyeget tartalmaz." border="false":::

5. A tulajdonosi jogkivonat létrehozásához másolja a **felhasználói azonosítót** és a **scim jelszavát**. Illessze be őket a Jegyzettömbbe + + kettősponttal elválasztva. A karakterlánc értékének kódolásához navigáljon a **Plugins > MIME-eszközök > Basic64 kódolása**. 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Képernyőkép egy jegyzettömb + + dokumentumról. A plugins menüben a MIME-eszközök ki vannak emelve. A MIME-eszközök menüben ki van emelve a Base64 kódolás." border="false":::

3.  Másolja a tulajdonosi jogkivonatot. Ez az érték a Azure Portalban az infor CloudSuite alkalmazás létesítés lapjának titkos jogkivonat mezőjében lesz megadva.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Infor-CloudSuite hozzáadása a katalógusból

Az Azure AD-vel való automatikus felhasználó-kiépítés előtt az Azure ad-beli infor-CloudSuite hozzáadásához hozzá kell adnia a felügyelt SaaS-alkalmazások listájához tartozó infor-CloudSuite.

**Az Azure AD-CloudSuite az alábbi lépéseket követve adhat hozzá infor-alkalmazásokat:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **infor CloudSuite**, válassza az **infor CloudSuite** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Infor-CloudSuite az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Az automatikus felhasználó-kiépítés beállítása az infor CloudSuite 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy CloudSuite alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést az infor-CloudSuite, az [infor CloudSuite egyszeri bejelentkezés oktatóanyagában](./infor-cloud-suite-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

> [!NOTE]
> Ha többet szeretne megtudni az infor CloudSuite SCIM-végpontról, olvassa el [ezt a témakört](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>A felhasználó automatikus kiépítés beállítása az Azure AD-beli infor-CloudSuite:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **infor CloudSuite** elemet.

    ![Az CloudSuite hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` meg a **bérlői URL-címet**. Adja meg a **titkos jogkivonatban** korábban lekért tulajdonosi jogkivonat értékét. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni az infor CloudSuite. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy az infor CloudSuite-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása az infor CloudSuite** lehetőséget.

    ![Az infor CloudSuite felhasználói leképezései](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD és az infor CloudSuite között szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használhatók az infor CloudSuite felhasználói fiókjainak megfelelően. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Az infor CloudSuite felhasználói attribútumai](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása az infor CloudSuite** lehetőséget.

    ![Az infor CloudSuite csoport leképezései](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ből szinkronizált CloudSuite az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez tartozó CloudSuite-csoportokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Az infor CloudSuite csoport attribútumai](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás az infor CloudSuite való engedélyezéséhez módosítsa a **kiépítési állapotot** a  **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket az infor-CloudSuite szeretne kiépíteni, ehhez válassza a **Beállítások** szakaszban lévő kívánt értékeket a **hatókörben** .

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységek jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által az infor CloudSuite végzett összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)