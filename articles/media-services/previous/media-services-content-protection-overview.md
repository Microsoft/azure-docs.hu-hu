---
title: A tartalom Azure Media Servicessal való védelemmel való ellátása | Microsoft Docs
description: Ez a cikk áttekintést nyújt a tartalomvédelem Azure Media Services v2-vel történő védelméről.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: edffa2dddd0ec877a4b825a69a76fb158928c89f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016763"
---
# <a name="content-protection-overview"></a>Tartalomvédelem – áttekintés

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A Azure Media Services használatával biztonságossá teheti az adathordozót, amikor a számítógépét tárolás, feldolgozás és kézbesítés útján elhagyja. A Media Services használatával dinamikusan titkosíthatja az élő és igény szerinti tartalmat Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (DRM) rendszerből: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services egy szolgáltatást is biztosít az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licencek továbbítására a hitelesítő ügyfelek számára. 

Az alábbi ábra a Media Services tartalomvédelem munkafolyamatát mutatja be: 

![Védelem biztosítása a PlayReadyvel](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Ez a cikk ismerteti a Media Servicesokkal való tartalomvédelem megismeréséhez kapcsolódó fogalmakat és terminológiát. A cikk a tartalmak védelemmel kapcsolatos cikkeire mutató hivatkozásokat is tartalmaz. 

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A Media Services használatával a tartalmakat a PlayReady, Widevine vagy FairPlay használatával dinamikusan titkosíthatja az AES Clear Key vagy a DRM titkosításával. Ha a tartalom titkosítása AES Clear kulccsal történik, és HTTPS-kapcsolaton keresztül történik, nem egyértelmű, amíg el nem éri az ügyfelet. 

Minden titkosítási módszer a következő folyamatos átviteli protokollokat támogatja:
 
- AES: MPEG-DASH, Smooth Streaming és HLS
- PlayReady: MPEG-DASH, Smooth Streaming és HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

A progresszív letöltések titkosítása nem támogatott. 

Az adategységek titkosításához hozzá kell rendelnie egy titkosítási tartalom kulcsát az eszközhöz, és konfigurálnia kell a kulcshoz tartozó engedélyezési házirendet is. A tartalmi kulcsok megadhatók, vagy a Media Services automatikusan létrehozhatók.

Az eszköz kézbesítési házirendjét is konfigurálnia kell. Ha egy Storage-titkosítású eszközt szeretne továbbítani, mindenképpen adja meg, hogyan szeretné kézbesíteni az eszköz kézbesítési házirendjét.

Ha egy lejátszó egy adatfolyamot kér, Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat az AES Clear Key vagy a DRM titkosítás használatával. Az adatfolyam visszafejtéséhez a Player Media Services Key Delivery Service-ből kéri a kulcsot. Annak eldöntéséhez, hogy a felhasználó jogosult-e a kulcs lekérésére, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 Clear Key és DRM
Az ügyfelek gyakran kíváncsiak arra, hogy az AES-titkosítást vagy a DRM-rendszereket használják-e. A két rendszer közötti elsődleges különbség az, hogy az AES-titkosítás a tartalmi kulcsot titkosítatlan formában továbbítja az ügyfélnek ("egyértelmű"). Ennek eredményeképpen a tartalom titkosításához használt kulcs megtekinthető az ügyfél egy egyszerű szövegben lévő hálózati nyomkövetésében. AES-128 – a titkosítatlan kulcsos titkosítás olyan használati esetekben használható, ahol a megjelenítő egy megbízható fél (például a vállalaton belül az alkalmazottak által megtekintett vállalati videók titkosítása).

A PlayReady, a Widevine és a FairPlay minden nagyobb titkosítási szintet biztosít az AES-128-titkosításhoz képest. A tartalmi kulcsot titkosított formában továbbítja a rendszer. Emellett az operációs rendszer szintjén egy biztonságos környezetben kezeli a visszafejtést, ahol a rosszindulatú felhasználók támadása nehezebbé válik. A DRM használata olyan esetekben ajánlott, amikor a megjelenítő nem megbízható fél, és Önnek a legmagasabb szintű biztonságra van szüksége.

## <a name="storage-encryption"></a>Storage-titkosítás
A tárterület titkosításával titkosíthatja a tartalmakat helyileg az AES 256 bites titkosítás használatával. Ezután feltöltheti az Azure Storage-ba, ahol titkosított állapotban tárolja. A tároló titkosításával védett eszközök automatikusan titkosítva lesznek, és a kódolás előtt titkosított fájlrendszerbe kerülnek. Az eszközök opcionálisan újra titkosítva lesznek új kimeneti eszközként való feltöltés előtt. A tárolás titkosításának elsődleges használati esete, ha a magas színvonalú bemeneti médiafájlokat erős titkosítással szeretné biztonságossá tenni a lemezen.

A Storage-titkosítású adategységek kézbesítéséhez konfigurálnia kell az eszköz kézbesítési házirendjét, hogy Media Services tudja, hogyan szeretné kézbesíteni a tartalmat. Az eszköz adatfolyamként történő továbbítása előtt a streaming kiszolgáló visszafejti és továbbítja a tartalmat a megadott kézbesítési házirend (például AES, Common encryption vagy nincs titkosítás) használatával.

## <a name="types-of-encryption"></a>Titkosítási típusok
A PlayReady és a Widevine általános titkosítást (AES CTR-módot) használ. A FairPlay AES CBC-módú titkosítást használ. AES-128 – a titkosítatlan kulcs titkosítása boríték-titkosítást használ.

## <a name="licenses-and-keys-delivery-service"></a>Licencek és kulcsok kézbesítési szolgáltatása
A Media Services kulcsfontosságú kézbesítési szolgáltatást biztosít a DRM-(PlayReady-, Widevine-, FairPlay-) licencek és AES-kulcsok kézbesítéséhez a hitelesítő ügyfelek számára. A .NET-hez készült [Azure Portal](media-services-portal-protect-content.md), a REST API vagy a Media Services SDK használatával konfigurálhatja a licencekhez és a kulcsokhoz tartozó engedélyezési és hitelesítési házirendeket.

## <a name="control-content-access"></a>Tartalom-hozzáférés szabályozása
Szabályozhatja, hogy ki férhet hozzá a tartalomhoz a tartalmi kulcs engedélyezési házirendjének konfigurálásával. A tartalmi kulcs engedélyezési házirendje támogatja a nyitott vagy a jogkivonat korlátozását.

### <a name="open-authorization"></a>Engedély megnyitása
Egy nyitott engedélyezési házirenddel a rendszer minden ügyfélnek (korlátozás nélkül) elküldi a tartalmi kulcsot.

### <a name="token-authorization"></a>Jogkivonat-hitelesítés
A jogkivonat-korlátozott engedélyezési házirend használatával a rendszer csak olyan ügyfél számára küldi el a tartalmat, amely érvényes JSON Web Token (JWT) vagy egyszerű webes tokent (SWT) jelenít meg a kulcs/licenc kérésben. Ezt a jogkivonatot egy biztonságijogkivonat-szolgáltatásnak (STS) kell kiállítania. A Azure Active Directoryt STS-ként vagy egyéni STS üzembe helyezésével is használhatja. Az STS-t úgy kell konfigurálni, hogy a megadott kulccsal aláírt tokent hozzon létre, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket. A Media Services Key Delivery Service visszaadja a kért kulcsot/licencet az ügyfélnek, ha a jogkivonat érvényes, és a jogkivonatban lévő jogcímek megegyeznek a kulcshoz/licenccel konfigurált jogcímekkel.

A jogkivonat korlátozott házirendjének konfigurálásakor meg kell adnia az elsődleges ellenőrző kulcsot, a kiállítót és a célközönség paramétereit. Az elsődleges ellenőrző kulcs tartalmazza azt a kulcsot, amelyet a jogkivonat aláírt. A kiállító az a biztonságos jogkivonat-szolgáltatás, amely kiadja a jogkivonatot. A célközönség, más néven hatókör, leírja a jogkivonat célját vagy azt az erőforrást, amelyet a jogkivonat engedélyez a hozzáféréshez. A Media Services Key Delivery Service ellenőrzi, hogy a jogkivonat értékei egyeznek-e a sablon értékeivel.

### <a name="token-replay-prevention"></a>Jogkivonat-újrajátszás megelőzése

A *jogkivonat-Visszajátszások megelőzési* funkciója lehetővé teszi, hogy Media Services ügyfelek megszabják, hogy egy adott jogkivonat hányszor használható kulcs vagy licenc igénylésére. Az ügyfél hozzáadhat egy típusú jogcímet `urn:microsoft:azure:mediaservices:maxuses` a jogkivonathoz, ahol az érték az a szám, ahányszor a jogkivonat használható licenc vagy kulcs beszerzéséhez. Az ugyanazzal a jogkivonattal rendelkező összes további kérelem nem engedélyezett választ ad vissza. Lásd: a jogcím hozzáadása a DRM- [mintában](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Megfontolandó szempontok

* Az ügyfeleknek meg kell határoznia a jogkivonat-generálást. A jogcímet maga a jogkivonatban kell elhelyezni.
* A szolgáltatás használatakor a rendszer nem engedélyezett válaszként utasítja el azokat a jogkivonatokat, amelyek lejárati ideje meghaladja a kérés fogadásának idejét.
* A jogkivonatokat az aláírásuk egyedileg azonosítja. A hasznos adatok (például a lejárati idő vagy a jogcím frissítése) változása megváltoztatja a jogkivonat aláírását, és új tokenként fog megjelenni, amely a kulcs kézbesítése előtt nem érkezett meg.
* A lejátszás meghiúsul, ha a jogkivonat túllépte az `maxuses` ügyfél által beállított értéket.
* Ez a szolgáltatás az összes meglévő védett tartalomhoz használható (csak a kiállított jogkivonat módosítására van szükség).
* Ez a szolgáltatás a JWT és a SWT egyaránt működik.

## <a name="streaming-urls"></a>Streaming URL-címek
Ha az eszköz több DRM-mel lett titkosítva, használjon titkosítási címkét a streaming URL-címében: (Format = 'm 3u8-AAPL ', encryption = ' xxx ').

A következő szempontokat kell figyelembe venni:

* Legfeljebb egy titkosítási típus adható meg.
* Az URL-címben nem kell megadni a titkosítási típust, ha csak egy titkosítás lett alkalmazva az eszközre.
* A titkosítási típus a kis-és nagybetűk megkülönböztetése.
* A következő titkosítási típusokat lehet megadni:

  * **Cenc**: PlayReady vagy Widevine (általános titkosítás)
  * **CBCS-AAPL**: a FAIRPLAY (AES CBC Encryption) esetében
  * **CBC**: AES-borítékok titkosítása

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések
A következő cikkek a tartalomvédelem megkezdéséhez szükséges lépéseket ismertetik:

* [Védelem a Storage encryption szolgáltatással](media-services-rest-storage-encryption.md)
* [Védelem AES-titkosítással](media-services-protect-with-aes128.md)
* [Védelem PlayReady és/vagy Widevine](media-services-protect-with-playready-widevine.md)
* [Védelem a FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások

* [JWT jogkivonat-hitelesítés](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Azure Media Services OWIN MVC-alapú alkalmazás integrálása Azure Active Directory és a JWT-jogcímek alapján történő kézbesítés korlátozása](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
