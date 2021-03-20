---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Comm100 Live chattel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Comm100 élő csevegés között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 9636c8cbb517c7aece450f53cfc37e4ddd9803b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455493"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Comm100 élő csevegéssel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Comm100 élő csevegést Azure Active Directory (Azure AD) használatával. Ha integrálja a Comm100 élő csevegést az Azure AD-vel, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Comm100 élő csevegéshez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy Comm100 az élő csevegést az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Comm100 az élő csevegéses egyszeri bejelentkezés (SSO) engedélyezett előfizetését.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Comm100 élő csevegés támogatja az **SP** által KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Élő csevegés Comm100 hozzáadása a katalógusból

A Comm100 élő csevegés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Comm100 élő csevegést a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Comm100 élő csevegés** kifejezést a keresőmezőbe.
1. Válassza a **Comm100 élő csevegés** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése Comm100 élő csevegéshez

Konfigurálja és tesztelje az Azure AD SSO-t a Comm100 Live Chat használatával egy **B. Simon** nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az élő csevegés Comm100.

Az Azure AD SSO és a Comm100 élő csevegés konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Comm100 élő csevegéses SSO konfigurálása](#configure-comm100-live-chat-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Comm100 élő csevegési teszt felhasználó létrehozása](#create-comm100-live-chat-test-user)** – hogy a B. Simon Comm100 élő csevegéssel rendelkezzen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Comm100 élő csevegés** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > A bejelentkezési URL-cím értéke nem valós. A bejelentkezési URL-címet a tényleges bejelentkezési URL-címmel fogja frissíteni, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Az Comm100 Live Chat-alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Comm100 élő csevegési alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre is fel vannak töltve, de a követelménynek megfelelően áttekintheti őket.

    | Name |  Forrás attribútum|
    | ---------------| --------------- |
    |   e-mail    | User. mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Comm100 élő csevegés beállítása** szakaszban a követelmények alapján másolja ki a megfelelő URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát, ha hozzáférést biztosít a Comm100 élő csevegéshez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Comm100 élő csevegés** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-comm100-live-chat-sso"></a>Comm100 élő csevegéses egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az élő csevegés Comm100 biztonsági rendszergazdaként.

1. A lap jobb felső részén kattintson a **saját fiók** elemre.

   ![Comm100 élő csevegés MyAccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. A menü bal oldalán kattintson a **Biztonság** elemre, majd az **ügynök egyszeri bejelentkezés** elemre.

   ![Képernyőkép, amely a bal oldali fiók menüt jeleníti meg a "biztonság" és az "ügynök egyszeri bejelentkezés" kiemelésével.](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Az **ügynök egyszeri bejelentkezés** lapján hajtsa végre a következő lépéseket:

   ![Comm100 élő csevegés biztonsága](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Másolja ki az első Kiemelt hivatkozást, és illessze be a **bejelentkezési URL** szövegmezőbe a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában.

   b. Az **SAML SSO URL** szövegmezőbe illessze be a **bejelentkezési URL**-címet, amelyet a Azure Portal másolt.

   c. A **távoli kijelentkezési URL-cím** szövegmezőbe illessze be a **KIjelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

   d. Kattintson a **fájl kiválasztása** lehetőségre a Azure Portalból letöltött base-64 kódolt tanúsítvány feltöltéséhez a **tanúsítványba**.

   e. Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-comm100-live-chat-test-user"></a>Comm100 élő csevegési teszt felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek az élő csevegés Comm100, a Comm100 élő csevegésbe kell kiépíteni őket. Az élő csevegés Comm100 a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az élő csevegés Comm100 biztonsági rendszergazdaként.

2. A lap jobb felső részén kattintson a **saját fiók** elemre.

    ![Comm100 élő csevegés MyAccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. A menü bal oldalán kattintson az **ügynökök** elemre, majd az **új ügynök** elemre.

    ![Comm100 élő csevegési ügynök](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Az **új ügynök** oldalon hajtsa végre a következő lépéseket:

    ![Comm100 élő csevegés – új ügynök](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Az **e-mail** szövegmezőbe írja be a következőhöz hasonló felhasználó e-mail címét: **B. Simon \@ contoso.com**.

    b. Az **Utónév** szövegmezőbe írja be a (z) " **B**" felhasználó utónevét.

    c. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon** nevet.

    d. A **megjelenítendő név** szövegmezőbe írja be a felhasználó megjelenítendő nevét, például **B. Simon**

    e. A **jelszó** szövegmezőbe írja be a jelszót.

    f. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Comm100 élő csevegés csempére kattint, automatikusan be kell jelentkeznie a Comm100 Live Chat szolgáltatásba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Próbálja ki a Comm100 élő csevegést az Azure AD-vel](https://aad.portal.azure.com/)