---
title: Media Services v3 offline FairPlay streaming iOS rendszerhez
description: Ez a témakör áttekintést nyújt, és bemutatja, hogyan használható a Azure Media Services v3, hogy dinamikusan titkosítsa HTTP Live Streaming (HLS) tartalmát az Apple FairPlay kapcsolat nélküli módban.
services: media-services
author: willzhan
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: acff3e77b92110c818ea0ab0f8281aef87e9db81
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069082"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Offline FairPlay streaming iOS-hez Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

 A Azure Media Services jól megtervezett [tartalomvédelem-szolgáltatásokat](https://azure.microsoft.com/services/media-services/content-protection/) biztosít, amelyek a következőket fedik le:

- Microsoft-PlayReady
- Google Widevine
    
    A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.
- Apple FairPlay
- AES-128-titkosítás

A digitális jogkezelési (DRM)/Advanced Encryption Standard (AES) titkosítását a rendszer dinamikusan hajtja végre különböző folyamatos átviteli protokollok esetén. A DRM-licenc/AES-visszafejtési kulcs kézbesítési szolgáltatásait a Media Services is megadja.

A különböző streaming protokollokon keresztüli online streaming-tartalmak védelme mellett a védett tartalmak offline módja is gyakran igényelt funkció. A következő esetekben offline módú támogatásra van szükség:

* Lejátszás, ha az internetkapcsolat nem érhető el, például utazás közben.
* Előfordulhat, hogy egyes tartalomszolgáltatók nem engedélyezik a DRM-licencek kézbesítését az ország/régió szegélyén kívül. Ha a felhasználók az ország/régión kívüli utazás közben szeretnének megtekinteni a tartalmat, offline letöltésre van szükség.
* Egyes országokban/régiókban az Internet rendelkezésre állása és/vagy sávszélessége továbbra is korlátozott. A felhasználók úgy dönthetnek, hogy először letöltik a tartalmat egy olyan felbontásban, amely elég magas a megfelelő megtekintési élmény érdekében. Ebben az esetben a probléma általában nem a hálózat rendelkezésre állása, hanem a korlátozott hálózati sávszélesség. A over-the-top (OTT)/online video platform (OVP) szolgáltatói kapcsolat nélküli módú támogatást igényelnek.

Ez a cikk az FairPlay streaming (FPS) offline módú támogatását ismerteti, amely az iOS 10 vagy újabb rendszerű eszközöket célozza meg. Ez a funkció nem támogatott más Apple platformokon, például a watchOS, a tvOS vagy a Safari macOS rendszeren.

> [!NOTE]
> Az offline DRM-t csak akkor számítjuk fel, ha egy licencre vonatkozó kérést készítenek a tartalom letöltésekor. A hibák számlázása nem történik meg.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt offline DRM-t implementál a FairPlay iOS 10 + eszközön:

* Tekintse át az online tartalomvédelem FairPlay: 

    - [Apple FairPlay-licencek követelményei és konfigurálása](drm-fairplay-license-overview.md)
    - [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](drm-protect-with-drm-tutorial.md)
    - Egy .NET-minta, amely az online FPS streaming konfigurációját tartalmazza: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Szerezze be az FPS SDK-t az Apple Developer Network webhelyről. Az FPS SDK két összetevőt tartalmaz:

    - Az FPS Server SDK, amely a legfontosabb biztonsági modult (KSM), az ügyfél mintáit, a specifikációt és a tesztelési vektorok készletét tartalmazza.
    - Az FPS üzembehelyezési csomagja, amely a D függvény specifikációját tartalmazza, valamint útmutatást nyújt az FPS-tanúsítvány, az ügyfél-specifikus titkos kulcs és az alkalmazás titkos kulcsának létrehozásához. Az Apple csak a licencelt tartalomszolgáltatók számára bocsát ki az FPS központi telepítési csomagot.
* Klónozás https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git . 

    A FairPlay-konfigurációk hozzáadásához módosítania kell a kódot a [DRM-titkosítással a .NET használatával](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .  

## <a name="configure-content-protection-in-azure-media-services"></a>Tartalomvédelem konfigurálása a Azure Media Servicesban

A [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metódusban tegye a következőket:

A FairPlay házirend beállítását konfiguráló kód megjegyzésének visszaadása:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Emellett a CBCS ContentKeyPolicyOption tartalmazó kód megjegyzésének visszahelyezése a ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Offline mód engedélyezése

Az offline mód engedélyezéséhez hozzon létre egy egyéni StreamingPolicy, és használja a nevét, amikor StreamingLocator hoz létre a [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L561)-ben.
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true // This enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true // Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        // Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }
}

```

Most a Media Services-fiókja az offline FairPlay-licencek továbbítására van konfigurálva.

## <a name="sample-ios-player"></a>IOS-lejátszó minta

Az FPS nélküli offline üzemmódú támogatás csak iOS 10 és újabb rendszereken érhető el. Az FPS Server SDK (3,0-es vagy újabb verzió) tartalmazza a dokumentumot és a mintát az FPS offline üzemmódhoz. Pontosabban, az FPS Server SDK (3,0-es vagy újabb verzió) a következő két, offline üzemmódhoz kapcsolódó elemet tartalmazza:

* Dokumentum: "offline lejátszás FairPlay streaming és HTTP Live Streaming." Apple, szeptember 14., 2016. Az FPS Server SDK 4,0-es verziójában ez a dokumentum egyesül a fő FPS-dokumentumba.
* Mintakód: HLSCatalog minta (az Apple FPS Server SDK része) a \FairPlay Streaming Server SDK 3.1-es verziójának Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. A HLSCatalog-minta alkalmazásban a következő kódrészletek használatosak az offline üzemmódú szolgáltatások megvalósításához:

    - AssetPersistenceManager. Swift-kódrészlet: az AssetPersistenceManager a minta fő osztálya, amely a következőket mutatja be:

        - Kezelheti a HLS-adatfolyamok letöltését, például a letöltések elindításához és lemondásához használt API-kat, valamint a meglévő eszközöket az eszközökön.
        - A letöltési folyamat figyelése.
    - AssetListTableViewController. Swift és AssetListTableViewCell. Swift Code Files: a AssetListTableViewController a minta fő felülete. Felsorolja azokat az eszközöket, amelyeket a minta használhat a letöltés lejátszásához, letöltéséhez, törléséhez vagy megszakításához. 

Ezek a lépések bemutatják, hogyan állíthat be egy futó iOS-lejátszót. Tegyük fel, hogy az FPS Server SDK-ban a HLSCatalog mintából indul el a következő kód módosításával:

A HLSCatalog\Shared\Managers\ContentKeyDelegate.swift alkalmazásban implementálja a metódust `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` a következő kóddal. Hagyja, hogy a "drmUr" változó legyen a HLS URL-címhez rendelve.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

A HLSCatalog\Shared\Managers\ContentKeyDelegate.swift-ben implementálja a metódust `requestApplicationCertificate()` . Ez a megvalósítás attól függ, hogy a tanúsítványt (csak nyilvános kulcs) ágyazza-e be az eszközzel, vagy a tanúsítványt a weben tárolja. A következő implementáció a tesztelési mintákban használt üzemeltetett alkalmazás tanúsítványát használja. Hagyja, hogy a "certUrl" olyan változó legyen, amely az alkalmazás tanúsítványának URL-címét tartalmazza.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

A végső integrált teszt esetében a videó URL-címe és az alkalmazás-tanúsítvány URL-címe az "integrált teszt" szakaszban található meg.

A HLSCatalog\Shared\Resources\Streams.plist-ben adja hozzá a teszt videó URL-címét. A tartalmi kulcs AZONOSÍTÓjának egyedi értékeként használja a FairPlay licenc-beszerzési URL-címét a SKD protokollal.

![Offline FairPlay iOS-alkalmazás streamek](media/drm-offline-fairplay-for-ios-concept/offline-fairplay-ios-app-streams.png)

Ha beállította, használja a saját teszt videó URL-címét, a FairPlay-licenc beszerzésének URL-címét és az alkalmazás tanúsítványának URL-címét. Vagy folytassa a következő szakasszal, amely tesztelési mintákat tartalmaz.

## <a name="integrated-test"></a>Integrált teszt

A Media Services három tesztelési mintája a következő három forgatókönyvet fedi le:

* FPS-mel védett, videó-, hang-és alternatív hangsávokkal
* FPS-védelemmel ellátott, videóval és hanggal, de nincs másodlagos hangsáv
* FPS-védelemmel ellátott, csak videóval és hang nélkül

Ezek a minták [ebben a bemutató webhelyen](https://aka.ms/poc#22)találhatók, és az Azure-webalkalmazásban üzemeltetett megfelelő alkalmazás-tanúsítvánnyal is rendelkeznek.
Ha az FPS Server SDK 3-as vagy 4-es verziójú verzióját használja, és ha egy fő lista másodlagos hangot tartalmaz, offline módban csak hanglejátszást használ. Ezért szalagra kell állítania a másodlagos hangot. Más szóval a korábban felsorolt második és harmadik minták online és offline módban is működnek. A felsorolt minta először csak az offline módban, míg az online streaming megfelelően működik.

## <a name="offline-fairplay-questions"></a>Kapcsolat nélküli Fairplay kapcsolatos kérdések

Lásd: [kapcsolat nélküli Fairplay kapcsolatos kérdések](questions-collection.md#why-does-only-audio-play-but-not-video-during-offline-mode).
