---
title: 'Oktatóanyag: a REST API-k használata'
description: Ez az oktatóanyag leírja, hogyan használhatja az Azure hatáskörébe tartozó REST API-kat a katalógus tartalmának eléréséhez.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: bfb808c634ba946e1a4825d7828db6df8963352c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98951243"
---
# <a name="tutorial-use-the-rest-apis"></a>Oktatóanyag: a REST API-k használata

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure hatáskörébe REST API-kat. Bárki, aki egy Azure hatáskörébe tartozó katalógusba kívánja elküldeni az adatok beküldését, belefoglalja a katalógusba egy automatizált folyamat részeként, vagy saját felhasználói élményt hozhat létre a katalógusban a REST API-k használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Egyszerű szolgáltatásnév (alkalmazás) létrehozása.
> * Konfigurálja a katalógust úgy, hogy megbízzon az egyszerű szolgáltatásban (alkalmazás).
> * Tekintse meg a REST API-k dokumentációját.
> * Gyűjtse össze a szükséges értékeket a REST API-k használatához.
> * Használja a Poster-ügyfelet a REST API-k meghívásához.
> * .NET-ügyfél létrehozása a REST API-k meghívásához.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Első lépésként egy meglévő Azure-fiókkal kell rendelkeznie. Ha nem rendelkezik katalógussal, tekintse meg az Azure-beli [hatáskörébe tartozó fiók létrehozásának](create-catalog-portal.md)rövid útmutatóját.

