---
title: Mi az a képelemzés?
titleSuffix: Azure Cognitive Services
description: A Képelemzési szolgáltatás előre betanított AI-modelleket használ a képek számos különböző vizuális jellemzőjéhez.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
keywords: számítógépes látás, számítógépes látást látó alkalmazások, computer vision szolgáltatás
ms.openlocfilehash: 0258eb7c57bc0734b5c0a67644cbaa4f62a34537
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766914"
---
# <a name="what-is-image-analysis"></a>Mi az a képelemzés?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A Computer Vision Image Analysis szolgáltatás számos különféle vizuális jellemzőt képes kinyerni a képekből. Meghatározhatja például, hogy egy kép tartalmaz-e felnőtt tartalmat, megkereshet-e bizonyos márkákat vagy tárgyakat, vagy emberi arcokat keres-e.

A Képelemzést használhatja ügyféloldali kódtár SDK-ként, vagy közvetlenül a REST API [hívhatja.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v2-ga/operations/5d986960601faab4bf452005) Az első [lépésekhez kövesse](quickstarts-sdk/image-analysis-client-library.md) a rövid útmutatót.

Ez a dokumentáció a következő típusú cikkeket tartalmazza:
* A [rövid útmutatók olyan](./quickstarts-sdk/image-analysis-client-library.md) részletes utasítások, amelyek segítségével hívásokat kezdeményez a szolgáltatáshoz, és rövid idő alatt lekért eredményeket. 
* Az [útmutatók a](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) szolgáltatás pontosabb vagy testreszabottabb módon való használatával kapcsolatos utasításokat tartalmaznak.
* Az [elméleti cikkek](concept-tagging-images.md) részletes magyarázatot nyújtanak a szolgáltatás funkcióiról és funkcióiról.
* Az [oktatóanyagok hosszabb](./tutorials/storage-lab-tutorial.md) útmutatók, amelyekből megtudhatja, hogyan használhatja ezt a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.

## <a name="image-analysis-features"></a>Képelemzési funkciók

A képek elemzésével betekintést nyerhet a vizuális jellemzőikbe és jellemzőikbe. Az alábbi lista összes funkcióját a Analyze Image [API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) biztosítja. Az első [lépésekhez kövesse](./quickstarts-sdk/image-analysis-client-library.md) a rövid útmutatót.


### <a name="tag-visual-features"></a>Vizuális jellemzők címkézése

A képek vizuális jellemzőinek azonosítása és címkézése felismerhető tárgyak, élőlények, tájak és tevékenységek ezreiből. Ha a címkék nem egyértelműek vagy nem köztudottak, az API-válasz tippeket ad a címke kontextusának tisztázására. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb. [Vizuális jellemzők címkézése](concept-tagging-images.md)

### <a name="detect-objects"></a>Objektumok észlelése

Az objektumészlelés hasonló a címkézéshez, de az API visszaadja az alkalmazott címkék határolókeret-koordinátáit. Ha például egy képen kutya, macska és személy látható, a Detect művelet ezeket az objektumokat a koordinátáival együtt sorolja fel a képen. Ezzel a funkcióval további kapcsolatokat is feldolgozhat a képeken lévő objektumok között. Azt is meg tudja tudni, ha egy címkének több példánya is van egy képen. [Objektumok észlelése](concept-object-detection.md)

### <a name="detect-brands"></a>Márkák észlelése

