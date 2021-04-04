---
title: Egy Térkép koordinátáival kapcsolatos információk megjelenítése | Microsoft Azure térképek
description: Megtudhatja, hogyan jelenítheti meg a térképen a címekre vonatkozó információkat, amikor a felhasználó kiválaszt egy koordinátákat.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 31e4004379340912051204786da592fe33a5bd63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890751"
---
# <a name="get-information-from-a-coordinate"></a>Koordináta információinak lekérése

Ebből a cikkből megtudhatja, hogyan végezhet fordított címtartomány-keresést, amely megjeleníti egy rákattintott előugró hely címeit.

A fordított címek keresésének kétféleképpen végezhető el. Az egyik módszer az, ha lekérdezi az [Azure Maps fordított címtartomány-keresés API](/rest/api/maps/search/getsearchaddressreverse) -t egy szolgáltatási modulon keresztül. A másik lehetőség, hogy a [beolvasás API](https://fetch.spec.whatwg.org/) használatával kérelmet küld a [Azure Maps fordított CÍMTARTOMÁNY-kereső API](/rest/api/maps/search/getsearchaddressreverse) -nak a címek kereséséhez. Az alábbiakban mindkét módszert megvizsgáljuk.

## <a name="make-a-reverse-search-request-via-service-module"></a>Fordított keresési kérelem küldése a szolgáltatási modulon keresztül

<iframe height='500' scrolling='no' title='Információ lekérése egy koordinátaből (szolgáltatási modul)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>A CodePen-on Azure Maps () című cikkből megtudhatja, hogyan <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>kérhet le adatokat a koordináta (szolgáltatási modul) szolgáltatásból</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

A fenti kódban az első blokk létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A második kódrészlet létrehoz egy `TokenCredential` -t, amely hitelesíti a hozzáférési jogkivonattal Azure maps http-kérelmeket. Ezután továbbítja a- `TokenCredential` t, `atlas.service.MapsURL.newPipeline()` és létrehoz egy [folyamat](/javascript/api/azure-maps-rest/atlas.service.pipeline) -példányt. A a `searchURL` Azure Maps [keresési](/rest/api/maps/search) műveletekhez tartozó URL-címet jelöli.

A harmadik kódú blokk frissíti az egérmutató stílusát egy mutatóra, és létrehoz egy [előugró](/javascript/api/azure-maps-control/atlas.popup#open) objektumot. Útmutatásért tekintse [meg az előugró ablak hozzáadása a térképen](./map-add-popup.md) .

A kód negyedik blokkja egy egérkattintással jeleníti meg az [esemény-figyelőt](/javascript/api/azure-maps-control/atlas.map#events). Az aktiváláskor egy keresési lekérdezést hoz létre a rákattintott pont koordinátáival. Ezután a [getSearchAddressReverse](/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)metódus használatával kérdezi le a [keresési fordított API](/rest/api/maps/search/getsearchaddressreverse) -t a koordináták címeihez. Ezután a válaszból a metódussal Kinyer egy GeoJSON-gyűjteményt `geojson.getFeatures()` .

A kód ötödik blokkja beállítja a HTML-előugró tartalmat, hogy megjelenjen a válasz címe a rákattintott koordináta-pozícióhoz.

A kurzor, a felugró objektum és a click esemény változása mind a Térkép [betöltési eseményének figyelőben](/javascript/api/azure-maps-control/atlas.map#events)jön létre. A kód szerkezete biztosítja a Térkép teljes terhelését a koordináták adatainak beolvasása előtt.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Fordított keresési kérelem készítése a fetch API használatával

A térképre kattintva fordított geocode-kérelmet készíthet az adott helyhez a fetch használatával.

<iframe height='500' scrolling='no' title='Koordináta információinak lekérése' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a CodePen-on található Azure Maps () által <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>koordinált adatokból</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

A fenti kódban a kód első blokkja létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja frissíti az egérmutató stílusát egy mutatóra. Előugró objektumot hoz [](/javascript/api/azure-maps-control/atlas.popup#open) létre. Útmutatásért tekintse [meg az előugró ablak hozzáadása a térképen](./map-add-popup.md) .

A kód harmadik blokkja egy esemény-figyelőt hoz létre egérkattintáshoz. Ha egérkattintásra kattint, a [beolvasás API](https://fetch.spec.whatwg.org/) használatával kérdezi le a [Azure Maps fordított CÍMTARTOMÁNY-keresés API](/rest/api/maps/search/getsearchaddressreverse) -t a rákattintott koordináták címeire. A sikeres válaszhoz a rendszer a kijelölt helyhez tartozó címeket gyűjti. Meghatározza az előugró tartalmat és a pozíciót az előugró osztály [setOptions](/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) funkciójával.

A kurzor, a felugró objektum és a click esemény változása mind a Térkép [betöltési eseményének figyelőben](/javascript/api/azure-maps-control/atlas.map#events)jön létre. A kód szerkezete biztosítja a Térkép teljes terhelését a koordináták adatainak beolvasása előtt.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ajánlott eljárások a keresési szolgáltatás használatához](how-to-use-best-practices-for-search.md)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Felugró ablak](/javascript/api/azure-maps-control/atlas.popup)

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [Útvonal megjelenítése A-ból B-be](./map-route.md)

> [!div class="nextstepaction"]
> [Forgalom megjelenítése](./map-show-traffic.md)