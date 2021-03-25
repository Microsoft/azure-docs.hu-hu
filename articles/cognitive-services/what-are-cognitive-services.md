---
title: Mi az Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Cognitive Services a mesterséges intelligenciát minden fejlesztő számára elérhetővé teszi a gépi tanulás és az adattudományi szakértelem megkövetelése nélkül. Csak egy API-hívást kell létrehoznia az alkalmazásból, amely lehetővé teszi a (speciális képkeresés és-felismerés), a hallás, a beszéd, a keresés és a döntéshozatal bevezetését az alkalmazásokba.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: kognitív szolgáltatások, kognitív intelligencia, kognitív megoldások, AI-szolgáltatások, kognitív ismeretek, kognitív funkciók
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 71f8635d1cd96a6436cfc902622bf18bc608a143
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867185"
---
# <a name="what-are-azure-cognitive-services"></a>Mi az Azure Cognitive Services?

Az Azure Cognitive Services a REST API-kkal és az ügyféloldali kódtár SDK-kkal rendelkező felhőalapú szolgáltatások, amelyek segítenek a kognitív intelligencia alkalmazásokban való kiépítésében. Az alkalmazásokhoz a mesterséges intelligencia (AI) vagy az adattudományi képességek nélkül is hozzáadhat kognitív funkciókat. Az Azure Cognitive Services különböző AI-szolgáltatásokból áll, amelyek lehetővé teszik olyan kognitív megoldások készítését, amelyek megtekinthetik, meghallgatják, megértik, megértették és akár döntéseket is hozhatnak.

## <a name="categories-of-cognitive-services"></a>A Cognitive Services kategóriái

A kognitív ismereteket biztosító kognitív szolgáltatások katalógusa öt fő pillérbe van kategorizálva:

* Látás
* Beszéd
* Nyelv
* Döntés
* Keresés

A cikk következő fejezetei az öt pillér részét képező szolgáltatások listáját ismertetik.

## <a name="vision-apis"></a>Jövőkép API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Computer Vision](./computer-vision/index.yml "Computer Vision")|A Computer Vision szolgáltatás a képek feldolgozásához és a visszaadott adatokhoz való hozzáférést biztosít a speciális kognitív algoritmusokhoz. A szolgáltatás használatának megkezdéséhez tekintse meg [Computer Vision](./computer-vision/quickstarts-sdk/client-library.md) rövid útmutatót.|
|[Egyéni vizuális szolgáltatás](./custom-vision-service/index.yml "Egyéni vizuális szolgáltatás")|A Custom Vision Service lehetővé teszi saját rendszerkép-besorolások készítését, üzembe helyezését és fejlesztését. A képosztályozó egy AI-szolgáltatás, amely a vizualizáció tulajdonságai alapján címkéket alkalmaz a képeken. |
|[Face](./face/index.yml "Arcfelismerés")| A Face szolgáltatás hozzáférést biztosít a speciális arc-algoritmusokhoz, lehetővé téve a Face attribútumok észlelését és felismerését. A szolgáltatás használatának megkezdéséhez tekintse meg a [Face](./face/quickstarts/client-libraries.md) rövid útmutatót.|
|[Form Recognizer](./form-recognizer/index.yml "Form Recognizer")|Az űrlap-felismerő azonosítja és kigyűjti a kulcs-érték párokat és a táblák adatait az űrlap dokumentumaiból. Ezután a a strukturált adatokat is megjeleníti, beleértve az eredeti fájlban lévő kapcsolatokat is. Az első lépésekhez tekintse meg az [űrlap-felismerő](./form-recognizer/quickstarts/client-library.md) rövid útmutatóját.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|A Video Indexer lehetővé teszi a videóból származó adatok kinyerését. A kezdéshez tekintse meg [video Indexer](/media-services/video-indexer/video-indexer-get-started.md) rövid útmutatót.|

## <a name="speech-apis"></a>Beszédfelismerési API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Beszédfelismerési szolgáltatás](./speech-service/index.yml "Speech szolgáltatás")|A beszédfelismerési szolgáltatás lehetővé teszi a beszédfelismerést támogató funkciók használatát az alkalmazásokhoz. A Speech Service számos funkciót tartalmaz, például a beszédfelismerést, a szöveg és a beszéd fordítását, valamint sok mást.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Nyelvi API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Language Understanding LUIS](./luis/index.yml "Language Understanding")|Language Understanding (LUIS) egy felhőalapú társalgási szolgáltatás, amely egyéni gépi tanulási intelligenciát alkalmaz a felhasználó társalgási, természetes nyelvű szövegére az általános jelentés kiszámításához, valamint a releváns és részletes információk lekéréséhez. A szolgáltatás használatának megkezdéséhez [tekintse](./luis/get-started-portal-build-app.md) meg a Luis rövid útmutatót.|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|QnA Maker lehetővé teszi, hogy a félig strukturált tartalomból hozzon létre egy kérdés-válasz szolgáltatást. A szolgáltatás használatának megkezdéséhez [tekintse meg QnA Maker](./qnamaker/quickstarts/create-publish-knowledge-base.md) rövid útmutatót.|
|[Text Analytics](./text-analytics/index.yml "Szövegelemzés")| A Text Analytics természetes nyelvi feldolgozást tesz lehetővé a nyers szövegekben az érzelmek elemzése, a fő kifejezés kinyerése és a nyelvfelismerés terén. A szolgáltatás használatának megkezdéséhez tekintse meg [text Analytics](./text-analytics/quickstarts/client-libraries-rest-api.md) rövid útmutatót.|
|[Translator](./translator/index.yml "Fordító")|A Translator gépi alapú szöveges fordítást biztosít közel valós időben.|
| [Modern olvasó](./immersive-reader/index.yml "Modern olvasó") | Az olvasói és a szövegértési képességek az alkalmazásokban is felhasználhatók. A szolgáltatás használatának megkezdéséhez tekintse meg a részletes [olvasói](./immersive-reader/quickstarts/client-libraries.md) útmutató című témakört. |