* Ha hozzá kell adnia az adatait a katalógushoz, tekintse meg az [oktatóanyagot a Starter Kit és az adatvizsgálati szolgáltatás futtatásához](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-service-principal-application"></a>Egyszerű szolgáltatás létrehozása (alkalmazás)

Ahhoz, hogy egy REST API ügyfél hozzáférhessen a katalógushoz, az ügyfélnek rendelkeznie kell egy egyszerű szolgáltatással (alkalmazással), valamint egy identitással, amelyet a katalógus felismer és megbízhatóként konfigurált. Amikor REST API hívásokat kezdeményez a katalógusba, az egyszerű szolgáltatásnév identitását használja.

A meglévő egyszerű szolgáltatásokat (alkalmazás-azonosítókat) használó ügyfelek esetében nagy a hiba. Ezért javasoljuk, hogy hozzon létre egy új egyszerű szolgáltatást az API-k meghívásához.

Új egyszerű szolgáltatás létrehozása:

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a **Azure Active Directory**.
1. A **Azure Active Directory** lapon válassza a **Alkalmazásregisztrációk** lehetőséget a bal oldali ablaktáblán.
1. Válassza az **új regisztráció** lehetőséget.
1. Az **alkalmazás regisztrálása** oldalon:
    1. Adja meg az alkalmazás **nevét** (az egyszerű szolgáltatásnév).
    1. **Csak a szervezeti címtárban válassza a fiókok lehetőséget (csak _&lt; a &gt; bérlő neve_ – egyetlen bérlő)**.
    1. Az **átirányítási URI-hoz (nem kötelező)** válassza a **web** lehetőséget, és adjon meg egy értéket. Ennek az értéknek nem kell érvényes URI-nak lennie.
    1. Válassza a **Regisztráció** lehetőséget.
1. Az új szolgáltatásnév lapon másolja a **megjelenítendő név** és az **alkalmazás (ügyfél) azonosítójának** értékét a mentéshez később.

   Az alkalmazás azonosítója a `client_id` mintakód értéke.

Az egyszerű szolgáltatásnév (alkalmazás) használatához be kell szereznie a jelszavát. Ezt a következőképpen teheti meg:

1. A Azure Portal keresse meg és válassza ki a **Azure Active Directory**, majd a bal oldali panelen válassza a **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki a szolgáltatásnevet (alkalmazást) a listából.
1. Válassza ki a **tanúsítványok & a titkos kulcsokat** a bal oldali ablaktáblán.
1. Válassza az **Új titkos ügyfélkód** lehetőséget.
1. Az **ügyfél titkos kulcsának hozzáadása** lapon adja meg a **leírást**, válasszon ki egy lejárati időt a **lejárat** területen, majd válassza a **Hozzáadás** lehetőséget.

   Az **ügyfél-titkok** lapon az új titkos kulcs **Value** oszlopában lévő karakterlánc a jelszava. Mentse ezt az értéket.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Az ügyfél titkos kulcsát ábrázoló képernyőkép.":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>A katalógus konfigurálása az egyszerű szolgáltatás megbízhatóságára (alkalmazás)

Az Azure hatáskörébe az új szolgáltatásnév megbízhatóságának beállítása:

1. Navigáljon a hatáskörébe-fiókjába

1. A **hatáskörébe tartozó fiók** lapon válassza a **hozzáférés-vezérlés (iam) lapot.**

1. Kattintson a **+ Hozzáadás** gombra

1. Válassza a **szerepkör-hozzárendelés hozzáadása** elemet.

1. A szerepkör típusa a következőben: **hatáskörébe tartozó Adatkurátor**

    > [!Note]
    > Az Azure hatáskörébe Data Catalog engedélyekkel kapcsolatos további információkért lásd: [katalógus engedélyei](catalog-permissions.md). Ha például engedélyre van szüksége a vizsgálat elindításához, a szerepkör típusának a **hatáskörébe tartozó adatforrás-rendszergazdának** kell lennie.

1. Az alapértelmezett, a **felhasználó, a csoport vagy az egyszerű szolgáltatásnév** elhagyása a **hozzáférés kiosztásával**

1. A **kiválasztás** mezőben adja meg a hozzárendelni kívánt Previosly nevű egyszerű szolgáltatásnév nevét, majd kattintson a nevére az eredmények ablaktábláján.

1. Kattintson a **Mentés** gombra.

Ezzel konfigurálta az egyszerű szolgáltatást alkalmazás-rendszergazdaként, amely lehetővé teszi a tartalom küldését a katalógusba.

## <a name="view-the-rest-apis-documentation"></a>A REST API-k dokumentációjának megtekintése

Az API hencegő dokumentációjának megtekintéséhez töltse le [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip), bontsa ki a fájljait, és nyissa meg index.html.

Ha többet szeretne megtudni az Azure hatáskörébe tartozó speciális keresési/javaslati API-ról, tekintse meg a REST API keresési szűrő dokumentációját. Az autorest által generált ügyfél jelenleg nem támogatja a testreszabott keresési paramétereket. A megkerülő megoldáshoz kövesse a keresés – szűrő dokumentumot, hogy definiáljon-e szűrési osztályokat a kódban API-hívási paraméterekként. A index.html dokumentum példákat tartalmaz ezekre az API-kra.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>A REST API-k használatához szükséges értékek összegyűjtése

Keresse meg és mentse a következő értékeket:

* Bérlő azonosítója:
  * A [Azure Portal](https://portal.azure.com)keresse meg és válassza a **Azure Active Directory** lehetőséget.
  * A bal oldali ablaktábla **kezelés** szakaszában válassza a **Tulajdonságok** elemet, keresse meg a **bérlő azonosítóját**, majd válassza a **Másolás a vágólapra** ikont az érték mentéséhez.
* Atlas-végpont:
  * A Azure Portal [Azure hatáskörébe tartozó fiókok lapján](https://aka.ms/purviewportal) keresse meg és válassza ki az Azure-beli hatáskörébe tartozó fiókot a listában.
  * Válassza a **Tulajdonságok** lehetőséget, keresse meg az **Atlas-végpontot**, majd válassza a **Másolás a vágólapra** ikont az érték mentéséhez. Ha később használja, távolítsa el a karakterlánc *https://* részét.
* Fiók neve:
  * Bontsa ki a katalógus nevét az Atlas Endpoint sztringből. Ha például az Atlas-végpontja `https://ThisIsMyCatalog.catalog.purview.azure.com` , a fiók neve `ThisIsMyCatalog` .

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>A Poster-ügyfél használata a REST API-k meghívásához

1. Telepítse a [Poster-ügyfelet](https://www.getpostman.com/).
1. Az ügyfél lapon válassza az **Importálás** lehetőséget, majd használja [ aTest.postman_collection.js](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json).
1. Válassza a **gyűjtemények** lehetőséget, majd válassza a **teszt** lehetőséget.
1. Válassza a **lekérési jogkivonat** elemet:
    1. A POST következő URL-címében cserélje le a *&lt; -bérlő- &gt; azonosítót* az előző szakaszban MÁSOLt bérlői azonosítóra.
    1. Válassza a **törzs** fület, és cserélje le a helyőrzőket az előző lépésben megadott elérési útra és törzsre.

       Miután kiválasztotta a **Send (Küldés**) lehetőséget, a válasz törzse JSON-struktúrát tartalmaz, beleértve a nevet *access_token* és egy idézett karakterlánc-értéket. 
    1. Másolja a tulajdonosi jogkivonat értékét (idézőjelek nélkül) a következő lépésben való használatra.

1. **/V2/types/typedefs** kiválasztása:
    1. Cserélje le az elérési út helyőrzőjét az Atlas-végpont értékére. Ez az érték az Ibiza-portálon található Catalog-példányra, majd az Áttekintés elemre kattintva szerezhető be. 

       A tulajdonosi jogkivonat az első lépésben van beállítva (vagy manuálisan is másolhatja az "engedélyezés" lapon).

    1. A válasz beszerzéséhez válassza a **Küldés** lehetőséget.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>.NET-ügyfél létrehozása a REST API-k meghívásához

### <a name="install-autorest"></a>Az automatikus Rest telepítése



1. [Telepítse a Node.js](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md).
1. Nyissa meg a PowerShellt, és futtassa az alábbi parancsot:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>Töltse le rest-api-specs.zip és hozza létre az ügyfelet

1. Töltse le [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) és bontsa ki a fájljait.
1. Futtassa a következő parancsot a PowerShellben a REST-API-specs kibontott mappából:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   Ennek a folyamatnak a kimenete a REST-API-specs mappában található PurviewCatalogClient és almappa CSharp hozza létre.

### <a name="create-a-sample-net-console-application"></a>Minta .NET-konzolbeli alkalmazás létrehozása

1. Nyissa meg a Visual Studio 2019 alkalmazást. Ezek az utasítások az ingyenes közösségi kiadással lettek tesztelve.
1. A **create a New Project (új projekt létrehozása** ) lapon hozzon létre egy **Console app (.net Core)** projektet a C#-ban.
1. Másolja és illessze be a megadott [mintakód](#sample-code-for-the-console-application).
1. Cserélje le a *accountName*, a *servicePrincipalId*, a *servicePrincipalKey* és a *tenantId* értéket a korábban összegyűjtött értékekre.
1. A **megoldáskezelő** használatával hozzáadhat egy **létrehozott** mappát a Visual Studio-projektben.
1. Másolja a korábban létrehozott rest-api-specs\PurviewCatalogClient\CSharp mappát a \Generated mappába. A másolási művelethez használja a fájlkezelőt vagy a parancssort, amely elindítja a Visual studiót, hogy automatikusan hozzáadja a fájlokat a projekthez.
1. **Megoldáskezelő** kattintson a jobb gombbal a projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.
1. Válassza a **Tallózás** lapot. Keresse meg és válassza a **Microsoft. Rest. ClientRuntime** elemet.
1. Győződjön meg arról, hogy a verzió legalább 2.3.21, majd válassza a **telepítés** lehetőséget.
1. Hozza létre és futtassa az alkalmazást.

A mintakód azt a darabszámot adja vissza, hogy hány TypeDef szerepel a katalógusban, és bemutatja, hogyan kezelheti a szerepkör-hozzárendeléseket. Részletekért lásd: a `DoRoleAssignmentOperations()` mintakód. További információ a projektről: [Project Setup (projekt beállítása](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md)).

### <a name="sample-code-for-the-console-application"></a>Mintakód a konzol alkalmazáshoz

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatforrások kezelése](manage-data-sources.md)
