---
title: feltöltés, kódolás és stream Media Services v3-val: Azure Media Services Leírás: oktatóanyag, amely bemutatja, hogyan tölthet fel fájlokat, kódolhat és továbbíthatja a tartalmat a Azure Media Services v3 használatával.
szolgáltatások: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. munkaterhelés: MS. topic: oktatóanyag MS. Custom: MVC MS. Date: 08/31/2020 MS. Author: inhenkel
---

# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Oktatóanyag: videók feltöltése, kódolása és továbbítása a Media Services v3 segítségével

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Bár ez az oktatóanyag [.net SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) -példákat használ, az általános lépések megegyeznek [a REST API](/rest/api/media/liveevents), a [CLI](/cli/azure/ams/live-event)vagy más támogatott [SDK](media-services-apis-overview.md#sdks)-k esetében.

Azure Media Services lehetővé teszi, hogy a médiafájlokat a különböző böngészőkön és eszközökön lejátszani kívánt formátumokba kódolja. Például előfordulhat, hogy az Apple HLS vagy MPEG DASH formátumában szeretné streamelni a tartalmakat. A streamelés előtt érdemes kódolni a jó minőségű digitális médiafájlokat. A kódolással kapcsolatos segítségért lásd a [kódolási koncepciót](encoding-concept.md). Ez az oktatóanyag feltölt egy helyi videofájlt, és kódolja a feltöltött fájlt. Egy HTTPS URL-címen keresztül elérhetővé tehető tartalmat is kódolhat. További információ: [Feladatbemenet létrehozása HTTP(s) URL-címből](job-input-from-http-how-to.md).

![Videó lejátszása Azure Media Player](./media/stream-files-tutorial-with-api/final-video.png)

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Töltse le a következő témakörben ismertetett minta alkalmazást:.
> * Vizsgálja meg a feltöltési, kódolási és adatfolyam-kódot.
> * Futtassa az alkalmazást.
> * Tesztelje a folyamatos átviteli URL-címet.
> * Az erőforrások eltávolítása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nincs telepítve a Visual Studio, a [Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Hozzon létre egy Media Services fiókot](./create-account-howto.md).<br/>Ügyeljen arra, hogy az erőforráscsoport neveként használt értékeket jegyezze fel, és Media Services a fiók nevét.
- Kövesse a [Azure Media Services API-nak az Azure CLI-vel való elérésének](./access-api-howto.md) lépéseit, és mentse a hitelesítő adatokat. Ezeket az API-k eléréséhez kell használnia.

## <a name="download-and-set-up-the-sample"></a>A minta letöltése és beállítása

A következő paranccsal klónozott egy GitHub-tárházat, amely a streaming .NET-mintát használja a gépre:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

A minta az [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles) mappában található.

Nyissa meg [appsettings.jsa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) letöltött projektben. Cserélje le az értékeket az API-k [eléréséhez](./access-api-howto.md)kapott hitelesítő adatokkal.

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>A feltöltést, kódolást és streamelést végrehajtó kód vizsgálata

Ez a szakasz az *UploadEncodeAndStreamFiles* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) nevű fájljában megadott függvényeket vizsgálja meg.

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy új **átalakítót** (először ellenőrzi, hogy létezik-e a megadott átalakító).
2. Létrehozza a kódolási **feladatok** **kimenetéhez használt kimeneti objektumot** .
3. Hozzon létre **egy bemeneti objektumot** , és töltse fel a megadott helyi videofájl. Az objektum lesz a feladat bemenete.
4. Elküldi a kódolási feladatot a létrehozott bemenet és kimenet használatával.
5. Ellenőrzi a feladat állapotát.
6. Adatfolyam- **keresőt** hoz létre.
7. Streamelési URL-címeket épít fel.

### <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyek szükségesek ahhoz, hogy az ügyfél csatlakozhasson az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bemeneti objektum létrehozása és helyi fájl feltöltés az objektumba

A **CreateInputAsset** függvény létrehoz egy új bemeneti [objektumot](/rest/api/media/assets), és feltölti az objektumba a megadott helyi videofájlt. Ez az **eszköz** a kódolási feladatnak való bemenetként szolgál. A Media Services v3-as verziójában a **feladatok** bemenete lehet egy olyan **eszköz** vagy tartalom, amelyet a Media Services-fiók számára elérhetővé tesz a HTTPS URL-címeken keresztül. A HTTPS URL-címekről történő kódolásról [ebben](job-input-from-http-how-to.md) a cikkben talál további információt.

