---
title: 'Oktatóanyag: mérsékelt e-kereskedelmi termék lemezképei – Content Moderator'
titleSuffix: Azure Cognitive Services
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthat be egy alkalmazást a termékek rendszerképeinek a megadott címkékkel való elemzéséhez és besorolásához (az Azure Computer Vision és Custom Vision használatával). A kifogásolt képek címkézése további felülvizsgálatra kerül (az Azure Content Moderator használatával).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 211f58e26ec89c393bf9f91cc3a05044c6b1e802
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221278"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Oktatóanyag: az e-kereskedelmi termékkel foglalkozó közepesen súlyos rendszerképek az Azure Content Moderator

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Cognitive Servicest, beleértve a Content Moderatoreket, hogy az e-kereskedelmi forgatókönyvek termékeinek besorolását és mérsékelt termékeit. Computer Vision és Custom Vision segítségével címkéket (címkéket) alkalmazhat a képekre, majd létrehoz egy csapat-áttekintést, amely a Content Moderator gépi tanuláson alapuló technológiáit kombinálja az emberi felülvizsgálati csapatokkal intelligens moderációs rendszer biztosításához.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Regisztráljon a Content Moderatorra, és hozzon létre egy felülvizsgálati csapatot.
> * A Content Moderator Image API-jának használata a lehetséges felnőtteknek szóló és kényes tartalom keresésére.
> * Használja a Computer Vision szolgáltatást a Celebrity-tartalmak (vagy más számítógépek által észlelhető címkék) vizsgálatához.
> * A Custom Vision szolgáltatás segítségével megvizsgálhatja a jelzők, a játékok és a tollak (vagy más egyéni címkék) jelenlétét.
> * A humán felülvizsgálati és a végső döntéshozatali eredmények összevont vizsgálatának eredményei jelennek meg.

A teljes mintakód elérhető az [e-kereskedelmi katalógus-moderálási](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) tárházban a githubon.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Előfeltételek

