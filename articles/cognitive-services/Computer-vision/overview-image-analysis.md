---
title: Mi a rendszerkép elemzése?
titleSuffix: Azure Cognitive Services
description: A Image Analysis Service előre betanított AI-modelleket használ a képekből származó számos különböző vizualizációs funkció kinyeréséhez.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: Computer vízió, Computer víziós alkalmazások, számítógépes jövőkép szolgáltatás
ms.openlocfilehash: f262fdb49cac4ab9abe7f3f6873160d3059968c6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287234"
---
# <a name="what-is-image-analysis"></a>Mi a rendszerkép elemzése?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A Computer Vision Image Analysis Service számos vizuális funkciót képes kinyerni a képekből. Meghatározhatja például, hogy egy rendszerkép tartalmaz-e felnőtt tartalmat, meghatározott márkákat vagy objektumokat keres, vagy megkeresi az emberi arcokat.

A képelemzést egy ügyféloldali kódtár SDK-n keresztül, vagy közvetlenül a [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) meghívásával is használhatja. [A kezdéshez kövesse a rövid](quickstarts-sdk/image-analysis-client-library.md) útmutatót.

Ez a dokumentáció a következő típusú cikkeket tartalmazza:
* A [rövid](./quickstarts-sdk/image-analysis-client-library.md) útmutatók részletes útmutatást tesznek lehetővé, amelyekkel hívásokat indíthat a szolgáltatásba, és rövid idő alatt lekérheti az eredményeket. 
* A [útmutató útmutatók](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) a szolgáltatás használatára vonatkozó utasításokat tartalmaznak részletesebb vagy testreszabott módokon.
* A [fogalmi cikkek](concept-tagging-images.md) részletesen ismertetik a szolgáltatás funkcióit és funkcióit.
* Az [oktatóanyagok](./tutorials/storage-lab-tutorial.md) már olyan útmutatók, amelyek bemutatják, hogyan használhatja ezt a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.

## <a name="image-analysis-features"></a>Rendszerkép-elemzési funkciók

Elemezheti a képeket, hogy információkat szolgáltasson a vizualizáció szolgáltatásairól és jellemzőiről. Az alábbi lista összes funkcióját a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API tartalmazza. [A kezdéshez kövesse az első](./quickstarts-sdk/image-analysis-client-library.md) lépéseket.


### <a name="tag-visual-features"></a>Vizuális jellemzők címkézése

Azonosíthatja és címkézheti egy kép vizualizációs funkcióit több ezer felismerhető objektumból, az élő dolgokból, a díszletből és a tevékenységekből álló készletből. Ha a címkék nem egyértelműek vagy nem általános ismeretek, az API-válasz a címke kontextusának tisztázására szolgáló tippeket tartalmaz. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb. [Vizuális jellemzők címkézése](concept-tagging-images.md)

### <a name="detect-objects"></a>Objektumok észlelése

Az objektum-észlelés hasonlít a címkézéshez, de az API az egyes alkalmazott címkékhez tartozó határoló mezők koordinátáit adja vissza. Ha például egy rendszerkép kutyát, macskát és személyt tartalmaz, az észlelési művelet a képen látható koordinátákkal együtt listázza ezeket az objektumokat. Ezt a funkciót használhatja a képek objektumai közötti további kapcsolatok feldolgozásához. Azt is megtudhatja, hogy mikor van több példánya ugyanazzal a címkével egy képen. [Objektumok észlelése](concept-object-detection.md)

### <a name="detect-brands"></a>Márkák észlelése

