---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a nitro hatékonyságnövelő csomaggal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a nitro hatékonyságnövelő csomag között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 3280c97e735e68aa36f018a8de59964ade9567b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181913"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a nitro hatékonyságnövelő csomaggal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a nitro Productivity Suite-t Azure Active Directory (Azure AD) használatával. A nitro hatékonyságnövelő csomag Azure AD-val való integrálásakor a következőket teheti:

* Szabályozza az Azure AD-t, aki hozzáfér a nitro hatékonyságnövelő csomaghoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a nitro hatékonyságnövelő csomagba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következők szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy nitro termelékenységi csomag [nagyvállalati előfizetése](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A nitro hatékonyságnövelő csomag támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést.
* A nitro Productivity Suite **csak időben támogatja a** felhasználók üzembe helyezését.

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>A nitro hatékonyságnövelő csomag hozzáadása a katalógusból

A nitro hatékonyságnövelő csomag Azure AD-integrációjának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a nitro hatékonyságnövelő csomagot.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali ablaktáblán válassza a **Azure Active Directory** lehetőséget.
1. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **nitro hatékonyságnövelő csomag** kifejezést a keresőmezőbe.
1. Válassza a **nitro hatékonyságnövelő csomag** lehetőséget az eredmények közül, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a nitro hatékonyságnövelő csomaghoz

Konfigurálja és tesztelje az Azure AD SSO-t a nitro hatékonyságnövelő csomaggal, egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a nitro Productivity Suite-ban.

Az Azure AD SSO és a nitro hatékonyságnövelő csomag konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.

    a. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    
    b. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
    
2. [Hozzon létre egy nitro termelékenységi csomag tesztelésére szolgáló felhasználót](#create-a-nitro-productivity-suite-test-user) , hogy az a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel rendelkezzen.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **nitro hatékonyságnövelő csomag** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt. Válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** szakaszt. A **Letöltés** gombra kattintva letöltheti a tanúsítványt, és mentheti a számítógépre.

    ![Képernyőfelvétel az SAML-aláíró tanúsítványról szakasz, a letöltési hivatkozás kiemelve](common/certificatebase64.png)
    
1. A **nitro hatékonyságnövelő csomag beállítása** szakaszban válassza a másolás ikont a **bejelentkezési URL-cím** mellett.
    
    ![Képernyőfelvétel a nitro hatékonyságnövelő csomag beállításáról, URL-címekkel és a másolási ikonok kiemelésével](common/copy-configuration-urls.png)
    
1. A [nitro felügyeleti portál](https://admin.gonitro.com/) **Vállalati beállítások** lapján keresse meg az **egyszeri bejelentkezés** szakaszt. Válassza az **SAML SSO beállítása** lehetőséget.

    a. Illessze be a **bejelentkezési URL-címet** az előző lépésben a **bejelentkezési URL-cím** mezőbe.
    
    b. Töltse fel a **tanúsítványt (Base64)** a **X509 aláíró tanúsítványa** mező korábbi lépésében.
    
    c. Válassza a **Küldés** lehetőséget.
    
    d. Válassza az **egyszeri bejelentkezés engedélyezése** lehetőséget.


1. Lépjen vissza az [Azure Portalra](https://portal.azure.com/). Az **egyszeri Sign-On beállítása az SAML-vel** lapon válassza az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikont a beállítások szerkesztéséhez.

   ![Képernyőfelvétel az egyszeri Sign-On az SAML-oldallal való beállításáról, a ceruza ikon kiemelve](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe másolja és illessze be az **SAML-entitás azonosítója** mezőt a [nitro felügyeleti portálról](https://admin.gonitro.com/). A következő mintát kell tartalmaznia: `urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. A **Válasz URL-címe** szövegmezőbe másolja és illessze be az **ACS URL-címét** a [nitro felügyeleti portálról](https://admin.gonitro.com/). A következő mintát kell tartalmaznia: `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Válassza a **további URL-címek beállítása** lehetőséget, majd hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:  `https://sso.gonitro.com/login`

1. Kattintson a **Mentés** gombra.

1. A nitro Productivity Suite-alkalmazás azt várja, hogy az SAML-állítások megadott formátumban legyenek, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Az alapértelmezett attribútumok képernyőképe](common/default-attributes.png)

1. Az előző attribútumok mellett a nitro Productivity Suite alkalmazás néhány további attribútumot vár, amelyeket vissza kell adni az SAML-válaszban. Ezek az attribútumok előre ki vannak töltve, de a követelmények szerint áttekinthetők.
    
    | Name  |  Forrás attribútum|
    | ---------------| --------------- |
    | employeeNumber |  User. ObjectId |


### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát a nitro hatékonyságnövelő csomag elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.
1. Válassza az alkalmazások lista **nitro hatékonyságnövelő csomag** elemét.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **Felhasználó hozzáadása** elemet. Ezután a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználók listájából a **B. Simon** elemet. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés** lehetőséget.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Egy nitro termelékenységi csomag tesztelési felhasználójának létrehozása

A nitro hatékonyságnövelő csomag támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Nincs szükség további műveletre. Ha egy felhasználó még nem létezik a nitro hatékonyságnövelő csomagban, akkor a hitelesítés után létrejön egy újat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>Az SP inicializálva:

1. Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez átirányítja a nitro Productivity Suite bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

2. Lépjen a nitro Productivity Suite bejelentkezési URL-címére, és indítsa el innen a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie arra a nitro hatékonyságnövelő csomagba, amelyhez be szeretné állítani az egyszeri bejelentkezést 

A Microsoft Access panel használatával bármilyen módban tesztelheti az alkalmazást. Ha a hozzáférési panelen a nitro hatékonyságnövelő csomag csempére kattint, ha az SP módban van konfigurálva, a rendszer átirányítja az alkalmazás bejelentkezési lapjára a bejelentkezési folyamat elindításához, és ha IDENTITÁSSZOLGÁLTATÓ módban van konfigurálva, automatikusan be kell jelentkeznie a nitro hatékonyságnövelő csomagba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Következő lépések

A nitro hatékonyságnövelő csomag konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezete bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáféréstől. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).