---
title: 'Oktatóanyag: a Salesforce-munkafolyamatok konfigurálása automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, milyen lépéseket kell elvégeznie a Salesforce homokozóban és az Azure AD-ben, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7e3f8e5e975468b468712ae8907cdca0e80a5f9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94352608"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Oktatóanyag: a Salesforce-homokozó konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ennek az oktatóanyagnak a célja, hogy megmutassa a Salesforce homokozóban és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Salesforce-alapú homokozóba.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

*   Egy Azure Active Directory-bérlő.
*   Érvényes bérlő a Salesforce-homokozóban munkahelyi vagy Salesforce-homokozóban az oktatáshoz. Bármelyik szolgáltatáshoz használhatja az ingyenes próbaverziós fiókot is.
*   Felhasználói fiók a Salesforce-munkaterületen a csoport rendszergazdai engedélyeivel.

## <a name="assigning-users-to-salesforce-sandbox"></a>Felhasználók Salesforce-beli Sandboxhoz rendelése

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra vagy csoportokra van szükség az Azure AD-ben a Salesforce homokozó alkalmazáshoz. A döntés elvégzése után ezeket a felhasználókat hozzárendelheti a Salesforce sandbox-alkalmazáshoz a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md) című részben leírtak szerint.

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Fontos Tippek a felhasználók Salesforce-beli Sandboxhoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Salesforce-munkaterülethez a létesítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a Salesforce a Sandboxhoz, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

> [!NOTE]
> Ez az alkalmazás a kiépítési folyamat részeként importálja az egyéni szerepköröket a Salesforce-munkaterületről, amelyet az ügyfél a felhasználók kiosztásakor érdemes kiválasztani.

## <a name="enable-automated-user-provisioning"></a>Automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz végigvezeti az Azure AD-nek a homokozó felhasználói fiókok üzembe helyezési API-Salesforce való csatlakoztatásán, valamint a kiépítési szolgáltatásnak az Azure AD-ben a felhasználók és csoportok hozzárendelése alapján történő létrehozásához, frissítéséhez és letiltásához a Salesforce-homokozóban.

>[!Tip]
>Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri Sign-Ont a Salesforce homokozóban, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="configure-automatic-user-account-provisioning"></a>A felhasználói fiókok automatikus üzembe helyezésének konfigurálása

Ennek a szakasznak a célja annak ismertetése, hogyan engedélyezhető Active Directory felhasználói fiókok felhasználói fiókjainak Salesforce a homokozóban.

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

1. Ha már konfigurálta a Salesforce-alapú munkaterületet az egyszeri bejelentkezéshez, keresse meg a Salesforce-homokozó példányát a keresőmező használatával. Ellenkező esetben válassza a **Hozzáadás** lehetőséget, és keresse meg a **Salesforce** -munkaterületet az alkalmazás-galériában. Válassza ki a Salesforce-munkaterületet a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

1. Válassza ki a Salesforce-homokozó példányát, majd válassza ki a **kiépítés** lapot.

1. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőfelvétel: a Salesforce-kiépítési mód a kiépítési móddal automatikus és más beállítható értékekre van beállítva.](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a következő konfigurációs beállításokat:
   
    a. A **rendszergazda felhasználóneve** szövegmezőbe írja be a Salesforce.com hozzárendelt **rendszergazdai** profillal rendelkező Salesforce.
   
    b. A **rendszergazdai jelszó** szövegmezőbe írja be a fiókhoz tartozó jelszót.

1. A Salesforce-beli sandbox biztonsági jogkivonat beszerzéséhez nyisson meg egy új lapot, és jelentkezzen be ugyanahhoz a Salesforce sandbox-beli rendszergazdai fiókba. A lap jobb felső sarkában kattintson a nevére, majd a **Beállítások** elemre.

     ![Képernyőfelvétel: a Beállítások hivatkozás kiválasztva.](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Automatikus felhasználó-kiépítés engedélyezése")

1. A bal oldali navigációs panelen kattintson a **személyes adatok** elemre a kapcsolódó szakasz kibontásához, majd kattintson a **biztonsági jogkivonat alaphelyzetbe állítása** elemre.
  
    ![Képernyőfelvétel: a személyes adatokból kiválasztott biztonsági jogkivonat alaphelyzetbe állítása.](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Automatikus felhasználó-kiépítés engedélyezése")

1. A **biztonsági jogkivonat alaphelyzetbe** állítása lapon kattintson a **biztonsági jogkivonat alaphelyzetbe állítása** gombra.

    ![Képernyőfelvétel a REST biztonsági jogkivonat oldalon, a magyarázó szöveggel és a biztonsági jogkivonat alaphelyzetbe állítására szolgáló lehetőséggel](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Automatikus felhasználó-kiépítés engedélyezése")

1. Keresse meg a rendszergazdai fiókhoz társított e-mailek mappáját. Keressen egy e-mailt a Salesforce Sandbox.com, amely az új biztonsági jogkivonatot tartalmazza.

1. Másolja ki a tokent, nyissa meg az Azure AD-ablakot, és illessze be a **titkos jogkivonat** mezőbe.

1. A Azure Portalban kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak a Salesforce homokozó-alkalmazáshoz.

1. Az **értesítő e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek a kiépítési hibákra vonatkozó értesítéseket kell kapnia, majd jelölje be a jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**  
    
1.  A leképezések szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók Salesforce a homokozóban lehetőséget.**

1. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből szinkronizált felhasználói attribútumokat a Salesforce homokozóban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Salesforce-homokozóban lévő felhasználói fiókoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások véglegesítéséhez válassza a Mentés gombot.

1. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Salesforce homokozóban, módosítsa a **kiépítési állapotot** **a következőre** a beállítások szakaszban:

1. Kattintson a **Mentés gombra.**

Elindítja a felhasználók és csoportok szakaszban a Salesforce-Sandboxhoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a Salesforce homokozó alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](./salesforce-sandbox-tutorial.md)