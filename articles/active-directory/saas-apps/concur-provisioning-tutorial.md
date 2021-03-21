---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés konfigurálása a Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és az egyetért.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: edb21287b30f8ba77d6312ec6b456e20aa260598
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358211"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés megadásának beállítása

Ennek az oktatóanyagnak a célja, hogy megmutassa, milyen lépéseket kell elvégeznie a egyetértve és az Azure AD-ben, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

*   Egy Azure Active Directory-bérlő.
*   Egy egyetértő egyszeri bejelentkezésre engedélyezett előfizetés.
*   Egy felhasználói fiók egyetért a csoport rendszergazdai engedélyeivel.

## <a name="assigning-users-to-concur"></a>Felhasználók kiosztása a egybeesik

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok milyen felhasználók számára férhetnek hozzá a saját belefoglalt alkalmazásához. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a saját belefoglalt alkalmazásához:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-concur"></a>Fontos Tippek a felhasználók az összevonáshoz való hozzárendeléséhez

*   Az üzembe helyezési konfiguráció teszteléséhez egyetlen Azure AD-felhasználóhoz is hozzá kell rendelni egyet. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

*   Ha egy felhasználót az összevonáshoz rendel, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="enable-user-provisioning"></a>A felhasználó üzembe helyezésének engedélyezése

Ez a szakasz végigvezeti az Azure AD és a hozzá tartozó felhasználói fiókok kiépítési API-k összekapcsolásának, valamint a kiépítési szolgáltatás konfigurálásának az Azure AD-ben a felhasználó-és csoport-hozzárendelésen alapuló, a felhasználók és csoportok hozzárendelése alapján való létrehozásához, frissítéséhez és letiltásához.

> [!Tip] 
> Dönthet úgy is, hogy az SAML-alapú önálló Sign-Ont is engedélyezte a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-user-account-provisioning"></a>A felhasználói fiókok üzembe helyezésének konfigurálása:

Ennek a szakasznak a célja, hogy megtudja, hogyan engedélyezheti Active Directory felhasználói fiókok kiépítési feltételeit.

Ahhoz, hogy az alkalmazások engedélyezve legyenek a költségfelosztási szolgáltatásban, a webszolgáltatás-rendszergazdai profil megfelelő beállításának és használatának kell lennie. Ne adja hozzá a WS rendszergazdai szerepkört a T&E rendszergazdai funkciókhoz használt meglévő rendszergazdai profilhoz.

Az egyetértő tanácsadók vagy az ügyfél rendszergazdájának létre kell hoznia egy külön webszolgáltatás-rendszergazdai profilt, és az ügyfél rendszergazdájának ezt a profilt kell használnia a webszolgáltatások rendszergazdai funkcióihoz (például az alkalmazások engedélyezéséhez). Ezeket a profilokat el kell különíteni az ügyfél-rendszergazda napi T&E rendszergazdai profiljától (a T&E rendszergazdai profilnak nincs hozzárendelve a WSAdmin szerepköre).

Amikor létrehozza az alkalmazás engedélyezéséhez használandó profilt, adja meg az ügyfél-rendszergazda nevét a felhasználói profil mezőiben. Ez tulajdonosi jogosultságot rendel a profilhoz. Egy vagy több profil létrehozása után az ügyfélnek be kell jelentkeznie ezzel a profillal, ha rákattint az "*Engedélyezés*" gombra a webszolgáltatások menüjében lévő partneri alkalmazáshoz.

A következő okok miatt ez a művelet nem hajtható végre a normál T&E felügyelethez használt profillal.

* Az ügyfélnek olyannak kell lennie, amely az alkalmazás engedélyezése után megjelenő párbeszéd ablak "*Igen*" gombjára kattint. Ezzel a beállítással visszaigazolhatja, hogy az ügyfél hajlandó-e hozzáférni az adateléréshez, így Ön vagy a partner nem kattinthat erre az Igen gombra.

* Ha a T&E rendszergazdai profillal rendelkező alkalmazást engedélyező ügyfél-rendszergazda elhagyja a vállalatot (ami a profil inaktiválását eredményezi), az adott profil használatával engedélyezett alkalmazások mindaddig nem működnek, amíg az alkalmazás nem engedélyezett egy másik aktív WS felügyeleti profillal. Ezért érdemes különálló WS rendszergazdai profilokat létrehozni.

* Ha a rendszergazda elhagyja a vállalatot, a WS rendszergazdai profilhoz társított név módosítható a csere-rendszergazda számára, ha szükséges, az engedélyezett alkalmazás befolyásolása nélkül, mert a profilnak nincs szüksége inaktiválásra.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **egyetértő** bérlőbe.

2. Az **Adminisztráció** menüben válassza a **webszolgáltatások** lehetőséget.
   
    ![A bérlő egyetért](./media/concur-provisioning-tutorial/IC721729.png "A bérlő egyetért")

3. A bal oldalon a **webszolgáltatások** ablaktáblán válassza a **partner alkalmazás engedélyezése** lehetőséget.
   
    ![Partneri alkalmazás engedélyezése](./media/concur-provisioning-tutorial/ic721730.png "Partneri alkalmazás engedélyezése")

4. Az **alkalmazás engedélyezése** listából válassza a **Azure Active Directory** lehetőséget, majd kattintson az **Engedélyezés** gombra.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. A **művelet megerősítése** párbeszédpanel bezárásához kattintson az **Igen** gombra.
   
    ![Művelet megerősítése](./media/concur-provisioning-tutorial/ic721732.png "Művelet megerősítése")

6. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

7. Ha már konfigurálta az egyszeri bejelentkezést, akkor a keresési mezővel keresse meg az Ön által a egyetértő példányát. Ha nem, válassza a **Hozzáadás** lehetőséget, és **Keresse meg a részvételt** az alkalmazás-gyűjteményben. Válassza a beletartás lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

8. Válassza ki a egyetértő példányát, majd válassza a **kiépítés** fület.

9. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre. 
 
    ![Képernyőkép a kiépítés lapról a Azure Portal. A kiépítési mód értéke automatikus, a test (kapcsolódás) gomb pedig ki van emelve.](./media/concur-provisioning-tutorial/provisioning.png)

10. A **rendszergazdai hitelesítő adatok** szakaszban adja meg az Ön által a egyetértő rendszergazda **felhasználónevét** és **jelszavát** .

11. A Azure Portalban kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak az alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg róla, hogy a fiókja rendelkezik rendszergazdai jogosultságokkal.

12. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítő e-mailek** mezőben, majd jelölje be a jelölőnégyzetet.

13. Kattintson a **Mentés gombra.**

14. A leképezések szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók egyetértenek.**

15. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletek során a felhasználói fiókok egyeztetésére szolgálnak. A módosítások véglegesítéséhez válassza a Mentés gombot.

16. Ahhoz, hogy az Azure AD-kiépítési szolgáltatás egyetért, a **Beállítások** szakaszban módosítsa a **kiépítési állapotot** **a** következőre:

17. Kattintson a **Mentés gombra.**

Most már létrehozhat egy teszt fiókot is. Várjon akár 20 percet, és ellenőrizze, hogy a fiók szinkronizálása megtörtént-e.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](concur-tutorial.md)