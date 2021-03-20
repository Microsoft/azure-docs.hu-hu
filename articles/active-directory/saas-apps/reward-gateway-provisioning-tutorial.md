---
title: 'Oktatóanyag: jutalmazási átjáró konfigurálása az automatikus felhasználó-kiépítés Azure Active Directory használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az átjáró jutalmazására.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2d51903aff6f3fd1cd53d85a980f1b5dc2a893e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94354284"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Oktatóanyag: jutalmazási átjáró konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa a jutalmazási átjáró és Azure Active Directory (Azure AD) által elvégzendő lépéseket az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikus kiosztását és kiépítését automatikusan kiépítse és kiépítse az átjáró számára.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* Egy [jutalmazási átjáró bérlője](https://www.rewardgateway.com/).
* Egy felhasználói fiók a jutalmazási átjáróban rendszergazdai engedélyekkel.

## <a name="assigning-users-to-reward-gateway"></a>Felhasználók kiosztása a jutalmazási átjáróhoz 

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok hozzáférhessenek-e a jutalmazási átjáróhoz. A döntés megkezdése után ezeket a felhasználókat és/vagy csoportokat a felhasználók [vagy csoportok vállalati alkalmazásokhoz rendelése](../manage-apps/assign-user-or-group-access-portal.md)című részben leírtak szerint rendelheti hozzá az átjáró jutalmazásához.


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Fontos Tippek a felhasználók a jutalmazási átjáróhoz való hozzárendeléséhez 

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a jutalmazási átjáróhoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználóhoz rendeli a jutalmazási átjárót, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-reward-gateway--for-provisioning"></a>A kiépítés beállítása a jutalmazási átjáró számára
Mielőtt beállítja a jutalmazási átjárót az Azure AD-vel való automatikus felhasználó-kiépítés során, engedélyeznie kell a SCIM-létesítést a jutalmazási átjárón.

1. Jelentkezzen be a [jutalmazási átjáró felügyeleti konzolján](https://rewardgateway.photoshelter.com/login/). Kattintson az **integrációk** elemre.

    ![Képernyőkép a jutalmazási átjáró felügyeleti konzolján az integrációk lehetőség kiválasztásával.](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Válassza **a saját integráció** lehetőséget.

    ![Képernyőkép a két integrációs lehetőségről a saját integrációk lehetőséggel.](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Másolja a **scim URL-cím (v2)** és a **OAuth tulajdonosi jogkivonat** értékeit. Ezeket az értékeket a rendszer a jutalmazási átjáró alkalmazás létesítés lapjának bérlő URL-címe és titkos jogkivonat mezőjébe írja be a Azure Portal.

    ![Képernyőkép a saját integrációk panelről, a OAuth tulajdonosi jogkivonat szövegmezőben.](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Jutalmazási átjáró hozzáadása a katalógusból

Az Azure AD-vel való automatikus felhasználó-kiépítés jutalmazási átjárójának konfigurálásához hozzá kell adnia a jutalmazási átjárót az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**A jutalmazási átjáró az Azure AD alkalmazás-katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **jutalmazási átjáró** kifejezést, válassza a **jutalom** átjáró lehetőséget az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![A jutalmazási átjáró az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Az automatikus felhasználó-kiépítés beállítása az átjáró jutalmazására  

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján a jutalmazási átjáróban lévő felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a jutalmazási átjáró számára, a [jutalmazási átjáró egyszeri bejelentkezésének oktatóanyagában](reward-gateway-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Automatikus felhasználó-kiépítés beállítása a jutalmazási átjáróhoz az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **jutalmazási átjáró** elemet.

    ![A jutalmazási átjáró hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **scim URL-címét (v2)** és a **OAuth-tulajdonosi jogkivonat** azon értékeit, amelyeket a **bérlői URL-cím** és a **titkos jogkivonat** korábban lekért. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak a jutalmazási átjáróhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a jutalmazási átjáró fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza az **Azure Active Directory-felhasználók szinkronizálása az átjáró jutalmazásához** lehetőséget.

    ![A leképezések szakasz képernyőképe a Azure Active Directory felhasználók szinkronizálása az átjáró jutalmazására lehetőséggel.](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő átjáró jutalmazásához. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a jutalmazási átjáróban lévő felhasználói fiókoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Képernyőfelvétel: az attribútum-hozzárendelések szakasz, amely hat leképezést jelenít meg.](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a jutalmazási átjáró számára, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni az átjáró kiosztásához. Ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a jutalmazási átjárón végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

A jutalmazási átjáró jelenleg nem támogatja a csoportos kiépítés használatát.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

[Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)