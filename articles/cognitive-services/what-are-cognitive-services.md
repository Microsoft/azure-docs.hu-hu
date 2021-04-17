---
title: Mik azok Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Cognitive Services teszi az MI-t minden fejlesztő számára gépi tanulás és adattudományi szakértelem nélkül. Csak egy API-hívást kell hívnia az alkalmazásból, hogy látható legyen (speciális képkeresés és -felismerés), hallás, beszéd, keresés és döntéshozatal az alkalmazásokban.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: cognitive services, kognitív intelligencia, kognitív megoldások, ai-szolgáltatások, kognitív ismeretek, kognitív funkciók
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: c89131cc34d45ea94f3bb290ac11ec86f4b83be3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587612"
---
# <a name="what-are-azure-cognitive-services"></a>Mik azok Azure Cognitive Services?

Azure Cognitive Services rest API-kat és ügyféloldali kódtár-ALAPÚ API-kat is elérhető felhőalapú szolgáltatások, amelyek segítségével kognitív intelligenciát építhet be alkalmazásaiba. Kognitív funkciókat adhat az alkalmazásokhoz mesterséges intelligencia (AI) vagy adattudományi készségek nélkül. Azure Cognitive Services AI-szolgáltatásokból állnak, amelyek lehetővé teszik kognitív megoldások építését, amelyek látnak, hallanak, beszélnek, értenek, és akár döntéseket is hozhatnak.

## <a name="categories-of-cognitive-services"></a>Kategóriák Cognitive Services

A kognitív ismereteket szolgáltató kognitív szolgáltatások katalógusa öt alappillérbe van besorolva:

* Látás
* Beszéd
* Nyelv
* Döntés
* Keresés

A cikk alábbi szakaszai az öt pillérhez tartozó szolgáltatások listáját ismertetik.

## <a name="vision-apis"></a>Vision API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Computer Vision](./computer-vision/index.yml "Computer Vision")|A Computer Vision szolgáltatás fejlett kognitív algoritmusokat biztosít a képek feldolgozásához és az információk visszaadása érdekében. A [Computer Vision első](./computer-vision/quickstarts-sdk/client-library.md) lépésekhez tekintse meg az Computer Vision rövid útmutatót.|
|[Egyéni vizuális szolgáltatás](./custom-vision-service/index.yml "Egyéni vizuális szolgáltatás")|A Custom Vision Service lehetővé teszi saját rendszerképosztályozók felépítését, üzembe helyezését és javítását. A képosztályozók olyan AI-szolgáltatások, amelyek a vizuális jellemzőik alapján címkéket alkalmaznak a képekre. |
|[Face](./face/index.yml "Arcfelismerés")| A Face szolgáltatás fejlett arcfelismerési algoritmusokat biztosít, lehetővé téve az arcattribútumok észlelését és felismerését. A [szolgáltatás első lépésekért](./face/quickstarts/client-libraries.md) tekintse meg a Face rövid útmutatóját.|
|[Form Recognizer](./form-recognizer/index.yml "Form Recognizer")|Form Recognizer azonosítja és kinyeri a kulcs-érték párokat és a táblaadatokat az űrlapdokumentumból; ezután strukturált adatokat ad vissza, beleértve az eredeti fájlban található kapcsolatokat is. Az [első Form Recognizer tekintse meg a rövid útmutatót.](./form-recognizer/quickstarts/client-library.md)|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|Video Indexer segítségével elemzéseket nyerhet ki a videóból. Lásd [Video Indexer rövid útmutatót](/azure/media-services/video-indexer/video-indexer-get-started) az első lépésekhez.|

## <a name="speech-apis"></a>Speech API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Speech szolgáltatás](./speech-service/index.yml "Speech szolgáltatás")|A Speech Service beszédfelismerési funkciókkal egészíti ki az alkalmazásokat. A Speech Service számos képességet kínál, például a beszédfelismerést, a szöveg-beszédfelismerést, a beszédfordítást és sok más képességet.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Nyelvi API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Language Understanding LUIS](./luis/index.yml "Language Understanding")|Language Understanding (LUIS) egy felhőalapú beszélgetési AI-szolgáltatás, amely egyéni gépi tanulási intelligenciát alkalmaz a felhasználó beszélgetési, természetes nyelvű szövegére az általános jelentés előrejelzése és a releváns, részletes információk kihozása érdekében. [A szolgáltatás első lépésekért](./luis/get-started-portal-build-app.md) tekintse meg a LUIS rövid útmutatóját.|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|QnA Maker lehetővé teszi, hogy kérdés-válasz szolgáltatást építsen ki a részben strukturált tartalomból. [Lásd QnA Maker rövid útmutatót](./qnamaker/quickstarts/create-publish-knowledge-base.md) a szolgáltatás első lépésekhez.|
|[Text Analytics](./text-analytics/index.yml "Szövegelemzés")| Text Analytics nyers szövegek természetes nyelvi feldolgozását biztosítja a hangulatelemzéshez, a kulcsszókereséshez és a nyelvfelismeréshez. Tekintse [Text Analytics rövid útmutatót](./text-analytics/quickstarts/client-libraries-rest-api.md) a szolgáltatás első lépésekhez.|
|[Translator](./translator/index.yml "Fordító")|A Translator gépi alapú szövegfordítást biztosít közel valós időben.|
| [Modern olvasó](./immersive-reader/index.yml "Modern olvasó") | Modern olvasó képernyőolvasó és -szövegértési képességeket ad hozzá az alkalmazásokhoz. Lásd [Modern olvasó rövid útmutatót](./immersive-reader/quickstarts/client-libraries.md) a szolgáltatás első lépésekhez. |

