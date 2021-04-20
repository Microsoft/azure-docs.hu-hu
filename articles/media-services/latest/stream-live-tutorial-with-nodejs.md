---
title: Élő streamelés Media Services 3-as Node.js
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan streamelhet élő Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730542"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>Oktatóanyag: Élő streamelés Media Services Node.js TypeScript használatával

> [!NOTE]
> Annak ellenére, hogy az oktatóanyag Node.js példákat használ, az általános lépések ugyanazok a REST API, [a](/rest/api/media/liveevents) [CLI](/cli/azure/ams/live-event)vagy más támogatott [ADK-k esetén.](media-services-apis-overview.md#sdks) 

A Azure Media Services élő [események az](/rest/api/media/liveevents) élő streamelési tartalmak feldolgozásáért felelősek. Az élő események bemeneti végpontot (bemeneti URL-címet) biztosítanak, majd meg kell adniuk az élő kódolónak. Az élő esemény élő bemeneti streameket fogad az élő kódolótól, és elérhetővé teszi egy vagy több streamvégponton [keresztüli streameléshez.](/rest/api/media/streamingendpoints) Az Élő események egy előzetes verziójú végpontot (előnézeti URL-címet) is biztosítanak, amely a stream előnézetének megtekintésére és érvényesítésére használható a további feldolgozás és teljesítés előtt. Ez az oktatóanyag bemutatja, hogyan  használható Node.js egy élő esemény átmenő típusának létrehozására és élő stream közvetítésére az [OBS Studio használatával.](https://obsproject.com/download)

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Töltse le a témakörben leírt mintakódot.
> * Vizsgálja meg az élő streamelést konfiguráló és azt végző kódot.
> * Tekintse meg az eseményt a [következő Azure Media Player:](https://amp.azure.net/libs/amp/latest/docs/index.html) [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Az erőforrások eltávolítása.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének feltételei a következők:

- A [Node.js](https://nodejs.org/en/download/)
- A [TypeScript telepítése](https://www.typescriptlang.org/)
- [Hozzon létre egy Media Services fiókot.](./create-account-howto.md)<br/>Ne felejtse el megjegyezni az erőforráscsoport nevéhez és a fióknévhez Media Services használt értékeket.
- Kövesse az [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Api elérése az Azure CLI használatával) lépéseit, és mentse a hitelesítő adatokat. Ezeket kell használnia az API eléréséhez és a környezeti változók fájljának konfigurálához.
- Először a [Konfigurálás és csatlakozás ](./configure-connect-nodejs-howto.md) a Node.jssegítségével lépésekkel végigveheti, hogyan használhatja a Node.js ügyféloldali SDK-t
- Telepítse Visual Studio Code vagy Visual Studio.
- [Az Visual Studio Code-környezet beállítása](https://code.visualstudio.com/Docs/languages/typescript) a TypeScript nyelv támogatásához.

## <a name="additional-settings-for-live-streaming-software"></a>További beállítások az élő streamelési szoftverekhez

- Kamera vagy eszköz (például laptop), amely egy esemény közvetítésére használható.
- Egy helyszíni szoftverkódoló, amely kódolja a kamerastreamet, és elküldi azt az Media Services live streaming szolgáltatásnak az RTMP protokoll használatával. Tekintse meg az ajánlott helyszíni [élő kódolókat.](encode-recommended-on-premises-live-encoders.md) A streamnek **RTMP** vagy **Smooth Streaming** formátumúnak kell lennie.  
- Ehhez a mintához javasoljuk, hogy kezdjen egy szoftverkódolóval, például az ingyenes [Open Broadcast Software OBS](https://obsproject.com/download) Studióban, hogy az első lépések egyszerűbbek legyen.

Ez a minta feltételezi, hogy az OBS Studiót fogja használni az RTMP a bemenő végpontra való szórásához. Először telepítse az OBS Studiót.
Használja a következő kódolási beállításokat az OBS Studióban:

- Kódoló: NVIDIA NVENC (ha elérhető) vagy x264
- Sebességvezérlés: CBR
- Bitrate: 2500 Kbps (vagy valami ésszerű a laptopja számára)
- Kulcskeret időköze: 2 másodperc, vagy 1 másodperc kis késés esetén  
- Előzetes beállítás: Kis késésű minőség vagy teljesítmény (NVENC) vagy "nagyon nagy" érték x264 használatával
- Profil: magas
- GPU: 0 (automatikus)
- B képkockák maximális száma: 2

> [!TIP]
> Mielőtt folytatná, mindenképp tekintse át [a Media Services 3-as verziójával megvalósított élő streamelést](stream-live-streaming-concept.md) bemutató cikket.

## <a name="download-and-configure-the-sample"></a>A minta letöltése és konfigurálása

Klónozza a következő Git Hub-adattárat, amely tartalmazza az élő streamelési Node.js mintát a gépre a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Az elő streamelési minta az [Élő](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live) mappában található.

Az [AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) mappában másolja a "sample.env" nevű fájlt egy új, ".env" nevű fájlba az [Access Azure Media Services API](./access-api-howto.md)with the Azure CLI (Hozzáférés az Azure CLI-vel) cikkben összegyűjtött környezeti változók beállításainak tárolására.
Győződjön meg arról, hogy a fájl tartalmazza az .env fájl előtt található "pont" (.) kódot, hogy megfelelően működjön a kódmintával.

Az [.env](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) fájl tartalmazza az AAD-alkalmazás kulcsát és titkos kulcsát, valamint a fiók nevét és az előfizetési adatokat, amelyek az SDK-hozzáférés hitelesítéséhez szükségesek az Media Services fiókhoz. A .gitignore fájl már konfigurálva van, hogy megakadályozza a fájl közzétételét az eltárolt adattárban. Ne engedje, hogy ezek a hitelesítő adatok kiszivárogtak legyenek, mivel ezek a fiók fontos titkos adatai.

> [!IMPORTANT]
> Ez a minta minden erőforráshoz egyedi utótagot használ. Ha megszakítja a hibakeresést, vagy le fogja mondani az alkalmazást anélkül, hogy végigfuttatja, akkor a fiókjában több élő esemény is lesz. <br/>Mindenképpen állítsa le a futó élő eseményeket. Ellenkező esetben a **számlázva lesz!** Futtassa a programot az erőforrások automatikus tisztítása érdekében a befejezésig. Ha a program összeomlik, vagy véletlenül leállítja a hibakeresőt, és megszakítja a program végrehajtását, ellenőrizze a portálon, hogy nem hagyott-e élő eseményeket a Futó vagy Készenléti módban, amelyek nem kívánt számlázási díjakat eredményeznek.

## <a name="examine-the-typescript-code-for-live-streaming"></a>A TypeScript-kód vizsgálata élő streameléshez

Ez a szakasz az élő projekt [index.ts fájljában](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) meghatározott *függvényeket vizsgálja* meg.

A minta minden erőforráshoz létrehoz egy egyedi utótagot, így nem ütközik a névütközés, ha többször futtatja a mintát a tisztítás nélkül.

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>A Media Services SDK használatának Node.js TypeScripttel

A Media Services API-k Node.js az SDK-modult az [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) npm csomagkezelővel kell hozzáadnia

```bash
npm install @azure/arm-mediaservices
```

A package.jskonfigurálva van, ezért csak az *npm install* futtatásával kell betöltenie a modulokat és a függőségeket.

1. Nyisson **meg egy parancssort,** és tallózással keresse meg a minta könyvtárát.
1. Váltsa át a könyvtárat az AMSv3Samples mappára.

    ```bash
    cd AMSv3Samples
    ```

1. Telepítse a fájlbanpackages.js *csomagokat.*

    ```bash
    npm install 
    ```

1. Indítsa Visual Studio kódot az *AMSv3Samples mappából.* (Ez ahhoz szükséges, hogy a fájlt abban a mappában indítsa el, ahol a *.vscode* mappa *éstsconfig.jsfájlokon* található.)

    ```bash
    cd ..
    code .
    ```

Nyissa meg a *Live (Élő)* mappát, és nyissa meg az *index.ts* fájlt a Visual Studio kódszerkesztőben.
Az *index.ts fájlban* nyomja le az F5 billentyűt a hibakereső elindításához.

### <a name="create-the-media-services-client"></a>A Media Services létrehozása

A következő kódrészlet bemutatja, hogyan hozható létre Media Services ügyfél a Node.js.
Figyelje meg, hogy ebben a kódban először az AzureMediaServicesOptions **longRunningOperationRetryTimeout** tulajdonságát 2 másodpercre juk meg, hogy csökkentsük az Azure Resource Management-végponton hosszú ideig futó művelet állapotának lekérdezéséhez szükséges időt.  Mivel az élő eseményeken végzett műveletek nagy része aszinkron lesz, és némi időt is igénybe vehet, érdemes csökkentenie ezt a lekérdezési időközt az SDK-ban az alapértelmezett 30 másodperces értékről, hogy felgyorsítsa az olyan nagyobb műveletek végrehajtásához szükséges időt, mint az élő események létrehozása, az indítás és a leállítás, amelyek mind aszinkron hívások. A legtöbb esetben a két másodperc az ajánlott érték.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Élő esemény létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre átmenő típusú élő eseményt **(a** LiveEventEncodingType none (Nincs) beállítással). További információ az élő események egyéb elérhető típusairól: [Élő események típusai.](live-event-outputs-concept.md#live-event-types) Az átmenő 720P vagy 1080P adaptív bites felhőkódolás élő átkódolása mellett élő átkódolási eseményt is használhat.
 
Néhány dolog, amit érdemes lehet megadni az élő esemény létrehozásakor:

* Az élő esemény (jelenleg az RTMP(S) és a Smooth Streaming protokollok) bemenő protokollja támogatott.<br/>A protokoll beállítását nem módosíthatja, amíg az élő esemény vagy a hozzá tartozó élő kimenetek futnak. Ha különböző protokollokra van szüksége, hozzon létre külön élő eseményt minden streamelési protokollhoz.  
* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja azokat az IP-címeket, amelyek számára engedélyezett a videók élő eseménybe való behívása. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.<br/>Ha nem ad meg IP-címet, és nincs szabálydefiníció, akkor az IP-cím nem engedélyezett. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címeknek a következő formátumok egyikében kell lennie: IpV4-cím négy számmal vagy CIDR-címtartománysal.
* Az esemény létrehozásakor megadhatja, hogy automatikusan elindul-e. <br/>Ha az automatikus indítás true (igaz) értékre van állítva, az élő esemény a létrehozás után elindul. Ez azt jelenti, hogy a számlázás azonnal elindul, amint az élő esemény elindul. A további számlázás leállításához explicit módon meg kell hívnia a Stop on the Live Event erőforrást. További információ: [Élő események állama és számlázása.](live-event-states-billing-concept.md)
Vannak készenléti módok is, amelyek az élő eseményt egy alacsonyabb "lefoglalt" költségű állapotban indítják el, így gyorsabban lehet "Futó" állapotba lépni. Ez olyan helyzetekben lehet hasznos, mint például a gyors készletek, amelyekben gyorsan kell kiadatani a csatornákat a streamelőknek.
* Ahhoz, hogy egy bemenő URL-cím prediktív és könnyebben karbantartható legyen egy hardveralapú élő kódolóban, állítsa a "useStaticHostname" tulajdonságot true (igaz) értékre, valamint használjon egyéni egyedi GUID azonosítót az "accessToken" (hozzáférési jogtár) tulajdonságban. Részletes információkért lásd: [Élő eseménybetöltési URL-címek.](live-event-outputs-concept.md#live-event-ingest-urls)

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Adattár létrehozása az élő esemény rögzítéséhez és archiválásához

Ebben a kódblokkban létre fog hozni egy üres adattárat, amely "szalagként" használható az élő eseményarchívum rögzítéséhez.
Ezeknek a fogalmaknak a megismerésekor a legjobb úgy gondolni az "Eszköz" objektumra, mint a szalagra, amit a régi időkben egy videoszalagrögzítőbe illesztett. Az "Élő kimenet" a szalagos rögzítőgép. Az "Élő esemény" csak a gép háta felé érkező videójel.

Ne feledje, hogy az asset (eszköz) vagy a "szalag" bármikor létre lehet hozható. Ez csupán egy üres "objektum", amit az élő kimenet objektumnak, a hasonlóságnak megfelelően a szalagos rögzítőnek kell átdüldenie.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>Az élő kimenet létrehozása

Ebben a szakaszban egy élő kimenetet hozunk létre, amely az Adateszköz nevét használja bemenetként annak megszava, hogy hova kell rögzíteni az élő eseményt. Emellett beállítjuk a felvételhez használt időáttolási (DVR) ablakot.
A mintakód bemutatja, hogyan állíthat be egy 1 órás időáttűnő ablakot. Ez lehetővé teszi az ügyfelek számára, hogy az esemény utolsó órájában bárhol visszajátssuk a lejátszást.  Emellett az élő eseménynek csak az utolsó 1 órája marad meg az archívumban. Ha szükséges, ezt akár 25 órára is kiterjesztheti.  Azt is vegye figyelembe, hogy a közzétételkor meg tudja vezérelni a HLS- és DASH-jegyzékfájlokat az URL-címekben.

A hasonlatunk élő kimenete vagy "szalagos rögzítője" is bármikor létre lehet hozva. Ez azt jelenti, hogy létrehozhat egy élő kimenetet a jelfolyam indítása előtt vagy után. Ha gyorsítsa fel a dolgokat, gyakran hasznos lehet a jelfolyam kezdete előtt létrehozni.

Az élő kimenetek létrehozáskor kezdődnek, és törléskor leállnak.  Az élő kimenet törlésekor nem törli az alapul szolgáló eszközt vagy az eszköz tartalmát. Gondoljon rá úgy, mint a szalag kiadására. A felvétellel együtt a felvétel az Ön számára megfelelő ideig fog tartani, és a kioltáskor (vagyis az élő kimenet törlésekor) azonnal megtekinthető lesz igény szerint.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>A betöltési URL-címek beolvasása

Az élő esemény létrehozása után lekérte az élő kódolónak betöltésre használt URL-címeket. A kódoló ezekkel az URL-címekkel ad meg egy élő streamet az RTMP protokoll használatával

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>Az előnézeti URL-cím lekérése

A previewEndpoint segítségével tekintse meg az előnézetet, és győződjön meg róla, hogy a kódolóról érkező bemenet valóban beérkezik.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a videó az előnézeti URL-címre mutat.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Élő események és élő kimenetek létrehozása és kezelése

Miután a stream be lett áramlva az élő eseménybe, a streamelési eseményt egy streamelési lokátor közzétételével kezdheti meg, amelyet az ügyféllejátszók használhatnak. Ez elérhetővé teszi a nézők számára a streamvégponton keresztül.

Először létre kell hoznia a jelet az "Élő esemény" létrehozásával.  A jel nem áramlik, amíg el nem indítja az élő eseményt, és nem csatlakoztatja a kódolót a bemenethez.

A "szalagrögzítő" leállításhoz hívja meg a delete parancsot a LiveOutput-on. Ez valójában nem  törli az archívum tartalmát az "Asset" szalagon, csak a "szalagrögzítőt" törli, és leállítja az archiválást. Az asset mindaddig az archivált videótartalmakban marad, amíg explicit módon nem hívja meg a delete parancsot az eszközben. Amint törli a liveOutput sebességet, az "Asset" rögzített tartalma továbbra is lejátszható a már közzétett streamelési lokátor URL-címekkel. Ha el szeretné távolítani az ügyfél azon képességét, hogy visszajátssa az archivált tartalmat, először el kell távolítania az összes lokátort az adattárból, és ki kell ürítenie a CDN-gyorsítótárat az URL-útvonalon, ha CDN-t használ a kézbesítéshez. Ellenkező esetben a tartalom a CDN gyorsítótárában fog élőben lennie a CDN normál élő időbeállítása számára (amely akár 72 óra is lehet).

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>Streamelési lokátor létrehozása HLS- és DASH-jegyzékek közzétételéhez

> [!NOTE]
> A Media Services létrehozásakor **a** rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva **állapotban.** A tartalmak streamelésének és [](encode-dynamic-packaging-concept.md) a dinamikus csomagolás és a dinamikus titkosítás előnyeinek kihasznál érdekében a tartalomstreameléshez használt streamvégpontnak Fut **állapotban** kell lennie.

Amikor streamelési lokátor használatával teszi közzé az adatobjektumot, az élő esemény (a DVR-ablak hosszáig) továbbra is megtekinthető lesz, amíg a streamelési lokátor le nem jár vagy töröl, amelyik előbb be nem következik. Így teszi elérhetővé a virtuális "szalag" felvételét a nézők számára, hogy élő és igény szerinti felvételt lássanak. Ugyanez az URL-cím használható az élő esemény, a DVR-ablak vagy az igény szerinti adateszköz figyelére a felvétel befejezésekor (az élő kimenet törlésekor).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>A HLS- és DASH-jegyzékek elérési útjának összeállítása

A mintában szereplő BuildManifestPaths metódus bemutatja, hogyan hozhat létre determinisztikusan olyan streamelési útvonalakat, amelyek a KÜLÖNBÖZŐ ügyfeleknek és lejátszó-keretrendszerek számára történő DASH- vagy HLS-továbbításhoz használhatók.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>Esemény megtekintése

Az esemény megtekintéshez másolja ki a Streamelési lokátor létrehozásacímben leírt kód futtatáskor kapott streamelési URL-címet. A választott médialejátszót is használhatja. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) elérhető a stream tesztelésére a https://ampdemo.azureedge.net következőn: .

Az élő esemény leállítva automatikusan igény szerinti tartalomká alakítja az eseményeket. A felhasználók az esemény leállítását és törlését követően is igény szerint streamelhetik videóként az archivált tartalmat, ha nem törli az eszközt. Egy esemény által használt eszközök nem törölhetők; először az eseményt kell törölni.

### <a name="cleaning-up-resources-in-your-media-services-account"></a>A Media Services-fiók erőforrásainak eltávolítása

Ha végig futtatja az alkalmazást, az automatikusan megtisztítja a "cleanUpResources" függvényben használt összes erőforrást. Győződjön meg arról, hogy az alkalmazás vagy a hibakereső végig fut a befejezésig, vagy előfordulhat, hogy kiszivárogtat erőforrásokat, és végül élő eseményeket futtat a fiókjában. Ellenőrizze a Azure Portal, hogy az összes erőforrás el van-e Media Services a Media Services fiókjában.  

A mintakódban a részletekért tekintse meg a **cleanUpResources** metódust.

> [!IMPORTANT]
> Az élő esemény futásának elhagyása számlázási költségekkel jár. Vegye figyelembe, hogy ha a projekt/program összeomlik vagy valamilyen okból bezáródik, az élő esemény számlázási állapotban hagyhatja a futását.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket feltenni, visszajelzést küldeni, frissítéseket kérni

Tekintse meg a [Azure Media Services a közösségi](media-services-community.md) cikket, amelyből különböző módszereket talál arra, hogyan tehet fel kérdéseket, adhat visszajelzést, és frissítéseket kaphat a Media Services.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>További fejlesztői dokumentáció az Azure-Node.js való használathoz

- [Azure JavaScript-& Node.js fejlesztőknek](/azure/developer/javascript/)
- [Media Services kód leírása a @azure/azure-sdk-for-js Git Hub-adattárban](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Az Azure Package dokumentációja Node.js fejlesztők számára](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Következő lépések

[Videofájlok streamelése](stream-files-tutorial-with-api.md)