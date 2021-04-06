---
title: 'Oktatóanyag: az Oracle Fusion ERP konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Oracle Fusion ERP számára.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: da6e1a8ba31f8f4991bde4803191598a015a68b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358439"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Oktatóanyag: az Oracle Fusion ERP konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa az Oracle Fusion ERP és Azure Active Directory (Azure AD) által elvégzendő lépéseket az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikus kiosztását és kiépítését az Oracle Fusion ERP-re végezze.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure előnézetekhez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* Egy [Oracle Fusion ERP-bérlő](https://www.oracle.com/applications/erp/).
* Egy felhasználói fiók az Oracle Fusion ERP-ben rendszergazdai engedélyekkel.

## <a name="assign-users-to-oracle-fusion-erp"></a>Felhasználók kiosztása az Oracle Fusion ERP-hez 
Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználói és/vagy csoportjai férhetnek hozzá az Oracle Fusion ERP-hez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az Oracle Fusion ERP-hez az alábbi utasításokat követve:
 
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Fontos Tippek a felhasználók Oracle Fusion ERP-hez való hozzárendeléséhez 

 * Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve az Oracle Fusion ERP-hez az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel az Oracle Fusion ERP-hez, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Az Oracle Fusion ERP beállítása az üzembe helyezéshez

Mielőtt beállítja az Oracle Fusion ERP-t az Azure AD-vel való automatikus felhasználó-kiépítés során, engedélyeznie kell az SCIM-létesítést az Oracle Fusion ERP-ben.

1. Jelentkezzen be az [Oracle Fusion ERP felügyeleti konzolba](https://cloud.oracle.com/sign-in)

2. Kattintson a bal felső sarokban található navigátorra. Az **eszközök** területen válassza a **biztonsági konzol** lehetőséget.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/login.png" alt-text="Képernyőkép az Oracle Fusion E R P felügyeleti konzoljának navigátor oldaláról. Az eszközök és a biztonsági konzol ki van emelve." border="false":::

3. Navigáljon a **felhasználókhoz**.
    
    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user.png" alt-text="Képernyőkép az Oracle Fusion E R P felügyeleti konzolján található panelről. A Users (felhasználók) elemek ki vannak emelve." border="false":::

4. Mentse a rendszergazdai felhasználói fiók felhasználónevét és jelszavát, amelyet az Oracle Fusion ERP felügyeleti konzolba való bejelentkezéshez fog használni. Ezeket az értékeket az Oracle Fusion ERP-alkalmazás üzembe helyezés lapjának **rendszergazdai Felhasználónév** és **jelszó** mezőiben kell megadni a Azure Portal.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Oracle Fusion ERP hozzáadása a katalógusból

Ha az Oracle Fusion ERP-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez szeretné konfigurálni, az Azure AD-alkalmazás-katalógusból hozzá kell adnia az Oracle Fusion ERP-t a felügyelt SaaS-alkalmazások listájához.

**Az Oracle Fusion ERP az Azure AD Application Galleryből való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Oracle Fusion ERP** kifejezést, majd az eredmények panelen válassza az **Oracle Fusion ERP** elemet.

    ![Oracle Fusion ERP az eredmények listájában](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Az automatikus felhasználó-kiépítés beállítása az Oracle Fusion ERP-be 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein az Azure ad-ben felhasználói és/vagy csoportos hozzárendelések alapján az Oracle Fusion ERP-ben a felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést az Oracle Fusion ERP-hez az [Oracle Fusion ERP egyszeri bejelentkezés oktatóanyagában](oracle-fusion-erp-tutorial.md)ismertetett utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást.

> [!NOTE]
> Az Oracle Fusion ERP SCIM-végpontjának megismeréséhez tekintse meg az [Oracle Applications Cloud általános szolgáltatásainak Rest APIét](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)ismertető témakört.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Gyutacs az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Oracle Fusion ERP** elemet.

    ![Az Oracle Fusion ERP hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` meg a **bérlői URL-címet**. Adja meg a rendszergazda **felhasználónevét** és jelszavát a korábban lekért rendszergazdai Felhasználónév és **jelszó** mezőkben. Kattintson az Azure AD és az Oracle Fusion ERP közötti **kapcsolat tesztelése** elemre. 

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/admin.png" alt-text="A rendszergazdai hitelesítő adatok szakasz képernyőképe. Az U R L, a rendszergazdai Felhasználónév és a rendszergazdai jelszó megadására vonatkozó tesztelési kapcsolatok gomb és mezők láthatók." border="false":::

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása az Oracle Fusion ERP**-be lehetőséget.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png" alt-text="Képernyőfelvétel a leképezésekről szakasz. A név területen szinkronizálja Azure Active Directory felhasználókat az Oracle Fusion E R P-vel." border="false":::

9. Tekintse át az Azure AD-ból az Oracle Fusion ERP-be szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Oracle Fusion ERP felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    :::image type="content" source="media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png" alt-text="Képernyőkép az attribútum-hozzárendelések lapról. A táblázat felsorolja Azure Active Directory és az Oracle Fusion E R P attribútumait, valamint a megfeleltetések sorrendjét." border="false":::

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása Oracle Fusion ERP-** re lehetőséget.

    ![Oracle Fusion ERP csoportos leképezések](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ből az Oracle Fusion ERP-be szinkronizált csoportok attribútumait az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Oracle Fusion ERP-ben lévő csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Oracle Fusion ERP csoport attribútumai](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Oracle Fusion ERP-hez készült Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne az Oracle Fusion ERP számára kiépíteni, ha a **Settings (beállítások** ) szakaszban szeretné kijelölni a kívánt értékeket a **hatókörben** .

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által az Oracle Fusion ERP-ben végrehajtott összes műveletet ismertetik.

    Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* Az Oracle Fusion ERP csak a SCIM-végpont alapszintű hitelesítését támogatja.
* Az Oracle Fusion ERP nem támogatja a csoportok üzembe helyezését.
* Az Oracle Fusion ERP szerepkörei az Azure AD-ben csoportokba vannak rendelve. Ahhoz, hogy szerepköröket rendeljen a felhasználókhoz az Azure AD-ből származó Oracle Fusion ERP-ben, hozzá kell rendelnie a felhasználókat az Oracle Fusion ERP szerepkörei után elnevezett kívánt Azure AD-csoportokhoz.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