Kereskedelmi márkák azonosítása képeken vagy videókon egy több ezer globális emblémát kezelő adatbázisból. Ezzel a funkcióval például felfedezheti, hogy mely márkák a legnépszerűbbek a közösségi médiában, vagy melyek a legnépszerűbbek a médiatermék-elhelyezésben. [Márkák észlelése](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Kép kategorizálása

Felismeri és kategorizálja a teljes képet egy [kategóriaelnevezési rendszer](Category-Taxonomy.md) segítségével, szülő/gyermek hierarchiák használatával. A kategóriák egyedül vagy az új címkéző modellekkel együtt is használhatóak.<br/>Jelenleg az egyetlen támogatott nyelv a képek címkézéséhez és kategorizálásához az angol. [Kép kategorizálása](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Képek leírása

Leírást hoz létre a teljes képhez a felhasználók által is olvasható nyelven, teljes mondatok használatával. A Computer Vision algoritmusai különböző leírásokat hoznak létre a képen felismert tárgyak alapján. A leírásokat a rendszer kiértékeli, majd megbízhatósági pontszámot hoz létre hozzájuk. A megbízhatósági pontszámok listája csökkenő sorrendbe rendezve érkezik vissza. [Képek leírása](concept-describing-images.md)

### <a name="detect-faces"></a>Arcfelismerés

Felismeri a képen található arcokat, és információval szolgál minden felismert arccal kapcsolatban. A Computer Vision visszaküldi az arcok koordinátáit, a téglalapot és a képen szereplő személy nemét és életkorát.<br/>Computer Vision a Face szolgáltatás funkcióinak egy [részkészletét](../face/index.yml) biztosítja. A Face szolgáltatást részletesebb elemzésekhez használhatja, például az arcfelismeréshez és a testek észleléséhez. [Arcfelismerés](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Képek típusának észlelése

Észleli a kép jellemzőit, például hogy a kép vonalrajz-e, vagy annak a valószínűségét, hogy a kép ClipArt. [Képek típusának észlelése](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Tartományspecifikus tartalom észlelése

Tartománymodellek segítségével észleli és felismeri a kép tartományspecifikus tartalmát, például a hírességeket vagy nevezetességeket. Ha például egy kép személyeket tartalmaz, Computer Vision a hírességek tartománymodellje alapján megállapíthatja, hogy a képen észlelt személyek ismert hírességek-e. [Tartományspecifikus tartalom észlelése](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Színséma észlelése

A képen használt színek elemzése. A Computer Vision eldönti, hogy a kép fekete-fehér-e vagy színes, illetve színes képek esetében felismeri a domináns és a kiegészítő színeket. [Színséma észlelése](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Miniatűr létrehozása

Elemzi a kép tartalmát, hogy megfelelő miniatűrt tudjon létrehozni a képhez. Computer Vision először létrehoz egy jó minőségű miniatűrt, majd elemzi a képen található objektumokat a kívánt *terület meghatározásához.* Computer Vision a képet úgy, hogy illeszkedjen a vonatkozó terület követelményeihez. Igény szerint a létrehozott miniatűr az eredeti képtől eltérő oldalaránnyal is rendelkezhet. [Miniatűr létrehozása](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>A érdekes terület lekért része

Egy kép tartalmának elemzése a fontos terület *koordinátáinak visszaadása érdekében.* A kép levágása és miniatűr létrehozása helyett a Computer Vision visszaadja a régió határolókeret-koordinátáit, így a hívó alkalmazás szükség szerint módosíthatja az eredeti képet. [A érdekes terület lekért része](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Képek tartalmának moderálása

A képeken Computer Vision felnőtteknek szóló tartalmak észlelésére, és a különböző besorolások megbízhatósági pontszámainak visszaadása. [](concept-detecting-adult-content.md) A tartalom megjelölésének küszöbértéke egy csúszka alapján is beállítható a beállításoknak megfelelően.

## <a name="image-requirements"></a>A rendszerképre vonatkozó követelmények

A képelemzés az alábbi követelményeknek megfelelő képeken működik:

- A képnek JPEG, PNG, GIF vagy BMP formátumúnak kell lennie
- A fájlméret nem érheti el a 4 megabájtot (MB)
- A képnek nagyobbnak kell lennie, mint 50 x 50 képpont

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Mint minden más Cognitive Services, az Computer Vision szolgáltatást használó fejlesztőknek tisztában kell lenniük a Microsoft ügyféladatokkal kapcsolatos szabályzatával. További Cognitive Services [a](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) Microsoft Biztonsági és biztonsági központ Cognitive Services oldalon.

## <a name="next-steps"></a>Következő lépések

A képelemzés első lépésekhez az Ön által választott fejlesztési nyelven található rövid útmutatót kell követnie:

- [Rövid útmutató: Computer Vision REST API vagy ügyfélkódtárak](./quickstarts-sdk/image-analysis-client-library.md)
