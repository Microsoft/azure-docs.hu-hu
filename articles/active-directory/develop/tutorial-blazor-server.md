---
title: Oktatóanyag – a Microsoft Identity platformot használó Blaze Server-alkalmazás létrehozása hitelesítéshez | Azure
titleSuffix: Microsoft identity platform
description: Ebben az oktatóanyagban a Microsoft Identity platform használatával állíthatja be a hitelesítést a Blazer Server-alkalmazásokban.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 5a631d9ae7a7d1792e3c4e4a2cbf8281e1168283
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226017"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Oktatóanyag: a Microsoft Identity platformot használó blézer-kiszolgálói alkalmazás létrehozása a hitelesítéshez

Ebben az oktatóanyagban egy olyan Blazer Server-alkalmazást hoz létre, amely bejelentkezik a felhasználók számára, és beolvassa a Microsoft Graph adatait a Microsoft Identity platform használatával, és regisztrálja az alkalmazást a Azure Active Directoryban (Azure AD).

A [Blazer WASM](tutorial-blazor-webassembly.md)oktatóanyaga is rendelkezésre áll.

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Azure Active Directory (Azure AD) használatára konfigurált új Blazer Server-alkalmazás létrehozása hitelesítéshez
> * A hitelesítést és az engedélyezést a Microsoft. Identity. Web használatával kezelheti
> * Adatok beolvasása védett webes API-ból, Microsoft Graph

## <a name="prerequisites"></a>Előfeltételek

