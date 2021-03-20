---
title: Keresés eredményeinek megjelenítése térképeken | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan hajthat végre keresési kéréseket Microsoft Azure Maps web SDK használatával, és hogyan jelenítheti meg az eredményeket a térképen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 647a8fc25f27ef7f441ed7459ecd543d4f35581e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895273"
---
# <a name="show-search-results-on-the-map"></a>Keresési eredmények megjelenítése a térképen

Ez a cikk bemutatja, hogyan keresheti meg az érdeklődési helyet, és hogyan jelenítheti meg a keresési eredményeket a térképen.

Az érdeklődési hely két módon kereshető. Az egyik módszer egy szolgáltatási modul használata a keresési kérelem elvégzéséhez. A másik lehetőség, hogy keresési kérést készítsen [Azure Maps fuzzy Search API](/rest/api/maps/search/getsearchfuzzy) -t a [Fetch API](https://fetch.spec.whatwg.org/)-n keresztül. Az alábbiakban mindkét módszert ismertetjük.

## <a name="make-a-search-request-via-service-module"></a>Keresési kérelem küldése a Service Module használatával

<iframe height='500' scrolling='no' title='Keresési eredmények megjelenítése térképeken (szolgáltatási modul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollak <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>keresési eredményeinek megjelenítése a térképeken (szolgáltatási modul)</a> a <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>-on Azure Maps ().
</iframe>

A fenti kódban az első blokk létrehozza a Térkép objektumot, és beállítja a hitelesítési mechanizmust a hozzáférési jogkivonat használatára. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja létrehoz egy `TokenCredential` -t a hozzáférési jogkivonattal Azure Maps HTTP-kérések hitelesítéséhez. Ezután továbbítja a- `TokenCredential` t, `atlas.service.MapsURL.newPipeline()` és létrehoz egy [folyamat](/javascript/api/azure-maps-rest/atlas.service.pipeline) -példányt. A a `searchURL` Azure Maps [keresési](/rest/api/maps/search) műveletekhez tartozó URL-címet jelöli.

A kód harmadik blokkja létrehoz egy adatforrás-objektumot az [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) osztály használatával, és hozzáadja a keresési eredményeket. A [szimbólum-réteg](/javascript/api/azure-maps-control/atlas.layer.symbollayer) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az [adatforrásban](/javascript/api/azure-maps-control/atlas.source.datasource) szimbólumként a térképen.  Ekkor létrejön egy szimbólum-réteg. A rendszer hozzáadja az adatforrást a szimbólum réteghez, amelyet aztán hozzáad a térképhez.

A negyedik kódú blokk a [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) metódust használja a [szolgáltatási modulban](how-to-use-services-module.md). Lehetővé teszi, hogy a [keresési fuzzy REST API](/rest/api/maps/search/getsearchfuzzy) -n keresztül ingyenes szöveges keresést végezzen, hogy megkeresse az érdekes helyet. A Search fuzzy API-nak küldött kérések a zavaros bemenetek tetszőleges kombinációját kezelhetik. Ezután a válaszból Kinyer egy GeoJSON-gyűjteményt `geojson.getFeatures()` , és hozzáadja az adatforráshoz, amely automatikusan azt eredményezi, hogy az adatok a térképen a szimbólum rétegen keresztül jelennek meg.

A kód utolsó blokkja a Térkép [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) tulajdonságával módosítja a térképhez tartozó kamera-határokat.

A keresési kérelem, az adatforrás, a szimbólum réteg és a kamera határai a Térkép [esemény-figyelőján](/javascript/api/azure-maps-control/atlas.map#events) belül találhatók. Szeretnénk biztosítani, hogy az eredmények a Térkép teljes betöltése után megjelenjenek.


## <a name="make-a-search-request-via-fetch-api"></a>Keresési kérelem készítése a fetch API használatával

<iframe height='500' scrolling='no' title='Keresés eredményeinek megjelenítése térképeken' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>keresési eredmények megjelenítése</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódban a kód első blokkja egy Térkép objektumot hoz létre. A hitelesítési mechanizmust a hozzáférési jogkivonat használatára állítja be. Ehhez útmutatást a [Térkép létrehozása](./map-create.md) című témakörben találhat.

A kód második blokkja egy URL-címet hoz létre, amely egy keresési kérést tesz elérhetővé. Emellett két tömböt hoz létre a határértékek és a PIN-kódok tárolására a keresési eredményekhez.

A kód harmadik blokkja a [beolvasás API](https://fetch.spec.whatwg.org/)-t használja. A [beolvasási API](https://fetch.spec.whatwg.org/) arra szolgál, hogy [Azure Maps fuzzy keresési API](/rest/api/maps/search/getsearchfuzzy) -ra irányuló kérést keressen a hasznos helyek kereséséhez. A fuzzy keresési API a fuzzy bemenetek tetszőleges kombinációját képes kezelni. Ezután kezeli és elemzi a keresési választ, és hozzáadja az eredmény-PIN-eket a searchPins tömbhöz.

A kód negyedik blokkja egy adatforrás-objektumot hoz létre az [adatforrás osztály használatával](/javascript/api/azure-maps-control/atlas.source.datasource) . A kódban a keresési eredményeket vesszük fel a forrás objektumba. A [szimbólum-réteg](/javascript/api/azure-maps-control/atlas.layer.symbollayer) szöveggel vagy ikonokkal jeleníti meg a pont-alapú adatforrásokat az [adatforrásban](/javascript/api/azure-maps-control/atlas.source.datasource) szimbólumként a térképen. Ekkor létrejön egy szimbólum-réteg. A rendszer hozzáadja az adatforrást a szimbólum réteghez, amelyet aztán hozzáad a térképhez.

A kód utolsó blokkja létrehoz egy [BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) objektumot. Az eredmények tömbjét használja, majd a Térkép [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)módosítja a térképhez tartozó kamera-határokat. Ezután megjeleníti az eredmény PIN-kód.

A keresési kérelem, az adatforrás, a szimbólum és a kamera határai a Térkép [esemény-figyelőn](/javascript/api/azure-maps-control/atlas.map#events) belül vannak beállítva, így biztosítva, hogy az eredmények a Térkép teljes betöltése után megjelenjenek.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ajánlott eljárások a keresési szolgáltatás használatához](how-to-use-best-practices-for-search.md)

További információ a **fuzzy keresésről**:

> [!div class="nextstepaction"]
> [Azure Maps fuzzy Search API](/rest/api/maps/search/getsearchfuzzy)

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](/javascript/api/azure-maps-control/atlas.map)

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [Koordináta információinak lekérése](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Útvonal megjelenítése A-ból B-be](map-route.md)