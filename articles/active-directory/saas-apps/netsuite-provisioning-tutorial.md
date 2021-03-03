---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés a NetSuite-OneWorld konfigurálása a Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a NetSuite OneWorld között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b1c03bafd6d97dd6a60defee00d4efe854315631
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648084"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó üzembe helyezéséhez szükséges a NetSuite konfigurálása

Ennek az oktatóanyagnak a célja, hogy megmutassa a NetSuite OneWorld és az Azure AD-ben elvégzendő lépéseket, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a NetSuite-ba.

> [!WARNING]
> Ez a kiépítési integráció nem fog működni a Microsoft által a NetSuite-ba való kiépítéséhez használt NetSuite API-k változása miatti, a 2021-as Spring frissítésének kiadásával.  Ez a frissítés a NetSuite-ügyfeleket 2021 február és április között éri el. Ennek eredményeképpen a rendszer hamarosan eltávolítja a NetSuite alkalmazás kiépítési funkcióit a Azure Active Directory Enterprise app Galleryben. Az alkalmazás egyszeri bejelentkezéses funkciója érintetlen marad. A Microsoft a NetSuite-nal együttműködve új, modernizált kiépítési integrációt hoz létre, de jelenleg nincs olyan ETA, amelyen ez a művelet befejeződik.

## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

*   Egy Azure Active Directory-bérlő.
*   Egy NetSuite OneWorld-előfizetés. Vegye figyelembe, hogy az automatikus felhasználó-kiépítés jelenleg csak a NetSuite OneWorld támogatott.
*   Felhasználói fiók a NetSuite-ban rendszergazdai engedélyekkel.
*   Az Azure AD-integrációhoz 2FA kivétel szükséges. Ha ezt a kivételt szeretné kérni, forduljon a NetSuite támogatási csapatához.

## <a name="assigning-users-to-netsuite-oneworld"></a>Felhasználók kiosztása a NetSuite OneWorld

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználók és/vagy csoportok szerepelnek az Azure AD-ben azon felhasználók számára, akiknek hozzáférésre van szükségük a NetSuite-alkalmazáshoz. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a NetSuite-alkalmazáshoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Fontos Tippek a felhasználók a NetSuite-OneWorld való hozzárendeléséhez

*   Az üzembe helyezési konfiguráció teszteléséhez egyetlen Azure AD-felhasználó van hozzárendelve a NetSuite-hez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

*   Amikor felhasználót rendel a NetSuite-hoz, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="enable-user-provisioning"></a>A felhasználó üzembe helyezésének engedélyezése

Ez a szakasz végigvezeti az Azure AD-nek a NetSuite felhasználói fiók létesítési API-hoz való csatlakoztatásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználó-és csoport-hozzárendelésen alapuló hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

> [!TIP] 
> Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri Sign-On a NetSuite számára, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-user-account-provisioning"></a>A felhasználói fiókok üzembe helyezésének konfigurálása:

Ennek a szakasznak a célja annak ismertetése, hogyan engedélyezhető Active Directory felhasználói fiókok telepítése a NetSuite-ra.

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

1. Ha már konfigurálta a NetSuite-t az egyszeri bejelentkezéshez, keressen rá a NetSuite-példányra a keresőmező használatával. Ellenkező esetben kattintson a **Hozzáadás** gombra, és keressen rá a **NetSuite** kifejezésre az alkalmazás-katalógusban. A keresési eredmények közül válassza ki a NetSuite elemet, majd adja hozzá az alkalmazások listájához.

1. Válassza ki a NetSuite példányát, majd válassza a **kiépítés** lapot.

1. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre. 

    ![Képernyőfelvétel: a NetSuite kiépítési lapja, a kiépítési mód pedig az automatikus és más beállítható értékekre van beállítva.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a következő konfigurációs beállításokat:
   
    a. A **rendszergazda felhasználóneve** szövegmezőbe írja be azt a NetSuite-fióknevet, amelynek a NetSuite.com hozzá van rendelve a **rendszergazda** profilja.
   
    b. A **rendszergazdai jelszó** szövegmezőbe írja be a fiókhoz tartozó jelszót.
      
1. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a NetSuite-alkalmazáshoz.

1. Az **értesítő e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek a kiépítési hibákra vonatkozó értesítéseket kell kapnia, majd jelölje be a jelölőnégyzetet.

1. Kattintson a **Mentés gombra.**

1. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a NetSuite-** ra lehetőséget.

1. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből a NetSuite-ra szinkronizált felhasználói attribútumokat. Vegye figyelembe, hogy a **megfelelő** tulajdonságokként kiválasztott attribútumok a NetSuite felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások véglegesítéséhez válassza a Mentés gombot.

1. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a NetSuite-hoz, módosítsa a **kiépítési állapotot** a következőre a beállítások **szakaszban:**

1. Kattintson a **Mentés gombra.**

Elindítja a felhasználók és csoportok szakaszban a NetSuitehoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. Vegye figyelembe, hogy a kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a NetSuite-alkalmazáson végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](netsuite-tutorial.md)
