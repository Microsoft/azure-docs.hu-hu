---
title: Bing Autosuggest go ügyféloldali kódtár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 1c3ed6cc97c6fc77083baa915fac2f5530922eac
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947421"
---
Ismerkedjen meg az Bing Autosuggest ügyféloldali kódtáraval a Go-ban. Az alábbi lépéseket követve telepítheti a könyvtárat, és kipróbálhatja a példákat az alapszintű feladatokhoz.

Használja az Bing Autosuggest ügyféloldali függvénytárát a Go-hoz a keresési javaslatok lekérdezéséhez részleges lekérdezési karakterláncok alapján.

[Dokumentáció](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)  |  [Mintakód](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [ingyenesen létrehozhat egyet](https://azure.microsoft.com/free/cognitive-services).
* A [Go](https://golang.org/dl/)legújabb verziója.

Kezdje el használni az Bing Autosuggest ügyféloldali függvénytárat egy Azure-erőforrás létrehozásával. Válassza ki a megfelelő erőforrás-típust az Ön számára:

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>Környezeti változók létrehozása

>[!NOTE]
> Az 2019. július 1. után létrehozott erőforrás-végpontok az alábbi egyéni altartomány-formátumot használják. További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../../../cognitive-services-custom-subdomains.md).

Ha a kulcsot és a végpontot a létrehozott erőforrás alapján hozza létre, hozzon létre két környezeti változót a hitelesítéshez:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: A kérések hitelesítéséhez használt erőforrás-kulcs.
* `AUTOSUGGEST_ENDPOINT`: Az erőforrás-végpont API-kérelmek küldéséhez. A következőhöz hasonlóan kell kinéznie: `https://<your-custom-subdomain>.api.cognitive.microsoft.com`

Használja az operációs rendszerének utasításait.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

A környezeti változó hozzáadása után indítsa újra a konzolablak ablakát.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

### <a name="macos"></a>[macOS](#tab/unix)

Szerkessze a t `.bash_profile` , és adja hozzá a környezeti változót:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
***

## <a name="create-a-new-go-project"></a>Új go-projekt létrehozása

A konzol ablakban (cmd, PowerShell, Terminal, bash) hozzon létre egy új munkaterületet a go-projekt számára, és navigáljon hozzá. A munkaterület három mappát fog tartalmazni:

* **src**: Ez a könyvtár forráskódot és csomagokat tartalmaz. A paranccsal telepített csomagok `go get` itt fognak megjelenni.
* **pkg**: Ez a könyvtár tartalmazza a lefordított go csomag objektumait. Ezek a fájlok mindegyike rendelkezik `.a` bővítménnyel.
* **bin**: Ez a könyvtár tartalmazza a futtatásakor létrehozott bináris végrehajtható fájlokat `go install` .

> [!TIP]
> További információ a [Go-munkaterület](https://golang.org/doc/code.html#Workspaces)struktúrájáról. Ez az útmutató a és a beállításával kapcsolatos információkat tartalmaz `$GOPATH` `$GOROOT` .

Hozzon létre egy nevű munkaterületet `my-app` és a szükséges alkönyvtárakat a, a és a következőhöz `src` `pkg` `bin` :

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>Az ügyféloldali kódtár telepítése a Go-hoz

Most telepítse az ügyféloldali kódtárat a Go-hoz:

```bash
$ go get -u <library-location-or-url>
```

vagy ha a DEP-t használja a tárházon belül, futtassa a következőket:

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>Go-alkalmazás létrehozása

Ezután hozzon létre egy nevű fájlt `src/sample-app.go` :

```bash
$ cd src
$ touch sample-app.go
```

Nyissa meg `sample-app.go` a csomagot, adja hozzá a csomag nevét, majd importálja a következő kódtárakat:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Hozzon létre egy nevű függvényt `main` . Ezután hozzon létre környezeti változókat a Bing Autosuggest kulcshoz és végponthoz:

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan hajthat végre alapszintű feladatokat az Bing Autosuggest ügyféloldali kódtár használatával a Go-hoz:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [API-kérelem küldése](#send-an-api-request)

### <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a rövid útmutató azt feltételezi, hogy [létrehozott egy környezeti változót](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)  a Bing autojavaslati kulcshoz, a named nevű `BING_AUTOSUGGEST_SUBSCRIPTION_KEY` és egy a nevű végponthoz `BING_AUTOSUGGEST_ENDPOINT` .

A `main()` függvényben hozza létre az ügyfelet a végponttal és a kulccsal.

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>API-kérelem küldése

Ugyanebben a metódusban az ügyfél [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) metódusával küldhet lekérdezést a bingnek. Ezután ismételje meg a [javaslatok](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions) válaszát, és nyomtassa ki az első javaslatot.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa a go alkalmazást a `go run [arguments]` paranccsal az alkalmazás könyvtárából.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Egy erőforráscsoport törlése a Azure Portalban](../../../cognitive-services-apis-create-account.md#clean-up-resources).
* [Egy erőforráscsoport törlése az Azure CLI-ben](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Bing Autosuggest-oktatóanyag](../../tutorials/autosuggest.md)

## <a name="see-also"></a>Lásd még

- [Mi a Bing Autosuggest?](../../get-suggested-search-terms.md)
- [A Bing Autosuggest API 7-es verziójának referenciája](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
