---
title: Helyszíni Azure Cognitive Services tárolók használata
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a Docker-tárolókat a Cognitive Services való használathoz.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 04/16/2021
ms.author: aahi
keywords: helyszíni, Docker, tároló, Kubernetes
ms.openlocfilehash: c40e91d81df448021be74af768bc9d5952b263dd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588224"
---
# <a name="azure-cognitive-services-containers"></a>Azure Cognitive Services-tárolók

Azure Cognitive Services [több Docker-tárolót](https://www.docker.com/what-container) is biztosít, amelyek lehetővé teszi az Azure-ban, a helyszínen elérhető API-k használatát. Ezeknek a tárolóknak a használatával rugalmasan közelebb Cognitive Services adatokhoz megfelelőségi, biztonsági vagy egyéb működési okokból. A tárolók támogatása jelenleg a tárolók egy részében Azure Cognitive Services.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

A tárolóba való elosztás egy olyan szoftverterjesztési megközelítés, amelyben egy alkalmazás vagy szolgáltatás , beleértve annak & konfigurációját is, tároló-rendszerképként van csomagolva. A tároló rendszerképe kevés módosítással vagy módosítás nélkül helyezhető üzembe egy tárológazdaszámítógépen. A tárolók el vannak különítve egymástól és a mögöttes operációs rendszertől, és kisebb a méretük, mint a virtuális gépeknek. A tárolók példányosodnak a tároló rendszerképeiből rövid távú feladatokhoz, és eltávolíthatók, ha már nincs rájuk szükség.

## <a name="features-and-benefits"></a>Funkciók és előnyök

- **Nem módosítható infrastruktúra:** Lehetővé teszi, hogy a DevOps-csapatok az ismert rendszerparaméterek konzisztens és megbízható készletét használva alkalmazkodjanak a változásokhoz. A tárolók rugalmasságot biztosítanak a kiszámítható ökoszisztémán belüli eltolódás és a konfigurációk eltérésének elkerülése érdekében.
- **Adatok szabályozása:** Itt adhatja meg, hogy a rendszer hol Cognitive Services. Ez elengedhetetlen lehet, ha nem tud adatokat küldeni a felhőbe, de hozzá kell férni a Cognitive Services API-k. A hibrid környezetek konzisztenciájának támogatása – az adatok, a felügyelet, az identitás és a biztonság terén.
- **Modellfrissítések szabályozása:** A megoldásokban üzembe helyezett modellek verziószámozásának és frissítésének rugalmassága.
- **Hordozható** architektúra: Lehetővé teszi hordozható alkalmazásarchitektúra létrehozását, amely üzembe helyezhető az Azure-ban, a helyszínen és a peremhálózaton. A tárolók üzembe helyezhetők közvetlenül a Azure Kubernetes Service [,](../aks/index.yml) [Azure Container Instances,](../container-instances/index.yml)vagy egy [kubernetes-fürtön,](https://kubernetes.io/) amely a [Azure Stack.](/azure-stack/operator) További információ: [Deploy Kubernetes to Azure Stack.](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)
- **Nagy átviteli sebesség/kis** késés: Lehetővé teszi az ügyfelek számára a nagy átviteli sebesség és az alacsony késés követelményeinek megfelelő skálázását azáltal, hogy lehetővé teszi a Cognitive Services számára, hogy fizikailag közel futtassanak az alkalmazáslogikához és az adataikhoz. A tárolók nem biztosítanak másodpercenkénti tranzakciókat (TPS), és a szükséges hardvererőforrások megszabadulása esetén fel- és felskálásos skálázhatóak a kereslet kezelésével.
- **Méretezhetőség:** A tárolók és tárolóvezénylési szoftverek, például a Kubernetes egyre népszerűbb népszerűségével; A skálázhatóság a technológiai fejlesztések élvonalában áll. A skálázható fürtalapra építve az alkalmazásfejlesztés gondoskodik a magas rendelkezésre állásról.

## <a name="containers-in-azure-cognitive-services"></a>Tárolók a Azure Cognitive Services

Azure Cognitive Services tárolók a következő Docker-tárolókat biztosítják, amelyek a szolgáltatások funkcióinak egy részhalmazát Azure Cognitive Services. Az utasításokat és a képek helyét az alábbi táblázatokban találja. A tároló [rendszerképeket tartalmazó](containers/container-image-tags.md) lista is elérhető.

### <a name="decision-containers"></a>Döntési tárolók

| Szolgáltatás |  Tároló | Description | Rendelkezésre állás |
|--|--|--|--|
| [Anomáliadetektor][ad-containers] | **anomáliadetektor** ([kép](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | A anomáliadetektor API segítségével gépi tanulással figyelheti és észlelheti az idősorozat-adatok rendellenességeit. | Általánosan elérhető |

### <a name="language-containers"></a>Nyelvtárolók

| Szolgáltatás |  Tároló | Description | Rendelkezésre állás |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([kép](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Betölt egy betanított vagy közzétett Language Understanding, más néven LUIS-alkalmazást egy Docker-tárolóba, és hozzáférést biztosít a tároló API-végpontjairól származó lekérdezés-előrejelzésekhez. Az alkalmazás előrejelzési pontosságának javítása érdekében [](https://www.luis.ai) lekérdezési naplókat gyűjthet a tárolóból, és feltöltheti őket a LUIS-portálra. | Általánosan elérhető |
| [Text Analytics][ta-containers-keyphrase] | **Kulcsszókeresés** ([kép](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Kinyeri a fő pontokat azonosító kulcskifejezéseket. Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”. | Előnézet |
| [Text Analytics][ta-containers-language] |  **Szöveg Nyelvfelismerés** ([kép](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | A legfeljebb 120 nyelv esetében a észleli, hogy a bemeneti szöveg melyik nyelven van megírva, és egyetlen nyelvi kódot jelent a kéréshez beküldött összes dokumentumhoz. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét. | Általánosan elérhető |
| [Text Analytics][ta-containers-sentiment] | **Hangulatelemzés v3** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Elemzi a nyers szöveget, és pozitív vagy negatív hangulatra vonatkozó adatokat keres. A hangulatelemzés ezen verziója hangulatcímkéket (például pozitív vagy *negatív)* ad vissza minden dokumentumhoz és mondathoz.  |  Általánosan elérhető |
| [Text Analytics][ta-containers-health] |  **Egészségügyi Text Analytics** | Egészségügyi információk kinyerása és címkézése strukturálatlan klinikai szövegből. | Kapus előzetes verzió. [Hozzáférés kérése:][request-access]. |

### <a name="speech-containers"></a>Speech-tárolók

> [!NOTE]
> A Speech-tárolók használatának igényléséhez ki kell kitöltötte az [online kérésűrlapját.](https://aka.ms/csgate)

| Szolgáltatás |  Tároló | Description | Rendelkezésre állás |
|--|--|--|
| [Speech Service API][sp-containers-stt] |  **Beszédfelismerés** ([kép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Folyamatos, valós idejű beszédet szöveggé alakít. | Általánosan elérhető |
| [Speech Service API][sp-containers-cstt] | **Custom Speech-to-Text** ([kép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | A folyamatos valós idejű beszédet szövegre írja át egy egyéni modell használatával. | Általánosan elérhető |
| [Speech Service API][sp-containers-tts] | **Szöveg-beszéd** ([kép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Az írott szöveget természetesnek hangzó beszéddé alakítja. | Általánosan elérhető |
| [Speech Service API][sp-containers-ctts] | **Egyéni szöveg-beszéd** ([kép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Természetes hangú beszédgé alakítja a szöveget egy egyéni modell használatával. | Kapus előzetes verzió |
| [Speech Service API][sp-containers-ntts] | **Neurális szövegfelismerés** ([kép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | A szöveget természetes hangú beszédgé alakítja mély neurális hálózati technológiával, így természetesebb szintetizált beszédet is lehetővé téve. | Általánosan elérhető |
| [Speech Service API][sp-containers-lid] | **Beszéd nyelvének észlelése** ([kép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Meghatározza a beszélt hang nyelvét. | Kapus előzetes verzió |

### <a name="vision-containers"></a>Vision-tárolók

> [!WARNING]
> 2020. június 11-én a Microsoft bejelentette, hogy nem értékesít arcfelismerő technológiát az Egyesült Államok rendőrségének egészen addig, amíg az emberi jogokra alapozott erős szabályozást nem vezetnek be. Ennek köszönhetően az ügyfelek nem használhatnak arcfelismerési funkciókat vagy az Azure-szolgáltatásokban foglalt funkciókat (például Face vagy Video Indexer, ha az ügyfél az ügyfél, vagy engedélyezi az ilyen szolgáltatások használatát a Egyesült Államok.

| Szolgáltatás |  Tároló | Description | Rendelkezésre állás |
|--|--|--|--|
| [Computer Vision][cv-containers] | **OcR olvasása** ([kép](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Az OCR-tároló olvasása lehetővé teszi nyomtatott és kézzel írt szövegek kinyerét JPEG-, PNG-, BMP-, PDF- és TIFF-fájlformátumokat támogató képekből és dokumentumokból. További információt a [Read API dokumentációjában talál.](./computer-vision/overview-ocr.md) | Kapus előzetes verzió. [Hozzáférés kérése:][request-access]. |
| [Térbeli elemzés][spa-containers] | **Térbeli elemzés** ([kép](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Valós idejű streamelési videót elemez, hogy megértse a személyek közötti térbeli kapcsolatokat, a mozgásukat és a fizikai környezetekben lévő objektumokkal való interakciókat. | Kapus előzetes verzió. [Hozzáférés kérése:][request-access]. |
| [Face][fa-containers] | **Face** | Észleli az emberi arcokat a képeken, és azonosítja az attribútumokat, beleértve az arcvonásokat (például az orrot és a tekintetet), a nemet, az életkort és a gép által előrejelezhető arcvonásokat. Az észlelés mellett a Face egy megbízhatósági pontszámmal ellenőrizheti, hogy egy képen vagy különböző képeken két arc azonos-e, vagy összehasonlítja az arcokat egy adatbázissal, hogy lássa, létezik-e már hasonló arc vagy azonos arc. Hasonló arcokat is képes csoportokba szervezni közös vizuális jellemzők használatával. | Nem érhető el |
| [Form Recognizer][fr-containers] | **Form Recognizer** | A Form Understanding gépi tanulási technológiával azonosítja és kinyeri a kulcs-érték párokat és táblákat az űrlapokról. | Nem érhető el | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Emellett egyes tárolók a többszolgáltatású Cognitive Services [is támogatottak.](cognitive-services-apis-create-account.md) Létrehozhat egy egyetlen Cognitive Services összes erőforrást, és használhatja ugyanazt a számlázási kulcsot a következő szolgáltatások támogatott szolgáltatásaiban:

* Computer Vision
* Face
* LUIS
* Text Analytics

## <a name="prerequisites"></a>Előfeltételek

A tárolók használata előtt teljesítenie kell az Azure Cognitive Services előfeltételt:

**Docker Engine:** A Docker Engine-nek helyileg telepítve kell lennie. A Docker olyan csomagokat biztosít, amelyek konfigurálják a Docker-környezetet [macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)és [Windows rendszeren.](https://docs.docker.com/docker-for-windows/) Windows rendszeren a Docker-t úgy kell konfigurálni, hogy támogassa a Linux-tárolókat. A Docker-tárolók közvetlenül is üzembe helyezhetők a Azure Kubernetes Service [vagy](../aks/index.yml) a [Azure Container Instances.](../container-instances/index.yml)

A Docker-t úgy kell konfigurálni, hogy engedélyezze a tárolók számára a kapcsolódást az Azure-hoz, és elküldjék a számlázási adatokat az Azure-nak.

Az Microsoft Container Registry és **a Docker** ismerete: Ismernie kell az Microsoft Container Registry és a Docker olyan alapfogalmait, mint a beállításregisztrálók, adattárak, tárolók és tárolók rendszerképei, valamint ismernie kell az alapszintű `docker` parancsokat.

A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).

Az egyes tárolókra is saját követelmények vonatkoznak, beleértve a kiszolgáló- és memóriafoglalási követelményeket is.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Fejlesztői minták

Fejlesztői minták a [GitHub-adattárunkban érhetők el.](https://github.com/Azure-Samples/cognitive-services-containers-samples)

## <a name="next-steps"></a>Következő lépések

Megismerheti [a tárolók](containers/container-reuse-recipe.md) receptjeit, amelyek a Cognitive Services.

Telepítse és fedezze fel a tárolók által biztosított funkciókat a Azure Cognitive Services:

* [anomáliadetektor tárolók][ad-containers]
* [Computer Vision tárolók][cv-containers]
* [Arctárolók][fa-containers]
* [Form Recognizer tárolók][fr-containers]
* [Language Understanding (LUIS) tárolók][lu-containers]
* [Speech Service API-tárolók][sp-containers]
* [Text Analytics tárolók][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://aka.ms/csgate
