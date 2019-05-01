---
title: Ismerkedés az Azure Maps a webes térkép vezérlőelem |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Maps térkép vezérlőelem ügyféloldali Javascript-függvénytárat.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c346c51db2c06c2ef91b101255076b811dfea9e7
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869750"
---
# <a name="use-the-azure-maps-map-control"></a>Az Azure Maps térkép vezérlőelem használata

A térkép vezérlőelem ügyféloldali Javascript-kódtár térképeket és beágyazott Azure Maps-funkciók jelennek meg a webes vagy mobilalkalmazás segítségével.

## <a name="create-a-new-map-in-a-web-page"></a>Egy weblapon egy új térkép létrehozása

A térkép vezérlőelem ügyféloldali Javascript-kódtár használatával egy térkép is beágyazható egy weblapon.

1. Hozzon létre egy új HTML-fájlt.

2. Töltse be az Azure Maps webes SDK-t. Ezt megteheti egy két lehetőség közül választhat;

    a. Az URL-végpontot a stíluslap és parancsfájl útmutatót hozzáadásával az Azure Maps Web SDK globálisan üzemeltetett CDN verzióját használja a `<head>` elem a fájl:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. Azt is megteheti, betöltése az Azure Maps Web SDK forráskódját használatával helyben a [azure maps-ellenőrző](https://www.npmjs.com/package/azure-maps-control) NPM csomag, és tárolja azt az alkalmazást. Ez a csomag is TypeScript definíciókat tartalmazza.

    > az npm telepítése azure-maps-vezérlő

    Ezután adja hozzá hivatkozásokat az Azure Maps stíluslap és parancsfájl forrás mutató hivatkozásokat a `<head>` elem a fájl:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. A térkép megjelenítési, hogy az kitöltse a teljes szervezet lap, adja hozzá a következő `<style>` elem a `<head>` elemet.

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

4. Az oldal törzsében, adjon hozzá egy `<div>` elemet, és adjon neki egy `id` , **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. A térkép vezérlőelem inicializálása, új definiálására a html törzsében, és hozzon létre egy parancsfájlt. A saját Azure Maps-fiók kulcsára vagy Azure Active Directory (AAD) hitelesítő adatokat használja a térkép használatával történő hitelesítéshez [hitelesítési beállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Ha hozzon létre egy fiókot, vagy keresse meg a, tekintse meg kell [az Azure Maps-fiók és kulcsok kezelése](how-to-manage-account-keys.md). A **nyelvi** beállítás megadja a térkép címkék és a vezérlők használt nyelv. A támogatott nyelvek további információkért lásd: [támogatott nyelvek](supported-languages.md). Ha egy előfizetési kulcsot használ a hitelesítéshez.

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    Az Azure Active Directory (AAD) használata a hitelesítéshez:

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

    További információkért lásd: [hitelesítés az Azure Maps](azure-maps-authentication.md) további részletekért.

6. Igény szerint hasznosnak találhatja, a következő meta tag elemeket ad hozzá a fő az oldal hasznos:

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. A teljes kép a HTML-fájl hasonlóan kell kinéznie a következő kódot:

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
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. A böngészőben nyissa meg a fájlt, és megtekintheti a megjelenített térképen. A következő kódhoz hasonlóan kell kinéznie:

    <iframe height="700" style="width: 100%;" scrolling="no" title="A térkép vezérlőelem használata" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>a térkép vezérlőelem használata</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre, és a egy térkép interakcióba:

> [!div class="nextstepaction"]
> [Térkép létrehozása](map-create.md)

Ismerje meg, hogyan térkép stílus:

> [!div class="nextstepaction"]
> [Térkép stílus kiválasztása](choose-map-style.md)
