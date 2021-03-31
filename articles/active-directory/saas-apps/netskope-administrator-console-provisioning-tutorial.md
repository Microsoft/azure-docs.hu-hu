---
title: 'Oktatóanyag: az Netskope felhasználói hitelesítés konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus kiépítéséhez és üzembe helyezéséhez a felhasználói hitelesítés Netskope.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: Zhchia
ms.openlocfilehash: 46766a7439185714648572f3f1b9d51ef96abba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357470"
---
# <a name="tutorial-configure-netskope-user-authentication-for-automatic-user-provisioning"></a>Oktatóanyag: Netskope felhasználói hitelesítés konfigurálása automatikus felhasználó-kiépítés esetén

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Netskope felhasználói hitelesítésben és Azure Active Directory (Azure AD) az Azure AD konfigurálásához a felhasználók és/vagy csoportok automatikus kiépítéséhez és kiépítéséhez a felhasználói hitelesítés Netskope.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy Netskope felhasználói hitelesítési bérlő](https://www.netskope.com/)
* Felhasználói fiók a Netskope felhasználói hitelesítéshez rendszergazdai engedélyekkel.

## <a name="assigning-users-to-netskope-user-authentication"></a>Felhasználók kiosztása Netskope felhasználói hitelesítéshez

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok hozzáférhessenek a Netskope felhasználói hitelesítéshez. A döntés után ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a felhasználói hitelesítés Netskope az itt leírt útmutatás alapján:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-netskope-user-authentication"></a>Fontos Tippek a felhasználók Netskope felhasználói hitelesítéshez való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve Netskope felhasználói hitelesítéshez a felhasználó automatikus kiépítési konfigurációjának teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a Netskope felhasználói hitelesítéshez, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-netskope-user-authentication-for-provisioning"></a>Netskope felhasználói hitelesítés beállítása a kiépítés számára

1. Jelentkezzen be a [Netskope felhasználói hitelesítés felügyeleti konzolra](https://netskope.goskope.com/). Navigáljon a **kezdőlap > beállítások** elemre.

    ![Netskope felhasználói hitelesítés felügyeleti konzolja](media/netskope-administrator-console-provisioning-tutorial/admin.png)

2.  Navigáljon az **eszközökhöz**. Az **eszközök** menüben navigáljon a **Directory-eszközök > scim-integráció** elemre.

    ![Netskope felhasználói hitelesítési eszközök](media/netskope-administrator-console-provisioning-tutorial/tools.png)

    ![Netskope felhasználói hitelesítés SCIM hozzáadása](media/netskope-administrator-console-provisioning-tutorial/directory.png)

3. Görgessen le, és kattintson a **jogkivonat hozzáadása** gombra. Az **OAuth-ügyfél nevének hozzáadása** párbeszédpanelen adja meg az **ügyfél nevét** , és kattintson a **Save (Mentés** ) gombra.

    ![Netskope-felhasználói hitelesítés – jogkivonat hozzáadása](media/netskope-administrator-console-provisioning-tutorial/add.png)

    ![Netskope-felhasználó hitelesítési ügyfelének neve](media/netskope-administrator-console-provisioning-tutorial/clientname.png)

3.  Másolja a **scim-kiszolgáló URL-címét** és a **tokent**. Ezek az értékek a bérlői URL-cím és a titkos jogkivonat mezőiben lesznek megadva a Netskope felhasználói hitelesítési alkalmazás létesítés lapján a Azure Portal.

    ![Netskope felhasználói hitelesítés létrehozása token](media/netskope-administrator-console-provisioning-tutorial/token.png)

## <a name="add-netskope-user-authentication-from-the-gallery"></a>Netskope-felhasználó hitelesítésének hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Netskope felhasználói hitelesítés konfigurálása előtt hozzá kell adnia a Netskope felhasználói hitelesítést az Azure AD-alkalmazás-katalógusból a felügyelt SaaS-alkalmazások listájára.

**Az Azure AD Netskope való felhasználói hitelesítés hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Netskope felhasználói hitelesítés** elemet, válassza az **Netskope felhasználói hitelesítés** lehetőséget az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Netskope felhasználói hitelesítés az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-netskope-user-authentication"></a>Automatikus felhasználó-kiépítés beállítása Netskope felhasználói hitelesítéshez 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, amelyekkel felhasználókat és/vagy csoportokat hozhat létre, frissíthet és tilthatja le Netskope felhasználói hitelesítésre az Azure AD-ben a felhasználók és/vagy csoportok hozzárendelései alapján.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Netskope felhasználói hitelesítéshez az [Netskope felhasználói hitelesítés egyszeri bejelentkezési oktatóanyagában](./netskope-cloud-security-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció kiegészíti egymást.

> [!NOTE]
> A Netskope felhasználói hitelesítés SCIM végpontjának megismeréséhez tekintse meg [ezt](https://docs.google.com/document/d/1n9P_TL98_kd1sx5PAvZL2HS6MQAqkQqd-OSkWAAU6ck/edit#heading=h.prxq74iwdpon)a témakört.

### <a name="to-configure-automatic-user-provisioning-for-netskope-user-authentication-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Netskope felhasználói hitelesítéshez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Netskope felhasználói hitelesítés** lehetőséget.

    ![Az Netskope felhasználói hitelesítés hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **scim-kiszolgáló URL-címét** , amely korábban a **bérlői URL**-címben lett lekérve. Adja meg a **titkos jogkivonatban** korábban lekért **jogkivonat** -értéket. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a Netskope felhasználói hitelesítéshez. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Netskope felhasználói hitelesítési fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók Netskope a felhasználói hitelesítés** lehetőséget.

    ![Netskope felhasználói hitelesítés felhasználói leképezései](media/netskope-administrator-console-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban Netskope felhasználói hitelesítéshez. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Netskope felhasználói fiókjainak a frissítési műveletekhez való egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Netskope felhasználói hitelesítés felhasználói attribútumai](media/netskope-administrator-console-provisioning-tutorial/userattributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget a felhasználói hitelesítés Netskope**.

    ![Netskope felhasználói hitelesítési csoport leképezései](media/netskope-administrator-console-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ből szinkronizált Netskope az attribútumok **leképezése** szakaszban található felhasználói hitelesítéshez. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Netskope felhasználói hitelesítésben lévő csoportok egyeztetésére szolgálnak a frissítési műveletek során. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Netskope felhasználói hitelesítési csoport attribútumai](media/netskope-administrator-console-provisioning-tutorial/groupattributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Netskope felhasználói hitelesítéshez, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a Netskope szeretné kiépíteni a felhasználói hitelesítéshez. Ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Netskope felhasználói hitelesítés során végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)