- [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Egy Azure AD-bérlő, ahol regisztrálhat egy alkalmazást. Ha nincs hozzáférése egy Azure AD-bérlőhöz, beszerezhet egyet a [Microsoft 365 fejlesztői programhoz](https://developer.microsoft.com/microsoft-365/dev-program) való regisztrációval, vagy egy [ingyenes Azure-fiók](https://azure.microsoft.com/free)létrehozásával.

## <a name="register-the-app-in-the-azure-portal"></a>Az alkalmazás regisztrálása a Azure Portalban

Minden Azure Active Directory (Azure AD) hitelesítést használó alkalmazást regisztrálni kell az Azure AD-ben. Kövesse az [alkalmazás regisztrálása](quickstart-register-app.md) a következő kiegészítésekkel című témakör utasításait:

- **Támogatott fióktípus** esetén **csak a szervezeti címtárban** válassza a fiókok lehetőséget.
- Hagyja meg az **átirányítási URI** legördülő listát a web értékre, és írja be a **következőt** : `https://localhost:5001/signin-oidc` . A vércse-on futó alkalmazások alapértelmezett portja 5001. Ha az alkalmazás egy másik porton érhető el, a portszámot a helyett kell megadnia `5001` .

A **kezelés** területen válassza a **hitelesítés**  >  **implicit engedélyezés és a hibrid folyamatok** elemet. Válassza a **hozzáférési jogkivonatok** és **azonosító tokenek** lehetőséget, majd kattintson a **Mentés** gombra.

Végül, mivel az alkalmazás meghívja a védett API-t (ebben az esetben Microsoft Graph), az ügyfél titkos kulcsára van szükség ahhoz, hogy igazolja az identitását, amikor hozzáférési jogkivonatot kér az API meghívásához.

1. Ugyanannak az alkalmazásnak a regisztrációja alatt a **kezelés** területen válassza a **tanúsítványok & Secrets** elemet.
2. Hozzon létre egy **új ügyfél titkot** , amely soha nem jár le.
3. Jegyezze fel a titkos kulcs **értékét** , mert a következő lépésben használni fogja. A panelről való kilépést követően többé nem férhet hozzá. Azonban szükség esetén újra létrehozhatja.

## <a name="create-the-app-using-the-net-cli"></a>Az alkalmazás létrehozása a .NET parancssori felület használatával

Futtassa a következő parancsot a Microsoft. Identity. Web sablonjainak letöltéséhez, amelyet a jelen oktatóanyagban használunk majd.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

Ezután futtassa a következő parancsot az alkalmazás létrehozásához. Cserélje le a parancsban található helyőrzőket az alkalmazás áttekintés lapjáról, és hajtsa végre a parancsot egy parancs-rendszerhéjban. A beállítással megadott kimeneti hely `-o|--output` létrehoz egy projekt mappát, ha az nem létezik, és az alkalmazás nevének részévé válik.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Helyőrző   | Azure Portal neve       | Példa                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | Alkalmazás (ügyfél) azonosítója | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | Címtár (bérlő) azonosítója   | `e86c78e2-0000-0000-0000-918e0565a45e` |

Most navigáljon az új Blazer-alkalmazáshoz a szerkesztőben, és adja hozzá az ügyfél titkos kulcsát a *appsettings.js* fájlhoz, és cserélje le a "Secret-from-app-Registration" szöveget.

```json
"ClientSecret": "secret-from-app-registration",
```

## <a name="test-the-app"></a>Az alkalmazás tesztelése

Most már létrehozhatja és futtathatja az alkalmazást. Ha futtatja ezt a sablon alkalmazást, meg kell adnia a keretrendszert, amelyet a-keretrendszer használatával kell futtatni. Ez az oktatóanyag a .NET Core 3,1 SDK-t használja.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

A böngészőben nyissa meg a alkalmazást `https://localhost:5001` , és jelentkezzen be egy Azure ad-felhasználói fiók használatával, és tekintse meg az alkalmazást futtató alkalmazást.

## <a name="retrieving-data-from-microsoft-graph"></a>Adatok beolvasása a Microsoft Graphból

[Microsoft Graph](/graph/overview) számos olyan API-t kínál, amelyek hozzáférést biztosítanak a felhasználók Microsoft 365 adataihoz. Ha a Microsoft Identity platformot használja az alkalmazáshoz tartozó identitás-szolgáltatóként, könnyebben hozzáférhet ezekhez az adatokhoz, mivel Microsoft Graph közvetlenül támogatja a Microsoft Identity platform által kiállított jogkivonatokat. Ebben a szakaszban kódot adhat hozzá a bejelentkezett felhasználó e-mail-címének megjelenítéséhez az alkalmazás "lekérési információk" lapján.

Mielőtt elkezdené, jelentkezzen ki az alkalmazásból, mert a szükséges engedélyeket módosítja, és az aktuális jogkivonat nem fog működni. Ha még nem tette meg, futtassa újra az alkalmazást, és válassza a **kijelentkezés** lehetőséget az alábbi kód frissítése előtt.

Most frissítenie kell az alkalmazás regisztrációját és kódját, hogy lekérje a felhasználó e-mail-címét, és megjeleníti az üzeneteket az alkalmazáson belül. Ennek eléréséhez először ki kell terjesztenie az alkalmazás regisztrációs engedélyeit az Azure AD-ben, hogy engedélyezze az e-mailek elérését. Ezután vegyen fel kódot a Blazer alkalmazásba, és jelenítse meg ezeket az adatlapokat az egyik oldalon.

1. A Azure Portal válassza ki az alkalmazást **Alkalmazásregisztrációkban**.
1. A **kezelés** területen válassza az **API-engedélyek** lehetőséget.
1. Válassza **az engedély hozzáadása**  >  **Microsoft Graph** elemet.
1. Válassza a **delegált engedélyek** elemet, majd keresse meg és válassza ki a **mail. Read** engedélyt.
1. Válassza az **engedélyek hozzáadása** lehetőséget.

A *appsettings.js* fájlon frissítse a kódot, hogy a megfelelő engedélyekkel beolvassa a megfelelő jogkivonatot. Adja hozzá a "mail. Read" kifejezést a "user. Read" hatókörben a "DownstreamAPI" alatt. Ez határozza meg, hogy az alkalmazás mely hatókörökhöz (vagy engedélyekhez) kér hozzáférést.

```json
"Scopes": "user.read mail.read"
```

Ezután frissítse a kódot a *FetchData. Razor* fájlban az e-mail-adatok lekéréséhez az alapértelmezett (véletlenszerű) időjárási adatok helyett. Cserélje le a fájlban lévő kódot a következőre:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

        if (dataRequest.IsSuccessStatusCode)
        {
            var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
            userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
        }

        var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

        if (mailRequest.IsSuccessStatusCode)
        {
            var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
            var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

            foreach (var m in messagesArray)
            {
                var message = new MailMessage();
                message.Subject = m.GetProperty("subject").GetString();
                message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                messages.Add(message);
            }
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

Az alkalmazás elindítása. Megfigyelheti, hogy az újonnan hozzáadott engedélyek megadását kéri, ami azt jelzi, hogy minden a várt módon működik. Mostantól az alapszintű felhasználói profilhoz tartozó adatokon túl az alkalmazás hozzáférést kér az e-mail-adatokhoz.

A jóváhagyás megadása után navigáljon az "adat lekérése" lapra, és olvasson el néhány e-mailt.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Képernyőkép a végleges alkalmazásról. Van egy fejléce, amely szerint a Hello Nicholas, és megjeleníti a Nicholas-hoz tartozó e-mailek listáját.":::

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan hívhat meg olyan webalkalmazásokat, amelyek a többrészes forgatókönyvek felhasználói számára jelentkeznek be:

> [!div class="nextstepaction"]
> [Forgatókönyv: a felhasználók által bejelentkezett webalkalmazás](scenario-web-app-sign-user-overview.md)
