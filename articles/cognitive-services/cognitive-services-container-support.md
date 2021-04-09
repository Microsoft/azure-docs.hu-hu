---
title: Helyszíni Azure Cognitive Services-tárolók használata
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a Docker-tárolókat Cognitive Services helyszíni használatra.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 12/16/2020
ms.author: aahi
keywords: helyszíni, Docker, tároló, Kubernetes
ms.openlocfilehash: f5caac0a0da3595075dddd5a273a6883a0cca468
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97861836"
---
# <a name="azure-cognitive-services-containers"></a>Azure Cognitive Services-tárolók

Az Azure Cognitive Services több [Docker-tárolót](https://www.docker.com/what-container) biztosít, amelyek lehetővé teszik az Azure-ban, a helyszínen elérhető API-k használatát. Ezen tárolók használatával rugalmasan Cognitive Services a megfelelőségi, biztonsági vagy egyéb működési okokból az adataihoz. A tárolók támogatása jelenleg az Azure Cognitive Services egy részhalmaza számára érhető el.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

A tárolókra bontás olyan szoftverterjesztési módszer, amelyben egy alkalmazás vagy szolgáltatás, beleértve annak függőségeit & konfigurációját, a rendszer egy tároló képként együtt csomagolja. Kevés vagy nem módosítható, ha egy tároló-lemezképet telepítenek egy tároló gazdagépre. A tárolók el vannak különítve egymástól és a mögöttes operációs rendszertől, kisebb helyigénysel, mint a virtuális gépek. A tárolók lemezképeit rövid távú feladatokhoz is készíthetik, és ha már nincs szükség rájuk, el lesznek távolítva.

## <a name="features-and-benefits"></a>Funkciók és előnyök

- Nem módosítható **infrastruktúra**: a DevOps csapatok lehetővé teszik az ismert rendszerparaméterek egységes és megbízható készletének kihasználása, miközben a változáshoz alkalmazkodni tudnak. A tárolók rugalmasságot biztosítanak a kiszámítható ökoszisztémán belül, és elkerülheti a konfigurációs eltolódást.
- **Vezérlés az adat felett**: válassza ki, hogy a Cognitive Services hogyan dolgozza fel az adatait. Ez elengedhetetlen lehet, ha nem tud az adatküldés a felhőbe, de hozzá kell férnie Cognitive Services API-khoz. A hibrid környezetek – az adatok, a felügyelet, az identitás és a biztonság – közötti konzisztencia támogatása.
- A **modell frissítéseinek szabályozása**: rugalmasság a verziószámozásban és a megoldásokban üzembe helyezett modellek frissítésekor.
- **Hordozható architektúra**: lehetővé teszi egy olyan hordozható alkalmazás-architektúra létrehozását, amely az Azure-on, a helyszínen és a peremhálózati üzembe helyezhető. A tárolók üzembe helyezhetők közvetlenül az [Azure Kubernetes szolgáltatásban](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)vagy egy [Azure stack](/azure-stack/operator)üzembe helyezett [Kubernetes](https://kubernetes.io/) -fürtön. További információ: [a Kubernetes telepítése Azure stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Magas átviteli sebesség/kis késés**: az ügyfelek számára lehetővé teszi a nagy átviteli sebesség és az alacsony késési követelmények méretezését azáltal, hogy a Cognitive Services fizikailag közel van az alkalmazás-logikához és az adatokhoz. A tárolók másodpercenként nem kaphatnak tranzakciót (TPS), és az igény szerint méretezhetők, ha megadja a szükséges hardveres erőforrásokat.
- **Méretezhetőség**: a tárolókra bontás és a tároló-összehangoló szoftverek egyre növekvő népszerűsége, például a Kubernetes; a skálázhatóság a technológiai fejlesztések élvonalában van. A skálázható fürt alapjainak kiépítésekor az alkalmazások fejlesztése magas rendelkezésre állást biztosít.

## <a name="containers-in-azure-cognitive-services"></a>Tárolók az Azure-ban Cognitive Services

Az Azure Cognitive Services tárolók a következő Docker-tárolókat biztosítják, amelyek mindegyike az Cognitive Services Azure-beli szolgáltatásokból származó funkciók egy részét tartalmazza. Az alábbi táblázatokban megtalálhatja az utasításokat és a képek helyét. A [tároló lemezképek](containers/container-image-tags.md) listája is elérhető.

### <a name="decision-containers"></a>Döntési tárolók

| Szolgáltatás |  Tároló | Leírás | Rendelkezésre állás |
|--|--|--|--|
| [Anomália detektor][ad-containers] | **Anomália detektor** ([rendszerkép](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | A rendellenesség-Kiderítő API lehetővé teszi, hogy az idősoros adataiban a gépi tanulással figyelje és azonosítsa a rendellenességeket. | Általánosan elérhető |

### <a name="language-containers"></a>Nyelvtárolók

| Szolgáltatás |  Tároló | Leírás | Rendelkezésre állás |
|--|--|--|--|
| [LUIS][lu-containers] |  **Luis** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Betölti a betanított vagy közzétett Language Understanding modellt, más néven LUIS alkalmazást egy Docker-tárolóba, és hozzáférést biztosít a tároló API-végpontjai lekérdezési előrejelzéséhez. Lekérdezési naplókat gyűjthet a tárolóból, és feltöltheti ezeket a [Luis-portálra](https://www.luis.ai) az alkalmazás előrejelzési pontosságának javítása érdekében. | Általánosan elérhető |
| [Text Analytics][ta-containers-keyphrase] | **Kulcsszókeresés** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | A fő pontok azonosítására szolgáló legfontosabb kifejezések kibontása. Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”. | Előnézet |
| [Text Analytics][ta-containers-language] |  **Szöveg nyelvfelismerés** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | A legfeljebb 120 nyelv esetében azt észleli, hogy a bemeneti szöveg milyen nyelven íródott, és egyetlen nyelvi kódot jelentsen a kérelemben elküldött összes dokumentumhoz. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét. | Előnézet |
| [Text Analytics][ta-containers-sentiment] | **Hangulatelemzés v3** ([rendszerkép](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | A nyers szöveget a pozitív vagy negatív hangulatú nyomokra elemzi. Az érzelmek elemzése ebben a verzióban az egyes dokumentumokhoz és mondatokhoz tartozó hangulati címkéket (például *pozitív* vagy *negatív*) adja vissza. |  Általánosan elérhető |
| [Text Analytics][ta-containers-health] |  **Egészségügyi Text Analytics** | Orvosi adatok kinyerése és címkézése strukturálatlan klinikai szövegből. | Megtekintett előzetes verzió. [Hozzáférés kérése][request-access]. |

### <a name="speech-containers"></a>Speech-tárolók

> [!NOTE]
> A beszédfelismerési tárolók használatához el kell végeznie egy [online kérelem űrlapját](https://aka.ms/csgate).

| Szolgáltatás |  Tároló | Leírás | Rendelkezésre állás |
|--|--|--|
| [Speech Service API][sp-containers-stt] |  **Beszéd – szöveg** ([rendszerkép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Folyamatos, valós idejű beszédet szöveggé alakít. | Általánosan elérhető |
| [Speech Service API][sp-containers-cstt] | **Custom Speech – szöveg** ([rendszerkép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | A folyamatos valós idejű beszédet szövegbe helyezi egy egyéni modell használatával. | Általánosan elérhető |
| [Speech Service API][sp-containers-tts] | **Szöveg – beszéd** ([rendszerkép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Az írott szöveget természetesnek hangzó beszéddé alakítja. | Általánosan elérhető |
| [Speech Service API][sp-containers-ctts] | **Egyéni szöveg-beszéd** ([rendszerkép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Átalakítja a szöveget természetes hangú beszédre egyéni modell használatával. | Megtekinthető előzetes verzió |
| [Speech Service API][sp-containers-ntts] | **Neurális szöveg-beszéd** ([rendszerkép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | A mély neurális hálózati technológiával természetes hangú beszédre alakítja át a szöveget, ami lehetővé teszi a természetes szintetizált beszédek használatát. | Általánosan elérhető |
| [Speech Service API][sp-containers-lid] | **Beszédfelismerés nyelvének észlelése** ([rendszerkép](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Meghatározza a beszélt hang nyelvét. | Megtekinthető előzetes verzió |

### <a name="vision-containers"></a>Vision-tárolók

> [!WARNING]
> 2020. június 11-én a Microsoft bejelentette, hogy nem értékesít arcfelismerő technológiát az Egyesült Államok rendőrségének egészen addig, amíg az emberi jogokra alapozott erős szabályozást nem vezetnek be. Ilyen esetben előfordulhat, hogy az ügyfelek nem használhatják az Azure-szolgáltatások (például a Face vagy a Video Indexer) arc-felismerési funkcióit és funkcióit, ha az ügyfél vagy a által a Egyesült Államok egy rendőrségi részlege által vagy a szolgáltatásban való használatát engedélyezte.

| Szolgáltatás |  Tároló | Leírás | Rendelkezésre állás |
|--|--|--|--|
| [Computer Vision][cv-containers] | **OCR olvasása** ([rendszerkép](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Az olvasási OCR-tároló lehetővé teszi a nyomtatott és a kézírásos szöveg kinyerését képekből és dokumentumokból JPEG-, PNG-, BMP-, PDF-és TIFF-fájlformátumok támogatásával. További információ az API-k [olvasása dokumentációban](./computer-vision/concept-recognizing-text.md)található. | Megtekintett előzetes verzió. [Hozzáférés kérése][request-access]. |
| [Térbeli elemzés][spa-containers] | **Térbeli elemzés** ([rendszerkép](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | A valós idejű adatfolyam-továbbítási videót elemzi a személyek közötti térbeli kapcsolatok, a mozgásuk és a fizikai környezetekben található objektumok közötti interakciók megismerése érdekében. | Megtekintett előzetes verzió. [Hozzáférés kérése][request-access]. |
| [Face][fa-containers] | **Face** | Észleli az emberi arcokat a képekben, és azonosítja az attribútumokat, beleértve az arc-tereptárgyak (például az orr és a szemek), a nemek, az életkor és más, a géppel előre jelzett arc-funkciókat. Az észlelésen kívül a Face utasítással megtekintheti, hogy két arc vagy különböző kép azonos-e a megbízhatósági pontszám használatával, vagy összehasonlítja az arcokat egy adatbázissal, hogy megtudja, hasonló vagy azonos arc létezik-e. A hasonló arcok csoportokba rendezésére is képes, közös vizualizációs jellemzőkkel. | Nem érhető el |
| [Űrlap-felismerő][fr-containers] | **Form Recognizer** | Az űrlap ismerete a gépi tanulási technológiákat alkalmazza a kulcs-érték párok és táblák űrlapokból való azonosításához és kinyeréséhez. | Nem érhető el | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Emellett egyes tárolók a Cognitive Services [több szolgáltatásból álló erőforrás](cognitive-services-apis-create-account.md) -ajánlatban is támogatottak. Egyetlen Cognitive Services is létrehozhat egy teljes körű erőforrást, és ugyanazt a számlázási kulcsot használhatja a támogatott szolgáltatások között a következő szolgáltatásokhoz:

* Computer Vision
* Face
* LUIS
* Text Analytics

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cognitive Services-tárolók használata előtt meg kell felelnie a következő előfeltételeknek:

**Docker-motor**: a Docker-motort helyileg kell telepíteni. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)és [Windows](https://docs.docker.com/docker-for-windows/)rendszeren. Windows rendszeren a Docker-t úgy kell konfigurálni, hogy támogassa a Linux-tárolókat. A Docker-tárolók közvetlenül az [Azure Kubernetes szolgáltatásba](../aks/index.yml) vagy [Azure Container Instancesba](../container-instances/index.yml)is telepíthetők.

A Docker-t úgy kell konfigurálni, hogy lehetővé tegye a tárolók számára az Azure-ba való kapcsolódást és a számlázási információk küldését.

A **microsoft Container Registry és a Docker ismerete**: alapvető ismeretekkel kell rendelkeznie a Microsoft Container Registry és a Docker-fogalmak, például a nyilvántartók, a Tárházak, a tárolók és a tárolók rendszerképeiről, valamint az alapszintű parancsok megismeréséről `docker` .

A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).

Az egyes tárolók saját követelményeiket is magukban foglalhatják, beleértve a kiszolgáló és a memória foglalási követelményeit is.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Fejlesztői minták

A fejlesztői minták a GitHub- [tárházban](https://github.com/Azure-Samples/cognitive-services-containers-samples)érhetők el.

## <a name="next-steps"></a>Következő lépések

A Cognitive Services használható [tároló-receptek](containers/container-reuse-recipe.md) megismerése.

Telepítse és vizsgálja meg a tárolók által biztosított funkciókat az Azure Cognitive Servicesban:

* [Anomáliák detektor tárolói][ad-containers]
* [Computer Vision tárolók][cv-containers]
* [Face-tárolók][fa-containers]
* [Űrlap-felismerő tárolók][fr-containers]
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
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u