## <a name="decision-apis"></a>Döntési API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Anomáliadetektor](./anomaly-detector/index.yml "Anomáliadetektor") |anomáliadetektor segítségével figyelheti és észlelheti az idősorozat-adatok rendellenességeit. Lásd [anomáliadetektor rövid útmutatót](./anomaly-detector/quickstarts/client-libraries.md) a szolgáltatás első lépésekhez|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator a lehetséges sértő, nemkívánatos és kockázatos tartalmak figyelését teszi lehetővé. A [Content Moderator első](./content-moderator/client-libraries.md) lépésekhez tekintse meg a Content Moderator rövid útmutatót.|
|[Metrics Advisor](./metrics-advisor/index.yml) (előzetes verzió) | Metrics Advisor többféle idősorozat-adatok testreszabható anomáliadetektálását, valamint egy teljes funkcionalitású webes portált biztosít a szolgáltatás használatának segítése érdekében. A [Metrics Advisor első lépésekért](./metrics-advisor/quickstarts/rest-api-and-client-library.md) tekintse meg az Metrics Advisor rövid útmutatót. |
|[Personalizer](./personalizer/index.yml "Personalizer")|A Personalizer lehetővé teszi, hogy a valós idejű viselkedésükből tanulva válassza ki a felhasználók számára legjobb élményt. A [szolgáltatás első lépésekért tekintse](./personalizer/quickstart-personalizer-sdk.md) meg a Personalizer rövid útmutatóját.|

## <a name="search-apis"></a>Keresési API-k

> [!NOTE]
> A [Azure Cognitive Search](../search/index.yml)? Bár bizonyos Cognitive Services használ, más keresési technológia, amely más forgatókönyveket is támogat.

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Bing – Hírkeresés](/azure/cognitive-services/bing-news-search/ "Bing – Hírkeresés")|Bing News Search egy listát ad vissza a felhasználó lekérdezéséhez kapcsolódóként meghatározott hírekről.|
|[Bing – Videokeresés](/azure/cognitive-services/Bing-Video-Search/ "Bing – Videokeresés")|Bing Video Search a felhasználó lekérdezéséhez kapcsolódónak meghatározott videók listáját adja vissza.|
|[Bing Web Search](./bing-web-search/index.yml "Bing Web Search")|Bing Web Search olyan keresési eredményeket ad vissza, amelyek a felhasználó lekérdezése szempontjából relevánsnak határozzák meg.|
|[Bing Autosuggest](/azure/cognitive-services/Bing-Autosuggest "Bing Autosuggest")|Bing Autosuggest lehetővé teszi, hogy részleges keresési lekérdezési kifejezést küldjön a Bingnek, és lekérdezi a javasolt lekérdezések listáját.|
|[Bing – Egyéni keresés](/azure/cognitive-services/bing-custom-search "Bing – Egyéni keresés")|Bing Custom Search lehetővé teszi személyre szabott keresési élmények létrehozásához az Ön számára fontos témaköröket.|
|[Bing – Entitáskeresés](/azure/cognitive-services/bing-entities-search/ "Bing – Entitáskeresés")|Bing Entity Search olyan entitásokkal kapcsolatos információkat ad vissza, amelyek a Bing által a felhasználó lekérdezéséhez kapcsolódónak határozzák meg.|
|[Bing – Képkeresés](/azure/cognitive-services/bing-image-search "Bing – Képkeresés")|Bing Image Search a felhasználó lekérdezéséhez relevánsnak meghatározott képek megjelenítését adja vissza.|
|[Bing vizuális keresés](/azure/cognitive-services/bing-visual-search "Bing vizuális keresés")|Bing Visual Search olyan információkat ad vissza egy képpel kapcsolatban, mint a vizuálisan hasonló képek, a képen található termékek vásárlási forrásai és a kapcsolódó keresések.|
|[Bing Local Business Search](/azure/cognitive-services/bing-local-business-search/ "Bing Local Business Search")| Bing Local Business Search API lehetővé teszi, hogy az alkalmazások a keresési lekérdezések alapján megtalálják a helyi vállalkozásokkal kapcsolatos kapcsolattartási és helyadatokat.|
|[Bing – Helyesírás-ellenőrzés](/azure/cognitive-services/bing-spell-check/ "Bing – Helyesírás-ellenőrzés")|Bing Spell Check lehetővé teszi a kontextuson alapuló nyelvtani és helyesírás-ellenőrzést.|

