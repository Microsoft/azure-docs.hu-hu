---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a MongoDB Cloud | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory És a MongoDB Cloud között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: 5904d3eeec3f5880213f8a8c6a41cefbe76801b3
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520088"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a MongoDB-felhővel

Ez az oktatóanyag bemutatja, hogyan integrálhatja a MongoDB Cloudot a Azure Active Directory (Azure AD) használatával. Ha integrálja a MongoDB-felhőt az Azure AD-val, a következőt tudja:

* Az Azure AD vezérlése, aki hozzáfér a MongoDB Cloudhoz, a MongoDB Atlashoz, a MongoDB-közösséghez, a MongoDB Universityhöz és a MongoDB-támogatáshoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve lesznek a MongoDB-felhőbe az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* MongoDB Cloud single sign-on (SSO) enabled subscription (A MongoDB felhőalapú egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD SSO-t.

* A MongoDB Cloud támogatja **az sp** és az **idP által** kezdeményezett SSO-t.
* A MongoDB Cloud támogatja **az időben való felhasználóátépítést.**

## <a name="add-mongodb-cloud-from-the-gallery"></a>MongoDB Cloud hozzáadása a katalógusból

A MongoDB-felhő Azure AD-be való integrálásának konfigurálához hozzá kell adni a MongoDB Cloudot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a Vállalati **alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a Keresőmezőbe a **MongoDB Cloud (MongoDB-felhő)** parancsot.
1. Válassza **a MongoDB Cloud** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-mongodb-cloud"></a>Azure AD SSO konfigurálása és tesztelése MongoDB-felhőhöz

Konfigurálja és tesztelje az Azure AD SSO-t a MongoDB Cloud használatával egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, összekapcsolt kapcsolatot kell létesítenie egy Azure AD-felhasználó és a mongoDB-felhőben a kapcsolódó felhasználó között.

Az Azure AD SSO MongoDB-felhővel való konfiguráláshoz és teszteléshez hajtsa végre a következő lépéseket:

1. [Konfigurálja úgy az Azure AD SSO-t,](#configure-azure-ad-sso) hogy a felhasználók használják ezt a funkciót.
    1. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének tesztelésére a B.Simon használatával.
    1. [Rendelje hozzá az Azure AD-tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy engedélyezze a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. [Konfigurálja a MongoDB felhőalapú SSO-t](#configure-mongodb-cloud-sso) az egyszeri bejelentkezési beállítások alkalmazásoldalon való konfigurálához.
    1. [Hozzon létre egy Felhőalapú MongoDB-tesztfelhasználót,](#create-a-mongodb-cloud-test-user) hogy a MongoDB-felhőben a B.Simon megfelelője legyen, amely a felhasználó Azure AD-ábrázolásán van összekapcsolva.
1. [Az SSO tesztelése](#test-sso) annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A [Azure Portal](https://portal.azure.com/)a **MongoDB** Felhőalkalmazás-integráció lapján keresse meg a **Kezelés** szakaszt. Válassza **az egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer kiválasztása lapon** válassza az **SAML lehetőséget.**
1. A **Set up Single Sign-On with SAML** (Egyetlen alkalmazás beállítása SAML-sel) lapon válassza az SamL-alapkonfiguráció ceruzaikonját a beállítások szerkesztéséhez. 

   ![Képernyőkép a Set up Single Sign-On with SAML (SamL-alkalmazás beállítása) lapról, kiemelt ceruza ikonnal](common/edit-urls.png)

1. Ha az **SAML-konfiguráció** alapszintű szakaszában az alkalmazást **idP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az Azonosító **szövegmezőbe** írjon be egy URL-címet, amely a következő mintát használja: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. A Válasz **URL-cím** szövegmezőbe írjon be egy URL-címet, amely a következő mintát használja: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Válassza **a További URL-címek beállítása** lehetőséget, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni:

    A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet, amely a következő mintát használja:  `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-lel. Ezek az értékek a [MongoDB felhőalapú ügyfelének támogatási csapatához ( ) vannak felveveve.](https://support.mongodb.com/) Az **SAML** alapkonfiguráció szakaszában látható mintákra is hivatkozhat a Azure Portal.

1. A MongoDB felhőalkalmazás azt várja, hogy az SAML helyességi feltételek egy adott formátumban lesznek, amelyhez egyéni attribútumleképezéseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjában. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Az alapértelmezett attribútumok képernyőképe](common/default-attributes.png)

1. A fenti attribútumok mellett a MongoDB Felhőalkalmazás arra számít, hogy néhány további attribútumot is vissza kell adni az SAML-válaszba. Ezek az attribútumok is előre ki vannak töltve, de a követelményeknek megfelelően áttekintheti őket.
    
    | Name | Forrásattribútum|
    | ---------------| --------- |
    | e-mail | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lap **SAML** aláíró tanúsítvány szakaszában keresse meg az Összevonási metaadatok **XML-fájlját.** Válassza **a Letöltés lehetőséget** a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![Képernyőkép az SAML aláíró tanúsítvány szakaszról, kiemelt Letöltés hivatkozással](common/metadataxml.png)

1. A **MongoDB-felhő beállítása szakaszban** másolja ki a követelményeinek megfelelő URL-címeket.

    ![Képernyőkép a Mongo DB Felhő beállítása szakaszról, kiemelt URL-címekkel](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót Azure Portal a következő szakaszban: .

1. A bal oldali panelen válassza Azure Portal Felhasználók **Azure Active Directory**  >    >  **lehetőséget.**
1. Válassza **az Új felhasználó** lehetőséget a képernyő tetején.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszót.
   1. Válassza a **Létrehozás** lehetőséget.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát a MongoDB Cloudhoz való hozzáférés engedélyezésével.

1. A Azure Portal válassza a **Vállalati alkalmazások,** majd a **Minden alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza a **MongoDB-felhő lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-mongodb-cloud-sso"></a>A MongoDB felhőalapú SSO konfigurálása

Ha egyszeri bejelentkezést konfigurál a MongoDB felhő oldalán, szüksége lesz a megfelelő URL-címekre a Azure Portal. Az összevonási alkalmazást a MongoDB-felhőszervezethez is konfigurálnia kell. Kövesse a [MongoDB Felhő dokumentációjában található utasításokat.](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/) Ha problémája van, lépjen kapcsolatba a [MongoDB felhőtámogatási csapatával.](https://support.mongodb.com/)

### <a name="create-a-mongodb-cloud-test-user"></a>Felhőalapú MongoDB-tesztfelhasználó létrehozása

A MongoDB Cloud támogatja az alapértelmezés szerint engedélyezett, időben elérhető felhasználókiépítést. Nincs további teendő. Ha a felhasználó még nem létezik a MongoDB Cloudban, a hitelesítés után létrejön egy új.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

#### <a name="sp-initiated"></a>Sp kezdeményezve:

* Kattintson az **Alkalmazás tesztelése elemre a** Azure Portal. Ez átirányítja a MongoDB felhőbeli bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen közvetlenül a MongoDB felhőbe való bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>Az internetszolgáltató által kezdeményezett:

* Kattintson az **Alkalmazás tesztelése a** Azure Portal lehetőségre, és automatikusan be kell jelentkezve lennie arra a MongoDB-felhőre, amelyhez beállította az SSO-t. 

A Microsoft Saját alkalmazások bármilyen módban tesztelheti az alkalmazást. Ha az Saját alkalmazások-ban a MongoDB Felhő csempére kattint, a rendszer átirányítja az alkalmazás bejelentkezési oldalára a bejelentkezési folyamat kezdeményezésére, és ha idP módban van konfigurálva, automatikusan be kell jelentkeznie arra a MongoDB-felhőre, amelyhez beállította az SSO-t. További információ a Saját alkalmazások: [Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

A MongoDB Cloud konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