A képeken vagy videókban található kereskedelmi márkákat több ezer globális emblémából álló adatbázisból azonosíthatja. Ezt a funkciót használhatja például arra, hogy felderítse, mely márkák a legnépszerűbbek a közösségi médiában, vagy a média termékeinek legelterjedtebb eleme. [Márkák észlelése](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Rendszerkép kategorizálása

Felismeri és kategorizálja a teljes képet egy [kategóriaelnevezési rendszer](Category-Taxonomy.md) segítségével, szülő/gyermek hierarchiák használatával. A kategóriák egyedül vagy az új címkéző modellekkel együtt is használhatóak.<br/>Jelenleg az egyetlen támogatott nyelv a képek címkézéséhez és kategorizálásához az angol. [Rendszerkép kategorizálása](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Képek leírása

Leírást hoz létre a teljes képhez a felhasználók által is olvasható nyelven, teljes mondatok használatával. A Computer Vision algoritmusai különböző leírásokat hoznak létre a képen felismert tárgyak alapján. A leírásokat a rendszer kiértékeli, majd megbízhatósági pontszámot hoz létre hozzájuk. A megbízhatósági pontszámok listája csökkenő sorrendbe rendezve érkezik vissza. [Képek leírása](concept-describing-images.md)

### <a name="detect-faces"></a>Arcfelismerés

Felismeri a képen található arcokat, és információval szolgál minden felismert arccal kapcsolatban. A Computer Vision visszaküldi az arcok koordinátáit, a téglalapot és a képen szereplő személy nemét és életkorát.<br/>Computer Vision a [Face](../face/index.yml) szolgáltatás funkcióinak egy részét biztosítja. A Face szolgáltatást részletesebb elemzésre is használhatja, például az arc azonosítására és az észlelésre. [Arcfelismerés](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Képek típusának észlelése

Észleli a kép jellemzőit, például hogy a kép vonalrajz-e, vagy annak a valószínűségét, hogy a kép ClipArt. [Képek típusának észlelése](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Tartományspecifikus tartalom észlelése

Tartománymodellek segítségével észleli és felismeri a kép tartományspecifikus tartalmát, például a hírességeket vagy nevezetességeket. Ha például egy rendszerkép személyeket tartalmaz, Computer Vision a hírességek egy tartományi modelljével határozzák meg, hogy a rendszerképben észlelt személyek ismert hírességek-e. [Tartományspecifikus tartalom észlelése](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Színséma észlelése

A képen használt színek elemzése. A Computer Vision eldönti, hogy a kép fekete-fehér-e vagy színes, illetve színes képek esetében felismeri a domináns és a kiegészítő színeket. [Színséma észlelése](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Miniatűr létrehozása

Elemzi a kép tartalmát, hogy megfelelő miniatűrt tudjon létrehozni a képhez. Computer Vision először egy kiváló minőségű miniatűrt hoz létre, majd a képen található objektumokat elemzi a *fontos terület* meghatározásához. Computer Vision ezután megvágja a képet, hogy megfeleljen a fontos terület követelményeinek. Igény szerint a létrehozott miniatűr az eredeti képtől eltérő oldalaránnyal is rendelkezhet. [Miniatűr létrehozása](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>A fontos terület beszerzése

Egy rendszerkép tartalmának elemzése a *fontos terület* koordinátáinak visszaadásához. A rendszerkép kivágása és a miniatűr létrehozása helyett a Computer Vision a régió határolókeret koordinátáit adja vissza, így a hívó alkalmazás igény szerint módosíthatja az eredeti rendszerképet. [A fontos terület beszerzése](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Képek tartalmának moderálása

A Computer Vision használatával [felderítheti a felnőtt tartalmakat](concept-detecting-adult-content.md) egy rendszerképben, és a különböző besorolások megbízhatósági pontszámait is visszaküldheti. A tartalom megjelölésére vonatkozó küszöbérték beállítható egy csúszó skálán, hogy megfeleljenek a beállításoknak.

## <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A képelemzés a következő követelményeknek megfelelő lemezképeken működik:

- A képnek JPEG, PNG, GIF vagy BMP formátumúnak kell lennie
- A fájlméret nem érheti el a 4 megabájtot (MB)
- A képnek nagyobbnak kell lennie, mint 50 x 50 képpont

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes Cognitive Services esetében, a Computer Vision szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a rendszerkép-elemzéssel az előnyben részesített fejlesztői nyelv útmutatójának segítségével:

- [Rövid útmutató: Computer Vision REST API vagy ügyféloldali kódtárak](./quickstarts-sdk/image-analysis-client-library.md)
