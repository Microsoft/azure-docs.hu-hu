---
title: 'Oktatóanyag: felhasználó kiépítés – LinkedIn Sales Navigator, Azure AD'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus kiépítéséhez és üzembe helyezéséhez a LinkedIn Sales Navigator szolgáltatásban.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbf8923d62b49ed5341776aef03bc1bc2dabeaa9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276805"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Oktatóanyag: a LinkedIn Sales Navigator konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy megmutassa a LinkedIn Sales Navigatorban és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a LinkedIn Sales Navigatorba.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* Egy LinkedIn Sales Navigator-bérlő 
* Rendszergazdai fiók a LinkedIn Sales Navigatorban a LinkedIn Account Center eléréséhez

> [!NOTE]
> A Azure Active Directory a [scim](http://www.simplecloud.info/) protokoll használatával integrálódik a LinkedIn Sales Navigator szolgáltatással.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Felhasználók társítása a LinkedIn Sales navigatorhoz

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználók és/vagy csoportok szerepelnek az Azure AD-ben azon felhasználók számára, akiknek hozzáférésre van szükségük a LinkedIn Sales navigatorhoz. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a LinkedIn Sales navigatorhoz az alábbi utasításokat követve:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Fontos Tippek a felhasználók a LinkedIn Sales navigatorhoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a LinkedIn Sales navigatorhoz a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a LinkedIn Sales navigatorhoz, ki kell választania a **felhasználói** szerepkört a hozzárendelés párbeszédpanelen. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>A felhasználók üzembe helyezésének beállítása a LinkedIn Sales Navigatorba

Ez a szakasz végigvezeti az Azure AD-nek a LinkedIn Sales Navigator SCIM felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán a társított felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a LinkedIn Sales Navigator alkalmazáson alapuló felhasználó és Csoport-hozzárendelés az Azure AD-ben.

> [!TIP]
> Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri bejelentkezést a LinkedIn értékesítési Navigátorhoz, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>A felhasználói fiókok automatikus üzembe helyezésének beállítása a LinkedIn Sales Navigatorba az Azure AD-ben:

Az első lépés a LinkedIn hozzáférési jogkivonatának beolvasása. Ha Ön vállalati rendszergazda, létrehozhat egy hozzáférési jogkivonatot. A fiók központban lépjen a **beállítások &gt; globális beállítások** elemre, és nyissa meg a **scim telepítési** paneljét.

> [!NOTE]
> Ha közvetlenül a kapcsolaton keresztül éri el a fiók központját, akkor az alábbi lépések segítségével érheti el.

1. Jelentkezzen be a Account Center webhelyre.

2. Válassza a **rendszergazda &gt; rendszergazdai beállítások** lehetőséget.

3. A bal oldali oldalsávon kattintson a **speciális integrációk** elemre. A rendszer átirányítja a fiók központját.

4. Kattintson az **+ új scim-konfiguráció hozzáadása** lehetőségre, és kövesse az eljárást az egyes mezők kitöltésével.

    > [!NOTE]
    > Ha az autoassign-licencek nincsenek engedélyezve, az azt jelenti, hogy csak a felhasználói adatszolgáltatások szinkronizálva vannak.

    ![A LinkedIn Sales Navigator üzembe helyezése](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Ha engedélyezve van az autolicenc-hozzárendelés, fel kell jegyeznie az alkalmazás példányát és a licenc típusát. A licencek első alkalommal lesznek hozzárendelve, az összes licenc beszerzése előtt.

    ![A LinkedIn Sales Navigator üzembe helyezése](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Kattintson a **jogkivonat előállítása**elemre. A hozzáférési jogkivonat megjelenítéséhez a **hozzáférési jogkivonat** mezőben kell megjelennie.

6. Mentse a hozzáférési jogkivonatot a vágólapra vagy a számítógépre, mielőtt elhagyja a lapot.

7. Ezután jelentkezzen be a [Azure Portalba](https://portal.azure.com), és keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszban.

8. Ha már konfigurálta a LinkedIn Sales Navigatort az egyszeri bejelentkezéshez, keresse meg a LinkedIn Sales Navigator példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **LinkedIn Sales Navigator** alkalmazást az alkalmazás-gyűjteményben. Válassza a LinkedIn Sales Navigator elemet a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

9. Válassza ki a LinkedIn Sales Navigator példányát, majd válassza a **kiépítés** lapot.

10. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![A LinkedIn Sales Navigator üzembe helyezése](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Adja meg a következő mezőket a **rendszergazdai hitelesítő adatok** területen:

    * A **bérlői URL-cím** mezőben adja meg a https://api.linkedin.com.

    * A **titkos jogkivonat** mezőben adja meg az 1. lépésben létrehozott hozzáférési jogkivonatot, majd kattintson a **kapcsolat tesztelése** elemre.

    * A portál upperright oldalán a sikeres értesítés jelenik meg.

12. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítési e-mail** mezőben a kiépítési hibaüzeneteket, és jelölje be az alábbi jelölőnégyzetet.

13. Kattintson a **Save** (Mentés) gombra.

14. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből a LinkedIn Sales navigatorba szinkronizálandó felhasználói és csoportosítási attribútumokat. Vegye figyelembe, hogy a **megfelelő** tulajdonságokként kiválasztott attribútumok a LinkedIn Sales Navigator felhasználói fiókjainak és csoportjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Válassza ki a Mentés gombra a módosítások véglegesítéséhez.

    ![A LinkedIn Sales Navigator üzembe helyezése](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a LinkedIn Sales navigatorhoz, módosítsa a **kiépítési állapotot** a következőre a **Beállítások** **szakaszban:**

16. Kattintson a **Save** (Mentés) gombra.

Ezzel elindítja a felhasználók és csoportok szakaszban a LinkedIn Sales-Navigátorhoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb ideig tart, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a LinkedIn Sales Navigator alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../manage-apps/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
