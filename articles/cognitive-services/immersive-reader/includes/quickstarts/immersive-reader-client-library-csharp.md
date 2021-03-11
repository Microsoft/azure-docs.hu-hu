---
title: Az olvasó C# ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban létrehozhat egy webalkalmazást a semmiből, és hozzáadhatja a magával ragadó olvasó API funkcióját.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 442f51759b136200fd61706864568e580265e572
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619217"
---
A teljes körűen kialakított [olvasó](https://www.onenote.com/learningtools) egy kipróbált eljárás, amely az új olvasók, a nyelvi tanulók és a tanulással kapcsolatos különbségeket, például a diszlexiat is javítja. Az alkalmazásokban magával ragadó olvasóval elkülönítheti a szöveget a fókusz növeléséhez, képeket jeleníthet meg a gyakran használt szavakhoz, kiemelheti a beszéd részeit, beolvashatja a kijelölt szöveget hangosan, lefordíthatja a szavakat és kifejezéseket valós időben, és így tovább.

Ebben a rövid útmutatóban egy webalkalmazást hoz létre a semmiből, és integrálhatja a magával ragadó olvasót az olvasó ügyféloldali kódtár használatával. Ennek a rövid útmutatónak a teljes működő mintája elérhető [a githubon](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* A Azure Active Directory hitelesítéshez konfigurált, magával ragadó olvasó erőforrás. A beállításhoz kövesse az [alábbi utasításokat](../../how-to-create-immersive-reader.md) . A minta projekt tulajdonságainak konfigurálásakor itt létrehozott értékek némelyikére szüksége lesz. Mentse a munkamenet kimenetét szövegfájlba későbbi használatra.

## <a name="create-a-web-app-project"></a>Webalkalmazás-projekt létrehozása

Hozzon létre egy új projektet a Visual Studióban a ASP.NET Core webalkalmazás-sablon használatával, beépített modell-vezérlővel, és ASP.NET Core 2,1. Nevezze el a "QuickstartSampleWebApp" projektet.

![Új projekt – C #](../../media/quickstart-csharp/1-createproject.png)

![Új projekt konfigurálása – C #](../../media/quickstart-csharp/2-configureproject.png)

![Új ASP.NET Core webalkalmazás – C #](../../media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Hitelesítés beállítása

### <a name="configure-authentication-values"></a>Hitelesítési értékek konfigurálása

Kattintson a jobb gombbal a projektre a _megoldáskezelő_ , majd válassza a **felhasználói titkok kezelése** lehetőséget. Ekkor megnyílik egy _secrets.js_ nevű fájl. Ezt a fájlt a verziókövetés nem ellenőrzi. További információ [itt](/aspnet/core/security/app-secrets?tabs=windows). Cserélje le asecrets.jstartalmát a következőre, és adja _meg_ a magával ragadó olvasó erőforrásának létrehozásakor megadott értékeket.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="install-active-directory-nuget-package"></a>Active Directory NuGet-csomag telepítése

A következő kód a **Microsoft. IdentityModel. clients. ActiveDirectory** NuGet-csomag objektumait használja, ezért hozzá kell adnia egy hivatkozást az adott csomaghoz a projektben.

Nyissa meg a NuGet csomagkezelő konzolt a **Tools-> NuGet csomagkezelő-> Package Manager konzolon** , és futtassa a következő parancsot:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>A vezérlő frissítése a jogkivonat beszerzéséhez 

Nyissa meg a _Controllers\HomeController.cs_, és adja hozzá a következő kódot a fájl elején található _using_ utasítások után.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Most úgy konfigurálja a vezérlőt, hogy beszerezze az Azure AD-értékeket _secrets.jsról_. A _HomeController_ osztály tetején, az után ```public class HomeController : Controller {``` adja hozzá a következő kódot.

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Minta tartalmának hozzáadása
Először nyissa meg a _Views\Shared\Layout.cshtml_. A sor előtt ```</head>``` adja hozzá a következő kódot:

```html
@RenderSection("Styles", required: false)
```

Most hozzáadunk egy minta tartalmat ehhez a webalkalmazáshoz. Nyissa meg a _Views\Home\Index.cshtml_ , és cserélje le az összes automatikusan generált kódot a következő mintára:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

Figyelje meg, hogy az összes szöveg **lang** attribútummal rendelkezik, amely a szöveg nyelveit írja le. Ez az attribútum segíti a magával ragadó olvasót a megfelelő nyelvi és nyelvtani funkciók biztosításában.

## <a name="add-javascript-to-handle-launching-immersive-reader"></a>JavaScript hozzáadása a lebilincselő olvasó elindításához

A részletes olvasó függvénytár olyan funkciókat biztosít, mint például a magával ragadó olvasó elindítása és a magára ejtő olvasó gombok megjelenítése. További információ [itt](../../reference.md).

A _Views\Home\Index.cshtml_ alján adja hozzá a következő kódot:

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Készítsen buildet és futtassa az alkalmazást

A menüsávban válassza a **hibakeresés > a hibakeresés elindítása** lehetőséget, vagy nyomja le az **F5** billentyűt az alkalmazás elindításához.

A böngészőben a következőknek kell megjelennie:

![Minta alkalmazás – C #](../../media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>A lebilincselő olvasó elindítása

Ha a "magára olvasó" gombra kattint, megjelenik a megjelenő, az oldalon található tartalommal ellátott olvasó.

![Lebilincselő olvasó – C #](../../media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Erőforrás létrehozása és HRE konfigurálása](../../how-to-create-immersive-reader.md)