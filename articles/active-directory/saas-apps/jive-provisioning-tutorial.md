---
title: 'Oktatóanyag: a Jive konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg a Jive és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Jive-be.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: ebee5d986007e07d497056620f0cfc437b2da4d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94356399"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Jive konfigurálása

Ennek az oktatóanyagnak a célja, hogy megmutassa a Jive és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Jive.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

*   Egy Azure Active Directory-bérlő.
*   Egy Jive egyszeri bejelentkezésre engedélyezett előfizetés.
*   Felhasználói fiók a Jive-ben a csoport rendszergazdai engedélyeivel.

## <a name="assigning-users-to-jive"></a>Felhasználók kiosztása a Jive

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai képviselik a Jive alkalmazáshoz hozzáférő felhasználókat. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a Jive-alkalmazáshoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-jive"></a>Fontos Tippek a felhasználók Jive való hozzárendeléséhez

*   Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Jive a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

*   Ha a felhasználó Jive rendel hozzá, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="enable-user-provisioning"></a>A felhasználó üzembe helyezésének engedélyezése

Ez a szakasz végigvezeti az Azure AD-nek a Jive felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználó-és Jive alapján a felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri Sign-On a Jive számára, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-user-account-provisioning"></a>A felhasználói fiókok üzembe helyezésének konfigurálása:

Ennek a szakasznak a célja annak ismertetése, hogyan engedélyezhető Active Directory felhasználói fiókok Jive való kiépítés.
Ennek az eljárásnak a részeként meg kell adnia egy felhasználói biztonsági jogkivonatot, amelyet Jive.com kell kérnie.

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

1. Ha már konfigurálta a Jive az egyszeri bejelentkezéshez, keresse meg a Jive-példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **Jive** az alkalmazás-gyűjteményben. Válassza a Jive lehetőséget a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

1. Válassza ki a Jive példányát, majd válassza a **kiépítés** lapot.

1. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre. 

    ![Képernyőfelvétel: az Jive-létesítési oldal, a kiépítési mód pedig az automatikus és más beállítható értékekre van beállítva.](./media/jive-provisioning-tutorial/provisioning.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a következő konfigurációs beállításokat:
   
    a. A Jive-rendszergazda **felhasználóneve** szövegmezőbe írja be a Jive-fiók nevét, amely a Jive.com hozzárendelt **rendszergazdai** profillal rendelkezik.
   
    b. A **Jive-adminisztrátor jelszava** szövegmezőbe írja be a fiókhoz tartozó jelszót.
   
    c. A **Jive-bérlő URL-címe** szövegmezőbe írja be a Jive-bérlői URL-címet.
      
      > [!NOTE]
      > A Jive-bérlő URL-címe a szervezet által a Jive való bejelentkezéshez használt URL-cím.  
      > Általában az URL-cím formátuma a következő: **www. \<organization\> . jive.com**.          

1. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a Jive-alkalmazáshoz.

1. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítési e-mail** mezőben a kiépítési hibaüzeneteket, és jelölje be az alábbi jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**

1. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Jive lehetőséget.**

1. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből az Jive-be szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Jive felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások véglegesítéséhez válassza a Mentés gombot.

1. Az Azure AD-kiépítési szolgáltatás Jive való engedélyezéséhez módosítsa a **kiépítési állapotot** a következőre a beállítások **szakaszban:**

1. Kattintson a **Mentés gombra.**

Elindítja a felhasználók és csoportok szakaszban Jive rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a Jive alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](jive-tutorial.md)