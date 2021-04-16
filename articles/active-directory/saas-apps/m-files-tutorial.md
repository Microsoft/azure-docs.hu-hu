---
title: 'Oktatóanyag: Azure Active Directory M-Files-fájlokkal való integráció | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory M-Files között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 680380722746522a0eb3fe6518452472be952075
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482820"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Oktatóanyag: Azure Active Directory M-Files-integráció

Ez az oktatóanyag bemutatja, hogyan integrálhatja az M-Filest az Azure Active Directory (Azure AD) használatával. Ha integrálja az M-Filest az Azure AD-val, a következő funkciókat használhatja:

* Vezérlés az Azure AD-ban, aki hozzáféréssel rendelkezik az M-Files-hoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve jelentkeznek be az M-Filesba az Azure AD-fiókjukkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésekhez a következő elemekre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, egy ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* M-Files egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezését konfigurálja és teszteli tesztkörnyezetben.

* Az M-Files támogatja **az SP által** kezdeményezett SSO-t.

## <a name="add-m-files-from-the-gallery"></a>M-Files hozzáadása a katalógusból

Ahhoz, hogy konfigurálni tudja az M-Files integrálását az Azure AD-be, hozzá kell adni az M-Filest a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal vagy személyes fiókkal Microsoft-fiók.
1. A bal oldali navigációs panelen válassza ki **a Azure Active Directory** szolgáltatást.
1. Lépjen a **Vállalati alkalmazások lapra,** majd válassza a **Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az Új **alkalmazás lehetőséget.**
1. A Hozzáadás **a katalógusból szakaszban** írja be a keresőmezőbe az **M-Files** parancsot.
1. Válassza **az M-Files lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzá lesz adva a bérlőhöz.

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>Az Azure AD SSO konfigurálása és tesztelése M-Fileshoz

Konfigurálja és tesztelje az Azure AD SSO-t az M-Files segítségével egy **B.Simon nevű tesztfelhasználóval.** Ahhoz, hogy az SSO működjön, kapcsolati kapcsolatot kell létesítenie egy Azure AD-felhasználó és a kapcsolódó felhasználó között az M-Filesban.

