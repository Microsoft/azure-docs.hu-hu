---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció Cisco WebEx-értekezletekkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Cisco WebEx-értekezletek között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: bb8ea637d0353e4efa0cb946f486d68639fc699d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592489"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció Cisco WebEx-értekezletekkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cisco WebEx-értekezleteket Azure Active Directory (Azure AD) használatával. Ha a Cisco WebEx-értekezleteket az Azure AD-vel integrálja, a következőket teheti:

* A Cisco WebEx-értekezletekhez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Cisco WebEx-üléseire az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Cisco WebEx az egyszeri bejelentkezést (SSO) engedélyezte az előfizetést.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Cisco WebEx-találkozók támogatják **az SP és a identitásszolgáltató** által kezdeményezett SSO-t.

* A Cisco WebEx-értekezletek a felhasználó üzembe helyezésének **időpontját** támogatják.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Cisco WebEx-értekezletek hozzáadása a katalógusból

A Cisco WebEx-értekezleteknek az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cisco WebEx-találkozókat a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a Azure Portal munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Cisco WebEx-értekezletek** kifejezést a keresőmezőbe.
1. Válassza a **Cisco WebEx-értekezletek** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>Azure AD SSO konfigurálása és tesztelése Cisco WebEx-értekezletekhez

Konfigurálja és tesztelje az Azure AD SSO-t a Cisco WebEx ülésein egy **B. Simon** nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Cisco WebEx-értekezleteken.

Az Azure AD SSO és a Cisco WebEx-értekezletek konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
   1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
   1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
   
