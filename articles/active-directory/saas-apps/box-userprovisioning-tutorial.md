---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés beállítása a Azure Active Directoryhoz | Microsoft Docs'
description: Útmutató az Azure Active Directory és a Box közötti egyszeri bejelentkezés konfigurálásához.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: df4031a590eb4547d4327cebe96ccbe63d21785a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437808"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Oktatóanyag: a felhasználók automatikus üzembe helyezésének beállítása

Ennek az oktatóanyagnak a célja, hogy megmutassa a box-ban elvégzendő lépéseket, valamint az Azure AD-t, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Box-ba.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Box-nal való konfigurálásához a következő elemek szükségesek:

- Azure AD-bérlő
- Box üzleti terv vagy jobb

> [!NOTE]
> Ha teszteli az oktatóanyag lépéseit, javasoljuk, hogy *ne használjon éles* környezetet.

> [!NOTE]
> Először engedélyeznie kell az alkalmazásokat a Box alkalmazásban.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha nem rendelkezik Azure AD-próbaverzióval, egy [hónapos próbaverziót is beszerezhet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Felhasználók kiosztása a box-ban 

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. A felhasználói fiókok automatikus kiosztásának kontextusában a rendszer csak azokat a felhasználókat és csoportokat szinkronizálja, amelyeket az Azure AD-alkalmazáshoz rendeltek.

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai képviselik a Box-alkalmazáshoz hozzáférő felhasználókat. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a Box-alkalmazáshoz:

[Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="assign-users-and-groups"></a>Felhasználók és csoportok társítása
> a Azure Portal **felhasználók és csoportok** lapján megadhatja, hogy mely felhasználók és csoportok számára legyen elérhető a Box. Egy felhasználó vagy csoport hozzárendelése a következő okok miatt fordul elő:

* Az Azure AD lehetővé teszi a hozzárendelt felhasználó (közvetlen hozzárendelés vagy csoporttagság alapján) hitelesítését a box-ban. Ha a felhasználó nincs hozzárendelve, akkor az Azure AD nem engedélyezi, hogy bejelentkezzen a mezőbe, és hibát ad vissza az Azure AD bejelentkezési oldalán.
* A rendszer a felhasználó [alkalmazás-indítójának](../manage-apps/end-user-experiences.md)adja hozzá a mezőhöz tartozó alkalmazás-csempét.
* Ha az automatikus kiépítés engedélyezve van, akkor a hozzárendelt felhasználók és/vagy csoportok automatikusan kiépítve lesznek hozzáadva a kiépítési sorhoz.
  
  * Ha csak a felhasználói objektumok lettek kiépítve, akkor az összes közvetlenül hozzárendelt felhasználó a kiépítési várólistába kerül, és az összes hozzárendelt csoportba tartozó felhasználó a kiépítési várólistába kerül. 
  * Ha a csoportosítási objektumok úgy lettek konfigurálva, hogy kiépítve legyenek, akkor a rendszer az összes hozzárendelt csoportot kiépíti a Box-ba, és minden olyan felhasználót, aki ezen csoportok tagjai. A csoport-és felhasználói tagságok megmaradnak a box-ban való íráskor.

A következő **> attribútumokkal** konfigurálhatja, hogy mely felhasználói attribútumok (vagy jogcímek) jelennek meg a Box számára az SAML-alapú hitelesítés során, valamint az **attribútumok > kiépítés** lapon, hogy a felhasználói és a csoport attribútumai hogyan áramlanak be az Azure ad-ből a kiépítési műveletek során.

### <a name="important-tips-for-assigning-users-to-box"></a>Fontos Tippek a felhasználók a Box-hoz való hozzárendeléséhez 

*   Azt javasoljuk, hogy egyetlen Azure AD-felhasználóhoz legyen rendelve a kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

*   Egy felhasználó Box-hoz rendeléséhez ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="enable-automated-user-provisioning"></a>Automatikus felhasználó-kiépítés engedélyezése

Ez a szakasz azt ismerteti, hogyan csatlakoztatható az Azure AD a Box felhasználói fiók létesítési API-hoz, és hogyan konfigurálható a kiépítési szolgáltatás az Azure AD-ben a felhasználó-és csoport-hozzárendelésen alapuló hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

Ha az automatikus kiépítés engedélyezve van, akkor a hozzárendelt felhasználók és/vagy csoportok automatikusan kiépítve lesznek hozzáadva a kiépítési sorhoz.
    
 * Ha csak a felhasználói objektumok vannak konfigurálva, akkor a közvetlenül hozzárendelt felhasználókat a kiépítési várólistába helyezi, és minden hozzárendelt csoportba tartozó felhasználó a kiépítési várólistába kerül. 
    
 * Ha a csoportosítási objektumok úgy lettek konfigurálva, hogy kiépítve legyenek, akkor a rendszer az összes hozzárendelt csoportot kiépíti a Box-ba, és minden olyan felhasználót, aki ezen csoportok tagjai. A csoport-és felhasználói tagságok megmaradnak a box-ban való íráskor.

> [!TIP] 
> Azt is megteheti, hogy engedélyezte az SAML-alapú egyszeri Sign-On a Box számára, a [Azure Portalban](https://portal.azure.com)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-account-provisioning"></a>A felhasználói fiókok automatikus üzembe helyezésének konfigurálása:

Ennek a szakasznak a célja annak ismertetése, hogyan engedélyezhető Active Directory felhasználói fiókok kiépítés a Box-ba.

1. A [Azure Portal](https://portal.azure.com)keresse meg a **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** szakaszt.

2. Ha már konfigurálta az egyszeri bejelentkezést, a keresőmező használatával keresse meg a kívánt példányt. Ellenkező esetben válassza a **Hozzáadás** elemet, és keresse meg a **szövegmezőt** az alkalmazás-galériában. Jelölje ki a jelölőnégyzetet a keresési eredmények közül, és adja hozzá az alkalmazások listájához.

3. Válassza ki a Box-példányát, majd válassza a **kiépítés** lapot.

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre. 

    ![Képernyőkép a kiépítés lapról a Azure Portal. A kiépítési mód beállítása automatikus, és az engedélyezés beállítás a rendszergazdai hitelesítő adatokban van kiemelve.](./media/box-userprovisioning-tutorial/provisioning.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés** elemre egy új böngészőablakban a Box bejelentkezési párbeszédpanel megnyitásához.

6. A **belépés a Box** -ba lapon adja meg a szükséges hitelesítő adatokat, majd kattintson az **Engedélyezés** gombra. 
   
    ![Képernyőkép a bejelentkezéshez, hogy hozzáférést biztosítson a Box képernyőhöz, amely megjeleníti az e-mailek és a jelszó bejegyzését, valamint az Engedélyezés gombot.](./media/box-userprovisioning-tutorial/IC769546.png "Automatikus felhasználó-kiépítés engedélyezése")

7. A művelet engedélyezéséhez és a Azure Portalhoz való visszatéréshez kattintson a **hozzáférés engedélyezése jelölőnégyzetre** . 
   
    ![Képernyőkép: a hozzáférés engedélyezése képernyő a box-ban, egy magyarázó üzenet és a hozzáférés engedélyezése a Box-hoz gombra.](./media/box-userprovisioning-tutorial/IC769549.png "Automatikus felhasználó-kiépítés engedélyezése")

8. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a Box-alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Box-fiókja rendelkezik rendszergazdai jogosultságokkal, majd próbálja megismételni az **"engedélyezés"** lépést.

9. Adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia az **értesítő e-mailek** mezőben, majd jelölje be a jelölőnégyzetet.

10. Kattintson a **Mentés gombra.**

11. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása jelölőnégyzetet.**

12. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből a Box-ba szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használt felhasználói fiókokkal egyeznek meg. A módosítások véglegesítéséhez válassza a Mentés gombot.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Box számára, módosítsa a **kiépítési állapotot** **a következőre** a Settings (beállítások) szakaszban.

14. Kattintson a **Mentés gombra.**

Ezzel elindítja a felhasználók és csoportok szakaszban a mezőhöz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg a szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység naplóira mutató hivatkozásokat, amelyek leírják a kiépítési szolgáltatás által a Box alkalmazásban végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

A Box-bérlőben a szinkronizált felhasználók a **felügyelt felhasználók** területen jelennek meg a **felügyeleti konzolon**.

![Integrációs állapot](./media/box-userprovisioning-tutorial/IC769556.png "Integrációs állapot")


## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Egyszeri bejelentkezés konfigurálása](box-tutorial.md)