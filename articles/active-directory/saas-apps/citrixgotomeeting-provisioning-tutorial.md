---
title: 'Oktatóanyag: a GoToMeeting konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és GoToMeeting között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ff377b0f93968eb6743187e4e659f4e888e5010e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358899"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés GoToMeeting konfigurálása

Ennek az oktatóanyagnak a célja, hogy megmutassa a GoToMeeting és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a GoToMeeting.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

*   Egy Azure Active Directory-bérlő.
*   GoToMeeting egyszeri bejelentkezésre alkalmas előfizetés.
*   Felhasználói fiók a GoToMeeting-ben a csoport rendszergazdai engedélyeivel.

## <a name="assigning-users-to-gotomeeting"></a>Felhasználók kiosztása a GoToMeeting

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai képviselik a GoToMeeting alkalmazáshoz hozzáférő felhasználókat. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a GoToMeeting-alkalmazáshoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Fontos Tippek a felhasználók GoToMeeting való hozzárendeléséhez

*   Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a GoToMeeting a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

*   Ha a felhasználó GoToMeeting rendel hozzá, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="enable-automated-user-provisioning"></a>Automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-nek a GoToMeeting felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználó-és GoToMeeting alapján a felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri Sign-On a GoToMeeting számára, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-account-provisioning"></a>A felhasználói fiókok automatikus üzembe helyezésének konfigurálása:

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

1. Ha már konfigurálta a GoToMeeting az egyszeri bejelentkezéshez, keresse meg a GoToMeeting-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **GoToMeeting** az alkalmazás-gyűjteményben. Válassza a GoToMeeting lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

1. Válassza ki a GoToMeeting példányát, majd válassza a **kiépítés** lapot.

1. Állítsa a **kiépítési** módot **automatikus** értékre. 

    ![A Azure Portal GoToMeeting kiépítési lapjának képernyőképe. A kiépítési mód beállítása automatikus és rendszergazdai Felhasználónév, jelszó és tesztelési kapcsolatok kiemelése.](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. A rendszergazdai hitelesítő adatok szakaszban hajtsa végre a következő lépéseket:
   
    a. A **GoToMeeting rendszergazda felhasználóneve** szövegmezőbe írja be a rendszergazda felhasználónevét.

    b. A **GoToMeeting rendszergazdai jelszó** szövegmezőben a rendszergazda jelszava.

1. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a GoToMeeting-alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a GoToMeeting-fiókja rendelkezik rendszergazdai jogosultságokkal, és ismételje meg a **"rendszergazdai hitelesítő adatok"** lépést.

1. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítő e-mailek** mezőben, majd jelölje be a jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**

1. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a GoToMeeting lehetőséget.**

1. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből az GoToMeeting-be szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a GoToMeeting felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások véglegesítéséhez válassza a Mentés gombot.

1. Az Azure AD-kiépítési szolgáltatás GoToMeeting való engedélyezéséhez módosítsa a **kiépítési állapotot** a következőre a beállítások **szakaszban:**

1. Kattintson a **Mentés gombra.**

Elindítja a felhasználók és csoportok szakaszban GoToMeeting rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a GoToMeeting alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](./citrix-gotomeeting-tutorial.md)