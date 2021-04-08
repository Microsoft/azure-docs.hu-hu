---
title: Egyéni szabályzatok – első lépések
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan kezdheti el az egyéni házirendeket a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2d6c76a15ee62c26e0d0261c6b0d2d1e27443a40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518043"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Ismerkedés az egyéni szabályzatokkal Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Az [Egyéni házirendek](custom-policy-overview.md) olyan konfigurációs fájlok, amelyek meghatározzák a Azure Active Directory B2C (Azure ad B2C) bérlő viselkedését. Ebben a cikkben egy olyan egyéni házirendet hoz létre, amely támogatja a helyi fiók regisztrálását vagy bejelentkezését e-mail-cím és jelszó használatával. Az identitás-szolgáltatók hozzáadására is elő kell készítenie a környezetet.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem rendelkezik ilyennel, [hozzon létre egy Azure ad B2C bérlőt](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.
- [Regisztrálja az alkalmazást](tutorial-register-applications.md) a létrehozott bérlőn, hogy kommunikálni tudjon a Azure ad B2Cával.
- A Facebook-alkalmazás konfigurálásához hajtsa végre a [regisztráció és bejelentkezés Facebook-fiókkal](identity-provider-facebook.md) való beállításának lépéseit. Bár az egyéni házirendek használatához nem szükséges Facebook-alkalmazás, a jelen útmutatóban a közösségi bejelentkezés engedélyezését mutatja be egy egyéni házirendben.

