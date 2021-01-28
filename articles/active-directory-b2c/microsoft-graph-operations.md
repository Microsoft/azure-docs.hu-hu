---
title: Erőforrások kezelése a Microsoft Graph
titleSuffix: Azure AD B2C
description: Az Azure AD B2C-bérlő erőforrásainak kezelése a Microsoft Graph API meghívásával és az alkalmazás identitásának használatával a folyamat automatizálása érdekében.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 50042b749c917752d08198c31ada3c73a5ef540b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938491"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Azure AD B2C kezelése Microsoft Graph

Microsoft Graph lehetővé teszi az erőforrások kezelését a Azure AD B2C címtárban. A következő Microsoft Graph API-műveletek támogatottak Azure AD B2C erőforrások, például felhasználók, identitás-szolgáltatók, felhasználói folyamatok, egyéni házirendek és házirend-kulcsok kezeléséhez. A következő részben található hivatkozások a Microsoft Graph API-referencián belüli megfelelő lapot célozzák meg az adott művelethez. 

## <a name="prerequisites"></a>Előfeltételek

Ha az MS Graph APIt szeretné használni, és a Azure AD B2C-bérlő erőforrásaival szeretne kommunikálni, szüksége lesz egy alkalmazás-regisztrációra, amely erre engedélyt ad. A felügyeleti alkalmazás által használható alkalmazás-regisztráció létrehozásához hajtsa végre a [Azure ad B2C kezelése a Microsoft Graphval](microsoft-graph-get-started.md) című cikkben ismertetett lépéseket. 

## <a name="user-management"></a>Felhasználókezelés

