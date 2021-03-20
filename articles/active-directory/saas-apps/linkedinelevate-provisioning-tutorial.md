---
title: 'Oktatóanyag: felhasználó kiépítés a LinkedIn jogosultságszint-emeléshez – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a LinkedIn-jogosultságszint.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 5e972475530ad36a188f73990bb9eca35748c36c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358949"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Oktatóanyag: a LinkedIn jogosultságszint-emelésének beállítása a felhasználók automatikus üzembe helyezéséhez

Ennek az oktatóanyagnak a célja, hogy megmutassa a LinkedIn jogosultságszint-emeléséhez és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a LinkedIn-jogosultságszint-emeléshez.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Egy Azure Active Directory-bérlő
* LinkedIn-jogosultságszint-emelési bérlő
* Rendszergazdai fiók a LinkedIn-ben a LinkedIn Account Center elérésével

> [!NOTE]
> A Azure Active Directory a [scim](http://www.simplecloud.info/) protokoll használatával integrálható a LinkedIn jogosultságszint-emeléssel.

## <a name="assigning-users-to-linkedin-elevate"></a>Felhasználók társítása a LinkedIn-jogosultságszint-emeléshez

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai képviselik a LinkedIn jogosultságszint-emelést igénylő felhasználókat. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a LinkedIn-emeléshez:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Fontos Tippek a felhasználók LinkedIn-jogosultságszint-emeléshez való hozzárendeléséhez

* Az üzembe helyezési konfiguráció teszteléséhez egyetlen Azure AD-felhasználót kell hozzárendelni a LinkedIn-jogosultságszint-emeléshez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználót a LinkedIn jogosultságszint-emeléshez rendel, ki kell választania a **felhasználói** szerepkört a hozzárendelés párbeszédpanelen. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>A felhasználók üzembe helyezésének beállítása a LinkedIn-jogosultságszint-emelésre

Ez a szakasz végigvezeti az Azure AD-nek a LinkedIn emelt szintű SCIM felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán, hogy az Azure AD-ben felhasználói és csoport-hozzárendelés alapján hozza létre, frissítse és tiltsa le a hozzárendelt felhasználói fiókokat.

**Tipp:** Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri Sign-On a LinkedIn jogosultságszint-emeléshez a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Az automatikus felhasználói fiók kiépítés beállítása a LinkedIn-jogosultságszint-emelésre az Azure AD-ben:

Az első lépés a LinkedIn hozzáférési jogkivonatának beolvasása. Ha Ön vállalati rendszergazda, létrehozhat egy hozzáférési jogkivonatot. A fiók központban lépjen a **Beállítások &gt; globális beállítások** elemre, és nyissa meg a **scim telepítési** paneljét.

> [!NOTE]
> Ha közvetlenül a kapcsolaton keresztül éri el a fiók központját, akkor az alábbi lépések segítségével érheti el.

1. Jelentkezzen be a Account Center webhelyre.

2. Válassza a **rendszergazdai &gt; rendszergazdai beállítások** lehetőséget.

3. A bal oldali oldalsávon kattintson a **speciális integrációk** elemre. A rendszer átirányítja a fiók központját.

4. Kattintson az **+ új scim-konfiguráció hozzáadása** lehetőségre, és kövesse az eljárást az egyes mezők kitöltésével.

    > [!NOTE]
    > Ha az autoassign-licencek nincsenek engedélyezve, az azt jelenti, hogy csak a felhasználói adatszolgáltatások szinkronizálva vannak.

    ![A képernyőfelvételen a LinkedIn Account Center globális beállításai láthatók.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Ha engedélyezve van az autolicenc-hozzárendelés, fel kell jegyeznie az alkalmazás példányát és a licenc típusát. A licencek első alkalommal lesznek hozzárendelve, az összes licenc beszerzése előtt.

    ![A képernyőképen az S C I M telepítő lapja látható.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Kattintson a **jogkivonat előállítása** elemre. A hozzáférési jogkivonat megjelenítéséhez a **hozzáférési jogkivonat** mezőben kell megjelennie.

6. Mentse a hozzáférési jogkivonatot a vágólapra vagy a számítógépre, mielőtt elhagyja a lapot.

7. Ezután jelentkezzen be a [Azure Portalba](https://portal.azure.com), és keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás**  szakaszban.

8. Ha már konfigurálta a LinkedIn jogosultságszint-emelést az egyszeri bejelentkezéshez, keresse meg a LinkedIn emelt példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **LinkedIn jogosultságszint-emelését** az alkalmazás-gyűjteményben. Válassza a LinkedIn Jogosultságszint-emelés lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

9. Válassza ki a LinkedIn emelt példányát, majd válassza a **kiépítés** lapot.

10. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![A képernyőfelvételen a LinkedIn jogosultságszint-emelési kiépítési lapja látható.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Adja meg a következő mezőket a **rendszergazdai hitelesítő adatok** területen:

    * A **bérlői URL-cím** mezőben adja meg a értéket `https://api.linkedin.com` .

    * A **titkos jogkivonat** mezőben adja meg az 1. lépésben létrehozott hozzáférési jogkivonatot, majd kattintson a **kapcsolat tesztelése** elemre.

    * A portál upperright oldalán a sikeres értesítés jelenik meg.

12. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítési e-mail** mezőben a kiépítési hibaüzeneteket, és jelölje be az alábbi jelölőnégyzetet.

13. Kattintson a **Mentés** gombra.

14. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad és a LinkedIn közötti jogosultságszint-emeléssel szinkronizálni kívánt felhasználói és csoportosítási attribútumokat. Vegye figyelembe, hogy a **megfelelő** tulajdonságokként kiválasztott attribútumok a LinkedIn jogosultságszint-emelési műveletekhez tartozó felhasználói fiókok és csoportok egyeztetésére szolgálnak. A módosítások véglegesítéséhez válassza a Mentés gombot.

    ![A képernyőképen láthatók a leképezések, beleértve az attribútumok hozzárendeléseit is.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a LinkedIn jogosultságszint-emeléshez, módosítsa a **kiépítési állapotot** a következőre a **Beállítások** **szakaszban:**

16. Kattintson a **Mentés** gombra.

Ezzel elindítja a felhasználók és csoportok szakaszban a LinkedIn jogosultságszint-emeléshez rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb ideig tart, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a LinkedIn jogosultságszint-emelési alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
