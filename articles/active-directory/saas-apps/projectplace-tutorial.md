---
title: 'Oktatóanyag: Azure Active Directory integráció a Projectplace-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Projectplace között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.openlocfilehash: 508a3ca02ec6c0475e78d16691f6597e1a389f97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511851"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Oktatóanyag: a Projectplace és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Projectplace a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Projectplace-t az Azure AD-vel, a következőket teheti:

* A Projectplace-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Projectplace az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.
* A felhasználók a Projectplace automatikusan üzembe helyezhetők.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Projectplace egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. **A Projectplace az** **SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja, és a felhasználói üzembe helyezést is támogatja.

## <a name="adding-projectplace-from-the-gallery"></a>Projectplace hozzáadása a gyűjteményből

A Projectplace Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Projectplace a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Projectplace** kifejezést a keresőmezőbe.
1. Válassza ki a **Projectplace** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Projectplace a **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Projectplace-ben.

Az Azure AD SSO és a Projectplace konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. **[Konfigurálja a Projectplace](#configure-projectplace)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre Projectplace-tesztelési felhasználót](#create-projectplace-test-user)** , hogy a Projectplace B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Projectplace** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni az alkalmazást, az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:  `https://service.projectplace.com`

1. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a másolás **ikonra** az **alkalmazás-összevonási metaadatok URL-címének** másolásához, a követelménynek megfelelően, és mentse azt a Jegyzettömbben.

   ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

1. A **Projectplace beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Projectplace konfigurálása

Az egyszeri bejelentkezés az **Projectplace** oldalon való konfigurálásához el kell küldenie a másolt **alkalmazás-összevonási metaadatok URL-címét** a Azure Portalról a [Projectplace támogatási csapatához](https://success.planview.com/Projectplace/Support). Ez a csapat biztosítja, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

>[!NOTE]
>Az egyszeri bejelentkezés konfigurációját a [Projectplace támogatási csapatának](https://success.planview.com/Projectplace/Support)kell végrehajtania. Amint a konfiguráció elkészült, értesítést kap. 

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Projectplace.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Projectplace** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-projectplace-test-user"></a>Projectplace-tesztelési felhasználó létrehozása

>[!NOTE]
>Ezt a lépést kihagyhatja, ha a Projectplace-ben engedélyezve van a kiépítés. Megkérheti a [Projectplace támogatási csapatát](https://success.planview.com/Projectplace/Support) , hogy engedélyezze a provisoning-t, miután a felhasználók az első bejelentkezéskor a Projectplace-ben jönnek létre.

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a Projectplace, hozzá kell adnia őket a Projectplace-hez. Ezeket manuálisan kell felvennie.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Projectplace** vállalati webhelyre rendszergazdaként.

2. Lépjen a **személyek** elemre, majd válassza a **tagok** elemet:
   
    ![Lépjen a személyek elemre, majd válassza a tagok lehetőséget.](./media/projectplace-tutorial/ic790228.png "People")

3. Válassza a **tag hozzáadása** lehetőséget:
   
    ![Tag hozzáadása lehetőség kiválasztása](./media/projectplace-tutorial/ic790232.png "Tagok hozzáadása")

4. A **tag hozzáadása** szakaszban hajtsa végre a következő lépéseket.
   
    ![Tag hozzáadása szakasz](./media/projectplace-tutorial/ic790233.png "Új tagok")
   
    1. Az **új tagok** mezőben adja meg a hozzáadni kívánt érvényes Azure ad-fiók e-mail-címét.
   
    1. Kattintson a **Küldés** gombra.

   Egy e-mailt, amely egy hivatkozást tartalmaz, amely megerősíti a fiókot, mielőtt az aktívvá válik az Azure AD-fiók tulajdonosának.

>[!NOTE]
>Az Azure AD felhasználói fiókjainak hozzáadásához a Projectplace által biztosított bármely más felhasználói fiók létrehozására szolgáló eszközt vagy API-t is használhatja.


### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a Projectplace csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Projectplace, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi a feltételes hozzáférés a Azure Active Directory?](../conditional-access/overview.md)