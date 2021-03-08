---
title: Videók elemzése a Media Services v3
description: Ismerje meg, hogyan elemezheti a videókat Azure Media Services használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 4a050d838bae9b394f5f292698781a9a824af0bf
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454124"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Oktatóanyag: videók elemzése Media Services v3-val

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Bár ez az oktatóanyag a [.net SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) -példákat használja, az általános lépések megegyeznek [a REST API](/rest/api/media/liveevents), a [CLI](/cli/azure/ams/live-event)vagy más támogatott [SDK](media-services-apis-overview.md#sdks)-k esetében.

Az oktatóanyag bemutatja, hogyan elemezhet videókat az Azure Media Serviceszel. Bizonyos esetekben szüksége lehet a rögzített video- és audiotartalmak részletesebb elemzésére. Egy vállalatnál például a vásárlói elégedettség növelése érdekében szükség lehet a beszéd szöveggé alakítására, hogy az ügyfélszolgálati beszélgetéseket kereshető katalógussá alakítsák, amelyhez indexek és irányítópultok érhetőek el. Ezután betekintést nyerhetnek a vállalatba. Ezek az információk tartalmazzák a gyakori panaszok listáját, az ilyen panaszok forrásait és egyéb hasznos információkat.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Töltse le a következő témakörben ismertetett minta alkalmazást:.
> * Vizsgálja meg a megadott videót elemző kódot.
> * Futtassa az alkalmazást.
> * Vizsgálja meg a kimenetet.
> * Az erőforrások eltávolítása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Megfelelőség, adatvédelem és biztonság
 
Fontos megjegyezni, hogy meg kell felelnie a Video Indexer használatának összes vonatkozó törvényének, és előfordulhat, hogy nem használja Video Indexer vagy bármely más Azure-szolgáltatást olyan módon, amely sérti mások jogait, vagy más személyeknek is ártalmas lehet. Mielőtt bármilyen videót feltölt, beleértve a biometrikus adatokat is a Video Indexer szolgáltatásba feldolgozásra és tárolásra, az összes megfelelő jogosultsággal kell rendelkeznie, beleértve a megfelelő hozzájárulásokat is a videóban szereplő személy (ek) hoz. A megfelelőségről, az adatvédelemről és a biztonságról Video Indexer a Microsoft [Cognitive Services feltételeiben](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)olvashat. A Microsoft adatvédelmi kötelezettségei és az adatok kezelése érdekében tekintse át a Microsoft [adatvédelmi nyilatkozatát](https://privacy.microsoft.com/PrivacyStatement), az [online szolgáltatások használati feltételeit](https://www.microsoft.com/licensing/product-licensing/products) ("Ost") és az [adatfeldolgozási kiegészítést](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Az adatmegőrzéssel, törléssel és megsemmisítéssel kapcsolatos további adatvédelmi információk az OST-ben és [itt](../video-indexer/faq.md)érhetők el. Video Indexer használatával Ön vállalja, hogy az Cognitive Services feltételek, az OST, a DPA és az adatvédelmi nyilatkozat köti magát.

## <a name="prerequisites"></a>Előfeltételek

- Ha nincs telepítve a Visual Studio, szerezze be a [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)-es frissítést.
- [Hozzon létre egy Media Services fiókot](./create-account-howto.md).<br/>Ügyeljen arra, hogy az erőforráscsoport neveként használt értékeket jegyezze fel, és Media Services a fiók nevét.
- Kövesse a [Azure Media Services API-nak az Azure CLI-vel való elérésének](./access-api-howto.md) lépéseit, és mentse a hitelesítő adatokat. Ezeket az API-k eléréséhez kell használnia.

## <a name="download-and-configure-the-sample"></a>A minta letöltése és konfigurálása

Klónozza a gépre a .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

A minta az [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos) mappában található.

Nyissa meg [appsettings.jsa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) letöltött projektben. Cserélje le az értékeket az API-khoz [való hozzáféréshez](./access-api-howto.md)kapott hitelesítő adatokkal.

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>A megadott videót elemző kód vizsgálata

Ez a szakasz az *AnalyzeVideos* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) nevű fájljában megadott függvényeket mutatja be.

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy **átalakítót** és egy olyan **feladatot** , amely elemzi a videót.
2. Létrehoz egy bemeneti **eszközt** , és feltölti a videót. Az objektum lesz a feladat bemenete.
3. Létrehoz egy kimeneti objektumot, amely a feladat kimenetét tárolja.
4. Elküldi a feladatot.
5. Ellenőrzi a feladat állapotát.
6. Letölti a feladat futtatásának eredményeként kapott fájlokat.

> [!NOTE]
> Előzetes Video vagy Audio Analyzer-beállítások használata esetén állítsa be a fiókját 10 S3-as Media szolgáltatás számára fenntartott egységre az Azure Portalon. További információkért lásd: a [médiafájlok feldolgozásának skálázása](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bemeneti objektum létrehozása és helyi fájl feltöltés az objektumba 

A **CreateInputAsset** függvény létrehoz egy új bemeneti [objektumot](/rest/api/media/assets), és feltölti az objektumba a megadott helyi videofájlt. Az objektum lesz a kódolási feladat bemenete. A Media Services 3-as verziójában a feladat bemenete lehet egy objektum vagy egy olyan tartalom, amelyet egy HTTPS URL-cím használatával tesz elérhetővé a Media Services-fiók számára. A HTTPS URL-címekről történő kódolásról [ebben](job-input-from-http-how-to.md) a cikkben talál további információt.  

A Media Services 3-as verziójában Azure Storage API-k használatával tölthet fel fájlokat. Ennek módját a következő .NET-kódrészlet mutatja be.

A következő függvény hajtja végre ezeket a műveleteket:

* Létrehoz egy eszközt.
* Egy írható [sas URL-cím](../../storage/common/storage-sas-overview.md) beolvasása az eszköz tárolójában a [tárolóban](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).

    Ha az eszköz [ListContainerSas](/rest/api/media/assets/listcontainersas) funkcióját használja a sas URL-címek beszerzéséhez, vegye figyelembe, hogy a függvény több sas URL-címet ad vissza, mivel mindegyik Storage-fiókhoz két Storage-fiók kulcsa van. A Storage-fiók két kulccsal rendelkezik, mert lehetővé teszi a Storage-fiókok kulcsainak zökkenőmentes rotációját (például egy másik, az új kulcs használatának megkezdése és a másik kulcs elforgatása). Az 1. SAS URL-cím a Storage key1 és a második tárolási key2 jelöli.
* Feltölti a fájlt a Storage tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Kimeneti objektum létrehozása a feladat eredményeinek tárolásához

A kimeneti [objektum](/rest/api/media/assets) tárolja a feladat eredményeit. A projekt határozza meg a **DownloadResults** függvényt, amely letölti az eredményt ebből a kimeneti objektumból az „output” mappába, hogy megtekinthesse.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Átalakítás és videókat elemző feladat létrehozása

A Media Services tartalmának kódolásakor vagy feldolgozásakor gyakori minta a kódolási beállítások beállítása Receptként. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Új feladatok elküldésével minden új videóhoz ezt a receptet alkalmazza a könyvtárában lévő összes videóra. Media Services egy receptet **átalakítónak** nevezzük. További információ: [átalakítások és feladatok](./transforms-jobs-concept.md). Az ebben az oktatóanyagban leírt minta meghatároz egy receptet a megadott videó elemzésére.

#### <a name="transform"></a>Átalakítás

Egy új [átalakításpéldány](/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A **TransformOutput** egy kötelező paraméter. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Ebben a példában a rendszer a **VideoAnalyzerPreset** -készletet használja, és a nyelvet ("en-us") adja át a konstruktorának ( `new VideoAnalyzerPreset("en-US")` ). Ez az előzetes beállítás lehetővé teszi több audio- és videoelemzés elvégzését a videón. Az **AudioAnalyzerPreset** előzetes beállítás akkor lehet hasznos, ha több audioelemzést szeretne elvégezni a videón.

**Átalakítás** létrehozásakor először ellenőrizze, hogy a **Get** metódus használatával már létezik-e már az alábbi kódban látható módon. A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Feladat

Ahogy korábban említettük, az [átalakítási](/rest/api/media/transforms) objektum a recept, a [feladat](/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladatok** olyan információkat határoznak meg, mint a bemeneti videó helye és a kimenet helye. A videó helyét a következők használatával adhatja meg: HTTPS URL-címek, SAS URL-címek, vagy a Media Service-fiókban található objektumok.

A jelen példában a feladat bemeneti objektuma egy helyi videó.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladatok elvégzése hosszabb időt vesz igénybe. Ha igen, értesítést szeretne kapni. Több módon is kérhet értesítést a [feladat](/rest/api/media/jobs) végrehajtásáról. A legegyszerűbb lehetőség (ez az itt látható) a lekérdezés használata.

A lekérdezés nem ajánlott eljárás az üzemi alkalmazások számára a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt.

Az Event Grid egy magas rendelkezésre állású, egyenletes teljesítményű, dinamikusan skálázható szolgáltatás. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. Az egyszerű, HTTP-alapú reaktív eseménykezelés segít hatékony megoldásokat kialakítani az események intelligens szűrése és átirányítása révén. További információ: [események átirányítása egyéni webes végpontra](job-state-events-cli-how-to.md).

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladattípus hibát észlelt, a **hiba** állapota jelenik meg. Ha a feladat megszakítása folyamatban van, a rendszer **megszakítja** a műveletet, majd **megszakítja** az elkészült állapotot.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>A feladat eredményének letöltése

A következő függvény letölti a kimeneti [eszköz](/rest/api/media/assets) eredményeit a "kimenet" mappába, így ellenőrizheti a feladat eredményét.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Erőforrások eltávolítása a Media Services-fiókban

Általában érdemes megtisztítani mindent, kivéve azokat az objektumokat, amelyeket újra fel kíván használni (általában újra fogja használni az átalakításokat és megőrzi a StreamingLocators). Ha azt szeretné, hogy a fiókja a kísérletezés után is tiszta legyen, törölje azokat az erőforrásokat, amelyeket nem kíván újra felhasználni. A következő kód például törli a feladatot és a kimeneti eszközt:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>A mintaalkalmazás futtatása

A *AnalyzeVideos* alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt.

A program futtatásakor a feladat külön miniatűrt hoz létre a videóban található minden egyes archoz. Ezenkívül létrehozza az insights.json fájlt.

## <a name="examine-the-output"></a>A kimenet vizsgálata

A videók elemzésekor keletkező kimeneti fájl neve insights.json. Ez a fájl tartalmazza a videó elemzésének eredményeit. A [médiaintelligenciával](./analyzing-video-audio-files-concept.md) kapcsolatos cikk részletesen leírja a json-fájl elemeit.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Több szál használata

A Azure Media Services v3 SDK-k nem a szálon biztonságosak. Többszálas alkalmazás használata esetén a szálon új AzureMediaServicesClient objektumot kell kiállítani.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: fájlok feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)