- A Content Moderator előfizetői azonosítója. A Content Moderator szolgáltatásra való előfizetéshez és a kulcs lekéréséhez kövesse az [Cognitive Services fiók létrehozása](../cognitive-services-apis-create-account.md) című témakör utasításait.
- Egy Computer Vision előfizetési kulcs (a fenti utasításokkal).
- A [Visual Studio 2015-es vagy 2017-](https://www.visualstudio.com/downloads/)es verziójának bármely kiadása.
- Az Custom Vision osztályozó által használt összes címkéhez tartozó rendszerképek összessége (ebben az esetben a játékok, a tollak és az amerikai zászlók).

## <a name="create-a-review-team"></a>Felülvizsgálati csapat létrehozása

Tekintse át a [Content moderator kipróbálása a webes](quick-start.md) útmutatóban című témakört, amely útmutatást nyújt a [Content moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) regisztrálásához és egy felülvizsgálati csapat létrehozásához. Jegyezze fel a **csoport azonosító** értékét a **hitelesítő adatok** lapon.

## <a name="create-custom-moderation-tags"></a>Egyéni moderálási Címkék létrehozása

Ezután hozzon létre egyéni címkéket a felülvizsgálati eszközben (lásd a [címkék](./review-tool-user-guide/configure.md#tags) cikket, ha segítségre van szüksége ehhez a folyamathoz). Ebben az esetben a következő címkéket fogjuk hozzáadni: **Celebrity**, **USA**, **Flag**, **Toy** és **Pen**. Az összes címkének nem kell észlelhető kategóriának lennie Computer Visionban (például **híresség**); hozzáadhat saját egyéni címkéket is, ha a Custom Vision osztályozó betanításával később felismeri őket.

![Egyéni címkék konfigurálása](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. A Visual Studióban nyissa meg az új projekt párbeszédpanelt. Bontsa ki a **telepített**, majd a **Visual C#**, majd a **Console app (.NET-keretrendszer)** elemet.
1. Nevezze el az alkalmazás **EcommerceModeration**, majd kattintson **az OK gombra**.
1. Ha egy meglévő megoldáshoz adja hozzá ezt a projektet, válassza ki ezt a projektet egyetlen indítási projektként.

Ez az oktatóanyag kiemeli a projekt központi részét képező kódot, de nem fedi le a kód minden sorát. Másolja a _program.cs_ teljes tartalmát a minta projektből (az [e-kereskedelmi katalógus moderálása](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) az új projekt _program.cs_ -fájljába. Ezután a következő szakaszokból megismerheti, hogyan működik a projekt, és hogyan használhatja azt.

## <a name="define-api-keys-and-endpoints"></a>API-kulcsok és-végpontok definiálása

Ez az oktatóanyag három kognitív szolgáltatást használ; ezért három megfelelő kulcsot és API-végpontot igényel. Tekintse meg a **program** osztály következő mezőit:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Frissítenie kell a mezőket az `___Key` előfizetési kulcsok értékeivel, és módosítania kell a `___Uri` mezőket a megfelelő végponti URL-címekre (a Custom Vision kulcsot és a végpontot később kapja meg). Ezek az értékek az egyes Azure-erőforrások **gyors üzembe helyezési** lapjain találhatók. Töltse ki a `YOURTEAMID` mező részét a `ReviewUri` korábban létrehozott felülvizsgálati csapat azonosítójával. A mező utolsó részét később kitölti a következő `CustomVisionUri` időpontban:.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Elsődleges metódusok hívása

Tekintse meg a következő kódot a **Main** metódusban, amely a Képurl-címek listáján keresztül hurkokat mutat be. Elemzi az egyes lemezképeket a három különböző szolgáltatással, rögzíti az alkalmazott címkéket a **ReviewTags** tömbben, majd az emberi moderátorok számára áttekintést küld a rendszerképek Content moderator felülvizsgálati eszközre való elküldésével. Ezeket a módszereket a következő fejezetekben tekintheti át. Ha szeretné, szabályozhatja, hogy mely képek legyenek elküldve a felülvizsgálatra, a **ReviewTags** tömb használatával egy feltételes utasításban ellenőrizheti, hogy mely címkék lettek alkalmazva.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy metódus

Tekintse meg a **EvaluateAdultRacy** metódust a **program** osztályban. Ez a metódus képurl-címet és kulcs-érték párok tömbjét veszi fel paraméterekként. Meghívja a Content Moderator rendszerképének API-ját (a REST használatával) a rendszerkép felnőtt és zamatos pontszámának beszerzéséhez. Ha a pontszám értéke nagyobb, mint 0,4 (a tartomány 0 és 1 között van), akkor a **ReviewTags** tömb megfelelő értékét **igaz** értékre állítja.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags metódus

A következő módszer egy képurl-címet és a Computer Vision előfizetési adatait veszi figyelembe, és elemzi a rendszerképet a hírességek jelenléte érdekében. Ha egy vagy több híresség található, akkor a **ReviewTags** tömb megfelelő értékét **igaz** értékre állítja.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags metódus

Ezután tekintse meg a **EvaluateCustomVisionTags** metódust, amely a tényleges termékeket osztályozza &mdash; ebben az esetben a jelzőket, a játékokat és a tollat. Kövesse a [hogyan hozhat létre egy osztályozó](../custom-vision-service/getting-started-build-a-classifier.md) útmutatót a saját egyéni rendszerkép-osztályozó létrehozásához, valamint a képeken látható jelzők, játékok és tollak (vagy bármi más, az egyéni címkék alapján) észleléséhez. A [GitHub](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) **-tárház Sample-images** mappájában található lemezképek segítségével gyorsan betaníthatja a példában szereplő kategóriákat.

![Custom Vision weblap a tollak, játékok és jelzők betanítási képeivel](images/tutorial-ecommerce-custom-vision.PNG)

Az osztályozó betanítása után szerezze be az előrejelzési kulcs és az előrejelzési végpont URL-címét (lásd: [az URL-cím és az előrejelzési kulcs beszerzése](../custom-vision-service/use-prediction-api.md#get-the-url-and-prediction-key) , ha segítségre van szüksége a beolvasáshoz), és ezeket az értékeket a `CustomVisionKey` és a `CustomVisionUri` mezőkhöz rendeli A metódus ezeket az értékeket használja az osztályozó lekérdezéséhez. Ha az osztályozó a képen egy vagy több egyéni címkét talál, akkor ez a metódus a **ReviewTags** tömbben lévő megfelelő értékeket állítja be **true** értékre.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Felülvizsgálati eszköz felülvizsgálatának létrehozása

Az előző szakaszban megvizsgálta, hogy az alkalmazás hogyan vizsgálja meg a felnőtt és a zamatos tartalom (Content Moderator), a hírességek (Computer Vision) és a különböző egyéb objektumok (Custom Vision) beérkező képeit. Ezután tekintse meg a **CreateReview** metódust, amely feltölti a lemezképeket az összes alkalmazott címkével ( _metaadatokként_ átadva) a Content moderator felülvizsgálati eszközre.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

A rendszerképek megjelennek a [Content moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/)áttekintés lapján.

![Képernyőkép a Content Moderator felülvizsgálati eszközről több képpel és a Kiemelt címkékkel](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>A tesztelési lemezképek listájának elküldése

Ahogy a **Main** metódusban is látható, a program egy "C:test" könyvtárat keres egy _Urls.txt_ fájllal, amely tartalmazza a képurl-címek listáját. Hozza létre ezt a fájlt és könyvtárat, vagy módosítsa az elérési utat úgy, hogy a szöveges fájlra mutasson. Ezután töltse fel ezt a fájlt a tesztelni kívánt képek URL-címeivel.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>A program futtatása

Ha követte az összes fenti lépést, a programnak fel kell dolgoznia az egyes lemezképeket (mindhárom szolgáltatást lekérdezni a megfelelő címkékre), majd fel kell töltenie a címkével ellátott képeket a Content Moderator felülvizsgálati eszközre.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy olyan programot állít be, amellyel elemezheti a termék rendszerképeit, megcímkézheti a termékeket, és lehetővé teheti, hogy a felülvizsgálati csapat tájékozott döntéseket hozhasson a tartalom moderálásával kapcsolatban. Következő lépésként tekintse meg a képek moderálásának részleteit.

> [!div class="nextstepaction"]
> [Moderált képek áttekintése](./review-tool-user-guide/review-moderated-images.md)