## <a name="decision-apis"></a>Döntési API-k

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Anomáliadetektor](./anomaly-detector/index.yml "Anomáliadetektor") |Az anomáliák detektorral figyelheti és azonosíthatja a rendellenességeket az idősorozat-adataiban. A szolgáltatás használatának megkezdéséhez tekintse meg a [anomália-detektor](./anomaly-detector/quickstarts/client-libraries.md) rövid útmutatóját.|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator a lehetséges sértő, nemkívánatos és kockázatos tartalmak figyelését teszi lehetővé. A szolgáltatás használatának megkezdéséhez tekintse meg [Content moderator](./content-moderator/client-libraries.md) rövid útmutatót.|
|[Metrikai tanácsadó](./metrics-advisor/index.yml) (előzetes verzió) | A metrikák Advisor testreszabható anomáliák észlelését teszi lehetővé a többváltozós idősorozat-adatokon, valamint egy teljes körű webes portált, amely a szolgáltatás használatához nyújt segítséget. A szolgáltatás használatának megkezdéséhez tekintse meg a [metrikák Advisor](./metrics-advisor/quickstarts/rest-api-and-client-library.md) rövid útmutatóját. |
|[Personalizer](./personalizer/index.yml "Personalizer")|A személyre szabás lehetővé teszi, hogy kiválassza a legjobb élményt, amelyet a felhasználók láthatnak, valós idejű viselkedésük alapján. A szolgáltatás használatának megkezdéséhez tekintse meg a [személyre szabott](./personalizer/quickstart-personalizer-sdk.md) rövid útmutatót.|

## <a name="search-apis"></a>Keresési API-k

> [!NOTE]
> Az [Azure Cognitive Searcht](../search/index.yml)keresi? Bár egyes feladatokhoz Cognitive Services használ, ez egy másik keresési technológia, amely más forgatókönyveket is támogat.

|Szolgáltatásnév|A szolgáltatás leírása|
|:-----------|:------------------|
|[Bing – Hírkeresés](/azure/cognitive-services/bing-news-search/ "Bing – Hírkeresés")|Bing News Search a felhasználó lekérdezéséhez kapcsolódó Hírek listáját adja vissza.|
|[Bing – Videokeresés](/azure/cognitive-services/Bing-Video-Search/ "Bing – Videokeresés")|Bing Video Search a felhasználó lekérdezéséhez tartozó videók listáját adja vissza.|
|[Bing Web Search](./bing-web-search/index.yml "Bing Web Search")|Bing Web Search a felhasználó lekérdezéséhez szükséges keresési eredmények listáját adja vissza.|
|[Bing Autosuggest](/azure/cognitive-services/Bing-Autosuggest "Bing Autosuggest")|Bing Autosuggest lehetővé teszi részleges keresési lekérdezési kifejezés küldését a Bing számára, és a javasolt lekérdezések listájának visszaadása.|
|[Bing – Egyéni keresés](/azure/cognitive-services/bing-custom-search "Bing – Egyéni keresés")|Bing Custom Search lehetővé teszi, hogy testreszabott keresési funkciókat hozzon létre az Ön számára fontos témakörökhöz.|
|[Bing – Entitáskeresés](/azure/cognitive-services/bing-entities-search/ "Bing – Entitáskeresés")|Bing Entity Search a Bing által megadott entitásokra vonatkozó információkat ad vissza a felhasználó lekérdezéséhez.|
|[Bing – Képkeresés](/azure/cognitive-services/bing-image-search "Bing – Képkeresés")|Bing Image Search visszaadja a felhasználó lekérdezéséhez szükséges képek megjelenítését.|
|[Bing vizuális keresés](/azure/cognitive-services/bing-visual-search "Bing vizuális keresés")|A Bing Visual Search a képekkel kapcsolatos információkat ad vissza, például vizuálisan hasonló képeket, a lemezképben található termékek vásárlási forrásait és a kapcsolódó kereséseket.|
|[Bing Local Business Search](/azure/cognitive-services/bing-local-business-search/ "Bing Local Business Search")| A Bing local Business Search API lehetővé teszi az alkalmazások számára, hogy a keresési lekérdezések alapján megtalálják a helyi vállalkozások kapcsolattartási és tartózkodási információit.|
|[Bing – Helyesírás-ellenőrzés](/azure/cognitive-services/bing-spell-check/ "Bing – Helyesírás-ellenőrzés")|Bing Spell Check lehetővé teszi a kontextusos nyelvtan és a helyesírás-ellenőrzés végrehajtását.|

