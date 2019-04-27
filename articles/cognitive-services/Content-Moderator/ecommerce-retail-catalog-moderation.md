---
title: 'Oktatóanyag: Mérsékelt e-kereskedelmi Termékképek - Content Moderator'
titlesuffix: Azure Cognitive Services
description: Állítsa be egy alkalmazás elemzésére és Termékképek besorolása, a megadott címkék (Azure Computer Vision és a Custom Vision segítségével), és nem kívánatos képek címke tovább felülvizsgálva (az Azure Content Moderator használatával).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 900ad8b7f676eb67f9ac0fc808600779f832a102
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699407"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Oktatóanyag: Termékképek mérsékelt e-kereskedelem az Azure Content Moderator

Ebben az oktatóanyagban azt tekintjük a Content Moderator, beleértve az Azure cognitive services használatával hatékony besorolása és a egy e-kereskedelmi forgatókönyvben mérsékelt termékek képeit. Fogja használni a Computer Vision és egyéni vizuális címkéjét szeretné alkalmazni a különböző (címkék) lemezképek, és ezután létrehozhat egy csapat tekintse át, amely egyesíti a Content Moderator machine learning-alapú technológiák emberi vizsgálóeszközt csapataival együttműködve biztosítanak egy intelligens moderálása a rendszer.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * A Content Moderator regisztráljon, és tekintse át a csoport létrehozása.
> * A Content Moderator Image API-jának használata a lehetséges felnőtteknek szóló és kényes tartalom keresésére.
> * A Computer Vision service segítségével ellenőrizze a hírességek tartalom (vagy más számítógép-Látástechnológia – cserélhető eszközként észlelhetőnek címkék).
> * A Custom Vision service segítségével jelzők, a játékok, és tollak (vagy más egyéni címkéket) meglétének ellenőrzése.
> * Jelenleg az emberi ellenőrző és végső döntéshozatal kombinált vizsgálat eredményeit.

