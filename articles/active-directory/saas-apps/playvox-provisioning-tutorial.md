---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés Playvox konfigurálása Azure Active Directory használatával | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a Playvox.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96862479"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Playvox konfigurálása

Ez az oktatóanyag ismerteti a Playvox és a Azure Active Directory (Azure AD) követésének lépéseit az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat vagy csoportokat az Azure AD kiépítési szolgáltatással történő [Playvox](https://www.playvox.com) . A szolgáltatás működéséről és működéséről, valamint a gyakori kérdésekről a következő témakörben talál további információt: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Playvox-ben.
> * Távolítsa el a felhasználókat a Playvox, amikor már nincs szükségük a hozzáférésre.
> * A felhasználói attribútumok szinkronizálása az Azure AD és a Playvox között.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban szereplő forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására. Előfordulhat például, hogy az alkalmazás rendszergazdája, a felhőalapú alkalmazás rendszergazdája, az alkalmazás tulajdonosa vagy a globális rendszergazdai szerepkör van.
* [Playvox](https://www.playvox.com) rendelkező felhasználói fiók rendszergazdai jogosultságokkal.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés: a kiépítési üzembe helyezés megtervezése

1. Ismerje meg, [Hogyan működik a kiépítési szolgáltatás](../app-provisioning/user-provisioning.md).

2. Határozza meg, hogy kik lesznek [a kiépítés hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

3. Határozza meg, hogy az [Azure ad és a Playvox között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>2. lépés: a Playvox konfigurálása a kiépítés támogatására az Azure AD használatával

1. Jelentkezzen be a Playvox felügyeleti konzolra, és lépjen a **beállítások > API-kulcsok menüpontra**.

2. Válassza az **API-kulcs létrehozása** lehetőséget.

    ![Részleges képernyőfelvétel: az API-kulcs létrehozása gomb helye a Playvox felhasználói felületén.](media/playvox-provisioning-tutorial/create.png)

3. Írjon be egy értelmes nevet az API-kulcshoz, majd válassza a **Mentés** lehetőséget. Az API-kulcs létrehozása után válassza a **Bezárás** lehetőséget.

4. A létrehozott API-kulcsban válassza a **részletek** ikont.

    ![Részleges képernyőkép a részletek ikonjának helyéről, amely egy nagyító, a Playvox felhasználói felületén.](media/playvox-provisioning-tutorial/api.png)

5. Másolja ki és mentse a **BASE64-kulcs** értékét. Később, a Azure Portal a Playvox alkalmazás **üzembe** helyezés lapján a **titkos jogkivonat** szövegmezőben megadhatja ezt az értéket.

    ![Képernyőkép a részletek API-kulcs üzenetéről, a BASE64 kulcs értékének kiemelésével.](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>3. lépés: Playvox hozzáadása az Azure AD Application Galleryből

A Playvox való kiépítés kezelésének megkezdéséhez adja hozzá az Playvox az Azure AD-bérlőhöz az alkalmazás-katalógusból. További információért lásd: rövid útmutató [: alkalmazás hozzáadása a Azure Active Directory (Azure ad) bérlőhöz](../manage-apps/add-application-portal.md).

Ha korábban már beállította a Playvox az egyszeri bejelentkezéshez (SSO), ugyanazt az alkalmazást használhatja. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: annak meghatározása, hogy ki lesz a kiépítés hatóköre

Az Azure AD kiépítési szolgáltatását az alkalmazáshoz való hozzárendelés vagy a felhasználó vagy csoport attribútumai alapján kell kiépíteni. Az alkalmazáshoz való hozzárendelés alapján kiépített hatókörhöz lásd: [felhasználói hozzárendelés kezelése a Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) alkalmazáshoz, amelyből megtudhatja, hogyan rendelhet hozzá felhasználókat vagy csoportokat az alkalmazáshoz. Azon hatókörhöz, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, használjon egy hatókör-szűrőt az [attribútum-alapú alkalmazás kiépítés hatókör-szűrőkkel](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)című cikkben leírtak szerint.

Jegyezze fel ezeket a pontokat:

* Amikor felhasználókat rendel a Playvox, ki kell választania az alapértelmezett hozzáféréstől eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alapértelmezett hozzáférés az egyetlen elérhető szerepkör az alkalmazásban, az [alkalmazás jegyzékfájlját](../develop/howto-add-app-roles-in-azure-ad-apps.md) más szerepkörök hozzáadásával is frissítheti.

* Kezdje kicsiben. Próbálja ki a felhasználókat vagy csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítési hatókör a hozzárendelt felhasználókon vagy csoportokon alapul, a készlet méretét úgy szabályozhatja, hogy csak egy vagy két felhasználót vagy csoportot rendel hozzá az alkalmazáshoz. Ha a kiépítési hatókör minden felhasználót és csoportot tartalmaz, megadhat egy [attribútum-alapú hatókör-szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) a tesztkörnyezet méretének korlátozására.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>5. lépés: az automatikus felhasználó-kiépítés konfigurálása a Playvox

Ez a szakasz végigvezeti az Azure AD üzembe helyezési szolgáltatás konfigurálásának lépésein a felhasználók vagy csoportok létrehozásához, frissítéséhez és letiltásához az Azure AD-ban felhasználói vagy csoportos hozzárendelések alapján.

Az automatikus felhasználó-kiépítés konfigurálása a Playvox az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások** lehetőséget, majd válassza **a minden alkalmazás** lehetőséget.

    ![A Azure Portal részleges képernyőképe a nagyvállalati alkalmazásokkal és a Kiemelt alkalmazások minden elemével](common/enterprise-applications.png)

2. Az alkalmazások listában keresse meg és válassza a **Playvox** lehetőséget.

    ![Az alkalmazások listájának részleges képernyőképe, amely kiemelte az alkalmazás keresési mezőjét.](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![A kiépítési menüelemet bemutató részleges képernyőkép.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![A kiépítés lap részleges képernyőképe, amely a kiépítési mód legördülő listájában kiválasztott automatikus beállítást mutatja.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Playvox **bérlői URL-címét** a következőképpen:

    `https://{tenant}.playvox.com/scim/v1`

    Adja meg a 2. lépésben korábban átmásolt **titkos jogkivonatot** . Ezután válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen csatlakozni a Playvox. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Playvox-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Részleges képernyőkép a rendszergazdai hitelesítő adatok szakaszról, beleértve a bérlői URL-címet és a titkos jogkivonat szövegmezőit, valamint a kapcsolat tesztelése hivatkozást.](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítő e-mail** szövegmezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, aki megkapja a kiépítési hibajelentési értesítéseket. Ezután jelölje be az **e-mailes értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Az értesítő e-mailek szövegmezőjét és az értesítő e-mail-értesítést tartalmazó részleges képernyőkép.](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Playvox** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő Playvox. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Playvox felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), ügyeljen arra, hogy a Playvox API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |displayName|Sztring|
   |emails[type eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |externalId|Sztring|

10. A hatóköri szűrők konfigurálásához tekintse meg a [hatóköri szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

11. Az Azure AD-kiépítési szolgáltatás Playvox való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![A beállítások szakaszának részleges képernyőképe, amely azt mutatja, hogy a kiépítési állapot be értékre van állítva.](common/provisioning-toggle-on.png)

12. Továbbra is a **Beállítások** területen adja meg a Playvox kiépíteni kívánt felhasználókat vagy csoportokat. Ehhez válassza ki a **hatókörben** használni kívánt értékeket.

    ![A beállítások szakaszának részleges képernyőképe, amelyen a hatókör legördülő lista látható.](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![A mentési és elvetési beállításokat bemutató részleges képernyőkép.](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus hosszabb időt vesz igénybe, mint a későbbi ciklusok. A későbbi ciklusok körülbelül 40 percenként történnek, ha az Azure AD kiépítési szolgáltatás fut.

## <a name="step-6-monitor-your-deployment"></a>6. lépés: az üzemelő példány figyelése

A kiépítés beállítása után a következő erőforrásokkal figyelheti az üzemelő példányt:

* A [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül.
* Tekintse meg a [folyamatjelző sáv](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) állapotát a kiépítési ciklus állapotának megtekintéséhez és a befejezéshez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. További információ a karanténba helyezési [állapotokról: az alkalmazás üzembe helyezése a karanténba helyezett állapotban](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)