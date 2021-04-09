---
title: 'Oktatóanyag: Azure Active Directory integráció a Ceridian Dayforce HCM-vel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Ceridian Dayforce HCM között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 1f2e01a79f980e63102bb6538859f0da30c555c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651847"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Oktatóanyag: Azure Active Directory integráció a Ceridian Dayforce HCM-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Ceridian Dayforce HCM-t Azure Active Directory (Azure AD) használatával. Ha integrálja az Ceridian Dayforce HCM-t az Azure AD-vel, a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Ceridian Dayforce HCM-hez.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Ceridian Dayforce HCM-be az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Ceridian Dayforce HCM egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Ceridian Dayforce HCM támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Ceridian Dayforce HCM hozzáadása a katalógusból

A Ceridian Dayforce HCM Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Ceridian Dayforce HCM-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Ceridian Dayforce HCM** kifejezést a keresőmezőbe.
1. Válassza ki a **Ceridian DAYFORCE HCM** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Azure AD SSO konfigurálása és tesztelése Ceridian Dayforce HCM-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Ceridian Dayforce HCM használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Ceridian Dayforce HCM-ben.

Az Azure AD SSO és a Ceridian Dayforce HCM konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Konfigurálja a Ceridian DAYFORCE HCM egyszeri bejelentkezést](#configure-ceridian-dayforce-hcm-sso)** – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
    1. **[Hozzon létre Ceridian DAYFORCE HCM-teszt felhasználót](#create-ceridian-dayforce-hcm-test-user)** – ha a felhasználó Azure ad-képviseletéhez csatolt B. Simon-partnert szeretne létrehozni a CERIDIAN Dayforce HCM-ben.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása 

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal **Ceridian DAYFORCE HCM** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfigurációhoz** tartozó ceruza ikonra a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Ceridian Dayforce HCM tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL** szövegmezőbe írja be a felhasználók által a CERIDIAN Dayforce HCM-alkalmazásba való bejelentkezéshez használt URL-címet.

    | Környezet | URL-cím |
    | :-- | :-- |
    | Éles környezetben | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Teszteléshez | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | Környezet | URL-cím |
    | :-- | :-- |
    | Éles környezetben | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Teszteléshez | `https://fs-test.dayforcehcm.com/sp` |

    c. A **Válasz URL-címe** szövegmezőbe írja be az Azure ad által a válasz közzétételéhez használt URL-címet.

    | Környezet | URL-cím |
    | :-- | :-- |
    | Éles környezetben | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Teszteléshez | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Sign-On URL-címmel, azonosítóval és válasz URL-címmel. Vegye fel a kapcsolatot a [Ceridian DAYFORCE HCM ügyfélszolgálati csapatával](https://www.ceridian.com/support) az értékek beszerzéséhez. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. A Ceridian Dayforce HCM alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![A képernyőképen a szerkesztési ikonnal jelölt felhasználói attribútumok láthatók.](common/edit-attribute.png)

6. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name | Forrás attribútum|
    | ---------| --------- |
    | name  | User. extensionattribute2 |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Képernyőfelvétel: a felhasználói jogcímek új jogcím hozzáadására szolgáló lehetőséggel jelennek meg.](common/new-save-attribute.png)

    ![Képernyőfelvétel: a felhasználói jogcímek kezelése párbeszédpanel, amelyen megadhatja a leírt értékeket.](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként** lehetőséget.

    e. A **forrás attribútum** listából válassza ki azt a felhasználói attribútumot, amelyet a megvalósításához használni kíván. Ha például az Alkalmazottkód-et egyedi felhasználói azonosítóként szeretné használni, és az attribútum értékét a ExtensionAttribute2 tárolja, válassza a User. ExtensionAttribute2 elemet.

    f. Kattintson **az OK** gombra

    : Kattintson a **Mentés** gombra.

7. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** lehetőségre a **metaadatok XML** -fájljának a megadott beállítások alapján történő letöltéséhez, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

8. A **Ceridian DAYFORCE HCM beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Ceridian Dayforce HCM-hez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Ceridian DAYFORCE HCM** elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Ceridian-Dayforce

Ha az egyszeri bejelentkezést az **Ceridian DAYFORCE HCM** oldalon szeretné konfigurálni, akkor a letöltött **METAADATOKat tartalmazó XML** -fájlt és a megfelelő másolt URL-címeket el kell küldenie a Azure Portalból a [Ceridian Dayforce HCM támogatási csapatának](https://www.ceridian.com/support). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Ceridian Dayforce HCM-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Ceridian Dayforce HCM-ben. A [Ceridian DAYFORCE HCM támogatási csapatának](https://www.ceridian.com/support) használata a felhasználók hozzáadásához a CERIDIAN Dayforce HCM platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. A rendszer átirányítja a Ceridian Dayforce HCM bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot. 

* Lépjen közvetlenül a Ceridian Dayforce HCM bejelentkezési URL-címére, és indítsa el onnan a bejelentkezési folyamatot.

* Használhatja a Microsoft saját alkalmazásait. Amikor a saját alkalmazások Ceridian Dayforce HCM csempére kattint, a rendszer átirányítja a Ceridian Dayforce HCM bejelentkezési URL-címére. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.

## <a name="next-steps"></a>Következő lépések

A Ceridian Dayforce HCM konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).