A Media Services 3-as verziójában Azure Storage API-k használatával tölthet fel fájlokat. Ennek módját a következő .NET-kódrészlet mutatja be.

A következő függvény ezeket a műveleteket hajtja végre:

* Létrehoz egy **eszközt**.
* Egy írható [sas URL-cím](../../storage/common/storage-sas-overview.md) beolvasása az eszköz tárolójában a [tárolóban](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).

    Ha az eszköz [ListContainerSas](/rest/api/media/assets/listcontainersas) funkcióját használja a sas URL-címek beszerzéséhez, vegye figyelembe, hogy a függvény több sas URL-címet ad vissza, mivel mindegyik Storage-fiókhoz két Storage-fiók kulcsa van. A Storage-fiók két kulccsal rendelkezik, mert lehetővé teszi a Storage-fiókok kulcsainak zökkenőmentes rotációját (például egy másik, az új kulcs használatának megkezdése és a másik kulcs elforgatása). Az 1. SAS URL-cím a Storage key1 és a második tárolási key2 jelöli.
* Feltölti a fájlt a Storage tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Kimeneti objektum létrehozása feladat eredményeinek tárolásához

A kimeneti [objektum](/rest/api/media/assets) tárolja a kódolási feladat eredményeit. A projekt határozza meg a **DownloadResults** függvényt, amely letölti az eredményt ebből a kimeneti objektumból az „output” mappába, hogy megtekinthesse.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Átalakítás és a feltöltött fájlt kódoló feladat létrehozása

A Media Services tartalmának kódolásakor vagy feldolgozásakor gyakori minta a kódolási beállítások beállítása Receptként. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Új feladatok elküldésével minden új videóhoz ezt a receptet alkalmazza a könyvtárában lévő összes videóra. Media Services egy receptet **átalakítónak** nevezzük. További információt az [átalakításokkal és feladatokkal](./transforms-jobs-concept.md) kapcsolatos cikkben olvashat. Az ebben az oktatóanyagban leírt minta meghatároz egy receptet, amely elvégzi a videó kódolását, hogy azt streamelni lehessen többféle iOS- és Android-eszközre.

#### <a name="transform"></a>Átalakítás

