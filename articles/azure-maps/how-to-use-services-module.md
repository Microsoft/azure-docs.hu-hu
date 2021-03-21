---
title: A Azure Maps Services modul használata | Microsoft Azure térképek
description: További információ a Azure Maps Services modulról. Tekintse meg, hogyan tölthető be és hogyan használhatja ezt a segítő függvénytárat Azure Maps REST-szolgáltatások eléréséhez a web-vagy Node.js alkalmazásokban.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 2e07b614e87ed5dad94cf9bc5994e78071187839
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008599"
---
# <a name="use-the-azure-maps-services-module"></a>A Azure Maps Services modul használata

A Azure Maps web SDK egy *Services modult* biztosít. Ez a modul egy segítő könyvtár, amely megkönnyíti a Azure Maps REST-szolgáltatások használatát a webes vagy Node.js alkalmazásokban JavaScript vagy írógéppel használatával.

## <a name="use-the-services-module-in-a-webpage"></a>Weblapon található szolgáltatások modul használata

1. Hozzon létre egy új HTML-fájlt.
1. Töltse be a Azure Maps Services modult. Kétféleképpen is betöltheti:
    - Használja az Azure Maps Services modul globálisan üzemeltetett, Azure Content Delivery Network verzióját. Adjon hozzá egy parancsfájl-hivatkozást a `<head>` fájl eleméhez:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Azt is megteheti, hogy az [Azure-Maps-Rest](https://www.npmjs.com/package/azure-maps-rest) NPM csomag használatával helyileg betölti a Azure Maps web SDK forráskódját, majd futtatja azt az alkalmazással. Ez a csomag írógéppel kapcsolatos definíciókat is tartalmaz. Használja a következő parancsot:
    
        > **npm install azure-maps-rest**
    
        Ezután adjon hozzá egy parancsfájl-hivatkozást a `<head>` fájl eleméhez:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Hitelesítési folyamat létrehozása. A szolgáltatás URL-címének ügyféloldali végpontjának inicializálásához létre kell hoznia a folyamatot. A Azure Maps keresési szolgáltatás ügyfelének hitelesítéséhez használja a saját Azure Maps fiókjának kulcsát vagy Azure Active Directory (Azure AD) hitelesítő adatait. Ebben a példában a keresési szolgáltatás URL-ügyfele lesz létrehozva. 

    Ha előfizetés-kulcsot használ a hitelesítéshez:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Ha az Azure AD-t használja a hitelesítéshez:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    További információ: [hitelesítés Azure Mapssal](azure-maps-authentication.md).

1. Az alábbi kód az újonnan létrehozott Azure Maps keresési szolgáltatás URL-ügyfelét használja a következő cím geocode: "1 Microsoft Way, Redmond, WA". A kód a `searchAddress` függvényt használja, és az eredményeket táblázatként jeleníti meg az oldal törzsében.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Itt látható a teljes, futó kód minta:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A szolgáltatások modul használata" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>szolgáltatások modullal</a> Azure Maps () használatával a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Felhő-támogatás Azure Government

A Azure Maps web SDK támogatja a Azure Government-felhőt. A Azure Maps web SDK eléréséhez használt összes JavaScript és CSS URL-cím változatlan marad, azonban a következő feladatokat kell elvégezni a Azure Maps platform Azure Government Cloud-verziójához való kapcsolódáshoz.

Az interaktív térkép vezérlőelem használatakor adja hozzá a következő kódrészletet az osztály egy példányának létrehozása előtt `Map` . 

```javascript
atlas.setDomain('atlas.azure.us');
```

Győződjön meg arról, hogy a Térkép és a szolgáltatások hitelesítése során Azure Maps hitelesítési adatokat használ a Azure Government Cloud platformon.

A szolgáltatások modul használatakor a szolgáltatás tartományát be kell állítani az API URL-végpontok példányának létrehozásakor. A következő kód például létrehozza a osztály egy példányát, `SearchURL` és a tartományt a Azure Government felhőre mutat.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Ha közvetlenül fér hozzá a Azure Maps REST-szolgáltatásokhoz, módosítsa az URL-tartományt a következőre: `atlas.azure.us` . Ha például a Search API szolgáltatást használja, módosítsa az URL-tartományt a következőre: `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/` .

## <a name="next-steps"></a>Következő lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

A szolgáltatások modult használó további példákért tekintse meg a következő cikkeket:

> [!div class="nextstepaction"]
> [Keresési eredmények megjelenítése a térképen](./map-search-location.md)

> [!div class="nextstepaction"]
> [Koordináta információinak lekérése](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Útvonal megjelenítése A-ból B-be](./map-route.md)