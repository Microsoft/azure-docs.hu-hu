---
title: 'Oktatóanyag: a Keeper Password Manager & Digital Vault konfigurálása a felhasználók automatikus kiosztásához Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a & digitális tárolóban.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 2670dc0cb56805a2afa966bee1d2aa52b6c8e46a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358983"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Oktatóanyag: a Keeper Password Manager & digitális tároló beállítása a felhasználók automatikus üzembe helyezéséhez

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Keeper Password Managerben & Digital Vaultban és Azure Active Directory (Azure AD), hogy az Azure AD-t úgy konfigurálja, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat a jelszó-kezelő & Digitális tárolóban.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [A Keeper Password Manager & A digitális tár bérlője](https://keepersecurity.com/pricing.html?t=e)
* Egy felhasználói fiók a Keeper Password Managerben & a digitális tárolóban rendszergazdai engedélyekkel.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>A Keeper Password Manager & Digital Vault hozzáadása a katalógusból

Mielőtt az Azure AD-vel automatikus felhasználó-kiosztásra konfigurálja a Keeper Password Manager & digitális tárolót, az Azure AD-alkalmazás-katalógusban hozzá kell adnia a Keeper Password Manager & Digital Vaultot a felügyelt SaaS-alkalmazások listájához.

**A Keeper Password Manager & digitális tárolójának Azure AD-alkalmazás-katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Keeper Password manager & digitális** tároló kifejezést, válassza a **Keeper Password Manager & Digitális** tároló elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![A Keeper Password Manager & Digital Vault az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Felhasználók kiosztása a Keeper Password Manager & Digital Vaulthoz

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus kiosztásának konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra és/vagy csoportokra van szükség az Azure AD-ben a Keeper Password Manager & digitális tárolóhoz való hozzáféréshez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Keeper Password Manager & Digital Vaulthoz az alábbi utasítások követésével:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Fontos Tippek a felhasználók a Keeper Password Manager & Digital Vaulthoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Keeper Password Manager & Digital Vaulthoz a felhasználók automatikus kiépítési konfigurációjának teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha egy felhasználót a Keeper Password Manager & Digital Vaulthoz rendel, akkor a hozzárendelés párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Az automatikus felhasználó-kiépítés beállítása a Keeper Password Manager & Digital Vaultba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy a felhasználók és/vagy csoportok felhasználóinak és/vagy csoportoknak az Azure AD-ban való létrehozásához, frissítéséhez és letiltásához a Keeper Password Managerben & a digitális tárolóban lévő felhasználókat és/vagy csoportokat.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Keeper Password Manager & Digital Vault számára, a [Keeper Password manager & Digital Vault egyszeri bejelentkezés oktatóanyagában](keeperpasswordmanager-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>A felhasználók automatikus üzembe helyezésének beállítása a Keeper Password Manager & digitális tárolóban az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Keeper Password Manager & Digital Vault** elemet.

    ![A Keeper Password Manager & Digital Vault-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Keeper Password Manager **bérlői URL-címét** és **titkos jogkivonatát** & digitális tároló fiókját a 6. lépésben leírtak szerint.

6. Jelentkezzen be a [Keeper felügyeleti konzolra](https://keepersecurity.com/console/#login). Kattintson a **rendszergazda** elemre, válasszon ki egy meglévő csomópontot, vagy hozzon létre újat. Navigáljon a **kiépítés** lapra, és válassza a **metódus hozzáadása** lehetőséget.

    ![A Keeper felügyeleti konzolja](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Válassza **a scim (rendszer a tartományok közötti identitások kezeléséhez** lehetőséget.

    ![SCIM hozzáadása](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Kattintson a létesítési **Jogkivonat létrehozása** lehetőségre.

    ![Megtartó létrehozása végpont](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Másolja az URL- **cím** és a **jogkivonat** értékét, és illessze be őket a **bérlői URL-cím** és a **titkos tokenbe** az Azure ad-ben. Kattintson a **Save (Mentés** ) gombra a kiépítési beállítás befejezéséhez a Keeper-ben.

    ![Megtartó létrehozása token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak a Keeper Password Manager & Digital vaulthoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Keeper Password Manager & a digitális tár fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Keeper Password Manager & Digital vaultban** lehetőséget.

    ![Felhasználó-hozzárendelések megőrzése](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található, a "Password Manager & Digital Vault" című témakörben. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Keeper Password Manager felhasználói fiókjainak a frissítési műveletek során való egyeztetésére szolgálnak & Digital vaultban. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Megtartó felhasználói attribútumok](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Keeper Password Manager & Digital vaultban** lehetőséget.

    ![Megtartó csoport leképezései](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Tekintse át az Azure AD-ből szinkronizált csoport-attribútumokat az **attribútum-hozzárendelési** szakaszban található & digitális tárolóban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Keeper Password Managerben lévő csoportoknak felelnek meg a frissítési műveletek során a & Digital vaultban. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Megtartó csoport attribútumai](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

15. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Keeper Password Manager & Digital Vault számára, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

16. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára szeretné kiépíteni a Keeper Password Manager & Digital Vaultot, ha a **Settings (beállítások** ) szakaszban szeretné kiválasztani a kívánt értékeket a **hatókörben** .

    ![Átadási hatókör](common/provisioning-scope.png)

17. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik a Keeper Password Manager & Digital vaultban.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Keeper Password Manager & Digital Vaultnak **e-maileket** és **felhasználóneveket** kell megadnia ugyanazzal a forrással, mivel bármelyik attribútum frissítései módosítják a másik értéket.
* A Keeper Password Manager & Digital Vault nem támogatja a felhasználók törlését, csak a tiltást. A letiltott felhasználók a Keeper felügyeleti konzol felhasználói felületén zárolva jelennek meg.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

