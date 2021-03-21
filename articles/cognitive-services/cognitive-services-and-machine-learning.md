---
title: Cognitive Services és Machine Learning
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy az Azure Cognitive Services hogyan illik a gépi tanulással kapcsolatos egyéb Azure-ajánlatokhoz.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: 1fc21b6338accef8e22b2e41ad2017d24ab8bd38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710464"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services és gépi tanulás

A Cognitive Services gépi tanulási képességeket biztosít olyan általános problémák megoldásához, mint például a szöveg elemzése az érzelmi hangulathoz, illetve a képek elemzése az objektumok vagy arcok felismeréséhez. Ezen szolgáltatások használatához nincs szükség különleges gépi tanulási vagy adatelemzési ismeretekre. 

[Cognitive Services](./what-are-cognitive-services.md) a szolgáltatások egy csoportja, amelyek mindegyike különböző, általánosított előrejelző képességeket támogat. A szolgáltatások különböző kategóriákba vannak osztva, így könnyebben megtalálhatja a megfelelő szolgáltatást. 

|Szolgáltatáskategória|Cél|
|--|--|
|[Döntés](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Olyan alkalmazásokat hozhat létre, amelyek javaslatokat jelenítenek meg a megalapozott és hatékony döntéshozatalhoz.|
|[Nyelv](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Engedélyezheti alkalmazásai számára a természetes nyelv előre beépített szkriptekkel történő feldolgozását, kiértékelheti a hangulatot, valamint megtudhatja, hogyan ismerheti fel, hogy mit szeretnének a felhasználók.|
|[Search](https://azure.microsoft.com/services/cognitive-services/directory/search/)|A Bing Search API-jait az alkalmazásaihoz adva több milliárd weboldalt, képet, videót és hírt fésülhet át egyetlen API-hívással.|
|[Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|A beszédet szöveggé, a szöveget pedig természetesen hangzó beszéddé alakíthatja. Egyik nyelvtől a másikra fordíthat, valamint engedélyezheti a beszélő ellenőrzését és felismerését.|
|[Látás](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Felismerheti, azonosíthatja, feliratozhatja, indexelheti és moderálhatja képeit, videóit és digitális szabadkézi tartalmait.|
||||

Cognitive Services használata, ha:

* Általánosított megoldást is használhat.
* Megoldás elérése programozási REST API vagy SDK-ból. 

Használjon másik gépi tanulási megoldást, ha:

* Ki kell választania az algoritmust, és nagyon konkrét adatfeldolgozásra van szükség.

## <a name="what-is-machine-learning"></a>Mit jelent a gépi tanulás?

A Machine learning egy olyan fogalom, amelyben összegyűjtheti az adatgyűjtést és egy algoritmust a konkrét igények megoldásához. Ha az adatokat és az algoritmust betanítják, a kimenet egy olyan modell, amelyet újra használhat a különböző adatmennyiséggel. A betanított modell az új adatelemzések alapján nyújt elemzéseket. 

A gépi tanulási rendszer kialakításának folyamata a gépi tanulással vagy az adatelemzéssel kapcsolatos ismereteket igényel.

A gépi tanulás [Azure Machine learning (pénzmosás) termékekkel és szolgáltatásokkal](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure%2fmachine-learning%2fstudio%2fcontext%2fml-context)van elbiztosítva.

## <a name="what-is-a-cognitive-service"></a>Mi az a kognitív szolgáltatás?

A kognitív szolgáltatás egy gépi tanulási megoldás részét képező alkatrészt vagy az összes összetevőt biztosítja: az adatmennyiséget, az algoritmust és a betanított modellt. Ezeknek a szolgáltatásoknak a célja, hogy általános ismereteket kérjenek az adatairól anélkül, hogy a gépi tanulással vagy az adatelemzéssel kellene foglalkoznia. Ezek a szolgáltatások REST API (ka) t és nyelvi alapú SDK-kat egyaránt biztosítanak. Ennek eredményeképpen a szolgáltatások használatához programozási nyelvtudásra van szükség.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Hogyan működik a Cognitive Services és Azure Machine Learning (pénzmosás)?

Mindkettőnek célja a mesterséges intelligencia (AI) alkalmazása az üzleti műveletek javítására, bár az egyes lehetőségek a megfelelő ajánlatokban különböznek. 

A célközönségek általában különbözőek:

* Cognitive Services a gépi tanulási élmény nélküli fejlesztők számára.
* Azure Machine Learning az adatszakértők számára igazított. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Miben különbözik a kognitív szolgáltatások a gépi tanulástól?

A kognitív szolgáltatás egy betanított modellt biztosít Önnek. Ez egy REST API (ok) vagy SDK által elérhetővé teszi az adatok és az algoritmus együttesét. A szolgáltatás a forgatókönyvtől függően percek alatt implementálható.  A kognitív szolgáltatás olyan általános problémákra ad választ, mint például a szöveges vagy az elemek azonosítása a képekben. 

A gépi tanulás olyan folyamat, amely általában hosszabb ideig igényel a sikeres megvalósításhoz. Ez az idő az adatgyűjtés, a tisztítás, az átalakítás, az algoritmus kiválasztása, a modell betanítása és az üzembe helyezés során a kognitív szolgáltatás által biztosított azonos szintű funkciók elérésére szolgál. A gépi tanulással a nagymértékben specializált és/vagy konkrét problémákra lehet választ adni. A gépi tanulási problémákhoz szükség van a szóban forgó probléma konkrét tárgyára és adataira, valamint az adatelemzési szaktudásra.

## <a name="what-kind-of-data-do-you-have"></a>Milyen típusú adattípusok vannak?

A Cognitive Services szolgáltatások csoportjaként a betanított modellhez sem, néhány vagy az összes egyéni adatmennyiségre lehet szükség. 

### <a name="no-additional-training-data-required"></a>Nincs szükség további betanítási adatgyűjtésre

A teljesen betanított modellt biztosító szolgáltatások _átlátszatlan dobozként_ is kezelhetők. Nem kell tudnia, hogyan működnek, vagy milyen módon használták őket a betanításhoz. Az adatai egy teljes körűen betanított modellbe kerülnek, így előrejelzést kaphat. 

### <a name="some-or-all-training-data-required"></a>Néhány vagy az összes szükséges betanítási érték

Egyes szolgáltatások lehetővé teszik saját adatai használatát, majd a modellek betanítását. Ez lehetővé teszi a modell kiterjesztését a szolgáltatás adatai és algoritmusa használatával a saját adataival. A kimenet megfelel az Ön igényeinek. A saját adatai használatakor előfordulhat, hogy a szolgáltatásra vonatkozó módon kell címkével ellátnia az adatait. Ha például a virágokat azonosító modellt szeretne betanítani, megadhatja a virág-rendszerképek katalógusát, valamint a virág helyét az egyes képeken a modell betanításához. 

A szolgáltatások _lehetővé_ tehetik, hogy a saját adatvédelme érdekében adatforrást szolgáltasson. Előfordulhat, hogy egy szolgáltatásnak _meg kell adnia_ az adattárolást. 

### <a name="real-time-or-near-real-time-data-required"></a>Valós idejű vagy közel valós idejű adatbevitel szükséges

Egy szolgáltatásnak valós idejű vagy közel valós idejű adattípusra lehet szüksége egy hatékony modell létrehozásához. Ezek a szolgáltatások jelentős mennyiségű modellt dolgoznak fel. 

## <a name="service-requirements-for-the-data-model"></a>Az adatmodell szolgáltatási követelményei

A következő adattípusok kategorizálják az egyes szolgáltatásokat, amelyekkel engedélyezheti vagy megkövetelheti a szükséges adattípust.

|Kognitív szolgáltatás|Nincs szükség betanítási adatgyűjtésre|Megadhat egy vagy több betanítási adatkészletet|Valós idejű vagy közel valós idejű adatgyűjtés|
|--|--|--|--|
|[Anomáliadetektor](./Anomaly-Detector/overview.md)|x|x|x|
|Bing Search |x|||
|[Computer Vision](./computer-vision/overview.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./custom-vision-service/overview.md)||x||
|[Face](./Face/Overview.md)|x|x||
|[Form Recognizer](./form-recognizer/overview.md)||x||
|[Modern olvasó](./immersive-reader/overview.md)|x|||
|[Ink Recognizer](/previous-versions/azure/cognitive-services/Ink-Recognizer/overview)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizer](./personalizer/what-is-personalizer.md)|x|x|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Beszélő felismerő](./speech-service/speaker-recognition-overview.md)||x||
|[Beszédfelismerési szöveg – beszéd (TTS)](speech-service/text-to-speech.md)|x|x||
|[Beszéd-beszéd – szöveg (STT)](speech-service/speech-to-text.md)|x|x||
|[Speech Translation](speech-service/speech-translation.md)|x|||
|[Text Analytics](./text-analytics/overview.md)|x|||
|[Translator](./translator/translator-info-overview.md)|x|||
|[Translator-Custom Translator](./translator/custom-translator/overview.md)||x||

* A megszemélyesítő csak a szolgáltatás által gyűjtött betanítási adatokat igényli (valós időben működik) a szabályzat és az adatok kiértékeléséhez. A személyre szabott beállításhoz nincs szükség nagyméretű korábbi adatkészletekre a kezdeti vagy a kötegelt képzéshez. 

## <a name="where-can-you-use-cognitive-services"></a>Hol használható Cognitive Services?
 
A szolgáltatás minden olyan alkalmazásban használatos, amely REST API (ka) t vagy SDK-hívásokat tesz elérhetővé. Ilyenek például a webhelyek, a robotok, a virtuális vagy a vegyes valóság, az asztali és a mobil alkalmazások. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Hogyan kapcsolódik az Azure Cognitive Search a Cognitive Serviceshoz?

Az [Azure Cognitive Search](../search/search-what-is-azure-search.md) egy különálló felhőalapú keresési szolgáltatás, amely igény szerint a Cognitive Services használatával adja hozzá a munkaterhelések indexeléséhez a rendszerképeket és a természetes nyelvi feldolgozást. A Cognitive Services az Azure-ban Cognitive Search az egyes API-kat burkoló [beépített készségekkel](../search/cognitive-search-predefined-skills.md) . A forgatókönyvek számára ingyenes erőforrást használhat, de megtervezheti a nagy mennyiségű [számlázható erőforrások](../search/cognitive-search-attach-cognitive-services.md) létrehozását és csatolását.

## <a name="how-can-you-use-cognitive-services"></a>Hogyan használhatja a Cognitive Services?

Minden szolgáltatás információt nyújt az adatairól. A szolgáltatások összevonásával olyan megoldásokat használhat, mint például a beszédfelismerés (hang) szöveggé konvertálása, a szöveg fordítása számos nyelvre, majd a lefordított nyelvek használatával választ kaphat a Tudásbázisból. Habár a Cognitive Services használhatók saját intelligens megoldások létrehozására, a modelleket és a fejlesztési folyamatot felgyorsító hagyományos gépi tanulási projektekkel kombinálva is létrehozhatók. 

Más gépi tanulási eszközökhöz exportált modelleket biztosító Cognitive Services:

|Kognitív szolgáltatás|Modell adatai|
|--|--|
|[Custom Vision](./custom-vision-service/overview.md)|[Exportálás](./custom-vision-service/export-model-python.md) az Androidhoz készült Tensorflow, CoreML for IOS11, ONNX for Windows ml|

## <a name="learn-more"></a>Tudjon meg többet

* [Architektúra-útmutató – Mik a gépi tanulási termékek a Microsoftnál?](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Gépi tanulás – a Deep learning és a Machine learning bemutatása](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Következő lépések

* Hozzon létre egy kognitív szolgáltatási fiókot a [Azure Portal](cognitive-services-apis-create-account.md) vagy az [Azure CLI](./cognitive-services-apis-create-account-cli.md)-vel.
* Ismerje meg, hogyan végezhet [hitelesítést](authentication.md) egy kognitív szolgáltatásban.
* [Diagnosztikai naplózás](diagnostic-logging.md) használata a problémák azonosításához és a hibakereséshez. 
* Kognitív szolgáltatás üzembe helyezése Docker- [tárolóban](cognitive-services-container-support.md).
* Naprakészen tarthatja a [szolgáltatás frissítéseit](https://azure.microsoft.com/updates/?product=cognitive-services).