1. **[Cisco WebEx-találkozók egyszeri bejelentkezésének konfigurálása](#configure-cisco-webex-meetings-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
   * **[Cisco WebEx-értekezletek létrehozása – tesztelje a felhasználót](#create-cisco-webex-meetings-test-user)** –, hogy a B. Simon partnere legyen a Cisco WebEx-találkozókon, amely a felhasználó Azure ad-képviseletéhez van társítva.
    
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A Azure Portal a **Cisco WebEx-értekezletek** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.
1. Az **egyszeres Sign-On beállítása az SAML-vel** lapon az alkalmazást **identitásszolgáltató** kezdeményezett módban is konfigurálhatja a **szolgáltatói metaadatok** fájljának a következőképpen történő feltöltésével:
   1. Kattintson a **metaadat-fájl feltöltése** elemre.
   1. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés** elemre.
   1. A szolgáltatói metaadatok feltöltésének sikeres befejezése után az **azonosító** és a **Válasz URL-** értékei az **alapszintű SAML-konfiguráció** szakaszban automatikusan feltöltve értékre kerülnek.
   
      > [!Note]
      > A szolgáltatói metaadat-fájlt a **Cisco WebEx-értekezletek SSO konfigurálása** című szakaszban találja, amelyet az oktatóanyag későbbi részében ismertet. 

1. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:  
   1. Az **alapszintű SAML-konfiguráció** szakaszban kattintson a Szerkesztés/toll ikonra.

      ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

   1. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<customername>.my.webex.com`

1. A Cisco WebEx-értekezletek alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

   ![image](common/edit-attribute.png)

1. A fentieken kívül a Cisco WebEx üléseinek alkalmazása néhány további attribútumot vár az SAML-válaszban való visszatéréshez. A felhasználó attribútumai párbeszédpanel felhasználói jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon: 

   | Name | Forrás attribútum|
   | ---------------|  --------- |
   |   FirstName    | User. givenName |
   |   LastName    | felhasználó. vezetéknév |
   |   e-mail       | User. mail |
   |   UID    | User. mail |

   1. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.
   1. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.
   1. Hagyja üresen a **névteret** .
   1. Válassza a forrás **attribútumként** lehetőséget.
   1. A **forrás attribútum** listából válassza ki az adott sorhoz tartozó attribútum értékét a legördülő listából.
   1. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Cisco WebEx-találkozók beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Cisco WebEx-értekezletekhez.

1. A Azure Portal válassza a **vállalati alkalmazások** lehetőséget, majd válassza a **minden alkalmazás** lehetőséget.
1. Az alkalmazások listában válassza a **Cisco WebEx-értekezletek** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.
1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.
1. Ha a felhasználókhoz hozzárendelni kívánt szerepkört vár, kiválaszthatja a **szerepkör kiválasztása** legördülő listából. Ha nem állított be szerepkört ehhez az alkalmazáshoz, a "default Access" szerepkör van kiválasztva.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cisco-webex-meetings-sso"></a>Cisco WebEx-értekezletek – SSO konfigurálása

1. Jelentkezzen be a Cisco WebEx-találkozókra a rendszergazdai hitelesítő adataival.
1. Nyissa meg a **gyakori hely beállításait** , és navigáljon az **SSO-konfigurációhoz**.

   ![Képernyőfelvétel: a Cisco WebEx felügyelete a közös hely beállításaival és a S S O konfigurációval van kiválasztva.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. A **WebEx-felügyelet** oldalon hajtsa végre a következő lépéseket:

   ![Képernyőfelvétel: a WebEx adminisztrációs oldal, amely az ebben a lépésben ismertetett információkat tartalmazza.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. Válassza az **SAML 2,0** -as **összevonási protokoll** lehetőséget.
   1. Kattintson az **SAML-Metaadatok importálása** hivatkozásra a metaadatok fájljának feltöltéséhez, amelyet a Azure Portal letöltött.
   1. Válassza ki az **SSO-profilt** **identitásszolgáltató kezdeményezve**  , és kattintson az **Exportálás** gombra a szolgáltatói metaadat-fájl letöltéséhez, majd töltse fel azt az **alapszintű SAML-konfigurációs** szakaszban Azure Portal.
   1. A **AuthContextClassRef** szövegmezőbe írja be a következő értékek egyikét:
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      Az MFA Azure AD-vel való engedélyezéséhez adja meg a következő két értéket: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

   1. Válassza az **automatikus fiók létrehozása** lehetőséget.
   
      > [!NOTE]
      > Az igény **szerinti felhasználó üzembe** helyezésének engedélyezéséhez ellenőriznie kell az **automatikus fiók létrehozását**. Az SAML-jogkivonat attribútumait is át kell adni az SAML-válaszban.

   1. Kattintson a **Mentés** gombra.

      > [!NOTE]
      > Ez a konfiguráció csak azoknál az ügyfeleknél használható, akik e-mail-formátumban használják a WebEx UserID-t.
      > 
      > A Cisco WebEx-értekezletek konfigurálásával kapcsolatos további tudnivalókért tekintse meg a [WebEx dokumentációs](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) oldalát.

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco WebEx-értekezletek létrehozása tesztelési felhasználó

Ennek a szakasznak a célja, hogy létrehozzon egy B. Simon nevű felhasználót a Cisco WebEx ülésein. A Cisco WebEx-értekezletek támogatják az **igény** szerinti üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Cisco WebEx ülésein, akkor a rendszer létrehoz egy újat, amikor megpróbál hozzáférni a Cisco WebEx-értekezletekhez.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban a következő lehetőségekkel tesztelheti az Azure AD egyszeri bejelentkezés konfigurációját. 

#### <a name="sp-initiated"></a>SP kezdeményezve

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre. Ez a szolgáltatás átirányítja a Cisco WebEx-értekezletek bejelentkezési URL-címére, ahol elindíthatja a bejelentkezési folyamatot.  

* Lépjen a Cisco WebEx értekezletek bejelentkezési URL-címére közvetlenül, és indítsa el onnan a bejelentkezési folyamatot.

#### <a name="idp-initiated"></a>IDENTITÁSSZOLGÁLTATÓ kezdeményezve:

* Kattintson az **alkalmazás tesztelése** Azure Portal lehetőségre, és automatikusan be kell jelentkeznie azokra a Cisco WebEx-értekezletekre, amelyekhez be szeretné állítani az egyszeri bejelentkezést.

A Microsoft My Apps használatával bármilyen módban tesztelheti az alkalmazást. Ha a saját alkalmazások Cisco WebEx-értekezletek csempére kattint, ha az SP módban van konfigurálva, akkor a bejelentkezési folyamat elindításához és a IDENTITÁSSZOLGÁLTATÓ módban konfigurált, automatikusan be kell jelentkeznie arra a Cisco WebEx-értekezletre, amelyhez be szeretné állítani az egyszeri bejelentkezést. A saját alkalmazásokkal kapcsolatos további információkért lásd: [Bevezetés a saját alkalmazások](../user-help/my-apps-portal-end-user-access.md)használatába.


## <a name="next-steps"></a>Következő lépések

A Cisco WebEx-értekezletek konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)