## <a name="get-started-with-cognitive-services"></a>Ismerkedés a Cognitive Services szolgáltatással

Először hozzon létre egy Cognitive Services-erőforrást gyakorlati útmutatókkal a következő módszerek használatával:

* [Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure Portal")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK-ügyfél kódtárai](cognitive-services-apis-create-account-cli.md?tabs=windows "kognitív-szolgáltatások-API-k-fiók-ügyfél-könyvtár? Pivots = Programming-Language-csharp")
* [Azure Resource Manager- (ARM-) sablonok](./create-account-resource-manager-template.md?tabs=portal "Azure Resource Manager- (ARM-) sablonok")

## <a name="using-cognitive-services-in-different-development-environments"></a>Cognitive Services használata különböző fejlesztői környezetekben

Az Azure és a Cognitive Services számos fejlesztési lehetőséghez férhet hozzá, például:

* Automatizálási és integrációs eszközök, mint például a Logic Apps és a Power automatizálása.
* Az üzembe helyezési lehetőségek, például a Azure Functions és a App Service. 
* Cognitive Services Docker-tárolókat a biztonságos hozzáféréshez.
* Olyan eszközök, mint például a Apache Spark, a Azure Databricks, az Azure szinapszis Analytics és az Azure Kubernetes szolgáltatás Big-adatokhoz. 

További információ: [Cognitive Services fejlesztői beállítások](./cognitive-services-development-options.md).

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Biztonságos Cognitive Services használata

Az Azure Cognitive Services többrétegű biztonsági modellt biztosít, beleértve a [hitelesítést](authentication.md "hitelesítés") Azure Active Directory hitelesítő adatokon, egy érvényes erőforrás-kulcson és az [Azure virtuális hálózatokon](cognitive-services-virtual-networks.md "Azure virtuális hálózatok")keresztül.

## <a name="containers-for-cognitive-services"></a>Tárolók a Cognitive Serviceshez

 Az Azure Cognitive Services több Docker-tárolót biztosít, amelyek lehetővé teszik az Azure-ban, a helyszínen elérhető API-k használatát. Ezen tárolók használatával rugalmasan Cognitive Services a megfelelőségi, biztonsági vagy egyéb működési okokból az adataihoz. További információ a [Cognitive Services tárolóról](cognitive-services-container-support.md "Cognitive Services-tárolók").

## <a name="regional-availability"></a>Regionális elérhetőség

A Cognitive Services API-jai a Microsoft által felügyelt adatközpontok egyre növekvő hálózatán futnak. Az egyes API-k regionális elérhetőségét az [Azure-régiók listájában](https://azure.microsoft.com/regions "Azure-régiók listája")találja.

Olyan régiót keresünk, amely még nem támogatott? Tudassa velünk, ha beküld egy szolgáltatási kérelmet a [UserVoice-fórumba](https://cognitive.uservoice.com/ "UserVoice-fórum").

## <a name="supported-cultural-languages"></a>Támogatott kulturális nyelvek

Cognitive Services számos kulturális nyelvet támogat a szolgáltatási szinten. Az egyes API-k nyelvi elérhetőségét a [támogatott nyelvek listájában](language-support.md "támogatott nyelvek listája")találja.

## <a name="certifications-and-compliance"></a>Minősítések és megfelelőség

Cognitive Services olyan minősítéseket kapott, mint például a CSA STAR minősítés, a FedRAMP mérsékelt és a HIPAA BAA. A saját auditokra és biztonsági felülvizsgálatokra vonatkozó tanúsítványokat is [letöltheti](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "letöltés") .

Az adatvédelem és az adatkezelés megismeréséhez nyissa meg a [megbízhatósági központot](https://servicetrust.microsoft.com/ "Trust Center").

## <a name="support"></a>Támogatás

A Cognitive Services számos támogatási lehetőséget kínál, amelyek segítenek az intelligens alkalmazások létrehozásában. A Cognitive Services a fejlesztők erős közösségét is felhasználhatják, akik segíthetnek az adott kérdések megválaszolásában. Az elérhető lehetőségek teljes listáját itt tekintheti meg: [Cognitive Services támogatási és Súgó beállításai](cognitive-services-support-options.md "Cognitive Services támogatás és Súgó lehetőségek").

## <a name="next-steps"></a>Következő lépések

* [Cognitive Services-fiók létrehozása](cognitive-services-apis-create-account.md "Cognitive Services-fiók létrehozása")
* [A Cognitive Services docs újdonságai](whats-new-docs.md "A Cognitive Services docs újdonságai")
* [A Cognitive Services költségeinek megtervezése és kezelése](plan-manage-costs.md)