Az Azure AD SSO M-Files használatával való konfiguráláshoz és teszteléséhez hajtsa végre a következő lépéseket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy lehetővé tegye a felhasználók számára a funkció használatát.
    1. **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének tesztelése a B.Simon használatával.
    1. **[Rendelje hozzá az Azure AD-tesztfelhasználót](#assign-the-azure-ad-test-user)** – ezzel engedélyezheti a B.Simon számára az Azure AD egyszeri bejelentkezés használatát.
1. **[M-Files SSO konfigurálása](#configure-m-files-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[M-Files-tesztfelhasználó létrehozása](#create-m-files-test-user)** – a B.Simon megfelelője az M-Filesban, amely a felhasználó Azure AD-reprezentációjával van összekapcsolva.
1. **[Az SSO tesztelése](#test-sso)** – annak ellenőrzéséhez, hogy működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez a Azure Portal.

1. A Azure Portal **M-Files alkalmazásintegráció** lapján keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés lehetőséget.**
1. Az Egyszeri **bejelentkezési módszer** kiválasztása lapon válassza az **SAML lehetőséget.**
1. Az Egyszeri **bejelentkezés beállítása SAML-sel** lapon kattintson az SamL-alapkonfiguráció ceruza ikonjára a beállítások szerkesztéséhez. 

   ![SamL alapszintű konfigurációjának szerkesztése](common/edit-urls.png)

4. Az **SAML-alapkonfiguráció szakaszban** hajtsa végre a következő lépéseket:

    a. A **Bejelentkezési URL-cím** szövegmezőbe írjon be egy URL-címet a következő mintával: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. Az Azonosító **(Entitásazonosító) szövegmezőbe** írjon be egy URL-címet a következő mintával: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Ezek az értékek nem valódiak. Frissítse ezeket az értékeket a bejelentkezési URL-cím és az azonosító tényleges értékével. Ezeket [az értékeket az M-Files-ügyfél](mailto:support@m-files.com) támogatási csapatával kell kapcsolatba lépnie. A minta az **SAML-konfiguráció** alapszintű szakaszában látható mintákat is Azure Portal.

5. A **Set up Single Sign-On with SAML** (Egyszeri bejelentkezés beállítása SAML-tanúsítványsal) lapon, az **SAML signing Certificate (SAML-aláíró** tanúsítvány) szakaszban kattintson a **Download** (Letöltés) elemre az összevonási metaadatok **XML-fájljának** letöltéséhez a megadott beállításokból igény szerint, majd mentse a számítógépre.

    ![A Tanúsítvány letöltése hivatkozás](common/metadataxml.png)

6. Az **M-Files beállítása szakaszban** másolja ki a megfelelő URL-cím(eke)t a saját követelményének megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót fog létrehozni a Azure Portal területen.

1. A bal oldali panelen válassza a Azure Portal **lehetőséget,** Azure Active Directory **a Felhasználók** lehetőséget, majd válassza a Minden **felhasználó lehetőséget.**
1. Válassza **a képernyő tetején** található Új felhasználó lehetőséget.
1. A Felhasználó **tulajdonságai** között kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév **mezőbe írja** be a következőt: username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja fel a Jelszó mezőben megjelenő értéket. 
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti a B.Simon számára az Azure-beli egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az M-Files szolgáltatáshoz.

1. A Azure Portal válassza a Vállalati **alkalmazások,** majd a Minden **alkalmazás lehetőséget.**
1. Az alkalmazások listájában válassza az **M-Files lehetőséget.**
1. Az alkalmazás áttekintő oldalán keresse meg a Kezelés **szakaszt,** és válassza a **Felhasználók és csoportok lehetőséget.**
1. Válassza **a Felhasználó hozzáadása** lehetőséget, majd a Hozzárendelés hozzáadása **párbeszédpanelen válassza a** Felhasználók és **csoportok** lehetőséget.
1. A Felhasználók **és csoportok** párbeszédpanelen válassza a **B.Simon**  lehetőséget a Felhasználók listában, majd kattintson a képernyő alján található Kijelölés gombra.
1. Ha azt várja, hogy egy szerepkör hozzá lesz rendelve a felhasználókhoz, kiválaszthatja **azt a Szerepkör kiválasztása** legördülő menüből. Ha nincs beállítva szerepkör ehhez az alkalmazáshoz, az "Alapértelmezett hozzáférés" szerepkör van kiválasztva.
1. A Hozzárendelés **hozzáadása párbeszédpanelen** kattintson a Hozzárendelés **gombra.**

## <a name="configure-m-files-sso"></a>M-Files SSO konfigurálása

1. Az SSO az alkalmazáshoz való konfigurálásához lépjen kapcsolatba az [M-Files](mailto:support@m-files.com) támogatási csapatával, és adja meg nekik a letöltött metaadatokat.
   
    >[!NOTE]
    >Ha az SSO-t az M-File asztali alkalmazáshoz szeretné konfigurálni, kövesse a következő lépéseket. Nincs szükség további lépésekre, ha csak az M-Files webes verziójához szeretné konfigurálni az SSO-t.  

1. A következő lépésekkel konfigurálhatja az M-File asztali alkalmazást, hogy engedélyezze az SSO-t az Azure AD-val. Az M-Files letöltéséhez kattintson az [M-Files letöltési oldalára.](https://www.m-files.com/customers/product-downloads/download-update-links/)

1. Nyissa meg **az M-Files Asztali beállítások** ablakot. Ezután kattintson az **Add (Hozzáadás) gombra.**
   
    ![Képernyőkép az M-Files desktop beállításairól, ahol kiválaszthatja a Hozzáadás lehetőséget.](./media/m-files-tutorial/settings.png)

1. A **Document Vault kapcsolat tulajdonságai ablakban** hajtsa végre a következő lépéseket:
   
    ![Képernyőkép a Document Vault kapcsolati tulajdonságairól, ahol megadhatja a leírt értékeket.](./media/m-files-tutorial/general.png)  

    A Kiszolgáló szakaszban írja be az értékeket a következőképpen:  

    a. A **Név mezőbe** írja be a következőt: `<tenant-name>.cloudvault.m-files.com` . 
 
    b. A **Portszám mezőbe írja** be a következőt: **4466.** 

    c. A **Protokoll beállításhoz** válassza a **HTTPS lehetőséget.** 

    d. A Hitelesítés **mezőben** válassza az **Adott Windows-felhasználó lehetőséget.** Ezután a rendszer egy aláírási oldalt kér. Szúrja be az Azure AD hitelesítő adatait. 

    e. A Vault **on Server (Tároló a kiszolgálón) mezőben** válassza ki a megfelelő tárolót a kiszolgálón.
 
    f. Kattintson az **OK** gombra.

### <a name="create-m-files-test-user"></a>M-Files-tesztfelhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása az M-Filesban. Az  [M-Files támogatási csapatával együtt](mailto:support@m-files.com) vegye fel a felhasználókat az M-Filesba.

## <a name="test-sso"></a>Az SSO tesztelése 

Ebben a szakaszban az alábbi beállításokkal teszteli az Azure AD egyszeri bejelentkezési konfigurációját. 

* Kattintson az **Alkalmazás tesztelése elemre** a Azure Portal. Ez átirányítja az M-Files bejelentkezési URL-címre, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül az M-Files bejelentkezési URL-címére, és onnan indítsa el a bejelentkezési folyamatot.

* Használhatja a Microsoft Saját alkalmazások. Amikor az M-Files csempére kattint a Saját alkalmazások, a rendszer átirányítja az M-Files bejelentkezési URL-címére. További információ a [Saját alkalmazások: Bevezetés a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)Saját alkalmazások.

## <a name="next-steps"></a>Következő lépések

Az M-Files konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáféréstől terjed ki. [Ismerje meg, hogyan kényszeríthető ki a munkamenet-vezérlés a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)