Egy új [átalakításpéldány](/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. Az előzetes beállítással kapcsolatos információkért tekintse meg a [sávszélességi skálák automatikus létrehozását](autogen-bitrate-ladder.md) ismertető részt.

Használhatja a beépített EncoderNamedPreset beállítást vagy az egyéni előzetes beállításokat. További információt [a kódoló előzetes beállításainak testreszabását](customize-encoder-presets-how-to.md) ismertető cikkben talál.

[Átalakítások](/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható. A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Feladat

Ahogy korábban említettük, az [átalakítási](/rest/api/media/transforms) objektum a recept, a [feladat](/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben a példában a bemeneti videó a helyi számítógépről lett feltöltve. Ha szeretné megismerni a HTTPS URL-címekről történő kódolást, tekintse meg [ezt a](job-input-from-http-how-to.md) cikket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Az alábbi kódminta bemutatja, hogyan kérdezheti le a [feladat](/rest/api/media/jobs) állapotát a szolgáltatásból. A lekérdezés nem ajánlott eljárás az üzemi alkalmazások számára a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt.

Az Event Grid egy magas rendelkezésre állású, egyenletes teljesítményű, dinamikusan skálázható szolgáltatás. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. Az egyszerű, HTTP-alapú reaktív eseménykezelés segít hatékony megoldásokat kialakítani az események intelligens szűrése és átirányítása révén.  További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítása folyamatban van, akkor **megszakítja** és **megszakítja** a műveletet, ha elkészült.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Folyamatos átviteli lokátor beszerzése

A kódolás befejezése után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. Két lépésben is elérhetővé teheti: először hozzon létre egy [adatfolyam-keresőt](/rest/api/media/streaminglocators), és Másodszor hozza létre az ügyfelek által használható Streaming URL-címeket.

Az **adatfolyam-kereső** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint az **adatfolyam-kereső** azonnal érvényes az API-hívások létrehozása után, és addig tart, amíg meg nem történik a törlés, hacsak nem konfigurálja a nem kötelező kezdési és befejezési időpontokat.

[StreamingLocator](/rest/api/media/streaminglocators)létrehozásakor meg kell adnia a kívánt **StreamingPolicyName**. Ebben a példában a folyamatos adatátvitelt (vagy nem titkosított tartalmat) fogja használni, így az előre definiált tiszta adatfolyam-szabályzatot (**PredefinedStreamingPolicy. ClearStreamingOnly**) használja a rendszer.

> [!IMPORTANT]
> Ha egyéni [folyamatos átviteli szabályzatot](/rest/api/media/streamingpolicies)használ, meg kell terveznie az ilyen szabályzatok korlátozott készletét a Media Service-fiókjához, és újra fel kell használni azokat a StreamingLocators, amikor ugyanazok a titkosítási beállítások és protokollok szükségesek. A Media Service-fiókhoz tartozik egy kvóta a folyamatos átviteli szabályzat bejegyzéseinek számára. Ne hozzon létre új folyamatos átviteli szabályzatot minden egyes adatfolyam-keresőhöz.

A következő kód azt feltételezi, hogy a függvényt egy egyedi locatorName hívja meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Míg a témakörben szereplő minta a streaminget tárgyalja, ugyanazt a hívást használhatja a videó továbbításához a progresszív letöltés használatával.

### <a name="get-streaming-urls"></a>Streamelési URL-címek lekérdezése

Most, hogy létrejött a [folyamatos átviteli lokátor](/rest/api/media/streaminglocators) , beolvashatja a streaming URL-címeket, ahogy az a **GetStreamingURLs**-ban is látható. URL-cím létrehozásához összefűzni kell a [streaming Endpoint](/rest/api/media/streamingendpoints) Host nevét és a **folyamatos átviteli lokátor** elérési útját. Ebben a példában az *alapértelmezett* **adatfolyam-végpontot** használja a rendszer. Amikor először hoz létre egy Media Service-fiókot, az *alapértelmezett* **folyamatos átviteli végpont** leállított állapotba kerül, ezért meg kell hívnia a **Start** parancsot.

> [!NOTE]
> Ebben a metódusban szüksége lesz a kimeneti eszköz **folyamatos átviteli lokátorának** létrehozásakor használt locatorName.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában érdemes megtisztítani mindent, kivéve azokat az objektumokat, amelyeket újra fel kíván használni (általában újra kell használni az átalakításokat, és továbbra is meg kell őriznie a StreamingLocators stb.). Ha azt szeretné, hogy a fiókja a kísérletezés után is tiszta legyen, törölje azokat az erőforrásokat, amelyeket nem kíván újra felhasználni. A következő kód például törli a feladatot, a létrehozott eszközöket és a tartalmi kulcs házirendjét:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>A mintaalkalmazás futtatása

1. A *EncodeAndStreamFiles* alkalmazás futtatásához nyomja le a CTRL + F5 billentyűkombinációt.
2. Másolja be az egyik streamelési URL-címet a konzolról.

Ebben a példában olyan URL-címek szerepelnek, amelyek lehetővé teszik, hogy a videót többféle protokollal le lehessen játszani:

![Példa kimenetre Media Services streaming videó URL-címeinek megjelenítéséhez](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>A streamelési URL-cím tesztelése

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez.

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy webböngészőt, és navigáljon a következőhöz: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Az **URL:** mezőben illessze be az alkalmazás futtatásakor kapott streaming URL-értékeket.
3. Válassza a **lejátszó frissítése** lehetőséget.

A Azure Media Player használható tesztelésre, de nem használható éles környezetben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Több szál használata

A Azure Media Services v3 SDK-k nem a szálon biztonságosak. Többszálas alkalmazások fejlesztésekor az új AzureMediaServicesClient objektum létrehozása és használata egy szálon történik.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan tölthet fel, kódolhat és streamelhet videókat, tekintse meg a következő cikket: 

> [!div class="nextstepaction"]
> [Videók elemzése](analyze-videos-tutorial-with-api.md)
