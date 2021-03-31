---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés a Azure Active Directory-mel való automatikus konfigurálása | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat.
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
ms.openlocfilehash: d8c3f64e5cb5269bfe7e555615f874ac3443c6eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357827"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Oktatóanyag: prémium konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa a Bonus és Azure Active Directory (Azure AD) szolgáltatásban elvégzendő lépéseket, hogy az Azure AD konfigurálja a felhasználókat és/vagy csoportokat a bónuszok automatikus kiépítéséhez és kiépítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Azure AD-bérlő
* [Prémium bérlő](https://bonus.ly/pricing)
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók

> [!NOTE]
> Az Azure AD kiépítési integrációja a prémium rendelkezésre állású [API](https://konghq.com/solutions/gateway/)-ra támaszkodik, amely a prémium fejlesztők számára érhető el.

## <a name="adding-bonusly-from-the-gallery"></a>Bónuszok hozzáadása a katalógusból

Az Azure AD-vel való automatikus felhasználói kiépítés előtt bónuszokat kell hozzáadnia az Azure AD-alkalmazás-katalógusból a felügyelt SaaS-alkalmazások listájára.

**Az Azure AD-alkalmazás-katalógusban található bónuszok hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Bonus**, válassza a **bónuszok** az eredmények panelen lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Bónuszok az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Felhasználók kiosztása bónuszhoz

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában a rendszer csak azokat a felhasználókat és/vagy csoportokat szinkronizálja, akik az Azure AD-alkalmazáshoz lettek rendelve. 

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználóknak és/vagy csoportoknak milyen jogosultságokkal kell rendelkezniük a bónuszok eléréséhez. A döntés megkezdése után a felhasználókat és/vagy csoportokat a következő utasítások követésével lehet bónuszhoz rendelni:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Fontos Tippek a felhasználók bónuszok hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen kiosztva az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha egy felhasználót a bónuszhoz rendel hozzá, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Automatikus felhasználó-kiépítés beállítása a bónuszok megadásához

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozzon létre, frissítsen és tiltsa le a felhasználókat és/vagy csoportokat.

> [!TIP]
> Azt is megteheti, hogy az SAML-alapú egyszeri bejelentkezést is lehetővé teszi a prémium szintű egyszeri [bejelentkezésre vonatkozó oktatóanyagban](bonus-tutorial.md)foglalt utasítások alapján. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Azure AD-ben való Többlettámogatáshoz:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **bónuszok** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **bónuszok** lehetőséget.

    ![Az alkalmazások listájában szereplő prémium hivatkozás](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningTab.png" alt-text="Képernyőkép a Bonus-kiépítés lapról. A kezelés területen ki van emelve a kiépítés." border="false":::

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png" alt-text="Képernyőfelvétel a kiépítési mód listájáról, amely automatikusan kijelölt és Kiemelt állapotú." border="false":::

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Bonus-fiók **titkos jogkivonatát** a 6. lépésben leírtak szerint.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/secrettoken.png" alt-text="A rendszergazdai hitelesítő adatok szakasz képernyőképe. A titkos jogkivonat mező üres, de a mező ki van emelve." border="false":::

6. A prémium fiók **titkos jogkivonata** a következő helyen található: **Admin > vállalati > integrációk**. Ha a **kódot szeretné használni** , kattintson az **API-> új API-hozzáférési jogkivonat létrehozása** lehetőségre új titkos jogkivonat létrehozásához.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png" alt-text="Képernyőfelvétel a bónuszok menüjéről. A rendszergazda területen a vállalat ki van emelve. A vállalat területen az integrációk kiemelten jelennek meg." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonsulyRestApi.png" alt-text="Képernyőkép: Ha a bónusz oldalon a kód szakaszát szeretné megtekinteni, a P kiemelve." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/CreateToken.png" alt-text="Képernyőfelvétel a bónuszok oldaláról. A szolgáltatások lap meg van nyitva. A P I hozzáférési jogkivonatok alatt hozzon létre egy új P I hozzáférési tokent." border="false":::

7. A következő képernyőn írja be a hozzáférési jogkivonat nevét a megadott szövegmezőbe, majd nyomja le az API- **kulcs létrehozása** elemet. Az új hozzáférési jogkivonat néhány másodpercig megjelenik egy előugró ablakban.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token01.png" alt-text="Képernyőkép a bónusz oldal új hozzáférési token oldaláról. A címke nélküli mező a saját tokent tartalmazza, a P I billentyű létrehozása gomb pedig ki van emelve." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token02.png" alt-text="Képernyőfelvétel a bónuszok oldaláról. Megjelenik egy értesítés, amely megjeleníti a létrehozott új hozzáférési jogkivonatot, majd egy inmegfejthető tokent." border="false":::

8. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad tud csatlakozni a bónuszokhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a prémium fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/TestConnection.png" alt-text="Képernyőfelvétel a Azure Portal rendszergazdai hitelesítő adatairól című szakaszáról. A szöveges kapcsolatok gomb ki van emelve." border="false":::

9. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/EmailNotification.png" alt-text="Képernyőfelvétel: üres értesítő e-mail mező. Egy olyan lehetőség látható, amely e-mailben értesítést küld, ha hiba történik." border="false":::

10. Kattintson a **Mentés** gombra.

11. A **leképezések** szakaszban válassza a **Azure Active Directory-felhasználók szinkronizálása bónuszra** lehetőséget.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserMappings.png" alt-text="Képernyőfelvétel a leképezésekről szakasz. A név alatt szinkronizálja Azure Active Directory felhasználókat a bónuszok kiemelésével." border="false":::

12. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok használatával a rendszer a frissítési műveletekhez bónuszban lévő felhasználói fiókokat is egyezteti. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserAttributeMapping.png" alt-text="Képernyőkép az attribútum-hozzárendelések lapról. A táblázat felsorolja Azure Active Directory attribútumokat, a megfelelő bónusz attribútumokat és a megfelelő állapotot." border="false":::

13. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

14. Ahhoz, hogy az Azure AD-kiépítési szolgáltatás bónuszok **után is engedélyezve** legyen, módosítsa a **kiépítési állapotot** a **Beállítások** szakaszban.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningStatus.png" alt-text="Képernyőfelvétel a beállítások szakaszról. A kiépítési állapot váltógomb ki értékre van állítva." border="false":::

15. Adja meg azokat a felhasználókat és/vagy csoportokat, akiket ki szeretne próbálni a bónuszok kiválasztásához. Ehhez válassza a **Settings (beállítások** ) szakaszban a kívánt értékeket a **hatókörben** .

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ScopeSync.png" alt-text="Képernyőfelvétel a hatókör lista megjelenítéséhez. Csak a hozzárendelt felhasználók és csoportok szinkronizálása van kiválasztva a mezőben." border="false":::

16. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/SaveProvisioning.png" alt-text="Képernyőkép a Bonus-kiépítés oldaláról, a Save (Mentés) gombbal kiemelve." border="false":::

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységek jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a bónuszok után végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png