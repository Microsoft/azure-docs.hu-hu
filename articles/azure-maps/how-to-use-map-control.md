---
title: Ismerkedés a Microsoft Azure Maps webes Térkép vezérlőelemmel
description: 'Ismerje meg, hogyan adhat hozzá térképeket webes és mobil alkalmazásokhoz a Azure Maps térképkezelés ügyféloldali JavaScript-kódtár használatával. Lásd: a térképek honosítása.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b283cacf94ff3567428787e50a3d7db9ba91fd9c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864040"
---
# <a name="use-the-azure-maps-map-control"></a>Az Azure Maps-térképvezérlő használata

A térképkezelés ügyféloldali JavaScript-kódtár lehetővé teszi a Maps és a Embedded Azure Maps funkcióinak megjelenítését a webes vagy mobil alkalmazásban.

Ez a dokumentáció a Azure Maps web SDK-t használja, de a Azure Maps Services bármely Térkép vezérlőelemmel használható. [Íme](open-source-projects.md#third-part-map-control-plugins) néhány népszerű, nyílt forráskódú Térkép vezérlőelem, amelyet a Azure Maps csapat készített a beépülő modulhoz.

## <a name="prerequisites"></a>Előfeltételek

A térképkezelés weblapon való használatához a következő előfeltételek egyikének kell megfelelnie:

* [Hozzon Azure Maps fiókot](quick-demo-map-app.md#create-an-azure-maps-account) , és [szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.

* Szerezze be a Azure Active Directory (HRE) hitelesítő adatait a [hitelesítési beállításokkal](/javascript/api/azure-maps-control/atlas.authenticationoptions).

## <a name="create-a-new-map-in-a-web-page"></a>Új Térkép létrehozása egy weblapon

A weblapokon a térképkezelés ügyféloldali JavaScript-kódtár segítségével ágyazhat be térképet.

1. Hozzon létre egy új HTML-fájlt.

2. Töltse be a Azure Maps web SDK-t. Két lehetőség közül választhat:

    * Használja a Azure Maps web SDK globálisan üzemeltetett CDN-verzióját úgy, hogy a `<head>` HTML-fájl elemében a JavaScriptre és a stíluslapra mutató hivatkozásokat ad hozzá:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * Töltse be az Azure Maps web SDK forráskódját helyileg az [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) NPM csomag használatával, és működtesse azt az alkalmazással. Ez a csomag írógéppel kapcsolatos definíciókat is tartalmaz.

      > **NPM telepítése Azure-Maps-Control**

    Ezután vegyen fel hivatkozásokat a Azure Maps stíluslapra a `<head>` fájl eleméhez:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > Az írógéppel definiált definíciók importálhatók az alkalmazásba a következő kód hozzáadásával:
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Ha a térképet úgy szeretné megjeleníteni, hogy az kitöltse az oldal teljes törzsét, adja hozzá a következő `<style>` elemet a `<head>` elemhez.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. A lap törzsében adjon hozzá egy elemet, `<div>` és adjon neki egy `id` **myMap**.

   ```HTML
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. Most pedig inicializáljuk a Térkép vezérlőelemet. A vezérlő hitelesítéséhez szükség van egy Azure Maps előfizetési kulcsra, vagy Azure Active Directory (HRE) hitelesítő adatokat kell használnia a [hitelesítési beállításokkal](/javascript/api/azure-maps-control/atlas.authenticationoptions).

    Ha előfizetés-kulcsot használ a hitelesítéshez, másolja és illessze be a következő script elemet az `<head>` elembe, és az első `<script>` elem alatt. Cserélje le a `<Your Azure Maps Key>` t a Azure Maps elsődleges előfizetési kulcsára.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
       }
    </script>
    ```

    Ha Azure Active Directoryt (HRE) használ a hitelesítéshez, másolja és illessze be a következő script elemet az `<head>` elembe, és az első `<script>` elem alatt.

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'aad',
                    clientId: '<Your AAD Client Id>',
                    aadAppId: '<Your AAD App Id>',
                    aadTenant: '<Your AAD Tenant Id>'
                }
            });
        }
    </script>
   ```

    A Azure Maps-hitelesítéssel kapcsolatos további információkért tekintse meg a [hitelesítés Azure Maps](azure-maps-authentication.md) dokumentummal című témakört. Ezen kívül a Azure Active Directory (HRE) Azure Maps használatával történő integrálását bemutató példák listáját [itt](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)találja.

    >[!TIP]
    >Ebben a példában a térképen átadták a következőt: `id` `<div>` . Ezt úgy teheti meg, hogy az `HTMLElement` `document.getElementById('myMap')` első paraméterként továbbítja az objektumot.

6. Igény szerint hasznos lehet a következő elemek hozzáadása az `meta` `head` oldal eleméhez:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. A HTML-fájl az alábbihoz hasonlóan fog kinézni:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. Nyissa meg a fájlt a böngészőben, és tekintse meg a megjelenített térképet. A következő képhez hasonlóan kell kinéznie:

   ![Megjelenített eredményt mutató Térkép képe](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map&quot;></a>A Térkép honosítása

A Azure Maps két különböző módszert biztosít a megjelenített Térkép nyelvének és regionális nézetének beállítására. Az első lehetőség, hogy hozzáadja ezt az információt a globális `atlas` névtérhez, ami az alkalmazás összes leképezés-vezérlési példányát az alapértelmezett beállítások alapján fogja eredményezni. A következő nyelvre állítja be a franciát (&quot;fr-FR") és a regionális nézetet az "Auto" értékre:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

A második lehetőség az, hogy ezeket az információkat a térképi beállításokba adja át, amikor a következőhöz hasonló leképezést tölt be:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!NOTE]
> Több térképes példány is betölthető ugyanazon a lapon eltérő nyelvi és területi beállításokkal. Emellett ezek a beállítások a Térkép betöltése után is frissíthetők `setStyle` .

Itt látható egy példa arra, hogy a "fr-FR", a regionális nézet pedig az "Auto" értékre van beállítva Azure Maps.

![Térkép ábrázolása a feliratokról francia nyelven](./media/how-to-use-map-control/websdk-localization.png)

A támogatott nyelvek és regionális nézetek teljes listáját [itt](supported-languages.md)dokumentáljuk.

## <a name="azure-government-cloud-support"></a>Felhő-támogatás Azure Government

A Azure Maps web SDK támogatja a Azure Government-felhőt. A Azure Maps web SDK eléréséhez használt összes JavaScript és CSS URL-cím változatlan marad. A Azure Maps platform Azure Government Cloud-verziójához való kapcsolódáshoz a következő feladatokat kell elvégezni.

Az interaktív térkép vezérlőelem használatakor adja hozzá a következő kódrészletet az osztály egy példányának létrehozása előtt `Map` .

```javascript
atlas.setDomain('atlas.azure.us');
```

Ügyeljen arra, hogy a Térkép és a szolgáltatások hitelesítése során a Azure Government Cloud platformon Azure Maps hitelesítés részleteit használja.

A szolgáltatások modul használatakor a szolgáltatás tartományát be kell állítani az API URL-végpontok példányának létrehozásakor. A következő kód például létrehozza a osztály egy példányát, `SearchURL` és a tartományt a Azure Government felhőre mutat.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Ha közvetlenül fér hozzá a Azure Maps REST-szolgáltatásokhoz, módosítsa az URL-tartományt a következőre: `atlas.azure.us` . Ha például a Search API szolgáltatást használja, módosítsa az URL-tartományt a következőre: `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/` .

## <a name="javascript-frameworks"></a>JavaScript-keretrendszerek

Ha JavaScript-keretrendszer használatával fejleszt, a következő nyílt forráskódú projektek egyike hasznos lehet:

* [ng-Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) -szögletes 10 burkoló az Azure Maps-ben.
* [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) – Azure Maps Blazer-összetevő.
* [Azure Maps reagáló összetevő](https://github.com/WiredSolutions/react-azure-maps) – a Azure Maps vezérlőre reagáló burkoló.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) – egy Azure Maps összetevő a Vue alkalmazáshoz.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan hozhat létre és kezelhet egy térképet:

> [!div class="nextstepaction"]
> [Térkép létrehozása](map-create.md)

A Térkép stílusa:

> [!div class="nextstepaction"]
> [Térképstílus kiválasztása](choose-map-style.md)

Ismerje meg az ajánlott eljárásokat, és tekintse meg a mintákat:

> [!div class="nextstepaction"]
> [Ajánlott eljárások](web-sdk-best-practices.md)

> [!div class="nextstepaction"]
> [Kódminták](/samples/browse/?products=azure-maps)

A Azure Active Directory (HRE) Azure Maps használatával történő integrálását bemutató minták listáját a következő témakörben tekintheti meg:

> [!div class="nextstepaction"]
> [Azure AD-hitelesítési minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