- [Felhasználók listázása](/graph/api/user-list)
- [Fogyasztói felhasználó létrehozása](/graph/api/user-post-users)
- [Felhasználó beolvasása](/graph/api/user-get)
- [Felhasználó frissítése](/graph/api/user-update)
- [Felhasználó törlése](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Felhasználói telefonszámok kezelése (bétaverzió)

A felhasználó által az [SMS-és hanghívások](identity-provider-local.md#phone-sign-in-preview)vagy a [többtényezős hitelesítés](multi-factor-authentication.md)használatával történő bejelentkezéshez használható telefonszám. További információ: [Azure ad Authentication Methods API](/graph/api/resources/phoneauthenticationmethod).

- [Hozzáadás](/graph/api/authentication-post-phonemethods)
- [Lista](/graph/api/authentication-list-phonemethods)
- [Lekérés](/graph/api/phoneauthenticationmethod-get)
- [Frissítés](/graph/api/phoneauthenticationmethod-update)
- [Törlés](/graph/api/phoneauthenticationmethod-delete)

## <a name="self-service-password-reset-email-address-beta"></a>Önkiszolgáló jelszó-visszaállítási e-mail-cím (bétaverzió)

E-mail-cím, amelyet a felhasználó a [Felhasználónév bejelentkezési fiók](identity-provider-local.md#username-sign-in)jelszavának megpihenésére használhat. További információ: [Azure ad Authentication Methods API](/graph/api/resources/emailauthenticationmethod).

- [Hozzáadás](/graph/api/emailauthenticationmethod-post)
- [Lista](/graph/api/emailauthenticationmethod-list)
- [Lekérés](/graph/api/emailauthenticationmethod-get)
- [Frissítés](/graph/api/emailauthenticationmethod-update)
- [Törlés](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Identitásszolgáltatók

Kezelheti az Azure AD B2C bérlője felhasználói folyamatai számára elérhető [identitás-szolgáltatókat](add-identity-provider.md) .

- [Az Azure AD B2C bérlőben regisztrált identitás-szolgáltatók listázása](/graph/api/identityprovider-list)
- [Identitás-szolgáltató létrehozása](/graph/api/identityprovider-post-identityproviders)
- [Identitás-szolgáltató beszerzése](/graph/api/identityprovider-get)
- [Identitás-szolgáltató frissítése](/graph/api/identityprovider-update)
- [Identitás-szolgáltató törlése](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Felhasználói folyamat

A regisztrációhoz, a bejelentkezéshez, a kombinált regisztrációhoz, a bejelentkezéshez, a jelszó-visszaállításhoz és a profil frissítéséhez szükséges előre elkészített szabályzatok konfigurálása.

- [Felhasználói folyamatok listázása](/graph/api/identitycontainer-list-b2cuserflows)
- [Felhasználói folyamat létrehozása](/graph/api/identitycontainer-post-b2cuserflows)
- [Felhasználói folyamat beszerzése](/graph/api/b2cidentityuserflow-get)
- [Felhasználói folyamat törlése](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Felhasználói folyamat hitelesítési módszerei (bétaverzió)

Válassza ki azt a mechanizmust, amellyel a felhasználók helyi fiókokon keresztül regisztrálhatnak. A helyi fiókok azok a fiókok, amelyekben az Azure AD az identitást. További információ: [b2cAuthenticationMethodsPolicy erőforrástípus](/graph/api/resources/b2cauthenticationmethodspolicy).

- [Lekérés](/graph/api/b2cauthenticationmethodspolicy-get)
- [Frissítés](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Egyéni szabályzatok

A következő műveletek lehetővé teszik a Azure AD B2C megbízhatósági keretrendszer házirendjeinek, más néven [Egyéni házirendeknek](custom-policy-overview.md)a kezelését.

- [A bérlőben konfigurált összes megbízhatósági keretrendszer szabályzatának listázása](/graph/api/trustframework-list-trustframeworkpolicies)
- [Megbízhatósági keretrendszer házirendjének létrehozása](/graph/api/trustframework-post-trustframeworkpolicy)
- [Meglévő megbízhatósági keretrendszer házirendjének tulajdonságainak olvasása](/graph/api/trustframeworkpolicy-get)
- [Frissítse vagy hozza létre a megbízhatósági keretrendszer szabályzatát.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Meglévő megbízhatósági keretrendszer házirendjének törlése](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Szabályzatkulcsok

Az Identity Experience Framework egy egyéni szabályzatban hivatkozott titkokat tárolja az összetevők közötti megbízhatósági kapcsolat létrehozásához. Ezek a titkok lehetnek szimmetrikus vagy aszimmetrikus kulcsok/értékek. A Azure Portalban ezek az entitások **házirend kulcsaként** jelennek meg.

A Microsoft Graph API legfelső szintű erőforrása a [megbízható keretrendszer kulcskészlet](/graph/api/resources/trustframeworkkeyset). Mindegyik **kulcskészlet** legalább egy **kulcsot** tartalmaz. A kulcs létrehozásához először létre kell hoznia egy üres kulcskészlet-t, majd létre kell hoznia egy kulcsot a kulcskészlet alkalmazásban. Létrehozhat egy manuális titkot, feltölthet egy tanúsítványt vagy egy PKCS12/pfx-profil kulcsot is. A kulcs lehet egy generált titok, egy karakterlánc (például a Facebook-alkalmazás titka) vagy egy feltöltött tanúsítvány. Ha egy kulcskészlet több kulccsal rendelkezik, akkor csak az egyik kulcs aktív.

### <a name="trust-framework-policy-keyset"></a>Megbízhatósági keretrendszer szabályzata kulcskészlet

- [A megbízhatósági keretrendszer alapbeállításainak listázása](/graph/api/trustframework-list-keysets)
- [Megbízhatósági keretrendszer alapbeállításainak létrehozása](/graph/api/trustframework-post-keysets)
- [Kulcskészlet beszerzése](/graph/api/trustframeworkkeyset-get)
- [Megbízhatósági keretrendszer alapbeállításainak frissítése](/graph/api/trustframeworkkeyset-update)
- [Megbízhatósági keretrendszer alapbeállításainak törlése](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Megbízhatósági keretrendszer házirendjének kulcsa

- [Jelenleg aktív kulcs beolvasása a kulcskészlet-ban](/graph/api/trustframeworkkeyset-getactivekey)
- [Kulcs létrehozása a kulcskészlet-ban](/graph/api/trustframeworkkeyset-generatekey)
- [Karakterlánc-alapú titkos kód feltöltése](/graph/api/trustframeworkkeyset-uploadsecret)
- [X. 509 tanúsítvány feltöltése](/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12/pfx-profil-formátumú tanúsítvány feltöltése](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Alkalmazások

- [Alkalmazások listázása](/graph/api/application-list)
- [Alkalmazás létrehozása](/graph/api/resources/application)
- [Az alkalmazás frissítése](/graph/api/application-update)
- [ServicePrincipal létrehozása](/graph/api/resources/serviceprincipal)
- [Oauth2Permission-támogatás létrehozása](/graph/api/resources/oauth2permissiongrant)
- [Alkalmazás törlése](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Alkalmazás-bővítmény tulajdonságai

- [Bővítmény tulajdonságainak listázása](/graph/api/application-list-extensionproperty)

Azure AD B2C egy olyan könyvtárat biztosít, amely felhasználónként 100 egyéni attribútumot tud tárolni. A felhasználói folyamatok esetében ezek a bővítmény-tulajdonságok [a Azure Portal használatával kezelhetők](user-flow-custom-attributes.md). Egyéni házirendek esetén a Azure AD B2C létrehozza a tulajdonságot, amikor a házirend első alkalommal ír egy értéket a bővítmény tulajdonságba.

## <a name="audit-logs"></a>Naplók

- [Naplófájlok listázása](/graph/api/directoryaudit-list)

További információ a Azure AD B2C naplók eléréséről: Azure AD B2C naplók [elérése](view-audit-logs.md).

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Mintakód: felhasználói fiókok programozott felügyelete

Ez a mintakód egy olyan .NET Core Console-alkalmazás, amely a [Microsoft Graph SDK](/graph/sdks/sdks-overview) -val együttműködik a Microsoft Graph API-val. A kód azt mutatja be, hogyan hívhatja meg az API-t, hogy programozott módon felügyelje a felhasználókat egy Azure AD B2C bérlőn.
[Letöltheti a minta archívumot](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*. zip), [böngészheti a tárházat](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) a githubon, vagy megnyithatja az adattárat:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

A mintakód beszerzése után konfigurálja a környezetet, majd hozza létre a projektet:

1. Nyissa meg a projektet a [Visual Studióban](https://visualstudio.microsoft.com) vagy a [Visual Studio Code](https://code.visualstudio.com)-ban.
1. Nyissa meg a következő fájlt: `src/appsettings.json`.
1. A `appSettings` szakaszban cserélje le a `your-b2c-tenant` nevet a bérlő nevére, és `Application (client) ID` `Client secret` adja meg a felügyeleti alkalmazás regisztrálásának értékeit. További információ: [Microsoft Graph alkalmazás regisztrálása](microsoft-graph-get-started.md).
1. Nyisson meg egy konzol ablakot a tárház helyi klónján belül, váltson át a `src` címtárra, majd hozza létre a projektet:

    ```console
    cd src
    dotnet build
    ```
    
1. Futtassa az alkalmazást a `dotnet` paranccsal:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Az alkalmazás megjeleníti a végrehajtható parancsok listáját. Például az összes felhasználó beszerzése, egyetlen felhasználó beszerzése, egy felhasználó törlése, a felhasználó jelszavának frissítése és a tömeges importálás.

### <a name="code-discussion"></a>Kód-vitafórum

A mintakód a [Microsoft Graph SDK](/graph/sdks/sdks-overview)-t használja, amelynek célja, hogy leegyszerűsítse a Microsoft Graphhoz hozzáférő, magas színvonalú, hatékony és rugalmas alkalmazások kialakítását.

A Microsoft Graph API-nak benyújtott minden kérelemhez hozzáférési jogkivonat szükséges a hitelesítéshez. A megoldás a [Microsoft. Graph. auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet csomag használatát teszi lehetővé, amely a Microsoft Authentication Library (MSAL) hitelesítési forgatókönyv-alapú burkolóját biztosítja a Microsoft Graph SDK-val való használatra.

A `RunAsync` metódus a _program.cs_ fájlban:

1. Az Alkalmazásbeállítások beolvasása a _appsettings.js_ fájlból
1. A [OAuth 2,0 ügyfél-hitelesítő adatok engedélyezési](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) folyamatát használva inicializálja a hitelesítési szolgáltatót. Az ügyfél hitelesítő adatainak megadása esetén az alkalmazás hozzáférési jogkivonatot kap a Microsoft Graph API meghívásához.
1. Beállítja a Microsoft Graph szolgáltatási ügyfelet az Auth szolgáltatóval:

    ```csharp
    // Read application settings from appsettings.json (tenant ID, app ID, client secret, etc.)
    AppSettings config = AppSettingsFile.ReadFromJsonFile();

    // Initialize the client credential auth provider
    IConfidentialClientApplication confidentialClientApplication = ConfidentialClientApplicationBuilder
        .Create(config.AppId)
        .WithTenantId(config.TenantId)
        .WithClientSecret(config.ClientSecret)
        .Build();
    ClientCredentialProvider authProvider = new ClientCredentialProvider(confidentialClientApplication);

    // Set up the Microsoft Graph service client with client credentials
    GraphServiceClient graphClient = new GraphServiceClient(authProvider);
    ```

A rendszer ezt követően a inicializált *GraphServiceClient* használja a _UserService.cs_ a felhasználói felügyeleti műveletek végrehajtásához. Például a bérlőben lévő felhasználói fiókok listájának beolvasása:

```csharp
public static async Task ListUsers(GraphServiceClient graphClient)
{
    Console.WriteLine("Getting list of users...");

    // Get all users (one page)
    var result = await graphClient.Users
        .Request()
        .Select(e => new
        {
            e.DisplayName,
            e.Id,
            e.Identities
        })
        .GetAsync();

    foreach (var user in result.CurrentPage)
    {
        Console.WriteLine(JsonConvert.SerializeObject(user));
    }
}
```

[Az Microsoft Graph SDK-kat használó API-hívások a](/graph/sdks/create-requests) Microsoft Graph információk olvasására és írására, a `$select` visszaadott tulajdonságok szabályozására, az egyéni lekérdezési paraméterek megadására, valamint a `$filter` és a lekérdezés paramétereinek használatára vonatkozó információkat tartalmaznak `$orderBy` .

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
