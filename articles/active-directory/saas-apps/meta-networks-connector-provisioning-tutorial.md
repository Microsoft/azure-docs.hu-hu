---
title: 'Oktatóanyag: a meta Networks-összekötő konfigurálása a felhasználók automatikus kiépítéséhez a Azure Active Directory segítségével | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus kiépítéséhez és üzembe helyezéséhez a meta Networks-összekötőhöz.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: b6a8f192cd26639431cc9fcb6b43e1bc5e8e2843
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353629"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Oktatóanyag: a meta Networks-összekötő konfigurálása automatikus felhasználók kiépítéséhez

Ennek az oktatóanyagnak a célja, hogy bemutassa a meta Networks Connector és a Azure Active Directory (Azure AD) szolgáltatásban elvégzendő lépéseket, hogy az Azure AD konfigurálja a felhasználókat és/vagy csoportokat a meta Networks Connectorhoz való automatikus kiépítéséhez és kiépítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy meta Networks Connector-bérlő](https://www.metanetworks.com/)
* Egy felhasználói fiók a meta Networks-összekötőben rendszergazdai engedélyekkel.

## <a name="assigning-users-to-meta-networks-connector"></a>Felhasználók kiosztása a meta Networks Connectorhoz

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus kiépítésének konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra és/vagy csoportokra van szükség az Azure AD-ben a meta Networks Connector eléréséhez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a meta Networks-összekötőhöz az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Fontos Tippek a felhasználók meta Networks-összekötőhöz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a meta Networks-összekötőhöz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználót a meta Networks Connectorhoz rendel, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-meta-networks-connector-for-provisioning"></a>A meta Networks-összekötő beállítása a kiépítéshez

1. Jelentkezzen be a [meta Networks Connector felügyeleti konzolra](https://login.metanetworks.com/login/) a szervezet neve alapján. Navigáljon az **adminisztráció > API-kulcsok** elemre.

    ![Meta Networks Connector felügyeleti konzol](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Kattintson a képernyő jobb felső részén található pluszjelre, és hozzon létre egy új **API-kulcsot**.

    ![Meta Networks-összekötő plusz ikon](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Adja meg az **API-kulcs nevét** és az **API-kulcs leírását**.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/keyname.png" alt-text="Képernyőkép a meta Networks Connector felügyeleti konzolról, amely a P I kulcs nevét és az Azure A D és a p i kulcsának a P i Key Description értékeit emeli ki." border="false":::

4.  A **csoportok** és **felhasználók** **írási** jogosultságának bekapcsolása.

    ![Meta Networks-összekötő jogosultságai](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Kattintson a **Hozzáadás** gombra. Másolja a **titkos kulcsot** , és mentse el, mivel ez az egyetlen időpontig megtekinthető. Ez az érték a Azure Portalban található meta Networks Connector alkalmazás létesítés lapjának titkos jogkivonat mezőjében lesz megadva.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/token.png" alt-text="Képernyőkép egy olyan ablakról, amely közli a felhasználókkal, hogy a P I kulcs hozzá lett adva. A titkos mező nem megfejthető értéket tartalmaz, és ki van emelve." border="false":::

6.  Vegyen fel egy identitásszolgáltató úgy, hogy az **adminisztráció > beállítások > identitásszolgáltató > új létrehozása** elemre navigál.

    ![Meta Networks Connector identitásszolgáltató hozzáadása](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  A **identitásszolgáltató-konfiguráció** lapon megadhatja **a identitásszolgáltató** konfigurációját, és kiválaszthat egy **ikont**.

    ![Meta Networks-összekötő identitásszolgáltató neve](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Meta Networks-összekötő identitásszolgáltató ikonja](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  A **scim konfigurálása** területen válassza ki az előző lépésekben létrehozott API-kulcsot. Kattintson a **Save (Mentés**) gombra.

    ![Meta Networks Connector – SCIM konfigurálása](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Navigáljon az **adminisztráció > beállítások > identitásszolgáltató fülre**. Kattintson az előző lépésekben létrehozott identitásszolgáltató-konfiguráció nevére a **identitásszolgáltató-azonosító** megtekintéséhez. Ezt az **azonosítót** a rendszer hozzáadja a **bérlői URL-cím** végéhez, miközben a Azure Portalban található meta Networks Connector alkalmazás létesítés lapján a **bérlői URL-cím** mezőben szereplő érték beírásával szerepel.

    ![Meta Networks-összekötő identitásszolgáltató azonosítója](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Meta Networks-összekötő hozzáadása a katalógusból

Mielőtt a meta Networks Connectort konfigurálja az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, hozzá kell adnia a meta Networks-összekötőt az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**A meta Networks-összekötő Azure AD-alkalmazás-katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **meta Networks Connector** kifejezést, válassza a **meta Networks Connector** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Meta Networks-összekötő az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Automatikus felhasználó-kiépítés konfigurálása a meta Networks-összekötőhöz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozzon létre, frissítsen és tiltsa le a meta Networks összekötőben lévő felhasználókat és/vagy csoportokat.

> [!TIP]
> Emellett engedélyezheti az SAML-alapú egyszeri bejelentkezést is a meta Networks-összekötőhöz, a [meta Networks Connector egyszeri bejelentkezés oktatóanyagában](./metanetworksconnector-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a meta Networks-összekötőhöz az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **meta Networks Connector** elemet.

    ![A meta Networks Connector hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://api.metanetworks.com/v1/scim/<IdP ID>` meg a **bérlői URL-címet**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a meta Networks-összekötőhöz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a meta Networks Connector-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a meta Networks-összekötőhöz** lehetőséget.

    ![Meta Networks-összekötő felhasználói leképezései](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ról a meta Networks-összekötőre szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a meta Networks összekötő felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![A meta Networks Connector felhasználói attribútumai](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a meta Networks-összekötőhöz** lehetőséget.

    ![Meta Networks-összekötő csoportok leképezései](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ről a meta Networks-összekötőre szinkronizált csoportosítási attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a meta Networks connectorban lévő csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Meta Networks-összekötő csoport attribútumai](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a meta Networks-összekötőhöz, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a meta Networks-összekötőhöz szeretne kiépíteni, ehhez válassza a **Beállítások** szakaszban lévő kívánt értékeket a **hatókörben** .

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a meta Networks-összekötőn végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)