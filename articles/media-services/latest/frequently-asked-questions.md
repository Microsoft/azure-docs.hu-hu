---
title: Azure Media Services v3 – gyakori kérdések
description: Ez a cikk a Azure Media Services v3-vel kapcsolatos gyakori kérdésekre ad választ.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a452a056ff2bdbad5d2e461716ee1a56d36c8523
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897560"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 – gyakori kérdések

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk a Azure Media Services v3-vel kapcsolatos gyakori kérdésekre ad választ.

## <a name="general"></a>Általános

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Melyek a Media Services v3 Azure Portal korlátozásai?

A [Azure Portal](https://portal.azure.com/) a v3 élő események kezelésére, a v3-eszközök és-feladatok megtekintésére, az API-k elérésére és a tartalmak titkosítására vonatkozó információk beszerzésére használható. <br/>Minden egyéb felügyeleti feladathoz (például átalakítások és feladatok kezelése vagy v3 tartalom elemzése) használja a [REST API](/rest/api/media/accountfilters), a [CLI](/cli/azure/ams)vagy az egyik támogatott [SDK](media-services-apis-overview.md#sdks)-t.

Ha a videó korábban fel lett töltve a Media Services-fiókba Media Services V3 API-val, vagy a tartalom egy élő kimenet alapján lett létrehozva, akkor nem jelenik meg a **kódolás**, az **elemzés** vagy a **titkosítás** gomb a Azure Portal. Ezeket a feladatokat a Media Services V3 API-k használatával hajthatja végre.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Milyen Azure-szerepkörök végezhetnek műveleteket Azure Media Services erőforrásokon? 

Lásd: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) Media Services fiókokhoz](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Hogyan streamet az Apple iOS-eszközökre?

Győződjön meg arról, hogy az elérési út végén található ( **Format = m3u8-AAPL)** (az URL-cím **/manifest** szakasza után), hogy az adatfolyam-küldő kiszolgáló visszaadja-e a http Live Streaming (HLS) tartalmat az Apple iOS Native-eszközökön való felhasználáshoz. Részletekért lásd: [tartalom továbbítása](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Hogyan a Media szolgáltatás számára fenntartott egységek konfigurálása?

A Media Services v3 vagy Video Indexer által aktivált hangelemzési és videó-elemzési feladatok esetében javasoljuk, hogy a fiókját 10 S3 Media szolgáltatás számára fenntartott egységgel (MRUs) adja meg. Ha több mint 10 S3 MRUs van szüksége, nyisson meg egy támogatási jegyet a [Azure Portal](https://portal.azure.com/)használatával.

Részletekért lásd: a [médiafájlok feldolgozásának skálázása](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Mi az ajánlott módszer a videók feldolgozására?

Az [átalakítások](/rest/api/media/transforms) használatával konfigurálhatja a videók kódolására vagy elemzésére szolgáló gyakori feladatokat. Mindegyik transzformáció egy receptet vagy egy munkafolyamatot ír le, amely a videó-vagy hangfájlok feldolgozására használható. A [feladatnak](/rest/api/media/jobs) az a tényleges kérése, hogy az átalakítást egy bemeneti videóra vagy hangtartalomra alkalmazza Media Services. Az átalakítás létrehozása után Media Services API-kkal vagy a közzétett SDK-kkal is elküldheti a feladatokat. További információt az [átalakításokkal és feladatokkal](transforms-jobs-concept.md) kapcsolatos cikkben olvashat.

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Feltöltöttem, kódoltam és közzétettem egy videót. Miért nem játssza le a videót, amikor megpróbálok adatfolyamot továbbítani?

Az egyik leggyakoribb ok az, hogy nem rendelkezik a folyamatos átviteli végponttal, amelyről a futó állapotot próbálja lejátszani.

### <a name="how-does-pagination-work"></a>Hogyan működik a tördelés?

Ha oldalszámozást használ, mindig a következő hivatkozásra kell használnia a gyűjtemény számbavételéhez, és nem függ egy adott oldalméret méretétől. A részleteket és példákat lásd: [szűrés, rendezés, lapozás](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Milyen funkciók még nem érhetők el a Azure Media Services v3 verzióban?

Részletekért tekintse meg [az áttelepítési útmutatót](migrate-v-2-v-3-migration-introduction.md).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Milyen folyamattal kell áthelyezni Media Services fiókot az előfizetések között?  

Részletekért lásd: [Media Services fiók áthelyezése az előfizetések között](media-services-account-concept.md).

## <a name="live-streaming"></a>Live streaming (Élő adatfolyam) 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Hogyan leállítani az élő streamet a közvetítés befejezése után?

Az ügyfél oldaláról vagy a kiszolgáló oldaláról is megközelítheti.

#### <a name="client-side"></a>Ügyféloldali

A webalkalmazásnak meg kell kérnie a felhasználót, ha a böngésző bezárásakor szeretné lezárni a szórást. Ez egy böngészőbeli esemény, amelyet a webalkalmazás képes kezelni.

#### <a name="server-side"></a>Kiszolgálóoldali

Az élő eseményeket a Azure Event Grid eseményekre való feliratkozással figyelheti. További információ: [EventGrid esemény sémája](media-services-event-schemas.md#live-event-types).

A következő lehetőségek közül választhat:

* [Fizessen elő](reacting-to-media-services-events.md) a [Microsoft. Media. LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) eseményeire, és figyelje meg, hogy az élő esemény leállításához és törléséhez nem érkezik Újrakapcsolódás.
* [Fizessen elő](reacting-to-media-services-events.md) a követési szintű [szívverési](media-services-event-schemas.md#liveeventingestheartbeat) eseményekre. Ha az összes pályán van egy bejövő bitráta 0 vagy az utolsó időbélyeg már nem növekszik, nyugodtan leállíthatja az élő eseményt. A szívverési események minden egyes pályán 20 másodpercenként érkeznek, így lehet, hogy ez egy kicsit részletes.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Hogyan helyezzen el szüneteket/videókat és képbeszúrásokat egy élő adatfolyamban?

Media Services v3 élő kódolás még nem támogatja a videó vagy a képek beszúrását az élő adatfolyamban. 

A forrás videó átváltásához használhat egy [élő helyszíni kódolót](recommended-on-premises-live-encoders.md) . Számos alkalmazás biztosítja a források váltását, beleértve a Wirecast, a Switching Studio (iOS) és a OBS Studio (ingyenes alkalmazás) lehetőséget.

## <a name="content-protection"></a>Tartalomvédelem

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Az AES-128 titkosítatlan kulcsú titkosítást vagy DRM-rendszereket használok?

Az ügyfelek gyakran kíváncsiak arra, hogy az AES-titkosítást vagy a DRM-rendszereket használják-e. A két rendszer közötti fő különbség az, hogy az AES-titkosítás, a tartalmi kulcs továbbítása az ügyfélnek a TLS protokollon keresztül történik, így a kulcs továbbítása átvitel alatt áll, de további titkosítás nélkül ("tiszta"). Ennek eredményeképpen a tartalom visszafejtéséhez használt kulcs elérhető az ügyfél-lejátszó számára, és az ügyfélen lévő hálózati nyomkövetésben is megtekinthető egyszerű szövegként. AES-128 – a titkosítatlan kulcsos titkosítás olyan használati esetekben használható, ahol a megjelenítő egy megbízható fél (például a vállalaton belül az alkalmazottak által megtekintett vállalati videók titkosítása).

A (z) PlayReady, Widevine és FairPlay DRM-rendszerek mindegyike további titkosítási szintet biztosít a tartalom visszafejtéséhez használt kulcshoz képest, egy AES-128 tiszta kulccsal szemben. A tartalmi kulcs titkosítása a DRM-futtatókörnyezet által védett kulcsra történik, a TLS által biztosított átviteli szintű titkosítás mellett. Emellett az operációs rendszer szintjén egy biztonságos környezetben kezeli a visszafejtést, ahol a rosszindulatú felhasználók támadása nehezebbé válik. Javasoljuk, hogy a DRM olyan használati eseteket használjon, amelyekben előfordulhat, hogy a megjelenítő nem megbízható fél, és a legmagasabb szintű biztonságra van szüksége.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hogyan csak bizonyos engedélyekkel rendelkező felhasználók számára jelenít meg videót az Azure AD használata nélkül?

Nem kell semmilyen speciális jogkivonat-szolgáltatót használnia, például Azure Active Directory (Azure AD). Az aszimmetrikus kulcsú titkosítás használatával saját [JWT](https://jwt.io/) -szolgáltatót (úgynevezett biztonságos jogkivonat-szolgáltatást vagy STS-t) is létrehozhat. Az egyéni STS-ben az üzleti logikája alapján adhat hozzá jogcímeket.

Győződjön meg arról, hogy a kibocsátó, a célközönség és a jogcímek pontosan egyeznek a JWT és a `ContentKeyPolicyRestriction` által használt érték között `ContentKeyPolicy` .

További információ: [a tartalom Media Services dinamikus titkosítással való védelemmel](content-protection-overview.md)való ellátása.

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Hogyan és hol kapok JWT tokent, mielőtt felhasználom egy licenc vagy kulcs igénylésére?

Éles környezetben a biztonságos jogkivonat-szolgáltatást (azaz egy webszolgáltatást) kell használnia, amely JWT-jogkivonatot ad ki egy HTTPS-kérelem alapján. A teszthez használhatja a `GetTokenAsync` [program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)-ben definiált metódusban megjelenő kódot.

A lejátszó kérést küld egy felhasználó hitelesítése után az STS számára egy ilyen jogkivonat számára, és hozzárendeli azt a jogkivonat értékeként. Használhatja a [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)-t.

Az STS szimmetrikus kulccsal vagy aszimmetrikus kulccsal való futtatására példát a [JWT eszközben](https://aka.ms/jwt)talál. Az JWT-tokent használó Azure Media Player-alapú játékosok esetében tekintse meg az [Azure Media test Tool eszközt](https://aka.ms/amtest). (A jogkivonat bemenetének megtekintéséhez bontsa ki a **player_settings** hivatkozást.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hogyan engedélyezi a videók AES-titkosítással való továbbítását?

A helyes módszer a biztonságos jogkivonat-szolgáltatás használata. Az STS-ben a felhasználói profiltól függően adjon hozzá különböző jogcímeket (például "prémium szintű felhasználó", "alapszintű felhasználó", ingyenes próbaverziós felhasználó). A JWT eltérő jogcímek esetén a felhasználó különböző tartalmakat láthat. A különböző tartalmakhoz és eszközökhöz `ContentKeyPolicyRestriction` a megfelelő `RequiredClaims` érték lesz.

Azure Media Services API-k használatával konfigurálhatja a licencek/kulcsok küldését és titkosíthatja az adategységeket (ahogy az [ebben a mintában](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)is látható).

További információkért lásd:

- [Tartalomvédelem – áttekintés](content-protection-overview.md)
- [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>HTTP vagy HTTPS protokollt használok?
A ASP.NET MVC Player alkalmazásnak a következőket kell támogatnia:

* Felhasználói hitelesítés az Azure AD-n keresztül, amely a HTTPS alatt található.
* JWT az ügyfél és az Azure AD között, amely HTTPS-alapú.
* Az ügyfél által megvásárolt DRM-licenc, amelynek a HTTPS alatt kell lennie, ha a Media Services biztosít a licencek kézbesítését. A PlayReady termék csomagja nem kötelezi a HTTPS protokoll megadását a licencek kézbesítéséhez. Ha a PlayReady-licenckiszolgáló kívül esik Media Serviceson, HTTP vagy HTTPS protokollt is használhat.

A ASP.NET Player alkalmazás a HTTPS protokollt használja ajánlott eljárásként, így Media Player a HTTPS alatt található oldalon. A HTTP azonban a streaming számára ajánlott, ezért ezeket a problémákat vegyes tartalommal kell figyelembe vennie:

* A böngésző nem engedélyezi a kevert tartalmat. A beépülő modulok (például a Silverlight és a OSMF beépülő modul) a zökkenőmentes és a kötőjel esetében is lehetővé teszik. A vegyes tartalom biztonsági problémát jelent, mivel a kártékony JavaScript beadásának lehetősége veszélyezteti a vásárlói adatokat. A böngészők alapértelmezés szerint blokkolja ezt a funkciót. Az egyetlen módja annak, hogy megkerülje a kiszolgálót (forrás) az összes tartomány (a HTTPS vagy HTTP protokolltól függetlenül) engedélyezésével. Ez valószínűleg nem jó ötlet.
* Kerülje a kevert tartalmat. A Player alkalmazásnak és a Media Playernek egyaránt HTTP vagy HTTPS protokollt kell használnia. Ha vegyes tartalmat játszik, a Silverlight technológiához vegyes tartalmú figyelmeztetést kell törölni. A Flashs Tech vegyes tartalmú figyelmeztetés nélkül kezeli a kevert tartalmakat.
* Ha a folyamatos átviteli végpontot 2014 augusztusa előtt hozták létre, nem támogatja a HTTPS protokollt. Ebben az esetben hozzon létre és használjon egy új streaming-végpontot a HTTPS-hez.

### <a name="what-about-live-streaming"></a>Mi a helyzet az élő közvetítéssel?

Pontosan ugyanazt a kialakítást és megvalósítást használhatja a Media Services élő közvetítésének védelméhez azáltal, hogy a programhoz a VOD-eszközként társított eszközt kezeli. Az élő tartalom több DRM elleni védelmének biztosításához alkalmazza ugyanazt a telepítést/feldolgozást az eszközre, mintha egy VOD-eszköz lenne, mielőtt az eszközt az élő kimenethez társítja.

### <a name="what-about-license-servers-outside-media-services"></a>Mi a teendő a Media Serviceson kívüli licenckiszolgálóra?

Az ügyfelek gyakran a saját adatközpontjában vagy a DRM-szolgáltatók által üzemeltetett licencelési kiszolgálófarm egyik farmján fektettek be. A Media Services Content Protection használatával hibrid módban is működhet. A tartalom üzemeltethető és dinamikusan védhető Media Servicesban, míg a DRM-licenceket a Media Serviceson kívüli kiszolgálók is továbbítják. Ebben az esetben vegye figyelembe a következő változásokat:

* Az STS-nek olyan jogkivonatokat kell kiállítania, amelyek elfogadhatók, és a licenckiszolgáló-Farm ellenőrizheti őket. A Axinom által biztosított Widevine-licenckiszolgálók például egy adott JWT igényelnek, amely jogosultsági üzenetet tartalmaz. Egy ilyen JWT kibocsátásához STS szükséges. 
* Már nem kell konfigurálnia a Media Services licenc kézbesítési szolgáltatását. A konfiguráláskor meg kell adnia a licenc-beszerzési URL-címeket (a PlayReady, a Widevine és a FairPlay) `ContentKeyPolicy` .

> [!NOTE]
> A Widevine a Google által biztosított szolgáltatás, amely a Google használati feltételeinek és adatvédelmi szabályzatának hatálya alá tartozik.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 és v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Használhatom a Azure Portal a v3-erőforrások kezeléséhez?

Jelenleg a [Azure Portal](https://portal.azure.com/) a következőket használhatja:

* [Élő események](live-events-outputs-concept.md) kezelése Media Services v3-ban. 
* V3- [eszközök](assets-concept.md)megtekintése (nem felügyelt). 
* [Az API-k elérésére vonatkozó információk beolvasása](./access-api-howto.md). 

Az összes többi felügyeleti feladathoz (például [átalakításokhoz, feladatokhoz](transforms-jobs-concept.md) és [tartalmak védelméhez](content-protection-overview.md)) használja a [REST API](/rest/api/media/), az [Azure CLI](/cli/azure/ams)vagy az egyik támogatott [SDK](media-services-apis-overview.md#sdks)-t.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Létezik AssetFile-koncepció a v3-as verzióban?

A `AssetFile` koncepció el lett távolítva a Media Services API-ból, hogy elkülönítse Media Services a Storage SDK-függőségtől. Az Azure Storage már nem Media Services, a Storage SDK-ban található információkat is megőrzi. 

További információ: [migrálás Media Services v3](migrate-v-2-v-3-migration-introduction.md)-ra.

### <a name="where-did-client-side-storage-encryption-go"></a>Hol található az ügyféloldali tároló-titkosítás?

Javasoljuk, hogy kiszolgálóoldali tároló-titkosítást használjon (amely alapértelmezés szerint be van kapcsolva). További információ: [Azure Storage Service encryption for](../../storage/common/storage-service-encryption.md)inaktív adatok.

## <a name="offline-streaming"></a>Offline adatfolyam

### <a name="fairplay-streaming-for-ios"></a>FairPlay streaming iOS-hez

A következő gyakori kérdések segítséget nyújtanak az iOS-hez készült offline FairPlay streaming hibaelhárításához.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Miért csak a hang lejátszása, de a videó nem érhető el offline módban?

Ez a viselkedés úgy tűnik, hogy a minta alkalmazás megtervezése. Ha egy másodlagos hangsáv van jelen (amely a HLS esetében) az offline módban, az iOS 10 és az iOS 11 alapértelmezett értéke a másodlagos hangsávoknak. Ha kompenzálni szeretné ezt a viselkedést az FPS kapcsolat nélküli módban, távolítsa el a másodlagos hangsávot az adatfolyamból. Ha ezt Media Services szeretné elvégezni, adja hozzá a dinamikus jegyzékfájl szűrője **csak hang = hamis értéket**. Más szóval a HLS URL-címe a **következővel végződik:. ISM/manifest (Format = m3u8-AAPL, csak hang = hamis)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>A csak hang hozzáadása után miért is csak a videó lejátszása offline módban

A Content Delivery Network gyorsítótár-kulcsának kialakítástól függően előfordulhat, hogy a tartalom gyorsítótárazva van. A gyorsítótár kiürítése.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Támogatott-e az FPS-kapcsolat nélküli mód az iOS 11 rendszeren az iOS 10 mellett?

Igen. Az FPS kapcsolat nélküli üzemmód az iOS 10 és az iOS 11 esetében támogatott.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Miért nem találom a "kapcsolat nélküli lejátszás az FairPlay Streamingtel és a HTTP Live Streaming" dokumentumot az FPS Server SDK-ban?

Az FPS Server SDK 4-es verziója óta ez a dokumentum a "FairPlay streaming programozási útmutatójában" lett egyesítve.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Mi a letöltött/offline fájl szerkezete iOS-eszközökön?

Az iOS-eszközön a letöltött fájl szerkezete a következő képernyőképre hasonlít. A `_keys` mappa a letöltött fps-licenceket egy áruházbeli fájllal tárolja az egyes licencelési szolgáltatások gazdagépei számára. A `.movpkg` mappa tárolja a hang-és video-tartalmakat. 

Az első mappa, amelynek a neve szaggatott vonallal végződik, majd egy szám, amely a videó tartalmát tartalmazza. A numerikus érték a videó-kiadatások maximális sávszélessége. A második mappa, amelynek neve kötőjel, majd 0, hanganyagot tartalmaz. A nevű harmadik mappa `Data` tartalmazza az fps-tartalom fő lejátszási listáját. Végezetül boot.xml a mappa tartalmának teljes leírását tartalmazza `.movpkg` . 

![Offline FairPlay iOS-minta alkalmazáshoz](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Példa boot.xml fájlra:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Widevine streaming Android rendszerhez

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Hogyan biztosíthatok állandó licenceket (offline) az egyes ügyfelek/felhasználók és a nem állandó licencek (offline letiltva) számára mások számára? Duplikálni kell a tartalmat, és külön tartalmi kulcsokat kell használniuk?

Mivel a Media Services v3 lehetővé teszi, hogy egy eszköz több `StreamingLocator` példányban is legyen, a következőket teheti:

* Egy példány, a következővel: `ContentKeyPolicy` `license_type = "persistent"` , `ContentKeyPolicyRestriction` jogcím: `"persistent"` , `StreamingLocator` .
* Egy másik példány, amely a (z) és a (z `ContentKeyPolicy` `license_type="nonpersistent"` `ContentKeyPolicyRestriction` ) jogcímevel rendelkezik `"nonpersistent` `StreamingLocator` .
* Két `StreamingLocator` példány, amelyek eltérő `ContentKey` értékekkel rendelkeznek.

Az egyéni STS üzleti logikától függően a rendszer különböző jogcímeket ad ki a JWT-jogkivonatban. A jogkivonattal csak a megfelelő licenc szerezhető be, és csak a megfelelő URL-cím játszható le.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Mi a Widevine és a Media Services DRM biztonsági szintek közötti leképezés?

A Google "Widevine DRM-architektúra áttekintése" három biztonsági szintet határoz meg. A Widevine- [licenc sablonjának Azure Media Services dokumentációja](widevine-license-template-overview.md) azonban öt biztonsági szintet vázol fel (a lejátszási ügyfél megbízhatósági követelményei). Ez a szakasz a biztonsági szintek leképezését ismerteti.

A Google Widevine a biztonsági szintek mindkét készletét definiálja. A különbség a használati szint: architektúra vagy API. Az öt biztonsági szint a Widevine API-ban használatos. A `content_key_specs` -t tartalmazó objektumot a `security_level` rendszer deszerializálja, és átadja a Widevine globális kézbesítési szolgáltatásnak a Azure Media Services Widevine License Service. A következő táblázat a két biztonsági szint közötti leképezést mutatja be.

| **A Widevine architektúrában definiált biztonsági szintek** |**A Widevine API-ban használt biztonsági szintek**|
|---|---| 
| **1. biztonsági szint**: a rendszer az összes tartalom feldolgozását, titkosítását és ellenőrzését a megbízható végrehajtási környezetben (Tee) hajtja végre. Egyes implementációs modellekben a biztonsági feldolgozás különböző chipeken végezhető el.|**security_level = 5**: a titkosítást, a dekódolást és az adathordozó összes kezelését (tömörített és tömörítetlen) egy HARDVERes pólón belül kell kezelni.<br/><br/>**security_level = 4**: a tartalom titkosítását és dekódolását HARDVERes pólón belül kell végrehajtani.|
**2. biztonsági szint**: a titkosítás (de nem a videó feldolgozása) a pólón belül történik. A visszafejtett pufferek visszakerülnek az alkalmazás-tartományba, és külön videó hardveren vagy szoftveren keresztül dolgozzák fel azokat. A 2. szinten azonban a titkosítási adatok feldolgozása még csak a PÓLÓn belül történik.| **security_level = 3**: a kulcsfontosságú anyagokat és titkosítási műveleteket egy HARDVERes pólón belül kell végrehajtani. |
| **3. biztonsági szint**: nincs póló az eszközön. A titkosítási információk és a visszafejtett tartalmak a gazdagép operációs rendszerén való ellátásához megfelelő mértékeket kell végrehajtani. A 3. szintű implementációk hardveres titkosítási motort is tartalmazhatnak, de ez csak a teljesítményt javítja, nem pedig a biztonságot. | **security_level = 2**: a szoftveres titkosítás és a megzavarodott dekóder szükséges.<br/><br/>**security_level = 1**: a szoftveres, fehér mezős titkosítás szükséges.|

#### <a name="why-does-content-download-take-so-long"></a>Miért tart ilyen sokáig a tartalom letöltése?

A letöltési sebesség javítása kétféleképpen lehetséges:

* Engedélyezheti a Content Delivery networkt, hogy a felhasználók nagyobb valószínűséggel elérjenek, hogy a tartalom letöltéséhez a forrás/streaming végpont helyett. Ha a felhasználó egy folyamatos átviteli végpontot talál, az egyes HLS-szegmensek és-GONDOLATJELek töredékei dinamikusan vannak csomagolva és titkosítva. Bár ez a késés az egyes szegmensekhez vagy töredékekhez tartozó ezredmásodperces skálán van, ha egy órás videóval rendelkezik, a halmozott késés nagy lehet, és hosszabb letöltést eredményezhet.
* Lehetővé teszi a felhasználóknak a minőségi rétegek és hangsávok szelektív letöltését az összes tartalom helyett. Kapcsolat nélküli módban az összes minőségi réteget nem lehet letölteni. A következő két módon valósítható meg:

  * Ügyfél által vezérelt: a lejátszó alkalmazás automatikusan kiválasztja, vagy a felhasználó kiválasztja a videó minőségi réteget és a letöltendő hangsávokat.
  * Felügyelt szolgáltatás: a Azure Media Services dinamikus jegyzékfájl funkciójának használatával létrehozhat egy (globális) szűrőt, amely korlátozza a HLS lejátszási lista vagy kötőjel MPD használatát egyetlen videó minőségi rétegre és kiválasztott hangsávokra. Ezután a felhasználók számára megjelenített letöltési URL-cím tartalmazza ezt a szűrőt.

## <a name="next-steps"></a>Következő lépések

[Media Services v3 – áttekintés](media-services-overview.md)