---
title: 'Oktatóanyag: a felhasználók üzembe helyezése a Cerner Central szolgáltatásban – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználók automatikus kiosztásához a Cerner Central szolgáltatásban.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 1f82cab1172e7293e2a5910d35280eefb30ed49e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357453"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Oktatóanyag: a Cerner központi beállítása a felhasználók automatikus üzembe helyezéséhez

Ennek az oktatóanyagnak a célja, hogy megmutassa a Cerner Central-ban és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Cerner Central felhasználói névsorára.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Egy Azure Active Directory-bérlő
* A Cerner központi bérlője

> [!NOTE]
> A Azure Active Directory a [scim](http://www.simplecloud.info/) protokoll használatával integrálódik a cerner Central szolgáltatással.

## <a name="assigning-users-to-cerner-central"></a>Felhasználók kiosztása a Cerner Central-ban

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek. 

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználói és/vagy csoportjai képviselik a Cerner Central-hoz hozzáférést igénylő felhasználókat. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a Cerner Central-hoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Fontos Tippek a felhasználók a Cerner Central-hoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Cerner Central szolgáltatáshoz a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Miután a kezdeti tesztelés befejeződött egyetlen felhasználónál, a Cerner Central javasolja, hogy a cerner-megoldáshoz (nem csak a Cerner Central-hoz) hozzáférő felhasználók teljes listáját hozzárendelje a Cerner felhasználói névsorához.  Más Cerner-megoldások használják a felhasználók listáját a felhasználói névsorban.

* Amikor felhasználót rendel a Cerner Central-hoz, ki kell választania a **felhasználói** szerepkört a hozzárendelés párbeszédpanelen. Az "alapértelmezett hozzáférés" szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-user-provisioning-to-cerner-central"></a>A felhasználók üzembe helyezésének konfigurálása a Cerner Central-ban

Ez a szakasz végigvezeti az Azure AD-nek a Cerner Central felhasználói beosztáshoz való csatlakoztatásának folyamatán, a Cerner SCIM felhasználói fiók létesítési API-ját és a kiépítési szolgáltatás konfigurálását a Cerner Central-ban az Azure AD-ben a felhasználók és a csoportok hozzárendelése alapján.

> [!TIP]
> Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri Sign-On a Cerner Central számára, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást. További információ: a [cerner Central egyszeri bejelentkezési oktatóanyaga](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>A felhasználói fiókok automatikus üzembe helyezésének beállítása a Cerner Central szolgáltatásban az Azure AD-ben:

Ahhoz, hogy felhasználói fiókokat lehessen kiépíteni a Cerner Central szolgáltatásba, egy Cerner központi rendszerfiókot kell kérnie a Cerner-ből, és létre kell hoznia egy OAuth tulajdonosi jogkivonatot, amelyet az Azure AD a Cerner SCIM-végponthoz való kapcsolódáshoz használhat. Azt is javasoljuk, hogy az integrációt a Cerner homokozóban végezze el az éles környezetbe való üzembe helyezés előtt.

1. Első lépésként győződjön meg arról, hogy a Cerner és az Azure AD-integrációt kezelő személyeknek van egy CernerCare-fiókja, amely az utasítások végrehajtásához szükséges dokumentáció eléréséhez szükséges. Ha szükséges, az alábbi URL-címek használatával hozzon létre CernerCare-fiókokat az egyes alkalmazható környezetekben.

   * Védőfal  https://sandboxcernercare.com/accounts/create

   * Éles  https://cernercare.com/accounts/create  

2. Ezután létre kell hoznia egy rendszerfiókot az Azure AD-hez. Az alábbi útmutatást követve igényelhet rendszerfiókot a homokozóban és az éles környezetekben.

   * Utasításokat  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Védőfal https://sandboxcernercentral.com/system-accounts/

   * Éles  https://cernercentral.com/system-accounts/

3. Ezután állítson be egy OAuth-tulajdonosi jogkivonatot mindegyik rendszerfiókhoz. Ehhez kövesse az alábbi utasításokat.

   * Utasításokat  https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Védőfal https://sandboxcernercentral.com/system-accounts/

   * Éles  https://cernercentral.com/system-accounts/

4. Végül meg kell adnia a felhasználói beosztási tartomány azonosítóit a Cerner-ben a homokozó és a termelési környezet számára a konfiguráció befejezéséhez. További információ a beszerzésével kapcsolatban: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM . 

5. Most már beállíthatja, hogy az Azure AD felhasználói fiókokat hozzon létre a Cerner-ben. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás**  szakaszban.

6. Ha már konfigurálta a Cerner Central-t az egyszeri bejelentkezéshez, keresse meg a Cerner Central-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **cerner Central** elemet az alkalmazás-katalógusban. Válassza ki a Cerner Central elemet a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

7. Válassza ki a Cerner Central-példányát, majd válassza ki a **kiépítés** lapot.

8. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

   ![A cerner központi üzembe helyezése](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Adja meg a következő mezőket a **rendszergazdai hitelesítő adatok** területen:

   * A **bérlői URL-cím** mezőben adjon meg egy URL-címet az alábbi formátumban, és cserélje le a "user-névsor-REALM-id" karakterláncot a #4 lépésben beszerzett tartományi azonosítóra.

    > Védőfal https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Éles https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * A **titkos jogkivonat** mezőben adja meg a #3 lépésben létrehozott OAuth tulajdonosi jogkivonatot, majd kattintson a **kapcsolat tesztelése** elemre.

   * A portál upperright oldalán a sikeres értesítés jelenik meg.

1. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítési e-mail** mezőben a kiépítési hibaüzeneteket, és jelölje be az alábbi jelölőnégyzetet.

1. Kattintson a **Mentés** gombra.

1. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből a Cerner Central-be szinkronizálandó felhasználói és csoportosítási attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a cerner Central felhasználói fiókjainak és csoportjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások véglegesítéséhez válassza a Mentés gombot.

1. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Cerner Central számára, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

1. Kattintson a **Mentés** gombra.

Ezzel elindítja a felhasználók és csoportok szakaszban a Cerner Centralhoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a cerner Central alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Cerner Central: személyazonossági adatok közzététele az Azure AD-vel](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Oktatóanyag: a Cerner Central konfigurálása egyszeri bejelentkezéshez Azure Active Directory](cernercentral-tutorial.md)
* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md).