---
title: 'Oktatóanyag: a DocuSign konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és DocuSign között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 71f95b08584a46fccb0975cd9285150573ac02d4
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218519"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés DocuSign konfigurálása

Ennek az oktatóanyagnak a célja, hogy megmutassa a DocuSign és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a DocuSign.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

*   Egy Azure Active Directory-bérlő.
*   DocuSign egyszeri bejelentkezésre alkalmas előfizetés.
*   Felhasználói fiók a DocuSign-ben a csoport rendszergazdai engedélyeivel.

## <a name="assigning-users-to-docusign"></a>Felhasználók kiosztása a DocuSign

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai képviselik a DocuSign alkalmazáshoz hozzáférő felhasználókat. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a DocuSign-alkalmazáshoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Fontos Tippek a felhasználók DocuSign való hozzárendeléséhez

*   Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a DocuSign a létesítési konfiguráció teszteléséhez. Később további felhasználók is hozzárendelhetők.

*   Ha a felhasználó DocuSign rendel hozzá, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

> [!NOTE]
> Az Azure AD nem támogatja a Docusign alkalmazással való csoportos kiépítés használatát, csak a felhasználók számára lehet kiépíteni.

## <a name="enable-user-provisioning"></a>A felhasználó üzembe helyezésének engedélyezése

Ez a szakasz végigvezeti az Azure AD-nek a DocuSign felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználó-és DocuSign alapján a felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

> [!Tip]
> Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri Sign-On a DocuSign számára, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-user-account-provisioning"></a>A felhasználói fiókok üzembe helyezésének konfigurálása:

Ennek a szakasznak a célja annak ismertetése, hogyan engedélyezhető Active Directory felhasználói fiókok DocuSign való kiépítés.

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

1. Ha már konfigurálta a DocuSign az egyszeri bejelentkezéshez, keresse meg a DocuSign-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **DocuSign** az alkalmazás-gyűjteményben. Válassza a DocuSign lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

1. Válassza ki a DocuSign példányát, majd válassza a **kiépítés** lapot.

1. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre. 

    ![A Azure Portal DocuSign kiépítési lapjának képernyőképe. A kiépítési mód beállítása automatikus és rendszergazdai Felhasználónév, jelszó és tesztelési kapcsolatok kiemelése.](./media/docusign-provisioning-tutorial/provisioning.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a következő konfigurációs beállításokat:
   
    a. A **rendszergazda felhasználóneve** szövegmezőbe írja be azt a DocuSign-fióknevet, amelyben a **rendszergazda profil található** a DocuSign.com hozzárendelve.
   
    b. A **rendszergazdai jelszó** szövegmezőbe írja be a fiókhoz tartozó jelszót.

> [!NOTE]
> Ha az egyszeri bejelentkezés és a felhasználó üzembe helyezése is be van állítva, a kiépítés során használt hitelesítési hitelesítő adatokat úgy kell konfigurálni, hogy az egyszeri bejelentkezési azonosítóval és a felhasználónévvel/jelszóval is működjenek.

1. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a DocuSign-alkalmazáshoz.

1. Az **értesítő e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek a kiépítési hibákra vonatkozó értesítéseket kell kapnia, majd jelölje be a jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**

1. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a DocuSign lehetőséget.**

1. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből az DocuSign-be szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a DocuSign felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások véglegesítéséhez válassza a Mentés gombot.

1. Az Azure AD-kiépítési szolgáltatás DocuSign való engedélyezéséhez módosítsa a **kiépítési állapotot** a következőre a beállítások **szakaszban:**

1. Kattintson a **Mentés gombra.**

Elindítja a felhasználók és csoportok szakaszban DocuSign rendelt felhasználók kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a DocuSign alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
* A Docusign felhasználó szerepkör-vagy engedélyezési profiljának kiépítéséhez használhatja az attribútum-hozzárendelések kifejezését a [switch](../app-provisioning/functions-for-customizing-application-data.md#switch) és a [singleAppRoleAssignment](../app-provisioning/functions-for-customizing-application-data.md#singleapproleassignment) függvények használatával. Az alábbi kifejezés például a "8032066" azonosítót fogja kiépíteni, ha egy felhasználónál az Azure AD-ben hozzá van rendelve a "DS-rendszergazda" szerepkör. Ha a felhasználó nincs hozzárendelve szerepkör az Azure AD-oldalhoz, nem fog semmilyen jogosultsági profilt kiépíteni. Az azonosító a DocuSign- [portálról](https://support.docusign.com/articles/Default-settings-for-out-of-the-box-DocuSign-Permission-Profiles)kérhető le.

Switch (SingleAppRoleAssignment ([appRoleAssignments]), "", "DS-rendszergazda", "8032066")


## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](docusign-tutorial.md)
