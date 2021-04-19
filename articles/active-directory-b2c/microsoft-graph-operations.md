---
title: Erőforrások kezelése Microsoft Graph
titleSuffix: Azure AD B2C
description: Hogyan kezelheti egy Azure AD B2C-bérlő erőforrásait az Microsoft Graph API hívásával és a folyamat automatizálásához egy alkalmazásidentitás használatával.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf62330fd677dc978c8f25a81c6a1e5bfbb612ac
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717602"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Az Azure AD B2C kezelése Microsoft Graph

Microsoft Graph lehetővé teszi a címtárban található erőforrások Azure AD B2C kezelését. A következő Microsoft Graph API-műveletek támogatottak a Azure AD B2C-erőforrások, például a felhasználók, identitásszolgáltatók, felhasználói folyamatok, egyéni szabályzatok és szabályzatkulcsok kezeléséhez. A következő szakaszokban található minden hivatkozás a művelet api-Microsoft Graph tartozó oldalt célozza meg. 

## <a name="prerequisites"></a>Előfeltételek

Az MS Graph API- és a Azure AD B2C-bérlő erőforrásainak használatával kapcsolatban olyan alkalmazásregisztrációra van szükség, amely erre engedélyt ad. A Manage [Azure AD B2C with Microsoft Graph](microsoft-graph-get-started.md) (Alkalmazás kezelése a következővel) cikkben található lépéseket követve hozzon létre egy alkalmazásregisztrációt, amit a felügyeleti alkalmazás használhat. 

## <a name="user-management"></a>Felhasználókezelés

- [Felhasználók listázása](/graph/api/user-list)
- [Fogyasztói felhasználó létrehozása](/graph/api/user-post-users)
- [Felhasználó lekérte](/graph/api/user-get)
- [Felhasználó frissítése](/graph/api/user-update)
- [Felhasználó törlése](/graph/api/user-delete)

## <a name="user-phone-number-management-beta"></a>Felhasználó telefonszámának kezelése (bétaverzió)