A teljes minta kódja megtalálható a [e-kereskedelmi katalógusok moderálása minták](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) tárházban a Githubon.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A Content Moderator előfizetői azonosítója. Kövesse a [Cognitive Services-fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Content Moderator szolgáltatás és a kulcs beszerzése.
- A Computer Vision előfizetési kulcs (a fenti ugyanezen utasításokat).
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.
- Képkészlet egyes címkékhez, amelyek a Custom Vision osztályozó fogja használni (ez megkülönbözteti a kis játékok, tollak és jelzők USA).

## <a name="create-a-review-team"></a>Tekintse át a csoport létrehozása

Tekintse meg a [próbálja ki a Content Moderator, a weben](quick-start.md) regisztrálni létrehozásával kapcsolatos útmutatást a rövid útmutatóban a [Content Moderator, tekintse át az eszköz](https://contentmoderator.cognitive.microsoft.com/) , és tekintse át a csoport létrehozása. Jegyezze fel a **Csapatazonosító** érték a **hitelesítő adatok** lapot.

## <a name="create-custom-moderation-tags"></a>Egyéni moderálás címkék létrehozása

Ezután hozzon létre egyéni címkéket a felülvizsgálati eszközben (tekintse meg a [címkék](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) . Ha ez a folyamat segítségre van szüksége a cikk). Ebben az esetben hozzáadjuk a következő címkékkel: **hírességek**, **USA**, **jelző**, **kisfia**, és **toll**. Vegye figyelembe, hogy a címkék nem az összes kell lennie a Computer Vision cserélhető eszközként észlelhetőnek kategóriák (például **hírességek**); egyéni címkéket adhat hozzá, mindaddig, amíg a Custom Vision osztályozó által igénybe vett észre később a vonat.

![Egyéni címkék konfigurálása](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Visual Studio-projekt létrehozása

1. A Visual Studióban nyissa meg az új projekt párbeszédpanel. Bontsa ki a **telepített**, majd **Visual C#** , majd **Console app (.NET Framework)**.
1. Adjon nevet az alkalmazásnak **EcommerceModeration**, majd kattintson a **OK**.
1. A projekthez hozzáadni egy meglévő megoldás, ha egy indítási projektként jelölje ki a projektet.

Ez az oktatóanyag kiemeli a kódot, amely a projekt központi, de azt nem fedezik kódsor szükséges. Másolja ki a teljes tartalmának _Program.cs_ a mintaprojektet a ([e-kereskedelmi katalógusok moderálása minták](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)), a _Program.cs_ fájlt az új projekt. Ezt követően lépésenként az alábbi szakaszok további információt a projekt működését, és hogyan használható a saját maga.

## <a name="define-api-keys-and-endpoints"></a>API-kulcsok és a végpontok megadása

Ahogy említettük, ebben az oktatóanyagban három a cognitive services; Ezért van szükség három tartozó kulcsok és API-végpontokon. Tekintse meg a következő mezőket a **Program** osztály:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Frissíteni kell a `___Key` mezőket az értékeket az előfizetési kulcsok (fog kapni a `CustomVisionKey` később), és előfordulhat, hogy módosítania a `___Uri` , hogy a megfelelő régióban azonosítókat tartalmazó mezők. Töltse ki a `YOURTEAMID` része a `ReviewUri` azonosítójú, a korábban létrehozott csapatától mezőt. Utolsó része belefoglalja kitölti a `CustomVisionUri` később mezőben.

## <a name="primary-method-calls"></a>Elsődleges metódust hívja

Tekintse meg az alábbi kódot a **fő** metódussal, amely végighalad a kép URL-címek listáját. Azt elemzi az egyes rendszerképek és a három különböző szolgáltatások, az alkalmazott címkék rögzíti a **ReviewTags** tömböt, és ekkor létrehoz egy az emberi moderátorok (küld az eszköz a Content Moderator, tekintse át a lemezképeket). Ezek a módszerek az alábbi szakaszokban található felderíti. Vegye figyelembe, hogy itt, ha szeretné, szabályozhatja, tekintse át, melyik lemezképek küldött használatával a **ReviewTags** tömböt, ellenőrizze, hogy mely címkéket alkalmazott feltételes utasításban.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy metódus

Tekintse meg a **EvaluateAdultRacy** metódus az a **Program** osztály. Ez a módszer paraméterekként egy kép URL-címe és a egy tömb, kulcs-érték párok vesz igénybe. A felnőtt és Racy pontszámok annak a lemezképnek beolvasásához meghívja a Content Moderator Image API (REST használatával). Ha a pontszám a nagyobb, mint a 0,4 (az értéktartomány: 0, 1), azt a megfelelő értéket állít be a **ReviewTags** a tárolótömb **igaz**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags metódus

A következő metódust vesz igénybe egy kép URL-címe és a Computer Vision előfizetési információk és elemzi a kép hírességek meglétének. Ha egy vagy több hírességek talál, azt a megfelelő értéket állít be a **ReviewTags** a tárolótömb **igaz**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags metódus

Ezután tekintse meg a **EvaluateCustomVisionTags** metódussal, amely a tényleges termékek osztályozza&mdash;ebben az esetben megőrzendő tartalomként jelöli meg, a játékok, és tollak. Kövesse az utasításokat a [hogyan hozhat létre egy osztályozó](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) útmutató hozhat létre saját egyéni rendszerkép osztályozó észleléséhez jelzőket, a játékok, és tollak (vagy bármilyen, az egyéni címkék választott) képeken. A lemezképeket is használhatja a **minta-lemezképek** mappában található a [GitHub-adattárat](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) gyorsan betanításához néhány ebben a példában kategóriája.

![Egyéni vizuális weblapját a betanító kép tollak, a játékok és jelzők](images/tutorial-ecommerce-custom-vision.PNG)

Után az osztályozó által igénybe vett van tanítva, az előrejelzési és előrejelzési végponti URL-cím beszerzése (lásd: [URL-cím és az előrejelzési kulcs lekérése](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) segítséget beolvasása őket), és rendelje hozzá ezeket az értékeket a `CustomVisionKey` és `CustomVisionUri` mezők , illetve. A metódus az osztályozó által igénybe vett lekérdezéséhez ezeket az értékeket használja. Az osztályozó által igénybe vett talál a kép egy vagy több egyéni címkével, ha ez a módszer beállítja a megfelelő értékeket a **ReviewTags** a tárolótömb **igaz**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Felülvizsgálati eszköz felülvizsgálatának létrehozása

Az előző szakaszokban áttekinthette a metódusokat, amelyekkel a bejövő képek közül kiszűrhetők a felnőtt és a szexuális tartalmak (Content Moderator), illetve megkereshetők a hírességek (Computer Vision) és különböző egyéb témák (Custom Vision). Ezután tekintse meg a **CreateReview** metódust, amely feltölti a képeket az összes alkalmazott (_Metadata_ formátumban továbbított) címkével együtt a Content Moderator áttekintő eszközébe, hogy a képek elérhetők legyenek emberi áttekintésre. 

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

A képek felülvizsgálati lapján fog megjelenni a [Content Moderator felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/).

![A Content Moderator felülvizsgálati eszköz számos olyan rendszerkép található, és a kiemelt címkétől képernyőképe](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Teszt rendszerképek listájának elküldése

Amint láthatja, hogy az a **fő** metódus, a program "C:Test" címtár keres egy _Urls.txt_ kép URL-címek listáját tartalmazó fájl. Hozzon létre egy fájlt és könyvtárat, vagy módosítsa az elérési utat, mutasson a szövegfájlba, és töltse fel az URL-címeket, a képek, tesztelni szeretné a fájlt.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>A program futtatása

Ha a fenti lépéseket követte, a program kell feldolgozni az egyes rendszerképek (lekérdezés mindhárom szolgáltatást a megfelelő címkék) és majd feltölti a lemezképeket az eszközcímke-információ a Content Moderator felülvizsgálati eszköz.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban beállította egy program Termékképek céljából megtekintett terméktípus, és lehetővé teszi a tartalom-jóváhagyás megalapozott döntéseket csapattal elemzéséhez. Ezután további információ a képmoderálás részleteit.

> [!div class="nextstepaction"]
> [Moderált rendszerképek felülvizsgálata](./review-tool-user-guide/review-moderated-images.md)
