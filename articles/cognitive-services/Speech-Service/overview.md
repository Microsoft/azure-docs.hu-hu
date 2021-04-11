---
title: Mi az a beszédszolgáltatás?
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás a beszéd-szöveg, a szöveg-beszéd és a beszéd fordításának egységesítése egyetlen Azure-előfizetésbe. Beszédet adhat hozzá alkalmazásaihoz, eszközeihez és eszközeihez a Speech SDK, a Speech Devices SDK vagy a REST API-k használatával.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/23/2020
ms.author: trbye
ms.openlocfilehash: ddb61a79e6af402b06fe8c18df8c62f9287437a3
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209714"
---
# <a name="what-is-the-speech-service"></a>Mi az a beszédszolgáltatás?

A beszédfelismerési szolgáltatás a beszéd-szöveg, a szöveg és a beszéd egyesítése, valamint a beszédfelismerés egyetlen Azure-előfizetésbe való fordítása. A Speech [parancssori](spx-overview.md)felülettel, a Speech [SDK](./speech-sdk.md)-val, a Speech [Device SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android)-val, a [Speech Studióval](https://speech.microsoft.com/)és a [REST API](#reference-docs)-kkal egyszerűen elvégezheti az alkalmazások, eszközök és eszközök használatát.

> [!IMPORTANT]
> A Speech szolgáltatás leváltotta Bing Speech API és Translator Speech. Az áttelepítési utasításokért tekintse meg az _áttelepítési_ szakaszt.

A következő funkciók a Speech Service részét képezik. A táblázatban található hivatkozásokat követve további információkat tudhat meg az egyes szolgáltatásokhoz tartozó általános használati esetekről, vagy böngészhet az API-referenciában.

| Szolgáltatás | Szolgáltatás | Leírás | SDK | REST |
|---------|---------|-------------|-----|------|
| [Beszéd – szöveg](speech-to-text.md) | Valós idejű beszéd – szöveg | A beszéd-szöveg szöveggé alakítja át vagy lefordítja a hangadatfolyamokat vagy helyi fájlokat valós időben, hogy az alkalmazások, eszközök vagy eszközök képesek legyenek a felhasználásra vagy a megjelenítésre. A beszéd-szöveg és a [Language Understanding (Luis)](../luis/index.yml) használatával származtatott beszédből származó felhasználói leképezéseket származtathat, és hangparancsokat alkalmazhat. | [Igen](./speech-sdk.md) | [Igen](#reference-docs) |
| | [Batch – beszéd – szöveg](batch-transcription.md) | A Batch-beszédfelismerés lehetővé teszi, hogy az Azure Blob Storageban tárolt nagy mennyiségű hangalapú beszéd-szöveg átírása aszinkron módon történjen. A beszédfelismerési hang szövegre való konvertálása mellett a Batch beszéd-szöveg is lehetővé teszi a diarization és a hangulat-elemzést. | No | [Igen](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [Több eszközre kiterjedő beszélgetés](multi-device-conversation.md) | Több eszköz vagy ügyfél csatlakoztatása egy beszélgetésben beszéd-vagy szöveges üzenetek küldéséhez egyszerű támogatással a transzkripció és a fordítás számára| Igen | Nem |
| | [Beszélgetés átirata](./conversation-transcription.md) | Lehetővé teszi a valós idejű beszédfelismerést, a hangszórók azonosítását és a diarization. A személyes értekezletek átírása tökéletes választás a hangszórók megkülönböztetésére. | Igen | Nem |
| | [Custom Speech modellek létrehozása](#customize-your-speech-experience) | Ha egy egyedi környezetben beszéd-szövegeket használ az elismeréshez és az átíráshoz, létrehozhat és betaníthat egyéni akusztikai, nyelvi és kiejtési modelleket a környezeti zajok vagy az iparági specifikus szókincsek kezeléséhez. | No | [Igen](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| [Szöveg – beszéd](text-to-speech.md) | Szövegfelolvasás | A szöveg és a beszéd szöveggé alakítja át a bemeneti szöveget az emberi környezetbe, például a [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md)használatával. Használjon neurális hangokat, amelyek a mély neurális hálózatok által működtetett, emberi jellegű hangok. Lásd: [nyelvi támogatás](language-support.md). | [Igen](./speech-sdk.md) | [Igen](#reference-docs) |
| | [Egyéni hangok létrehozása](#customize-your-speech-experience) | Egyedi hangbetűkészletek létrehozása a márka vagy a termék számára. | No | [Igen](#reference-docs) |
| [Speech Translation](speech-translation.md) | Beszédfordítás | A beszédfelismerés valós idejű, többnyelvű fordítást tesz lehetővé alkalmazásaihoz, eszközeihez és eszközeihez. Használja ezt a szolgáltatást beszéd – beszéd és beszéd – szöveg fordításához. | [Igen](./speech-sdk.md) | No |
| [Hangvezérelt asszisztensek](voice-assistants.md) | Hangvezérelt asszisztensek | A beszédfelismerési szolgáltatást használó hangsegédek lehetővé teszik a fejlesztők számára, hogy természetes, emberi jellegű társalgási felületet hozzanak létre alkalmazásaikban és tapasztalataikban. A hangsegéd szolgáltatás gyors, megbízható interakciót biztosít egy eszköz és egy asszisztens implementáció között, amely a bot Framework közvetlen vonalú beszédfelismerési csatornáját vagy az integrált egyéni parancsok szolgáltatást használja a feladatok befejezéséhez. | [Igen](voice-assistants.md) | No |
| [Speaker Recognition](speaker-recognition-overview.md) | A hangszórók ellenőrzése & azonosítása | A Speaker Recognition szolgáltatás olyan algoritmusokat biztosít, amelyek egyedi hangtulajdonságokkal rendelkeznek a hangszórók ellenőrzéséhez és azonosításához. Speaker Recognition a "ki beszél?" kérdésre válaszol. | Yes | [Igen](/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Próbálja ki ingyenesen a Beszédszolgáltatást

A következő lépésekhez egy Microsoft-fiók és egy Azure-fiókra van szükség. Ha nem rendelkezik Microsoft-fiókval, a [Microsoft-fiók portálon](https://account.microsoft.com/account)ingyenesen regisztrálhat. Válassza a **Bejelentkezés Microsoft-fiókkal** lehetőséget, majd amikor a rendszer kéri a bejelentkezést, válassza **a Microsoft-fiók létrehozása** lehetőséget. Az új Microsoft-fiók létrehozásához és ellenőrzéséhez kövesse az alábbi lépéseket.

Ha Microsoft-fiók, nyissa meg az [Azure regisztrációs oldalát](https://azure.microsoft.com/free/ai/), válassza az **ingyenes indítás** lehetőséget, és hozzon létre egy új Azure-fiókot egy Microsoft-fiók használatával. Az alábbi videóból [megtudhatja, hogyan regisztrálhat az ingyenes Azure-fiókra](https://www.youtube.com/watch?v=GWT2R1C_uUU).

> [!NOTE]
> Ha regisztrál egy ingyenes Azure-fiókra, az $200-as szolgáltatási Kredittel rendelkezik, amelyet a fizetős szolgáltatás előfizetéséhez is alkalmazhat, akár 30 napig érvényes. Az Azure-szolgáltatások le vannak tiltva, ha a kredit lejár, vagy a 30 nap végén lejár. Az Azure-szolgáltatások további használatához frissítenie kell a fiókját. További információ: az [ingyenes Azure-fiók frissítése](../../cost-management-billing/manage/upgrade-azure-subscription.md). 
>
> A beszédfelismerési szolgáltatásnak két szolgáltatási szintje van: ingyenes (F0) és előfizetés (S0), amelyek eltérő korlátozásokkal és előnyökkel rendelkeznek. Ha az ingyenes, kis mennyiségű Speech Service-szintet használja, akkor az ingyenes próbaverzió vagy a szolgáltatási jóváírás lejárta után is megtarthatja ezt az ingyenes előfizetést. További információ: [Cognitive Services díjszabása-Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-azure-resource"></a>Az Azure-erőforrás létrehozása

A Speech Service-erőforrások (ingyenes vagy fizetős szintek) hozzáadása az Azure-fiókhoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a Microsoft-fiókjával.

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső részén. Ha nem látja az **erőforrás létrehozása** lehetőséget, a képernyő bal felső sarkában található összecsukott menü kiválasztásával bármikor megkeresheti.

1. Az **új** ablakban írja be a "Speech" kifejezést a keresőmezőbe, majd nyomja le az ENTER billentyűt.

1. A keresési eredmények között válassza a **Speech (beszéd**) lehetőséget.

   ![beszédfelismerési találatok](media/index/speech-search.png)

1. Válassza a **Létrehozás** lehetőséget, majd:

   - Adjon egyedi nevet az új erőforrásnak. A név segít különbséget tenni az ugyanahhoz a szolgáltatáshoz kötött több előfizetés között.
   - Válassza ki azt az Azure-előfizetést, amelyhez az új erőforrás társítva van, hogy megtudja, hogyan történik a díjak számlázása. Itt látható az Azure- [előfizetések](../../cost-management-billing/manage/create-subscription.md#create-a-subscription-in-the-azure-portal) Azure Portalban való létrehozásának bevezetője.
   - Válassza ki azt a [régiót](regions.md) , ahol az erőforrást használni szeretné. Az Azure egy globális felhőalapú platform, amely világszerte számos régióban általánosan elérhető. A legjobb teljesítmény eléréséhez válasszon egy Önhöz legközelebb eső régiót, vagy az alkalmazás futását. A beszédfelismerési szolgáltatás elérhetősége különböző régiókban változhat. Győződjön meg arról, hogy az erőforrást egy támogatott régióban hozza létre. Lásd: [régió támogatása a Speech Services számára](./regions.md#speech-to-text-text-to-speech-and-translation).
   - Válassza az ingyenes (F0) vagy a fizetős (S0) árképzési szintet. Az egyes csomagokra vonatkozó díjszabási és használati kvótákkal kapcsolatos teljes információkért válassza a **teljes díjszabás megtekintése** vagy a [Speech Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)című témakört. Az erőforrásokra vonatkozó korlátokat az [Azure Cognitive Services korlátai](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits)című témakörben tekintheti meg.
   - Hozzon létre egy új erőforráscsoportot ehhez a beszédfelismerési előfizetéshez, vagy rendelje hozzá az előfizetést egy meglévő erőforráscsoporthoz. Az erőforráscsoportok segítségével megőrizheti különböző Azure-előfizetéseit.
   - Válassza a **Létrehozás** lehetőséget. Ezzel áttekintheti a központi telepítés áttekintését és megjeleníti az üzembe helyezési folyamat üzeneteit.  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
Az új beszédfelismerési erőforrás üzembe helyezése néhány percet vesz igénybe. 

### <a name="find-keys-and-region"></a>Kulcsok és régió keresése

A befejezett üzemelő példány kulcsai és régiójának megkereséséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a Microsoft-fiókjával.

2. Válassza a **minden erőforrás** lehetőséget, majd válassza ki a Cognitive Services erőforrás nevét.

3. A bal oldali ablaktábla erőforrás- **kezelés** területén válassza a **kulcsok és végpont** lehetőséget.

Minden előfizetés két kulccsal rendelkezik; az alkalmazásban bármelyik kulcsot használhatja. Ha egy kulcsot a Kódszerkesztő vagy más hely számára szeretne másolni/beilleszteni, válassza az egyes kulcsok melletti másolás gombot, kapcsolja be a Windowst, hogy beillessze a vágólap tartalmát a kívánt helyre.

Emellett másolja az `LOCATION` értéket, amely a régió azonosítója (pl. `westus`, `westeurope` ) SDK-hívásokhoz.

> [!IMPORTANT]
> Ezek az előfizetési kulcsok hozzáférnek a kognitív szolgáltatás API-hoz. Ne ossza meg a kulcsokat. Biztonságos tárolás – például Azure Key Vault használatával. Javasoljuk továbbá, hogy rendszeresen újragenerálja ezeket a kulcsokat. API-hívások létrehozásához csak egy kulcs szükséges. Az első kulcs újragenerálásakor a második kulcsot használhatja a szolgáltatás folyamatos eléréséhez.

## <a name="complete-a-quickstart"></a>Bevezetés befejezése

A legnépszerűbb programozási nyelveken olyan gyors útmutatókat találunk, amelyek az alapszintű tervezési mintákat tanítják, és kevesebb, mint 10 perc alatt futtatják a kódot. Az egyes szolgáltatásokra vonatkozó rövid útmutató az alábbi listában található.

* [Diktálás – gyorsútmutató](get-started-speech-to-text.md)
* [Szövegfelolvasás – gyorsútmutató](get-started-text-to-speech.md)
* [Beszédfordítási gyorsútmutató](./get-started-speech-translation.md)
* [Szándékfelismerés gyorsútmutatója](./get-started-intent-recognition.md)
* [A hangszórók felismerésének rövid útmutatója](./get-started-speaker-recognition.md)

A beszédfelismerési szolgáltatás használatának megkezdése után próbálja ki az oktatóanyagokat, amelyek bemutatják, Hogyan oldhatók meg a különböző forgatókönyvek.

- [Oktatóanyag: beszédfelismerési szándékok felismerése a Speech SDK és a LUIS, C használatával #](how-to-recognize-intents-from-speech-csharp.md)
- [Oktatóanyag: hang engedélyezése a robotnak a Speech SDK-val, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Oktatóanyag: a szöveg fordítását, a hangulat elemzését és a fordítás szövegét beszédre, REST-re alakító lombik-alkalmazás létrehozása](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json%252c%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json%252c%2fen-us%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json)

## <a name="get-sample-code"></a>Mintakód letöltése

Mintakód elérhető a GitHubon a Speech Service-hez. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata. Az alábbi hivatkozásokkal megtekintheti az SDK-t és a REST-mintákat:

- [Beszéd – szöveg, szöveg-beszéd és beszéd fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Szöveg – beszéd minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Hangsegéd-minták (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>A beszédfelismerési élmény testreszabása

A beszédfelismerési szolgáltatás a beépített modellekkel jól működik, azonban érdemes lehet tovább testreszabni és hangolni a termék vagy a környezet élményét. A testreszabási lehetőségek köre az akusztikus modell finomhangolása és a márka egyedi hangbetűkészletei között.

Más termékek olyan beszédfelismerési modelleket ajánlanak, mint például az egészségügyi ellátás vagy a biztosítás, de mindenki számára elérhetők. Az Azure Speech-ben való Testreszabás az *egyedülálló* versenyelőny részévé válik, amely bármely más felhasználó vagy ügyfél számára nem érhető el. Ez azt jelenti, hogy a modelleket saját és egyéni használatra beállították a használati esetekhez.

| Beszédszolgáltatás | Platform | Description |
| -------------- | -------- | ----------- |
| Speech-to-Text | [Custom Speech](https://aka.ms/customspeech) | Testreszabhatja a beszédfelismerési modelleket igényeihez és rendelkezésre álló adataihoz. Leküzdheti a beszédfelismerési akadályokat, például a beszéd stílusát, a szókincset és a háttérzajt. |
| Text-to-Speech | [Egyéni hang](https://aka.ms/customvoice) | Létrehozhat egy felismerhető és egyedülálló hangot szövegfelolvasási alkalmazásaihoz az elérhető beszédadatokkal. A hangkimeneteket tovább finomíthatja a hangparaméterek készletének módosításával. |

## <a name="deploy-on-premises-using-docker-containers"></a>Helyszíni üzembe helyezés Docker-tárolók használatával

A [Speech Service-tárolók használatával](speech-container-howto.md) üzembe helyezhet API-szolgáltatásokat a helyszínen. Ezek a Docker-tárolók lehetővé teszik, hogy a szolgáltatás a megfelelőségi, biztonsági vagy egyéb működési okokból közelebb kerüljön az adataihoz. A beszédfelismerési szolgáltatás a következő tárolókat kínálja:

* Szabványos beszéd – szöveg
* Custom Speech – szöveg
* Normál szöveg – beszéd
* Neurális szöveg – beszéd
* Egyéni szöveg-beszéd (előzetes verzió)
* Speech Nyelvfelismerés (előzetes verzió)

## <a name="reference-docs"></a>Dokumentációs dokumentumok

- [Beszéd SDK](./speech-sdk.md)
- [Beszédeszközök SDK](speech-devices-sdk.md)
- [REST API: beszéd – szöveg](rest-speech-to-text.md)
- [REST API: szövegről beszédre](rest-text-to-speech.md)
- [REST API: kötegelt átírás és testreszabás](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a beszédfelismerési szöveggel](./get-started-speech-to-text.md) 
>  [Ismerkedés a szöveg és a beszéd](get-started-text-to-speech.md) között