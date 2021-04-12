---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a Jumio használatával
titleSuffix: Azure AD B2C
description: Ebben az oktatóanyagban a Azure Active Directory B2Ct a Jumio-vel konfigurálja az automatikus azonosító-ellenőrzéshez, az ügyféladatok védelméhez.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: e344c849a8e9021daea9caebacec3289b99d03e6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256667"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Oktatóanyag a Jumio konfigurálásához a Azure Active Directory B2C

Ebben a példában a Azure Active Directory B2C (Azure AD B2C) és a [Jumio](https://www.jumio.com/)integrálásához nyújtunk útmutatást. A Jumio egy azonosító-ellenőrzési szolgáltatás, amely lehetővé teszi a valós idejű automatizált AZONOSÍTÓk ellenőrzését az ügyféladatok védelme érdekében.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- Egy [Azure ad B2C bérlő](./tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

## <a name="scenario-description"></a>Forgatókönyv leírása

A Jumio-integráció a következő összetevőket tartalmazza:

- Azure AD B2C: a felhasználó hitelesítő adatainak ellenőrzéséhez felelős engedélyezési kiszolgáló. Más néven identitás-szolgáltató.

- Jumio: az a szolgáltatás, amely a felhasználó által megadott azonosító adatokat veszi alapul, és ellenőrzi azokat.

- Köztes REST API: a Azure AD B2C és a Jumio szolgáltatás közötti integrációt megvalósító API.

- Azure Blob Storage: az a szolgáltatás, amely az egyéni felhasználói felületi fájlokat szolgáltatja a Azure AD B2C szabályzatoknak.

A következő architektúra-diagram a megvalósítást mutatja be.

![A Jumio-vel való Azure AD B2C integráció architektúrájának ábrája.](./media/partner-jumio/jumio-architecture-diagram.png)

|Lépés | Description |
|:-----| :-----------|
| 1. | A felhasználó egy lapon megérkezik, hogy jelentkezzen be, vagy regisztráljon, és hozzon létre egy fiókot. A Azure AD B2C a felhasználói attribútumokat gyűjti.
| 2. | Azure AD B2C meghívja a középső rétegbeli API-t, és átadja a felhasználói attribútumokat.
| 3. | A középső rétegbeli API gyűjti a felhasználói attribútumokat, és átalakítja azokat olyan formátumra, amelyet a Jumio API képes használni. Ezután elküldi az attribútumokat a Jumio.
| 4. | Miután a Jumio felhasználta az adatokat, és feldolgozza azt, visszaadja az eredményt a középső rétegbeli API-nak.
| 5. | A középső rétegbeli API feldolgozza az adatokat, és visszaküldi a kapcsolódó információkat a Azure AD B2Cba.
| 6. | Azure AD B2C adatokat kap vissza a középső rétegbeli API-ból. Ha hibát jelez, hibaüzenet jelenik meg a felhasználó számára. Ha sikeres választ mutat, a rendszer hitelesíti a felhasználót, és beírja a könyvtárba.

## <a name="sign-up-with-jumio"></a>Regisztráció a Jumio

Jumio-fiók létrehozásához forduljon a [Jumiohoz](https://www.jumio.com/contact/).

## <a name="configure-azure-ad-b2c-with-jumio"></a>Azure AD B2C konfigurálása a Jumio

Miután létrehozta a Jumio-fiókot, a fiók használatával konfigurálja a Azure AD B2C. A következő szakaszok ismertetik a folyamat menetét.

### <a name="deploy-the-api"></a>Az API üzembe helyezése

A megadott [API-kód](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) üzembe helyezése egy Azure-szolgáltatásban. A kódot a Visual studióból teheti közzé a következő [utasítások](/visualstudio/deployment/quickstart-deploy-to-azure)követésével.

>[!NOTE]
>Szüksége lesz az üzembe helyezett szolgáltatás URL-címére az Azure AD konfigurálásához a szükséges beállításokkal.

### <a name="deploy-the-client-certificate"></a>Az ügyféltanúsítvány üzembe helyezése

1. Az ügyféltanúsítványok segítenek a Jumio API-hívásban. Hozzon létre egy önaláírt tanúsítványt a következő PowerShell-mintakód használatával:

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   A tanúsítvány ezután a számára megadott helyre lesz exportálva ``{your-local-path}`` .

3. Importálja a tanúsítványt a Azure App Serviceba a [cikkben](../app-service/configure-ssl-certificate.md#upload-a-private-certificate)található utasításokat követve.

### <a name="create-a-signingencryption-key"></a>Aláírási/titkosítási kulcs létrehozása

Hozzon létre egy véletlenszerű karakterláncot, amelynek hossza nagyobb, mint 64 karakter, és csak betűket és számokat tartalmaz.

Például: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

A karakterlánc létrehozásához használja a következő PowerShell-parancsfájlt:

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>Az API konfigurálása

Az Alkalmazásbeállítások megadható [a Azure app Serviceban](../app-service/configure-common.md#configure-app-settings). Ezzel a módszerrel biztonságos módon konfigurálhatja a beállításokat anélkül, hogy azokat egy adattárba kellene benéznie. A REST API-hoz a következő beállításokat kell megadnia:

| Alkalmazásbeállítások | Forrás | Jegyzetek |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio-fiók konfigurálása |     |
|JumioSettings:AuthPassword | Jumio-fiók konfigurálása |     |
|AppSettings: SigningCertThumbprint|A létrehozott önaláírt tanúsítvány ujjlenyomata|  |
|AppSettings: IdTokenSigningKey| A PowerShell használatával létrehozott aláíró kulcs | |
| AppSettings: IdTokenEncryptionKey |A PowerShell használatával létrehozott titkosítási kulcs
| AppSettings: IdTokenIssuer | Az JWT-tokenhez használandó kiállító (a GUID értéke előnyben részesített) |
| AppSettings: IdTokenAudience  | Az JWT-tokenhez használandó célközönség (a GUID értéke előnyben részesített) |
|AppSettings: BaseRedirectUrl | A Azure AD B2C házirend alap URL-címe | https://{saját-bérlő neve}. b2clogin. com/{az alkalmazás-azonosító}|
| WEBSITE_LOAD_CERTIFICATES| A létrehozott önaláírt tanúsítvány ujjlenyomata |

### <a name="deploy-the-ui"></a>A felhasználói felület üzembe helyezése

1. Hozzon létre egy [blob Storage-tárolót a Storage-fiókban](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

2. Tárolja a felhasználói felületi fájlokat a blob-tároló [felhasználói felület mappájából](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) .

#### <a name="update-ui-files"></a>FELHASZNÁLÓIFELÜLET-fájlok frissítése

1. A felhasználói felület fájljaiban nyissa meg a mappát [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue).

2. Nyissa meg az egyes HTML-fájlokat.

3. Keresse meg és cserélje le a `{your-ui-blob-container-url}` blob-tároló URL-címét.

4. Keresse meg és cserélje le a `{your-intermediate-api-url}` köztes API app Service URL-címét.

>[!NOTE]
> Ajánlott eljárásként javasoljuk, hogy adjon hozzá egy beleegyező értesítést az attribútum-gyűjtemény lapon. Értesítse a felhasználókat arról, hogy az adatokat a harmadik féltől származó szolgáltatásoknak személyazonosság-ellenőrzés céljából küldi a rendszer.

### <a name="configure-the-azure-ad-b2c-policy"></a>A Azure AD B2C házirend konfigurálása

1. Lépjen a házirendek mappában található [Azure ad B2C házirendre](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) .

2. A [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)letöltéséhez kövesse [ezt a cikket](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) .

3. Konfigurálja a Azure AD B2C bérlő házirendjét.

>[!NOTE]
>Frissítse a megadott szabályzatokat az adott bérlőhöz kapcsolódóan.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.

2. Válassza ki a korábban létrehozott **SignUpSignIn**.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, majd:

   a. **Alkalmazás** esetén válassza ki a regisztrált alkalmazást (a minta a JWT).

   b. A **Válasz URL-cím** mezőben válassza ki az **átirányítási URL-címet**.

   c. Válassza a **felhasználói folyamat futtatása** lehetőséget.

4. Ugorjon végig a regisztrációs folyamaton, és hozzon létre egy fiókot.

5. A rendszer a Jumio szolgáltatást a folyamat során hívja meg a felhasználói attribútum létrehozása után. Ha a folyamat hiányos, ellenőrizze, hogy a felhasználó nincs-e mentve a címtárban.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
