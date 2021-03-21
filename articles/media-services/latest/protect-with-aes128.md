---
title: videó titkosítása AES-128 használatával: Azure Media Services Leírás: megtudhatja, hogyan titkosíthatja a videót AES 128 bites titkosítással, és hogyan használhatja a Key Delivery szolgáltatást a Azure Media Servicesban.
szolgáltatások: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. munkaterhelés: Media ms.tgt_pltfrm: na MS. devlang: na MS. topic: oktatóanyag MS. Date: 03/17/2021 MS. Author: inhenkel

---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>Oktatóanyag: videó titkosítása AES-128-mel és a Key Delivery Service használata

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Annak ellenére, hogy az oktatóanyag a [.net SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) -példákat használja, az általános lépések ugyanazok a [REST API](/rest/api/media/liveevents), a [CLI](/cli/azure/ams/live-event)vagy más támogatott [SDK](media-services-apis-overview.md#sdks)-k esetén.

A Media Services használatával HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming titkosítást biztosíthat az AES-vel 128 bites titkosítási kulcsok használatával. A Media Services a kulcsfontosságú kézbesítési szolgáltatást is biztosítja, amely titkosítási kulcsokat biztosít a hitelesítő felhasználók számára. Ha azt szeretné, hogy a Media Services a videó dinamikus titkosítását, társítsa a titkosítási kulcsot egy streaming-Lokátorhoz, és konfigurálja a tartalmi kulcs házirendjét is. Ha egy lejátszó egy adatfolyamot kér, Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat az AES-128 használatával. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). A [streamelési protokollokkal és a titkosítási típusokkal](content-protection-overview.md#streaming-protocols-and-encryption-types) kapcsolatos szakaszban megtekintheti, hogy mit mivel érdemes kombinálni. Lásd még: [védelem a DRM-mel](protect-with-drm.md).

A minta kimenete ez a cikk tartalmazza a Azure Media Player URL-címét, a jegyzékfájl URL-címét és a tartalom lejátszásához szükséges AES-tokent. A minta a JSON Web Token (JWT) token lejáratát 1 órára állítja be. Megnyithat egy böngészőt, és beillesztheti az eredményül kapott URL-címet, hogy elindítsa a Azure Media Player bemutató oldalt, amely az URL-címet és a tokent kitöltötte a következő formátumban: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}``` .

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Töltse le a cikkben ismertetett [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) -mintát.
> * Media Services API-k használatának megkezdése a .NET SDK-val.
> * Kimeneti eszköz létrehozása
> * Hozzon létre egy kódolási átalakítót.
> * Feladatok elküldése.
> * Várjon, amíg a feladatok befejeződik.
> * Tartalmi kulcsokra vonatkozó szabályzat létrehozása
> * Konfigurálja a házirendet az JWT-jogkivonat korlátozásának használatára.
> * Hozzon létre egy streaming-lokátort.
> * Konfigurálja a streaming-keresőt a videó AES-vel (ClearKey) való titkosításához.
> * Teszt token beolvasása.
> * Hozzon létre egy streaming URL-címet.
> * Az erőforrások eltávolítása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

* Tekintse meg a [Content Protection áttekintését](content-protection-overview.md) ismertető cikket.
* Telepítse a Visual Studio Code vagy a Visual Studio alkalmazást.
* [Hozzon létre egy Media Services fiókot](./create-account-howto.md).
* A [hozzáférési API](./access-api-howto.md)-k használatával Media Services API-k használatához szükséges hitelesítő adatok beolvasása.

## <a name="download-code"></a>Kód letöltése

Klónozza a gépre az ebben a cikkben ismertetett teljes .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

A "titkosítás AES-128" minta a [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) mappában található.

> [!NOTE]
> A minta egyedi erőforrásokat hoz létre minden alkalommal, amikor futtatja az alkalmazást. Általában újra fel kell használni a meglévő erőforrásokat, például átalakításokat és házirendeket (ha a meglévő erőforrásokhoz szükséges konfigurációk vannak).

## <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Az Media Services API-k .NET-tel való használatának megkezdéséhez hozzon létre egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása  

A kimeneti [objektum](/rest/api/media/assets) tárolja a kódolási feladat eredményeit.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Kódoló átalakítás beszerzése vagy létrehozása

Egy új [átalakításpéldány](/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előre megadott érték a bemeneti videót egy automatikusan létrehozott bitráta-ladderbe (bitráta-feloldási párok) kódolja, amely a bemeneti felbontás és a bitráta alapján lett létrehozva, majd az egyes bitráta-feloldási pároknak megfelelő, H. 264 videóval és AAC hanggal rendelkező ISO MP4-fájlokat hoz létre.

Új [átalakító](/rest/api/media/transforms)létrehozása előtt először ellenőrizze, hogy már létezik-e már a **Get** metódus használatával, ahogy az a következő kódban is látható. A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az [átalakítási](/rest/api/media/transforms) objektum a recept, a [feladat](/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladatok** olyan információkat határoznak meg, mint a bemeneti videó helye és a kimenet helye.

Ebben az oktatóanyagban létrehozjuk a feladatok bemenetét egy olyan fájl alapján, amely közvetlenül egy [https-forrás URL-címéből](job-input-from-http-how-to.md)lett betöltve.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladatok elvégzése hosszabb időt vesz igénybe. Ha igen, értesítést szeretne kapni. Az alábbi kódminta bemutatja, hogyan kérdezheti le a [feladat](/rest/api/media/jobs) állapotát a szolgáltatásból. A lekérdezés nem ajánlott eljárás az üzemi alkalmazások számára a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. További információ: [események átirányítása egyéni webes végpontra](monitoring/job-state-events-cli-how-to.md).

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladattípus hibát észlelt, a **hiba** állapota jelenik meg. Ha a feladat megszakítása folyamatban van, akkor **megszakítja** és **megszakítja** a műveletet, ha elkészült.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Tartalmi kulcsokra vonatkozó szabályzat létrehozása

A tartalomkulcsok biztonságos hozzáférést nyújtanak az objektumokhoz. Létre kell hoznia egy **tartalmi kulcsra vonatkozó házirendet** , amely azt konfigurálja, hogy a rendszer hogyan továbbítsa a tartalmi kulcsot a végfelhasználók számára. A tartalmi kulcs a **folyamatos átviteli lokátorhoz** van társítva. A Media Services a kulcsfontosságú kézbesítési szolgáltatást is biztosítja, amely titkosítási kulcsokat biztosít a hitelesítő felhasználók számára.

Ha egy lejátszó egy adatfolyamot kér, Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat (ebben az esetben az AES-titkosítás használatával). Az adatfolyam visszafejtéséhez a lejátszó a kulcs kézbesítési szolgáltatástól kéri a kulcsot. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Adatfolyam-kereső létrehozása

A kódolás befejezése és a tartalomkulcs-szabályzat beállítása után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. A videó a következő két lépésben érhető el:

1. Hozzon létre egy [streaming-lokátort](/rest/api/media/streaminglocators).
2. Hozza létre az ügyfelek által használt streamelési URL-címeket.

Az **adatfolyam-kereső** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint az **adatfolyam-kereső** az API-hívások után azonnal érvényes. Addig tart, amíg nem törlik, hacsak nem konfigurálja a nem kötelező kezdési és befejezési időpontokat.

[Adatfolyam-kereső](/rest/api/media/streaminglocators)létrehozásakor meg kell adnia a kívánt **StreamingPolicyName**. Ebben az oktatóanyagban a PredefinedStreamingPolicies egyikét használjuk, amely azt ismerteti, Azure Media Services hogyan teheti közzé a tartalmat a streaminghez. Ebben a példában az AES-borítékok titkosítása van érvényben (ezt a titkosítást ClearKey titkosításnak is nevezzük, mert a kulcsot HTTPS-en keresztül, nem pedig DRM-licenccel) továbbítják a lejátszási ügyfélnek.

> [!IMPORTANT]
> Egyéni [StreamingPolicy](/rest/api/media/streamingpolicies)használata esetén a Media Service-fiókhoz korlátozott számú ilyen szabályzatot kell terveznie, és újra fel kell használni azokat a streaming-lokátorokhoz, amikor ugyanazok a titkosítási beállítások és protokollok szükségesek. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. Ne hozzon létre új StreamingPolicy az egyes streaming-lokátorok számára.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése

Ebben az oktatóanyagban meghatározzuk, hogy a tartalomkulcs-szabályzat jogkivonat-korlátozással rendelkezzen. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. Media Services támogatja a tokeneket a [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) formátumban, és ezt a mintát a példában konfiguráljuk.

A ContentKeyIdentifierClaim a **tartalmi kulcs házirendjében** használják, ami azt jelenti, hogy a Key Delivery Service-nek bemutatott jogkivonat azonosítójának szerepelnie kell benne. A mintában nem határoztak meg tartalmi kulcsot a folyamatos átviteli lokátor létrehozásakor, a rendszer létrehozott egy véletlenszerűen kiválasztottat a számunkra. A teszt token létrehozásához be kell szereznie a ContentKeyId, amelyet a ContentKeyIdentifierClaim jogcímbe kell helyezni.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>DASH streamelési URL létrehozása

Most, hogy létrejött a [folyamatos átviteli lokátor](/rest/api/media/streaminglocators) , letöltheti a streaming URL-címeket. URL-cím létrehozásához összefűzni kell a [streamvégpontok](/rest/api/media/streamingendpoints) -gazdagép nevét és a **folyamatos átviteli lokátor** elérési útját. Ebben a példában az *alapértelmezett* **adatfolyam-végpontot** használja a rendszer. Amikor először hoz létre egy Media Service-fiókot, az *alapértelmezett* **folyamatos átviteli végpont** leállított állapotba kerül, ezért meg kell hívnia a **Start** parancsot.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában törölni kell mindent, kivéve azokat az objektumokat, amelyeket újra fel kíván használni (általában újra kell használni az átalakításokat, a streaming-Lokátorokat stb.). Ha azt szeretné, hogy a fiókja a kísérletezés után is tiszta legyen, törölje azokat az erőforrásokat, amelyeket nem kíván újra felhasználni. A következő kód például törli a feladatot, a létrehozott eszközöket és a tartalmi kulcs házirendjét:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Védelem a DRM-mel](protect-with-drm.md)
