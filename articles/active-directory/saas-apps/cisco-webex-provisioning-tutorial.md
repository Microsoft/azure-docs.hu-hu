---
title: 'Oktatóanyag: a Cisco WebEx konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus kiépítéséhez és üzembe helyezéséhez a Cisco WebEx.
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
ms.openlocfilehash: bc05e83ac6c7f0f7c5e9a571c1fa7397af858f44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180098"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Oktatóanyag: a Cisco WebEx konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Cisco WebEx és Azure Active Directory (Azure AD) az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat a Cisco WebEx.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Cisco WebEx-bérlő](https://www.webex.com/pricing/index.html).
* Felhasználói fiók a Cisco WebEx rendszergazdai engedélyekkel.

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco-WebEx hozzáadása a katalógusból

Mielőtt a Cisco WebEx-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez konfigurálja, hozzá kell adnia a Cisco WebEx az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**A Cisco WebEx az Azure AD alkalmazás-katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Cisco WebEx** kifejezést, válassza a **Cisco WebEx** az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Cisco WebEx az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Felhasználók kiosztása a Cisco WebEx

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában a rendszer csak azokat a felhasználókat és/vagy csoportokat szinkronizálja, akik az Azure AD-alkalmazáshoz lettek rendelve.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználói számára van szükség a Cisco WebEx elérésére. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a Cisco WebEx:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Fontos Tippek a felhasználók Cisco WebEx való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Cisco WebEx az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználók is hozzárendelhetők.

* Amikor felhasználót rendel a Cisco WebEx, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>A felhasználók automatikus üzembe helyezésének beállítása a Cisco WebEx

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein az Azure AD-ben felhasználói hozzárendelések alapján a Cisco WebEx-ben a felhasználók létrehozásához, frissítéséhez és letiltásához.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Cisco WebEx az Azure AD-ben:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Cisco WebEx** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Cisco WebEx** elemet.

    ![A Cisco WebEx hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    :::image type="content" source="common/provisioning.png" alt-text="Képernyőkép a Azure Portal egyik menüjéről. A kezelés területen ki van emelve a kiépítés." border="false":::

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    :::image type="content" source="common/provisioning-automatic.png" alt-text="Képernyőkép a kiépítési mód listájáról, amely automatikusan ki van emelve." border="false":::

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Cisco WebEx-fiókjának **bérlői URL-címét** és **titkos jogkivonatát** .

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/secrettoken1.png" alt-text="A rendszergazdai hitelesítő adatok szakasz képernyőképe. A bérlő U R L és titkos jogkivonat mezői kiemelve vannak, de üresek." border="false":::

6.  A **bérlői URL-cím** mezőben adjon meg egy értéket a formájában `https://api.ciscospark.com/v1/scim/[OrgId]` . A beszerzéshez `[OrgId]` Jelentkezzen be a [Cisco WebEx Control hub](https://admin.webex.com/login)webhelyre. Kattintson a szervezet nevére a bal alsó sarokban, és másolja az értéket a **szervezet azonosítójában**. 

    * A **titkos jogkivonat** értékének beszerzéséhez navigáljon erre az [URL-címre](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%2f%2flocalhost%253A3000%2fauth%2fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). A megjelenő WebEx bejelentkezési oldalon jelentkezzen be a szervezete teljes Cisco WebEx-rendszergazdai fiókjával. Megjelenik egy hibaüzenet, amely azt jelzi, hogy a hely nem érhető el, de ez normális.

        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test.png" alt-text="Képernyőkép egy hibaüzenetet megjelenítő weblapról. Az üzenet azt mondja, hogy a hely nem érhető el, és néhány hibaelhárítási tippet tartalmaz." border="false":::
 
    * Másolja a generált tulajdonosi jogkivonat értékét az URL-címről az alábbi kiemelt értékre. Ez a jogkivonat 365 napig érvényes.
        
        :::image type="content" source="./media/cisco-webex-provisioning-tutorial/test1.png" alt-text="Képernyőfelvétel: egy hosszú U R L. a cím egy része nem megfejthető, de Kiemelt és címkézett tulajdonosi jogkivonat." border="false":::

7. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak a Cisco WebEx. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Cisco WebEx-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Cisco WebEx** lehetőséget.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermapping.png" alt-text="Képernyőkép a Azure Portal leképezések szakaszáról. A név alatt szinkronizálja Azure Active Directory felhasználókat a CiscoSpark." border="false":::

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakaszban a Cisco WebEx. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Cisco WebEx felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    :::image type="content" source="./media/cisco-webex-provisioning-tutorial/usermappingattributes.png" alt-text="Képernyőkép az attribútum-hozzárendelésekről szakasz, amely a Azure Active Directory attribútumokat, a megfelelő CiscoSpark-attribútumokat és a megfelelő állapotot jeleníti meg." border="false":::

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Cisco WebEx, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Cisco WebEx szeretne kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Cisco WebEx végzett összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Cisco WebEx jelenleg a Cisco korai helyszíni tesztelési (EFT) fázisában található. További információért forduljon a [Cisco ügyfélszolgálatához](https://www.webex.co.in/support/support-overview.html). 
* A Cisco WebEx konfigurálásával kapcsolatos további információkért tekintse meg a Cisco dokumentációját [itt](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