A felhasználók által SMS-sel vagy hanghívásokkal, vagy többtényezős hitelesítéssel [való bejelentkezéshez használható telefonszám.](multi-factor-authentication.md) [](identity-provider-local.md#phone-sign-in-preview) További információ: [Azure AD hitelesítési módszerek API.](/graph/api/resources/phoneauthenticationmethod)

- [Hozzáadás](/graph/api/authentication-post-phonemethods)
- [Lista](/graph/api/authentication-list-phonemethods)
- [Lekérés](/graph/api/phoneauthenticationmethod-get)
- [Frissítés](/graph/api/phoneauthenticationmethod-update)
- [Törlés](/graph/api/phoneauthenticationmethod-delete)

Vegye figyelembe, [hogy a listaművelet](/graph/api/authentication-list-phonemethods) csak engedélyezett telefonszámokat ad vissza. A következő telefonszámot kell engedélyezni a listaműveletekkel való használathoz. 

![Telefonos bejelentkezés engedélyezése](./media/microsoft-graph-operations/enable-phone-sign-in.png)

> [!NOTE]
> A jelenlegi bétaverzióban ez az API csak akkor működik, ha a telefonszám az országkód és a telefonszám között szóközt tartalmaz. A Azure AD B2C szolgáltatás jelenleg nem adja hozzá ezt a területet alapértelmezés szerint.

## <a name="self-service-password-reset-email-address-beta"></a>Önkiszolgáló jelszó-visszaállítási e-mail-cím (bétaverzió)

Egy felhasználónévvel bejelentkező fiók [](identity-provider-local.md#username-sign-in) által a jelszó visszaállításához használható e-mail-cím. További információ: [Azure AD hitelesítési módszerek API.](/graph/api/resources/emailauthenticationmethod)

- [Hozzáadás](/graph/api/emailauthenticationmethod-post)
- [Lista](/graph/api/emailauthenticationmethod-list)
- [Lekérés](/graph/api/emailauthenticationmethod-get)
- [Frissítés](/graph/api/emailauthenticationmethod-update)
- [Törlés](/graph/api/emailauthenticationmethod-delete)

## <a name="identity-providers"></a>Identitásszolgáltatók

Kezelheti a [felhasználói folyamatok](add-identity-provider.md) számára elérhető identitásszolgáltatókat a Azure AD B2C bérlőben.

- [List identity providers registered in the Azure AD B2C tenant](/graph/api/identityprovider-list)
- [Identitásszolgáltató létrehozása](/graph/api/identityprovider-post-identityproviders)
- [Identitásszolgáltató lekérte](/graph/api/identityprovider-get)
- [Identitásszolgáltató frissítése](/graph/api/identityprovider-update)
- [Identitásszolgáltató törlése](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Felhasználói folyamat

Előre felépített szabályzatok konfigurálása regisztrációhoz, bejelentkezéshez, kombinált regisztrációhoz és bejelentkezéshez, jelszó-visszaállításhoz és profilfrissítéshez.

- [Felhasználói folyamatok felsorolása](/graph/api/identitycontainer-list-b2cuserflows)
- [Felhasználói folyamat létrehozása](/graph/api/identitycontainer-post-b2cuserflows)
- [Felhasználói folyamat lefolyása](/graph/api/b2cidentityuserflow-get)
- [Felhasználói folyamat törlése](/graph/api/b2cidentityuserflow-delete)

## <a name="user-flow-authentication-methods-beta"></a>Felhasználói folyamat hitelesítési módszerei (bétaverzió)

Válasszon egy olyan mechanizmust, amely lehetővé teszi a felhasználók számára a helyi fiókokon keresztüli regisztrációt. A helyi fiókok azok a fiókok, amelyek esetében az Azure AD teszi az identitás helyességi feltételét. További információ: [b2cAuthenticationMethodsPolicy erőforrástípus.](/graph/api/resources/b2cauthenticationmethodspolicy)

- [Lekérés](/graph/api/b2cauthenticationmethodspolicy-get)
- [Frissítés](/graph/api/b2cauthenticationmethodspolicy-update)

## <a name="custom-policies"></a>Egyéni szabályzatok

A következő műveletekkel kezelheti a Azure AD B2C Trust Framework szabályzatokat, más néven az [egyéni szabályzatokat.](custom-policy-overview.md)

- [A bérlőben konfigurált összes megbízhatósági keretrendszer-szabályzat felsorolása](/graph/api/trustframework-list-trustframeworkpolicies)
- [Megbízhatósági keretrendszer szabályzatának létrehozása](/graph/api/trustframework-post-trustframeworkpolicy)
- [Meglévő megbízhatósági keretrendszer-szabályzat tulajdonságainak olvasása](/graph/api/trustframeworkpolicy-get)
- [A megbízhatósági keretrendszer szabályzatának frissítése vagy létrehozása.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Meglévő megbízhatósági keretrendszer-szabályzat törlése](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Szabályzatkulcsok

A Identity Experience Framework tárolja az egyéni szabályzatban hivatkozott titkos adatokat az összetevők közötti megbízhatóság létrehozásához. Ezek a titkos kulcsok szimmetrikus vagy aszimmetrikus kulcsok/értékek is lehetek. A Azure Portal entitások szabályzatkulcsokként **jelennek meg.**

Az Microsoft Graph API házirendkulcsait kezelő legfelső szintű erőforrás a Megbízható keretrendszer [kulcskészlete.](/graph/api/resources/trustframeworkkeyset) Minden **kulcskészlet** legalább egy kulcsot **tartalmaz.** Kulcs létrehozásához először hozzon létre egy üres kulcskészletet, majd hozzon létre egy kulcsot a kulcskészletben. Létrehozhat manuálisan titkos adatokat, feltölthet egy tanúsítványt vagy egy PKCS12-kulcsot. A kulcs lehet egy létrehozott titkos kulcs, egy sztring (például a Facebook-alkalmazás titkos kulcsa) vagy egy feltöltött tanúsítvány. Ha egy kulcskészlet több kulcsból áll, csak az egyik aktív.

### <a name="trust-framework-policy-keyset"></a>Megbízhatósági keretrendszer szabályzatkulcskészlete

- [A megbízhatósági keretrendszer kulcskészletei](/graph/api/trustframework-list-keysets)
- [Megbízhatósági keretrendszer kulcskészletének létrehozása](/graph/api/trustframework-post-keysets)
- [Kulcskészlet lekérte](/graph/api/trustframeworkkeyset-get)
- [Megbízhatósági keretrendszer kulcskészletének frissítése](/graph/api/trustframeworkkeyset-update)
- [Megbízhatósági keretrendszer kulcskészletének törlése](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Megbízhatósági keretrendszer szabályzatkulcsa

- [Aktuálisan aktív kulcs lekért kulcs a kulcskészletben](/graph/api/trustframeworkkeyset-getactivekey)
- [Kulcs létrehozása a kulcskészletben](/graph/api/trustframeworkkeyset-generatekey)
- [Sztringalapú titkos fájl feltöltése](/graph/api/trustframeworkkeyset-uploadsecret)
- [X.509-tanúsítvány feltöltése](/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12 formátumú tanúsítvány feltöltése](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Alkalmazások

- [Alkalmazások listázása](/graph/api/application-list)
- [Alkalmazás létrehozása](/graph/api/resources/application)
- [Az alkalmazás frissítése](/graph/api/application-update)
- [ServicePrincipal létrehozása](/graph/api/resources/serviceprincipal)
- [Oauth2Permission Grant létrehozása](/graph/api/resources/oauth2permissiongrant)
- [Alkalmazás törlése](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Alkalmazásbővítmény tulajdonságai

- [Listabővítmény tulajdonságai](/graph/api/application-list-extensionproperty)

Azure AD B2C egy olyan könyvtárat biztosít, amely felhasználónként 100 egyéni attribútumot képes tartalmazni. Felhasználói folyamatok esetén ezeket a bővítménytulajdonságokat [a következő Azure Portal.](user-flow-custom-attributes.md) Egyéni szabályzatok Azure AD B2C létrehozza a tulajdonságot, amikor a szabályzat először ír értéket a bővítménytulajdonságba.

## <a name="audit-logs"></a>Naplók

- [Auditnaplók listába sorolva](/graph/api/directoryaudit-list)

További információ az auditnaplók Azure AD B2C: Hozzáférés Azure AD B2C [auditnaplókhoz.](view-audit-logs.md)

## <a name="conditional-access"></a>Feltételes hozzáférés

- [Az összes feltételes hozzáférési szabályzat felsorolása](/graph/api/conditionalaccessroot-list-policies?tabs=http)
- [Feltételes hozzáférési szabályzat tulajdonságainak és kapcsolatainak olvasása](/graph/api/conditionalaccesspolicy-get)
- [Új feltételes hozzáférési szabályzat létrehozása](/graph/api/resources/application)
- [Feltételes hozzáférési szabályzat frissítése](/graph/api/conditionalaccesspolicy-update)
- [Feltételes hozzáférési szabályzat törlése](/graph/api/conditionalaccesspolicy-delete)

## <a name="code-sample-how-to-programmatically-manage-user-accounts"></a>Kódminta: Felhasználói fiókok programozott kezelése

Ez a kódminta egy .NET Core-konzolalkalmazás, amely az [Microsoft Graph SDK-val](/graph/sdks/sdks-overview) kommunikál az Microsoft Graph API-val. A kódja bemutatja, hogyan hívható meg az API a felhasználók programozott módon történő kezeléséhez egy Azure AD B2C bérlőben.
Letöltheti a [mintaarchívum](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management/archive/master.zip) (*.zip) [](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) fájlt, böngészhet az adattárban a GitHubon, vagy klónozhatja az adattárat:

```cmd
git clone https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management.git
```

Miután beszerezta a kódmintát, konfigurálja a környezethez, majd állítsa össze a projektet:

1. Nyissa meg a projektet a [Visual Studio](https://visualstudio.microsoft.com) vagy [a Visual Studio kódban.](https://code.visualstudio.com)
1. Nyissa meg a következő fájlt: `src/appsettings.json`.
1. A szakaszban cserélje le a helyére a bérlője nevét, a és a értéket pedig a felügyeleti alkalmazás `appSettings` `your-b2c-tenant` `Application (client) ID` `Client secret` regisztrációja értékeire. További információ: Register a Microsoft Graph Application (Alkalmazás [regisztrálása).](microsoft-graph-get-started.md)
1. Nyisson meg egy konzolablakot az adatbázis helyi klónján belül, váltson át a `src` könyvtárra, majd buildje a projektet:

    ```console
    cd src
    dotnet build
    ```
    
1. Futtassa az alkalmazást az `dotnet` paranccsal:

    ```console
    dotnet bin/Debug/netcoreapp3.1/b2c-ms-graph.dll
    ```

Az alkalmazás megjeleníti a végrehajtható parancsok listáját. Lekért például minden felhasználót, egyetlen felhasználót kér le, töröl egy felhasználót, frissíti a felhasználó jelszavát, és tömeges importálást is kap.

### <a name="code-discussion"></a>Kódbefedő

A mintakód a [Microsoft Graph SDK-t](/graph/sdks/sdks-overview)használja, amely megkönnyíti az alkalmazásokhoz hozzáférő kiváló minőségű, hatékony és rugalmas Microsoft Graph.

A Microsoft Graph API-hoz Microsoft Graph hozzáférési jogkivonatra van szükség a hitelesítéshez. A megoldás a [Microsoft.Graph.Auth](https://www.nuget.org/packages/Microsoft.Graph.Auth/) NuGet-csomagot használja, amely a Microsoft Authentication Library (MSAL) hitelesítési forgatókönyv-alapú burkolóját biztosítja az Microsoft Graph SDK-val való használathoz.

A `RunAsync` _Program.cs fájl metódusa:_

1. Beolvassa az alkalmazásbeállításokat _appsettings.jsfájlban található fájlból_
1. Az [OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) ügyfél-hitelesítő adatok megadásának folyamatával inicializálja a hitelesítési szolgáltatót. Az ügyfél-hitelesítő adatok megadásának folyamatával az alkalmazás le tud szerezni egy hozzáférési jogkivonatot a Microsoft Graph API hívásához.
1. Beállítja a Microsoft Graph-ügyfélt a hitelesítési szolgáltatóval:

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

Az inicializált *GraphServiceClient* ezután a _UserService.cs_ fájlban lesz használva a felhasználókezelési műveletek végrehajtásához. Lekért például egy listát a bérlő felhasználói fiókjairól:

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

Az API-hívások Microsoft Graph [SDK-k](/graph/sdks/create-requests) használatával való hívása információkat tartalmaz az Microsoft Graph-ból származó információk beírásával, a visszaadott tulajdonságok vezérlésével, az egyéni lekérdezési paraméterek megírásával, valamint a és a lekérdezési paraméter használatával `$select` `$filter` `$orderBy` kapcsolatban.

<!-- LINK -->

[graph-objectIdentity]: /graph/api/resources/objectidentity
[graph-user]: (https://docs.microsoft.com/graph/api/resources/user)
