---
title: 'Oktatóanyag: felhasználói kiépítés a felhasználók számára – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: celested
ms.openlocfilehash: 4abc117ae0e983cf684f0e70a363758f9be196aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359426"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Oktatóanyag: a felhasználók automatikus üzembe helyezésének beállítása

Ennek az oktatóanyagnak a célja, hogy megmutassa, milyen lépéseket kell elvégeznie a és a Azure Active Directory (Azure AD) szolgáltatásban, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a facsoportba.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Azure AD-bérlő
* Egy [nagyvállalati](https://www.asana.com/pricing) csomaggal rendelkező,
* Felhasználói fiók a-ben, rendszergazdai jogosultságokkal

> [!NOTE]
> Az Azure AD-kiépítés integrációja a (z [), a](https://asana.com/developers/api-reference/users)(z), a (z), a (z), a

## <a name="assign-users-to-asana"></a>Felhasználók társítása a levele

Az Azure AD a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók lesznek szinkronizálva.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak kell hozzáférést biztosítania a leválasztási alkalmazáshoz. Ezt követően az alábbi utasításokat követve rendelheti hozzá a felhasználókhoz a következő lépéseket:

[Felhasználó társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Fontos Tippek a felhasználók kiosztásához

Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen a kiépítési konfiguráció teszteléséhez. További felhasználók is hozzárendelhetők később.

## <a name="configure-user-provisioning-to-asana"></a>A felhasználók üzembe helyezésének konfigurálása a levele

Ez a szakasz végigvezeti az Azure AD-nek a felhasználói fiókok kiépítési API-hoz való csatlakoztatásának folyamatán. A kiépítési szolgáltatást úgy is beállíthatja, hogy az Azure AD-ben felhasználói hozzárendelései alapján hozzon létre, frissítsen és tiltsa le a kiosztott felhasználói fiókokat.

> [!TIP]
> Ha az SAML-alapú egyszeri bejelentkezést engedélyezni szeretné, kövesse a [Azure Portalban](https://portal.azure.com)található utasításokat. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>A felhasználói fiókok automatikus kiépítés beállítása az Azure AD-ben

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory**  >  **vállalati alkalmazások**  >  **minden alkalmazás** szakaszt.

1. Ha az egyszeri bejelentkezéshez már beállította a (z)-t, keressen rá a legördülő menüben a Keresés mező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg **a következőt: az Application** Galleryben. A keresési eredmények **közül válassza ki a legördülő listát** , és adja hozzá az alkalmazások listájához.

1. Válassza ki a leválasztási példányt, majd válassza ki a **kiépítés** lapot.

1. A **kiépítési mód** beállítása **automatikusra**.

    ![Kiépítés](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban kövesse ezeket az utasításokat a jogkivonat létrehozásához és a  **titkos jogkivonatban** való megadásához:

    a. Jelentkezzen be [a](https://app.asana.com) faaba a rendszergazdai fiókjával.

    b. Válassza ki a profil fényképét a felső sávon, és válassza ki az aktuális szervezeti név beállításait.

    c. Nyissa meg a **szolgáltatásfiókok** lapot.

    d. Válassza a **szolgáltatásfiók hozzáadása** lehetőséget.

    e. Szükség szerint frissítse a **nevet** és a **névjegyet** és a profil fényképét. Másolja a tokent a **tokenbe**, és válassza ki a **módosítások mentése** gombra.

1. A Azure Portal válassza a **kapcsolat tesztelése** lehetőséget annak biztosításához, hogy az Azure ad képes legyen csatlakozni a-hoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy az, hogy az, hogy a-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra a **kapcsolat tesztelése** lépéssel

1. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek az  **értesítési e-mailben** szeretne kiépítési hibaüzeneteket kapni. Jelölje be a jelölőnégyzetet az alatt.

1. Kattintson a **Mentés** gombra.

1. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a** következőre lehetőséget.

1. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből a-ba szinkronizálni kívánt felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítés műveletekhez használhatók a leválasztási műveletek felhasználói fiókjainak megfelelően. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget. További információ: a [felhasználó-kiépítés attribútum-leképezések testreszabása](../app-provisioning/customize-application-attributes.md).

1. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a, a **Beállítások** szakaszban, módosítsa a **kiépítési állapotot** **a** következőre:.

1. Kattintson a **Mentés** gombra.

A kezdeti szinkronizálás mostantól elindul a **felhasználók** szakaszban a facsoporthoz rendelt összes felhasználónál. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat. A naplók a kiépítési szolgáltatás által végrehajtott összes műveletet ismertetik a a (z)-ben.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentés a felhasználói fiókok automatikus üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséről.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](asana-tutorial.md)