## <a name="get-started-with-cognitive-services"></a>Ismerkedés a Cognitive Services szolgáltatással

Első lépésekként hozzon létre Cognitive Services erőforrást gyakorlati gyorsútmutatók segítségével a következő módszerekkel:

* [Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure Portal")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK-ügyfélkódtárak](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Azure Resource Manager- (ARM-) sablonok](./create-account-resource-manager-template.md?tabs=portal "Azure Resource Manager- (ARM-) sablonok")

## <a name="using-cognitive-services-in-different-development-environments"></a>Az Cognitive Services használata különböző fejlesztési környezetekben

Az Azure és Cognitive Services számos fejlesztési lehetőséghez férhet hozzá, például:

* Automatizálási és integrációs eszközök, például Logic Apps és Power Automate.
* Üzembe helyezési lehetőségek, Azure Functions a App Service. 
* Cognitive Services Docker-tárolók a biztonságos hozzáférés érdekében.
* Big Data-Apache Spark, Azure Databricks, Azure Synapse Analytics és Azure Kubernetes Service eszközök. 

További tudnivalókért lásd a Cognitive Services [lehetőségeket.](./cognitive-services-development-options.md)

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Biztonságos Cognitive Services használata

Azure Cognitive Services réteges biztonsági modellt biztosít, [](authentication.md "hitelesítés") beleértve az Azure Active Directory hitelesítő adatokon, egy érvényes erőforráskulcson és az [Azure Virtual Networksen keresztüli hitelesítést.](cognitive-services-virtual-networks.md "Azure virtuális hálózatok")

## <a name="containers-for-cognitive-services"></a>Tárolók a Cognitive Serviceshez

 Azure Cognitive Services több Docker-tárolót is biztosít, amelyek lehetővé teszi az Azure-ban, a helyszínen elérhető API-k használatát. Ezeknek a tárolóknak a használata rugalmasságot biztosít ahhoz, Cognitive Services megfelelőségi, biztonsági vagy egyéb működési okokból közelebb hozza az adatokat. További információ a [Cognitive Services tárolókról.](cognitive-services-container-support.md "Cognitive Services-tárolók")

## <a name="regional-availability"></a>Regionális elérhetőség

A Cognitive Services API-k a Microsoft által felügyelt adatközpontok egyre bővülő hálózatán üzemelnek. Az egyes API-k regionális rendelkezésre állását az [Azure-régiók listájában találja.](https://azure.microsoft.com/regions "Azure-régiók listája")

Olyan régiót keres, amely még nem támogatott? Tudassa velünk egy funkciókérés benyújtásával a [UserVoice fórumunkon.](https://cognitive.uservoice.com/ "UserVoice-fórum")

## <a name="supported-cultural-languages"></a>Támogatott kulturális nyelvek

Cognitive Services a szolgáltatási szinten számos kulturális nyelvet támogat. Az egyes API-k nyelvének rendelkezésre állását a támogatott nyelvek [listájában találja.](language-support.md "támogatott nyelvek listája")

## <a name="certifications-and-compliance"></a>Tanúsítványok és megfelelőség

Cognitive Services olyan tanúsítványokat kapott, mint a CSA STAR minősítés, a FedRAMP Moderate és a HIPAA BAA. Letöltheti [a tanúsítványokat](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "letöltés") a saját auditjaihoz és biztonsági felülvizsgálataihoz.

Az adatvédelem és az adatkezelés a Biztonsági és [adatkezelési központban található.](https://servicetrust.microsoft.com/ "Trust Center")

## <a name="support"></a>Támogatás

Cognitive Services több támogatási lehetőséget is kínál, amelyek segítenek az intelligens alkalmazások létrehozásának tovább haladásában. Cognitive Services fejlesztői közössége is van, amely segíthet megválaszolni a konkrét kérdéseket. Az elérhető lehetőségek teljes listájáért tekintse meg a Cognitive Services [és súgóbeállításokat.](cognitive-services-support-options.md "Cognitive Services és súgóbeállítások")

## <a name="next-steps"></a>Következő lépések

* [Cognitive Services-fiók létrehozása](cognitive-services-apis-create-account.md "Cognitive Services-fiók létrehozása")
* [A Cognitive Services újdonsága](whats-new-docs.md "A Cognitive Services újdonsága")
* [A költségek megterve és kezelése a Cognitive Services](plan-manage-costs.md)
