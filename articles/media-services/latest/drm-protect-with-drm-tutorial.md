---
title: Media Services DRM titkosítás és licencek kézbesítése
description: Ismerje meg, hogyan használható a DRM Dynamic encryption és a License Delivery Service a Microsoft PlayReady, a Google Widevine vagy az Apple FairPlay-licenccel titkosított adatfolyamok továbbítására.
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
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: dab265c41bc1c951a31ff764c9214e7f21ee3df4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279561"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Oktatóanyag: a DRM dinamikus titkosításának és a licenc-kézbesítési szolgáltatásnak a használata

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> Bár ez az oktatóanyag a [.net SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) -példákat használja, az általános lépések megegyeznek [a REST API](/rest/api/media/liveevents), a [CLI](/cli/azure/ams/live-event)vagy más támogatott [SDK](media-services-apis-overview.md#sdks)-k esetében.

Az Azure Media Services segítségével a streamjeit Microsoft PlayReady-, Google Widevine- vagy Apple FairPlay-licencekkel titkosíthatja. A részletes magyarázatot lásd: [tartalomvédelem dinamikus titkosítással](drm-content-protection-concept.md).

A Media Services a PlayReady, a Widevine és a FairPlay DRM-licencek kézbesítését is biztosítja. Ha a felhasználó DRM-védelemmel ellátott tartalmat kér, a Player alkalmazás a Media Services licencelési szolgáltatástól kér licencet. Ha a Player alkalmazás engedélyezve van, akkor a Media Services licencelési szolgáltatás kiadja a licencet a lejátszónak. A licencek tartalmazzák a feloldási kulcsot, amelynek segítségével az ügyféllejátszó képes feloldani a titkosítást, majd streamelni a kért tartalmakat.

Ez a cikk a [DRM használatával való titkosítás](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) mintáján alapul.

A cikkben leírt minta a következőt eredményezi:

![AMS DRM-védelemmel ellátott videóval Azure Media Player](./media/drm-protect-with-drm-tutorial/ams_player.png)

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy kódolási átalakítót.
> * A jogkivonat ellenőrzéséhez használt aláírókulcs beállítása.
> * Követelmények beállítása a tartalmi kulcs házirendjében.
> * Hozzon létre egy StreamingLocator a megadott folyamatos átviteli házirenddel.
> * Hozza létre a fájl lejátszásához használt URL-címet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének feltételei a következők:

* Tekintse meg a [Content Protection áttekintését](drm-content-protection-concept.md) ismertető cikket.
* Tekintse át a [többplatformos DRM-alapú tartalomkezelő rendszereket a hozzáférés-vezérléssel](architecture-design-multi-drm-system.md).
* Telepítse a Visual Studio Code vagy a Visual Studio alkalmazást.
* Hozzon létre egy új Azure Media Services-fiókot [az ebben a gyors útmutatóban](./account-create-how-to.md) leírt módon.
* Szerezze be a Media Services API-k használatához szükséges hitelesítő adatokat az [adatelérési API-kat](./access-api-howto.md) bemutató szakasz leírását követve.
* Állítsa be a megfelelő értékeket az alkalmazás konfigurációs fájljába (appsettings.js).

## <a name="download-code"></a>Kód letöltése

Klónozza a gépre az ebben a cikkben ismertetett teljes .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Az „Encrypt with DRM” minta az [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) mappában található.

> [!NOTE]
> A minta egyedi erőforrásokat hoz létre minden alkalommal, amikor futtatja az alkalmazást. Általában újra fel kell használni a meglévő erőforrásokat, például átalakításokat és házirendeket (ha a meglévő erőforrásokhoz szükséges konfigurációk vannak).

## <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Az Media Services API-k .NET-tel való használatának megkezdéséhez hozzon létre egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása  

A kimeneti [objektum](assets-concept.md) tárolja a kódolási feladat eredményeit.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Kódoló átalakítás beszerzése vagy létrehozása

Egy új [átalakításpéldány](transform-jobs-concept.md) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy `transformOutput` objektum, ahogy az alábbi kódban is látható. Minden TransformOutput objektum tartalmaz **előzetes beállításokat**. Az előzetes beállítások részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt TransformOutput objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előre definiált bemeneti videó egy automatikusan létrehozott bitráta-ladderbe (bitráta-feloldási párokba) van kódolva, a bemeneti felbontás és a bitráta alapján, valamint az egyes bitráta-feloldási pároknak megfelelő, H. 264 videóval és AAC hanggal rendelkező ISO MP4-fájlokat hoz létre. 

Új **átalakítások** létrehozása előtt ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható.  A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az **átalakítási** objektum a recept, a [feladat](transform-jobs-concept.md) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladatok** olyan információkat határoznak meg, mint a bemeneti videó helye és a kimenet helye.

Ebben az oktatóanyagban létrehozjuk a feladatok bemenetét egy olyan fájl alapján, amely közvetlenül egy [https-forrás URL-címéből](job-input-from-http-how-to.md)lett betöltve.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladatok elvégzése hosszabb időt vesz igénybe. Ha igen, értesítést szeretne kapni. Az alábbi kódminta bemutatja, hogyan kérdezheti le a **feladat** állapotát a szolgáltatásból. A lekérdezés nem ajánlott eljárás az üzemi alkalmazások számára a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](monitoring/job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladattípus hibát észlelt, a **hiba** állapota jelenik meg. Ha a feladat megszakítása folyamatban van, akkor **megszakítja** és **megszakítja** a műveletet, ha elkészült.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Tartalmi kulcsokra vonatkozó szabályzat létrehozása

A tartalomkulcsok biztonságos hozzáférést nyújtanak az objektumokhoz. A tartalom DRM-mel való titkosításakor létre kell hoznia egy [tartalmi kulcsra vonatkozó házirendet](drm-content-key-policy-concept.md) . A házirend azt konfigurálja, hogy a rendszer hogyan továbbítsa a tartalmi kulcsot a végfelhasználók számára. A tartalmi kulcs egy adatfolyam-keresőhöz van társítva. A Media Services azt a kulcstovábbító szolgáltatást is biztosítja, amely titkosítási kulcsokat és licenceket továbbít a jogosult felhasználóknak.

Meg kell adnia azokat a **tartalmi kulcsokra vonatkozó házirend** követelményeit (korlátozásait), amelyeknek teljesülniük kell ahhoz, hogy a kulcsok a megadott konfigurációval legyenek kézbesítve. Ebben a példában a következő konfigurációkat és követelményeket állítjuk be:

* Konfiguráció

    A [PlayReady](drm-playready-license-template-concept.md)- és a [Widevine](drm-widevine-license-template-concept.md)-licencek úgy vannak konfigurálva, hogy a Media Services licenctovábbítási szolgáltatása továbbítani tudja azokat. Annak ellenére, hogy ez a minta alkalmazás nem konfigurálja a [Fairplay](drm-fairplay-license-overview.md) -licencet, a Fairplay konfigurálásához használható metódust tartalmaz. A FairPlay-konfigurációt másik lehetőségként is hozzáadhatja.

* Korlátozás

    Az alkalmazás egy JSON Web Token (JWT) token típusú korlátozást állít be a szabályzatra.

Amikor egy lejátszó streamet kér, a Media Services a megadott kulccsal titkosítja dinamikusan a tartalmat. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Adatfolyam-kereső létrehozása

A kódolás befejezése és a tartalomkulcs-szabályzat beállítása után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. A videó a következő két lépésben érhető el:

1. Hozzon létre egy [streaming-lokátort](stream-streaming-locators-concept.md).
2. Hozza létre az ügyfelek által használt streamelési URL-címeket.

Az **adatfolyam-kereső** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint az **adatfolyam-kereső** az API-hívások után azonnal érvényes. Addig tart, amíg nem törlik, hacsak nem konfigurálja a nem kötelező kezdési és befejezési időpontokat.

**Adatfolyam-kereső** létrehozásakor meg kell adnia a kívántat `StreamingPolicyName` . Ebben az oktatóanyagban az egyik előre definiált adatfolyam-szabályzatot használjuk, amely azt ismerteti, Azure Media Services hogyan teheti közzé a tartalmat a streaminghez. Ebben a példában a StreamingLocator.StreamingPolicyName elemet állítjuk be a „Predefined_MultiDrmCencStreaming” szabályzathoz. A rendszer alkalmazza a PlayReady és a Widevine titkosítást, és a kulcsot a konfigurált DRM-licencek alapján továbbítja a rendszer a lejátszási ügyfélnek. Ha CBCS (FairPlay) licenccel is titkosítani szeretné a streamet, használja a következőt: „Predefined_MultiDrmStreaming”.

> [!IMPORTANT]
> Ha egyéni [folyamatos átviteli szabályzatot](stream-streaming-policy-concept.md)használ, meg kell terveznie az ilyen szabályzatok korlátozott készletét a Media Service-fiókjához, és újra fel kell használni azokat a StreamingLocators, amikor ugyanazok a titkosítási beállítások és protokollok szükségesek. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. Minden StreamingLocator új StreamingPolicy kell létrehoznia.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése

Ebben az oktatóanyagban meghatározzuk, hogy a tartalomkulcs-szabályzat jogkivonat-korlátozással rendelkezzen. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. Media Services támogatja a [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) -formátumokban lévő jogkivonatokat, és a példában konfiguráltunk.

A ContentKeyIdentifierClaim szerepel a ContentKeyPolicy szabályzatban, ami azt jelenti, hogy a kulcstovábbító szolgáltatásnak bemutatott jogkivonatban szerepelnie kell a ContentKey azonosítójának. A mintában nem adunk meg tartalmi kulcsot a folyamatos átviteli lokátor létrehozásakor, a rendszer létrehoz egy véletlenszerűen kiválasztottat a számunkra. A teszt token létrehozásához be kell szereznie a ContentKeyId, amelyet a ContentKeyIdentifierClaim jogcímbe kell helyezni.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Streaming URL-cím létrehozása

A [StreamingLocator](/rest/api/media/streaminglocators) létrehozása után lekérheti a streamelési URL-címeket. URL-cím létrehozásához összefűzni kell a [streamvégpontok](/rest/api/media/streamingendpoints) -gazdagép nevét és a **folyamatos átviteli lokátor** elérési útját. Ebben a példában az *alapértelmezett* **adatfolyam-végpontot** használja a rendszer. Amikor először hoz létre egy Media Service-fiókot, az *alapértelmezett* **folyamatos átviteli végpont** leállított állapotba kerül, ezért meg kell hívnia a **Start** parancsot.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

Az alkalmazás futtatásakor a következő képernyő jelenik meg:

![Védelem a DRM-mel](./media/drm-protect-with-drm-tutorial/playready_encrypted_url.png)

Megnyithat egy böngészőt, és beillesztheti az eredményül kapott URL-t, amellyel elindíthatja az Azure Media Player bemutató oldalát az előre kitöltött URL-lel és jogkivonattal.

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában törölni kell mindent, kivéve azokat az objektumokat, amelyeket újra fel kíván használni (általában az átalakításokat, a StreamingLocators stb.). Ha azt szeretné, hogy a fiókja a kísérletezés után is tiszta legyen, törölje azokat az erőforrásokat, amelyeket nem kíván újra felhasználni. A következő kód például törli a feladatot, a létrehozott eszközöket és a tartalmi kulcs házirendjét:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

Kivétel

> [!div class="nextstepaction"]
> [Védelem AES-128](drm-playready-license-template-concept.md)
