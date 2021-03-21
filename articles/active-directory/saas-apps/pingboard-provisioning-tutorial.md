---
title: 'Oktatóanyag: felhasználói kiépítés a Pingboard – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Pingboard.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: ac36f5d6d1f57fd8453c54bcc8cf19dd964f47f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357895"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Pingboard konfigurálása

Ennek az oktatóanyagnak a célja, hogy megmutassa, milyen lépéseket kell követnie a felhasználói fiókok automatikus üzembe helyezésének és kivonásának engedélyezéséhez Azure Active Directory (Azure AD) Pingboard.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Azure AD-bérlő
* Pingboard bérlői [Pro-fiók](https://pingboard.com/pricing)
* Rendszergazdai jogosultságokkal rendelkező Pingboard felhasználói fiók

> [!NOTE]
> Az Azure AD-kiépítés integrációja a [PINGBOARD API](https://pingboard.docs.apiary.io/#)-ra támaszkodik, amely elérhető a fiókjában.

## <a name="assign-users-to-pingboard"></a>Felhasználók Pingboard rendelése

Az Azure AD egy "hozzárendelések" nevű koncepciót használ annak meghatározására, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók lesznek szinkronizálva. 

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak kell hozzáférést biztosítania a Pingboard alkalmazáshoz. Ezt követően az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a Pingboard-alkalmazáshoz:

[Felhasználó társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Fontos Tippek a felhasználók Pingboard való hozzárendeléséhez

Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen a Pingboard a létesítési konfiguráció teszteléséhez. További felhasználók is hozzárendelhetők később.

## <a name="configure-user-provisioning-to-pingboard"></a>A felhasználók üzembe helyezésének konfigurálása a Pingboard 

Ez a szakasz végigvezeti az Azure AD az Pingboard felhasználói fiók létesítési API-hoz való csatlakoztatásának folyamatán. A kiépítési szolgáltatást úgy is beállíthatja, hogy az Azure AD-ben felhasználói hozzárendelésein alapuló Pingboard hozzon létre, frissítsen és tiltsa le a hozzárendelt felhasználói fiókokat.

> [!TIP]
> Az SAML-alapú egyszeri bejelentkezés Pingboard való engedélyezéséhez kövesse a [Azure Portalban](https://portal.azure.com)megadott utasításokat. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>A felhasználói fiókok automatikus üzembe helyezésének beállítása az Azure AD-beli Pingboard

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory**  >  **vállalati alkalmazások**  >  **minden alkalmazás** szakaszt.

1. Ha már konfigurálta a Pingboard az egyszeri bejelentkezéshez, keresse meg a Pingboard példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **Pingboard** az alkalmazás-gyűjteményben. Válassza a **Pingboard** lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

1. Válassza ki a Pingboard példányát, majd válassza ki a **kiépítés** lapot.

1. A **kiépítési mód** beállítása **automatikusra**.

    ![Pingboard kiépítés](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban kövesse az alábbi lépéseket:

    a. A **bérlői URL-cím** mezőbe írja be a kifejezést `https://your_domain.pingboard.com/scim/v2` , és cserélje le a "your_domain" értéket a valódi tartományra.

    b. Jelentkezzen be a [Pingboard](https://pingboard.com/) a rendszergazdai fiók használatával.

    c. Válassza a **bővítmények**  >  **integrációs**  >  **Azure Active Directory** elemet.

    d. Lépjen a **configure (Konfigurálás** ) lapra, és válassza a felhasználó üzembe helyezésének **engedélyezése az Azure-ból** lehetőséget.

    e. Másolja a tokent a **OAuth tulajdonosi jogkivonatba**, és adja meg a **titkos jogkivonatban**.

1. A Azure Portal válassza a **kapcsolat tesztelése** lehetőséget az Azure ad-hez való kapcsolódáshoz a Pingboard-alkalmazáshoz. Ha a kapcsolat meghiúsul, ellenőrizze, hogy a Pingboard-fiókja rendelkezik-e rendszergazdai jogosultságokkal, majd próbálja megismételni a **kapcsolat tesztelése** lépést.

1. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek az **értesítési e-mailben** szeretne kiépítési hibaüzeneteket kapni. Jelölje be a jelölőnégyzetet az alatt.

1. Kattintson a **Mentés** gombra.

1. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Pingboard** lehetőséget.

1. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből az Pingboard-be szinkronizálandó felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Pingboard felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget. További információ: a [felhasználói kiépítési attribútumok társításának testreszabása](../app-provisioning/customize-application-attributes.md).

1. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Pingboard számára, a **Beállítások** szakaszban módosítsa a **kiépítési állapot** beállítást **a** következőre:.

1. Válassza a **Mentés** lehetőséget a Pingboard hozzárendelt felhasználók kezdeti szinkronizálásának elindításához.

A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a következő szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat. A naplók a kiépítési szolgáltatás által a Pingboard alkalmazásban végrehajtott összes műveletet írják le.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentés a felhasználói fiókok automatikus üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséről.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](pingboard-tutorial.md)
