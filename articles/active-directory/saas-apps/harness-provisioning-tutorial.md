---
title: 'Oktatóanyag: a felhasználói kiépítés automatikus kihasználásának beállítása a Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 13ae960f5d259314f00f8f09b2999a36c0919bc5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94353713"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Oktatóanyag: a felhasználói kiépítés automatikus kihasználásának beállítása

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Azure Active Directory (Azure AD) a felhasználók vagy csoportok automatikus kiépítésére és megszüntetésére.

> [!NOTE]
> Ez a cikk az Azure AD-felhasználó kiépítési szolgáltatására épülő összekötőt ismerteti. A szolgáltatással kapcsolatos fontos információkért és a gyakori kérdésekre adott válaszokért lásd: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory használatával](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy hám bérlő](https://harness.io/pricing/)
* *Rendszergazdai* engedélyekkel rendelkező felhasználói fiók

## <a name="assign-users-to-harness"></a>Felhasználók társításának kiosztása

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD-beli felhasználók vagy csoportok számára szükséges-e a kiaknázás. Ezeket a felhasználókat vagy csoportokat a felhasználók vagy csoportok [vállalati alkalmazásokhoz rendeléséhez](../manage-apps/assign-user-or-group-access-portal.md)tartozó utasítások követésével rendelheti hozzá.

## <a name="important-tips-for-assigning-users-to-harness"></a>Fontos Tippek a felhasználók kiosztásához

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen hozzá az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. További felhasználók vagy csoportok később is hozzárendelhetők.

* Ha a felhasználó hozzárendelését rendeli hozzá, a **hozzárendelés** párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az *alapértelmezett hozzáférési* szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-harness-for-provisioning"></a>Kihasználás beállítása a kiépítés számára

1. Jelentkezzen be a [hám felügyeleti konzolba](https://app.harness.io/#/login), majd lépjen a **folyamatos biztonsági**  >  **hozzáférés kezelése szolgáltatáshoz**.

    ![Hám felügyeleti konzol](media/harness-provisioning-tutorial/admin.png)

1. Válassza az **API-kulcsok** elemet.

    ![Hám API-kulcsok hivatkozása](media/harness-provisioning-tutorial/apikeys.png)

1. Válassza az **API-kulcs hozzáadása** lehetőséget. 

    ![Hám API-kulcs hozzáadása hivatkozás](media/harness-provisioning-tutorial/addkey.png)

1. Az **API-kulcs hozzáadása** panelen tegye a következőket:

    ![Az API-kulcs hozzáadása panel](media/harness-provisioning-tutorial/title.png)
   
   a. A **név** mezőben adja meg a kulcs nevét.  
   b. A legördülő listából válassza ki **a kívánt beállítást** . 
   
1. Válassza a **Küldés** lehetőséget.

1. Másolja a **kulcsot** az oktatóanyag későbbi használatához.

    ![Hám létrehozása jogkivonat](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Hám hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus felhasználó-kiépítés előtt az Azure AD-alkalmazás-katalógusban fel kell vennie a hevedert a felügyelt SaaS-alkalmazások listájára.

1. A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.

    ![A "Azure Active Directory" gomb](common/select-azuread.png)

1. Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![A "minden alkalmazás" hivatkozás](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az "új alkalmazás" gomb](common/add-new-app.png)

1. A keresőmezőbe írja be a **hám** kifejezést, válassza ki a **hám** elemet az eredmények listájában, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Hám az eredmények listájában](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>A felhasználók automatikus kiépítési állapotának beállítása 

Ez a szakasz végigvezeti az Azure AD üzembe helyezési szolgáltatás konfigurálásának lépésein az Azure AD-ben felhasználói vagy csoportos hozzárendeléseken alapuló felhasználók vagy csoportok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy az SAML-alapú egyszeri bejelentkezést is engedélyezi a hám számára a [hám egyszeri bejelentkezés oktatóanyagának](./harness-tutorial.md)utasításait követve. Az egyszeri bejelentkezést az automatikus felhasználó-létesítéstől függetlenül is konfigurálhatja, bár ez a két szolgáltatás kiegészíti egymást.

> [!NOTE]
> Ha többet szeretne megtudni a hám SCIM-végpontról, tekintse meg a hám [API-kulcsok](https://docs.harness.io/article/smloyragsm-api-keys) című cikket.

Az Azure AD-ben az automatikus felhasználó-kiépítés konfigurálásához tegye a következőket:

1. A [Azure Portal](https://portal.azure.com)válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **hám** elemet.

    ![A hám hivatkozás az alkalmazások listájában](common/all-applications.png)

1. Válassza a **kiépítés** lehetőséget.

    ![A kiépítés gomb](common/provisioning.png)

1. A **létesítési mód** legördülő listában válassza az **automatikus** lehetőséget.

    ![A "kiépítési mód" legördülő lista](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok** területen tegye a következőket:

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. A **bérlői URL-cím** mezőbe írja be a értéket **`https://app.harness.io/gateway/api/scim/account/<your_harness_account_ID>`** . A hám-fiók AZONOSÍTÓját a böngészőben megjelenő URL-címről szerezheti be, amikor bejelentkezett a rendszerbe.
   b. A **titkos jogkivonat** mezőbe írja be az scim-hitelesítési jogkivonat értékét, amelyet a (z) "a kiépítés a kiépítés beállítása" szakasz 6. lépésében mentett.  
   c. Válassza a **kapcsolat tesztelése** lehetőséget annak biztosításához, hogy az Azure ad képes legyen csatlakozni a hám-hez. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a fiókja *rendszergazdai* jogosultságokkal rendelkezik, majd próbálkozzon újra.

1. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, majd jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Az "értesítő E-mail" mező](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **leképezések** területen válassza **a szinkronizálás Azure Active Directory felhasználók** számára lehetőséget.

    ![A "Azure Active Directory felhasználók szinkronizálása a következővel" hivatkozás használata](media/harness-provisioning-tutorial/usermappings.png)

1. Az **attribútum-hozzárendelések** területen tekintse át az Azure ad-ből a hám-re szinkronizált felhasználói attribútumokat. Az *egyezőként* kiválasztott attribútumok a frissítési műveletek során a biztonsági öv felhasználói fiókjainak egyeztetésére szolgálnak. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    ![Felhasználói "attribútum-hozzárendelések" panel](media/harness-provisioning-tutorial/userattributes.png)

1. A **leképezések** területen válassza **a Azure Active Directory csoportok szinkronizálása** lehetőséget.

    ![A "Azure Active Directory csoportok szinkronizálása a kihasználható" hivatkozásra](media/harness-provisioning-tutorial/groupmappings.png)

1. Az **attribútumok hozzárendelése** területen tekintse át az Azure ad-ből a hám-re szinkronizált csoportok attribútumait. Az *egyeztetési* tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használt hám csoportjaival egyeznek meg. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    !["Attribútum-hozzárendelések" ablaktábla](media/harness-provisioning-tutorial/groupattributes.png)

1. A hatóköri szűrők konfigurálásához tekintse meg [az attribútum-alapú alkalmazások kiépítés hatókör-szűrőkkel](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)című témakört.

1. A **Beállítások** alatt engedélyezze az Azure ad kiépítési szolgáltatást a hám számára, és állítsa be a **kiépítési állapotot** **a be** értékre.

    ![A kiépítési állapot kapcsolója "bekapcsolva" értékre vált](common/provisioning-toggle-on.png)

1. A **Beállítások** területen a **hatókör** legördülő listában válassza ki, hogyan szeretné szinkronizálni a kiépíteni kívánt felhasználókat vagy csoportokat.

    ![Átadási hatókör](common/provisioning-scope.png)

1. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![A kiépítési mentés gomb](common/provisioning-configuration-save.png)

Ez a művelet elindítja a kiépített felhasználók vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbiek. A szinkronizálás körülbelül 40 percenként történik, feltéve, hogy az Azure AD kiépítési szolgáltatás fut. A folyamat figyeléséhez lépjen a **szinkronizálás részletei** szakaszra. A kiépítési tevékenységre vonatkozó jelentésre mutató hivatkozásokat is követheti, amelyek az Azure AD-kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik a hám használatával.

További információ az Azure AD-kiépítési naplók olvasásához: [jelentés a felhasználói fiókok automatikus üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséről.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)