> [!TIP]
> Ez a cikk azt ismerteti, hogyan állíthatja be a bérlőt manuálisan. Ez a cikk a teljes folyamatot automatizálhatja. Az automatizálás a Azure AD B2C [SocialAndLocalAccountsWithMFA Starter Pack csomagot](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)helyezi üzembe, amely lehetővé teszi a regisztrációt és bejelentkezést, a jelszó-visszaállítást és a profil szerkesztési menetét. Az alábbi forgatókönyv automatizálásához keresse fel a [IEF Setup alkalmazást](https://aka.ms/iefsetup) , és kövesse az utasításokat.


## <a name="add-signing-and-encryption-keys"></a>Aláírási és titkosítási kulcsok hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. Az Áttekintés lap **szabályzatok** területén válassza az **identitási élmény keretrendszere** elemet.

### <a name="create-the-signing-key"></a>Az aláíró kulcs létrehozása

1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Generate` .
1. A **név** mezőben adja meg a nevet `TokenSigningKeyContainer` . Lehet, hogy az előtag `B2C_1A_` automatikusan hozzá lesz adva.
1. A **kulcs típusa** beállításnál válassza az **RSA** elemet.
1. A **kulcshasználat** beállításnál válassza az **aláírás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="create-the-encryption-key"></a>A titkosítási kulcs létrehozása

1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Generate` .
1. A **név** mezőben adja meg a nevet `TokenEncryptionKeyContainer` . Lehetséges, hogy az `B2C_1A` _ előtag automatikusan hozzá van adva.
1. A **kulcs típusa** beállításnál válassza az **RSA** elemet.
1. A **kulcshasználat** beállításnál válassza a **titkosítás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="create-the-facebook-key"></a>A Facebook-kulcs létrehozása

Adja hozzá a Facebook-alkalmazás [titkos](identity-provider-facebook.md) kulcsát a szabályzat kulcsaként. Használhatja a cikk előfeltételeinek részeként létrehozott alkalmazás titkos kulcsát.

1. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
1. A **Beállítások** területen válassza a lehetőséget `Manual` .
1. A **név** mezőbe írja be a következőt: `FacebookSecret` . Lehet, hogy az előtag `B2C_1A_` automatikusan hozzá lesz adva.
1. A **Secret (titkos** kód) mezőben adja meg a Facebook-alkalmazás *titkos* kódját a Developers.Facebook.com. Ez az érték a titok, nem az alkalmazás azonosítója.
1. A **kulcshasználat** beállításnál válassza az **aláírás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework-alkalmazások regisztrálása

Azure AD B2C megköveteli, hogy regisztráljon, és jelentkezzen be a felhasználók számára helyi fiókkal: *IdentityExperienceFramework*, webes API és *ProxyIdentityExperienceFramework*, a IdentityExperienceFramework alkalmazáshoz delegált engedéllyel rendelkező natív alkalmazás. A felhasználók regisztrálhatnak e-mail-címmel vagy felhasználónévvel és jelszóval a bérlő által regisztrált alkalmazások eléréséhez, amely létrehoz egy "helyi fiókot". Helyi fiókok csak a Azure AD B2C-bérlőben léteznek.

Ezt a két alkalmazást csak egyszer kell regisztrálnia a Azure AD B2C-bérlőben.

### <a name="register-the-identityexperienceframework-application"></a>A IdentityExperienceFramework alkalmazás regisztrálása

Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja a **Alkalmazásregisztrációk** -élményt.

1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. A **név** mezőbe írja be a következőt: `IdentityExperienceFramework` .
1. A **támogatott fiókok típusai** területen **csak a szervezeti címtárban** válassza a fiókok elemet.
1. Az **átirányítási URI** területen válassza a **web** lehetőséget, majd írja be `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` `your-tenant-name` a nevet, ahol a a Azure ad B2C bérlői tartományneve.
1. Az **engedélyek** területen jelölje be a *rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez* jelölőnégyzetet.
1. Válassza a **Regisztráció** lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Következő lépésként tegye elérhetővé az API-t egy hatókör hozzáadásával:

1. A bal oldali menüben a **kezelés** alatt válassza ki **az API közzététele** lehetőséget.
1. Válassza a **hatókör hozzáadása** lehetőséget, majd válassza a mentés lehetőséget, majd fogadja el az alapértelmezett alkalmazás-azonosító URI **-** t.
1. Adja meg a következő értékeket egy olyan hatókör létrehozásához, amely lehetővé teszi az egyéni szabályzatok végrehajtását a Azure AD B2C bérlőben:
    * **Hatókör neve**: `user_impersonation`
    * **Rendszergazdai engedély megjelenítendő neve**: `Access IdentityExperienceFramework`
    * **Rendszergazdai engedély leírása**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **Hatókör hozzáadása** lehetőség kiválasztása

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>A ProxyIdentityExperienceFramework alkalmazás regisztrálása

1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza az **új regisztráció** lehetőséget.
1. A **név** mezőbe írja be a következőt: `ProxyIdentityExperienceFramework` .
1. A **támogatott fiókok típusai** területen **csak a szervezeti címtárban** válassza a fiókok elemet.
1. Az **átirányítási URI** alatt válassza a legördülő menüből a **nyilvános ügyfél/natív (mobil & Desktop)** lehetőséget.
1. Az **átirányítási URI** mezőbe írja be a következőt: `myapp://auth` .
1. Az **engedélyek** területen jelölje be a *rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez* jelölőnégyzetet.
1. Válassza a **Regisztráció** lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Ezután határozza meg, hogy az alkalmazást nyilvános ügyfélként kell kezelni:

1. A bal oldali menüben kattintson a **kezelés** elemre, majd válassza a **hitelesítés** lehetőséget.
1. A **Speciális beállítások** területen a **nyilvános ügyfelek forgalmának engedélyezése** szakaszban állítsa **a következő mobil-és asztali folyamatok engedélyezése** **Igen értéket**. Győződjön meg arról, hogy a **"allowPublicClient": true** érték van beállítva az alkalmazás jegyzékfájljában. 
1. Kattintson a **Mentés** gombra.

Most adja meg a *IdentityExperienceFramework* -regisztráció során korábban közzétett API-hatókörre vonatkozó engedélyeket:

1. A bal oldali menüben kattintson a **kezelés** elemre, majd válassza az **API-engedélyek** lehetőséget.
1. A **konfigurált engedélyek** területen válassza **az engedély hozzáadása** elemet.
1. Válassza a **saját API** -k fület, majd válassza ki a **IdentityExperienceFramework** alkalmazást.
1. Az **engedély** területen válassza ki a korábban megadott **user_impersonation** hatókört.
1. Válassza az **engedélyek hozzáadása** lehetőséget. Az utasítás szerint várjon néhány percet, mielőtt továbblép a következő lépésre.
1. Válassza a **rendszergazdai jóváhagyás megadása (a bérlő neve)** lehetőséget.
1. Válassza ki a jelenleg bejelentkezett rendszergazdai fiókot, vagy jelentkezzen be egy olyan fiókkal a Azure AD B2C-bérlőben, amely legalább a *Cloud Application Administrator* szerepkörhöz van rendelve.
1. Válassza ki az **Elfogadás** lehetőséget.
1. Válassza a **frissítés** lehetőséget, majd ellenőrizze, hogy a "engedélyezve..." a hatókörök – offline_access, OpenID és user_impersonation **állapot** alatt jelenik meg. Eltarthat néhány percig, amíg az engedélyek propagálása megtörténik.

* * *

## <a name="custom-policy-starter-pack"></a>Egyéni házirend alapszintű csomagja

Az egyéni házirendek a Azure AD B2C bérlőre feltöltött, a technikai profilok és a felhasználói utazások meghatározására feltöltött XML-fájlok összessége. Az alapszintű csomagokat számos előre elkészített szabályzattal biztosítjuk, hogy gyorsan elkészüljön. Az alapszintű csomagok mindegyike a leírt forgatókönyvek eléréséhez szükséges technikai profilok és felhasználói utazások legkisebb számát tartalmazza:

- **LocalAccounts** – csak a helyi fiókok használatát engedélyezi.
- **SocialAccounts** – engedélyezi a közösségi (vagy összevont) fiókok használatát.
- **SocialAndLocalAccounts** – lehetővé teszi mind a helyi, mind a közösségi fiókok használatát.
- **SocialAndLocalAccountsWithMFA** – lehetővé teszi a közösségi, a helyi és a többtényezős hitelesítési lehetőségek használatát.

Minden kezdő csomag a következőket tartalmazza:

- **Alapfájl** – kevés módosításra van szükség az alaphoz. Példa: *TrustFrameworkBase.xml*
- **Kiterjesztési fájl** – ez a fájl a legtöbb konfigurációs módosítást hajtja végre. Példa: *TrustFrameworkExtensions.xml*
- **Függő entitás fájljai** – az alkalmazás által meghívott feladat-specifikus fájlok. Példák: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

Ebben a cikkben a **SocialAndLocalAccounts** alapszintű csomagban lévő egyéni XML-házirendek fájljait szerkeszti. Ha XML-szerkesztőre van szüksége, próbálja ki a [Visual Studio Code](https://code.visualstudio.com/download)-ot, amely egy könnyű platformfüggetlen szerkesztő.

### <a name="get-the-starter-pack"></a>Az alapszintű csomag beszerzése

Szerezze be az egyéni házirend-előindítási csomagokat a GitHubról, majd frissítse az SocialAndLocalAccounts Starter Pack-ban található XML-fájlokat a Azure AD B2C bérlői nevével.

1. [Töltse le a. zip fájlt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) , vagy az adattár klónozása:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. A **SocialAndLocalAccounts** könyvtár összes fájljában cserélje le a karakterláncot a `yourtenant` Azure ad B2C bérlő nevére.

    Ha például a B2C-bérlő neve *contosotenant*, az összes példánya `yourtenant.onmicrosoft.com` lesz `contosotenant.onmicrosoft.com` .

### <a name="add-application-ids-to-the-custom-policy"></a>Alkalmazás-azonosítók hozzáadása az egyéni szabályzathoz

Adja hozzá az alkalmazás-azonosítókat a kiterjesztések fájlhoz *TrustFrameworkExtensions.xml*.

1. Nyissa meg `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** és keresse meg az elemet `<TechnicalProfile Id="login-NonInteractive">` .
1. Cserélje le mindkét példányát a `IdentityExperienceFrameworkAppId` korábban létrehozott IdentityExperienceFramework alkalmazás alkalmazás-azonosítójával.
1. Cserélje le mindkét példányát a `ProxyIdentityExperienceFrameworkAppId` korábban létrehozott ProxyIdentityExperienceFramework alkalmazás alkalmazás-azonosítójával.
1. Mentse a fájlt.

## <a name="upload-the-policies"></a>A szabályzatok feltöltése

1. Válassza ki az **Identity Experience Framework** menüpontot a B2C-bérlőben a Azure Portalban.
1. Válassza az **egyéni házirend feltöltése** lehetőséget.
1. Ebben a sorrendben töltse fel a házirend-fájlokat:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

A fájlok feltöltésekor az Azure hozzáadja az előtagot `B2C_1A_` mindegyikhez.

> [!TIP]
> Ha az XML-szerkesztő támogatja az érvényesítést, ellenőrizze a fájlokat az `TrustFrameworkPolicy_0.3.0.0.xsd` alapszintű csomag gyökérkönyvtárában található XML-séma alapján. Az XML-séma ellenőrzése a feltöltés előtt hibákat azonosít.

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Az **Egyéni házirendek** területen válassza a **B2C_1A_signup_signin** lehetőséget.
1. Az egyéni házirend áttekintés lapján jelölje ki az **alkalmazás kiválasztása** lehetőséget, majd válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást.
1. Győződjön meg arról, hogy a **Válasz URL-címe** : `https://jwt.ms` .
1. Válassza a **Futtatás most** lehetőséget.
1. Regisztráljon e-mail-cím használatával.
1. Válassza a **Futtatás most** újra lehetőséget.
1. Jelentkezzen be ugyanazzal a fiókkal, és ellenőrizze, hogy megfelelő-e a konfigurációja.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook hozzáadása identitás-szolgáltatóként

Az [Előfeltételek](#prerequisites)értelmében a Facebook *nem* szükséges egyéni szabályzatok használatához, de itt azt mutatjuk be, hogyan engedélyezheti az összevont közösségi bejelentkezést egy egyéni házirendben.

1. A `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** fájlban cserélje le a `client_id` Facebook-alkalmazás azonosítóját a (z) értékre:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Töltse fel a *TrustFrameworkExtensions.xml* fájlt a bérlőbe.
1. Az **Egyéni házirendek** területen válassza a **B2C_1A_signup_signin** lehetőséget.
1. Kattintson a **Futtatás most** lehetőségre, és válassza a Facebook lehetőséget a Facebook használatával való bejelentkezéshez és az egyéni szabályzat teszteléséhez.

## <a name="next-steps"></a>Következő lépések

Ezután próbálkozzon Azure Active Directory (Azure AD) identitás-szolgáltatóként való hozzáadásával. Az első lépéseket ismertető útmutatóban használt alapfájl már tartalmazza a más személyazonossági szolgáltatók (például az Azure AD) hozzáadásához szükséges tartalmakat. Az Azure AD identitás-szolgáltatóként való beállításával kapcsolatos információkért tekintse meg a következő témakört: [regisztráció és bejelentkezés Azure Active Directory fiókkal Active Directory B2C egyéni házirendek használatával](identity-provider-azure-ad-single-tenant.md). 

A [partneri galériában](partner-gallery.md) tájékozódhat arról, hogyan valósítja meg az ISV-integrációt egyéni szabályzatok használatával. 
