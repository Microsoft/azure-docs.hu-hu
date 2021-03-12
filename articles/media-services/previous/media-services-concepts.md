---
title: Azure Media Services fogalmak | Microsoft Docs
description: Ez a cikk rövid áttekintést nyújt a Microsoft Azure Media Services fogalmakról, és további részletekre mutató hivatkozásokat tartalmaz.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: fbe387b3f86dfd10cbc4e80a02ee2528e43c4b67
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016882"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services fogalmak

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Ez a témakör áttekintést nyújt a legfontosabb Media Services fogalmakról.

## <a name="assets-and-storage"></a><a name="assets"></a>Eszközök és tároló
### <a name="assets"></a>Objektumok
Az [eszközök](/rest/api/media/operations/asset) digitális fájlokat (például videó, hang, képek, miniatűr gyűjtemények, szöveges számok és zárt feliratú fájlok) és a fájlokra vonatkozó metaadatokat tartalmaznak. A digitális fájlok egy objektumba való feltöltése után a rendszer a Media Services kódolási és folyamatos átviteli munkafolyamatokban is felhasználhatja őket.

Az eszköz az Azure Storage-fiókban található blob-tárolóra van leképezve, és az adategységben található fájlok a tárolóban blokk blobként tárolódnak. Azure Media Services nem támogatja az oldal blobokat.

Amikor eldönti, hogy milyen médiatartalom feltöltésére és tárolására van egy eszközön, a következő szempontokat kell figyelembe venni:

* Egy eszköz csak egyetlen, egyedi médiatartalom-példányt tartalmazhat. Például egy TV-epizód, film vagy hirdetmény egyetlen szerkesztése.
* Egy eszköz nem tartalmazhat egy audiovizuális fájl több kiadatását vagy szerkesztését. Egy adott eszköz nem megfelelő használatának egyik példája, hogy több TV-epizódot, hirdetményt vagy több kamera látószögét szeretné tárolni egy adott eszközön belül. Ha egy eszközön több kiadatást vagy módosítást tárol, akkor nehézségeket okozhat a kódolási feladatok elküldése, a folyamatos átvitel és az eszköznek a munkafolyamatban történő kézbesítésének biztonságossá tétele.  

### <a name="asset-file"></a>Eszköz fájlja
A [AssetFile](/rest/api/media/operations/assetfile) a blob-tárolóban tárolt tényleges video-vagy hangfájlt jelöli. Az adategységek mindig egy adott objektumhoz vannak társítva, és egy adott eszköz egy vagy több fájlt is tartalmazhat. A Media Services Encoder feladat meghiúsul, ha egy objektum nem egy blob-tárolóban lévő digitális fájllal van társítva.

A **AssetFile** példány és a tényleges médiafájl két különálló objektum. A AssetFile-példány metaadatokat tartalmaz a médiafájlról, míg a médiafájl tartalmazza a tényleges médiatartalom tartalmát.

Ne próbálja meg módosítani a Media Services által generált blob-tárolók tartalmát a Media Service API-k használata nélkül.

### <a name="asset-encryption-options"></a>Eszköz titkosítási beállításai
A feltölteni, tárolni és kézbesíteni kívánt tartalom típusától függően Media Services különböző titkosítási lehetőségeket biztosít, amelyek közül választhat.

>[!NOTE]
>Nincs használatban titkosítás. Ez az alapértelmezett érték. Ha ezt a beállítást használja, a tartalmat a rendszer nem védi az átvitelben vagy a tárolás során.

Ha az MP4-t a progresszív letöltés használatával tervezi, akkor a tartalom feltöltéséhez használja ezt a lehetőséget.

**StorageEncrypted** – ezzel a beállítással titkosíthatja a tartalmakat HELYILEG az AES 256 bites titkosítással, majd feltöltheti azt az Azure Storage-ba, ahol a titkosított állapotban van tárolva. A Storage encryption szolgáltatással védett eszközök titkosítása automatikusan titkosítva történik, és a kódolás előtt titkosított fájlrendszerbe kerül, és szükség esetén újra titkosítva lesz, mielőtt új kimeneti eszközként feltölti őket. A tárolás titkosításának elsődleges használati esete, ha a magas színvonalú bemeneti médiafájlokat erős titkosítással szeretné védeni a lemezen. 

A titkosított adategységek kézbesítéséhez konfigurálnia kell az eszköz kézbesítési házirendjét, hogy Media Services tudja, hogyan szeretné kézbesíteni a tartalmat. Az eszköz adatfolyamként való továbbítása előtt a streaming kiszolgáló eltávolítja a tárolási titkosítást, és a megadott kézbesítési házirenddel (például AES, PlayReady vagy nincs titkosítás) továbbítja a tartalmat. 

**CommonEncryptionProtected** – ezt a lehetőséget akkor használja, ha Common encryption vagy PlayReady DRM-mel (Smooth streaming például a PlayReady DRM-mel védett védelemmel) szeretné titkosítani (vagy feltölteni a már titkosított) tartalmakat.

**EnvelopeEncryptionProtected** – ezt a beállítást akkor használja, ha a (vagy a már védett) http Live Streaming (HLS) Advanced Encryption Standard (AES) titkosítással kívánja védeni. Ha az AES-titkosítással már titkosított HLS tölt fel, akkor azt az átalakító kezelőjének kell titkosítania.

### <a name="access-policy"></a>Hozzáférési szabályzat
A [AccessPolicy](/rest/api/media/operations/accesspolicy) az engedélyeket (például olvasás, írás és Listázás) és az adott eszközhöz való hozzáférés időtartamát határozzák meg. Általában egy AccessPolicy objektumot kell átadnia egy olyan lokátorhoz, amely egy adott objektumban található fájlok elérésére szolgál.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

### <a name="blob-container"></a>Blobtároló
A blob-tároló Blobok egy csoportját biztosítja. A blob-tárolók a hozzáférés-vezérléshez és az eszközökön a közös hozzáférésű aláírási (SAS-) lokátorok határának Media Services használatosak. Egy Azure Storage-fiók korlátlan számú BLOB-tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható.

>[!NOTE]
> Ne próbálja meg módosítani a Media Services által generált blob-tárolók tartalmát a Media Service API-k használata nélkül.
> 
> 

### <a name="locators"></a><a name="locators"></a>Keresők
A [lokátor](/rest/api/media/operations/locator)megadhat egy belépési pontot az adott objektumban található fájlok eléréséhez. Hozzáférési szabályzattal határozható meg az engedélyek és az időtartam, ameddig az ügyfél hozzáfér egy adott eszközhöz. A lokátorok több kapcsolattal rendelkezhetnek egy hozzáférési házirenddel, így a különböző lokátorok különböző indítási időpontokat és kapcsolódási típusokat biztosíthatnak különböző ügyfelekhez, miközben ugyanazt az engedélyt és időtartamot használják. azonban az Azure Storage-szolgáltatások által meghatározott közös hozzáférésű házirend korlátozása miatt egyszerre legfeljebb öt egyedi lokátor társítható egy adott eszközhöz. 

A Media Services kétféle lokátort támogat: a OnDemandOrigin-lokátorokat (például MPEG DASH, HLS vagy Smooth Streaming), vagy fokozatosan letöltheti az adathordozókat és SAS URL-lokátorokat, amelyek az Azure Storage-to\from tölthetők fel vagy tölthetők le. 

>[!NOTE]
>OnDemandOrigin-lokátor létrehozásakor a List engedély (AccessPermissions. list) nem használható. 

### <a name="storage-account"></a>A(z)
Az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik. A Media Service-fiókok egy vagy több Storage-fiókkal is társíthatók. Egy fiók korlátlan számú tárolót tartalmazhat, feltéve, hogy a teljes méretük 500TB alatt van.  A Media Services SDK-szintű eszközöket biztosít, amelyekkel több Storage-fiókot kezelhet, és terheléselosztást végez az adategységek elosztása során, a metrikák és a véletlenszerű eloszlás alapján. További információ: az [Azure Storage](/previous-versions/azure/dn767951(v=azure.100))használata. 

## <a name="jobs-and-tasks"></a>Feladatok és tevékenységek
A [feladatok](/rest/api/media/operations/job) általában egy hang-vagy videó-bemutató feldolgozására (például indexre vagy kódolásra) használatosak. Ha több videót dolgoz fel, hozzon létre egy feladatot minden egyes videó kódolásához.

A feladatok a végrehajtandó feldolgozással kapcsolatos metaadatokat tartalmaznak. Minden feladat egy vagy több olyan [feladatot](/rest/api/media/operations/task)tartalmaz, amelyek egy atomi feldolgozási feladatot, a hozzá tartozó bemeneti eszközöket, kimeneti eszközöket, egy adathordozó-processzort és a hozzájuk tartozó beállításokat határoznak meg. Egy adott feladaton belüli feladatok összekapcsolhatók, ahol egy adott tevékenység kimeneti eszköze a bemeneti eszköz a következő feladathoz. Ily módon az egyik feladattípus tartalmazhatja a Media-bemutatóhoz szükséges összes feldolgozást.

## <a name="encoding"></a><a id="encoding"></a>Encoding
Azure Media Services több lehetőséget kínál a felhőben lévő adathordozók kódolására.

A Media Services indításakor fontos megérteni a kodekek és a fájlformátumok közötti különbséget.
A kodekek a tömörítési/kibontási algoritmust megvalósító szoftverek, míg a fájlformátumok olyan tárolók, amelyek a tömörített videót tárolják.

A Media Services dinamikus csomagolást biztosít, amely lehetővé teszi az adaptív sávszélességű MP4-vagy Smooth Streaming-kódolású tartalom továbbítását a Media Services által támogatott streaming formátumokban (MPEG DASH, HLS, Smooth Streaming) anélkül, hogy újra kellene csomagolnia ezeket a streaming formátumokba.

A [dinamikus csomagolás](media-services-dynamic-packaging-overview.md)kihasználásához kódolni kell a köztes (forrás) fájlt egy adaptív sávszélességű MP4-fájlba vagy adaptív sávszélességű Smooth streaming fájlokra, és legalább egy standard vagy prémium szintű streaming végpontot el kell indítani.

Media Services a jelen cikkben ismertetett, igény szerinti kódolót támogatja:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

További információ erről a támogatott kódolóról: [kódoló](media-services-encode-asset.md).

## <a name="live-streaming"></a>Élő streamelés
Azure Media Services a csatorna az élő adatfolyam tartalmának feldolgozására szolgáló folyamatot jelöli. A csatorna az élő bemeneti streameket kétféleképpen fogadja el:

* A helyszíni élő kódoló többszörös sávszélességű RTMP vagy Smooth Streaming (töredezett MP4) üzenetet küld a csatornának. Használhatja a következő élő kódolókat, amelyek a többszörös sávszélességű Smooth Streaming: MediaExcel, Ateme, Imagine Communications, envivio, Cisco és Elemental. A következő élő kódolók kimenete RTMP: Adobe Flash Live Encoder, [stream Wirecast](media-services-configure-wirecast-live-encoder.md), Teradek, Haivision Encoders. A betöltött adatfolyamok további átkódolás és kódolás nélkül haladnak át a csatornákon. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.
* Egy átviteli sebességű adatfolyam (a következő formátumok egyikében: RTMP vagy Smooth Streaming (darabolt MP4)) a rendszer elküldi a csatornára, amely lehetővé teszi, hogy élő kódolást végezzen a Media Services. A csatorna ezután a bejövő egyfajta sávszélességű adatfolyamot élő kódolás útján többféle sávszélességű (adaptív) video-adatfolyammá alakítja. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

### <a name="channel"></a>Csatorna
Media Services a [Channel](/rest/api/media/operations/channel)s az élő adatfolyam tartalmának feldolgozásához felelős. A csatorna egy bemeneti végpontot (betöltési URL-címet) biztosít, amelyet aztán egy élő transcoder számára biztosít. A csatorna élő bemeneti streameket fogad az élő átkódolóból, és egy vagy több StreamingEndpoints keresztül elérhetővé teszi a folyamatos átvitelt. A csatornák egy előzetes verziójú végpontot (előzetes verziójú URL-címet) is biztosítanak, amelyet a további feldolgozás és a továbbítás előtt a stream előzetes verziójának megtekintéséhez és érvényesítéséhez használhat.

A csatorna létrehozásakor betöltheti a betöltési URL-címet és az előnézeti URL-címet. Az URL-címek lekéréséhez a csatornának nem kell megkezdett állapotban lennie. Ha készen áll arra, hogy egy élő transcoder-ből elindítsa az adatok csatornába való küldését, el kell indítani a csatornát. Miután az élő transcoder elkezdi az adatfeldolgozást, megtekintheti az adatfolyamot.

Minden Media Services fiók több csatornát, több programot és több StreamingEndpoints is tartalmazhat. A sávszélességtől és a biztonsági igényektől függően a Streamvégpontok-szolgáltatások egy vagy több csatornára is kihasználhatók. Bármely Streamvégpontok bármely csatornáról lehívható.

### <a name="program-event"></a>Program (esemény)
A [program (esemény)](/rest/api/media/operations/program) lehetővé teszi a szegmensek közzétételét és tárolását egy élő adatfolyamban. Csatornákat kezelő programok (események). A csatorna és a program kapcsolata hasonló a hagyományos adathordozóhoz, ahol a csatornán állandó tartalom található, és a program hatóköre az adott csatornán futó eseményekre vonatkozik.
Megadhatja, hogy hány óra elteltével szeretné megőrizni a program rögzített tartalmát a **ArchiveWindowLength** tulajdonság beállításával. Ez az érték 5 perc és 25 óra közötti lehet.

A ArchiveWindowLength azt is diktálja, hogy az ügyfelek legfeljebb hány időt tudnak visszakeresni az aktuális élő pozícióból. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program (esemény) társítva van egy eszközhöz. A program közzétételéhez létre kell hoznia egy lokátort a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A csatornák három egyidejűleg zajló programot támogatnak, így egy bejövő streamből több archívumot is létre lehet hozni. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Az üzleti igény szerint például 6 órát kell archiválni egy programból, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Ebben az esetben állítsa be az egyik programot az esemény 6 órájának archiválására, de ne tegye közzé. A másik programot 10 perc archiválására állítsa be, és tegye is közzé.

További információkért lásd:

* [Olyan csatornák használata, amelyek engedélyezve vannak a Live Encoding végrehajtásához Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [A helyi kódolók többszörös sávszélességű Élő stream fogadó csatornák használata](media-services-live-streaming-with-onprem-encoders.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Tartalom védelme
### <a name="dynamic-encryption"></a>Dinamikus titkosítás
Azure Media Services lehetővé teszi az adathordozó biztonságossá tételét a számítógép tárolás, feldolgozás és kézbesítés útján történő elhagyása után. A Media Services lehetővé teszi a tartalom dinamikus titkosítását Advanced Encryption Standard (AES) (128 bites titkosítási kulcsok használatával) és a Common encryption (CENC) használatával a PlayReady és/vagy a Widevine DRM használatával. A Media Services egy szolgáltatást is biztosít az AES-kulcsok és a PlayReady-licencek engedélyezésére a hitelesítő ügyfelek számára.

Jelenleg a következő folyamatos átviteli formátumok titkosíthatók: HLS, MPEG DASH és Smooth Streaming. A progresszív letöltések nem titkosíthatók.

Ha Media Services szeretne titkosítani egy eszközt, hozzá kell rendelnie egy titkosítási kulcsot (CommonEncryption vagy EnvelopeEncryption) az eszközhöz, és konfigurálnia kell a kulcshoz tartozó engedélyezési házirendeket is.

Ha titkosított adategységet szeretne továbbítani, konfigurálnia kell az eszköz kézbesítési házirendjét annak megadásához, hogy hogyan szeretné kézbesíteni az eszközt.

Ha egy lejátszó egy adatfolyamot kér, Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat a boríték-titkosítás (AES) vagy a közös titkosítás (PlayReady vagy Widevine) használatával. Az adatfolyam visszafejtéséhez a lejátszó a kulcs kézbesítési szolgáltatástól kéri a kulcsot. Annak eldöntéséhez, hogy a felhasználó jogosult-e a kulcs lekérésére, a szolgáltatás kiértékeli a kulcshoz megadott engedélyezési házirendeket.

### <a name="token-restriction"></a>Jogkivonat-korlátozás
A tartalmi kulcs engedélyezési házirendje rendelkezhet egy vagy több engedélyezési korlátozással: Open, token korlátozás vagy IP-korlátozás. A tokennel korlátozott szabályzatokhoz a Secure Token Service (Biztonsági jegykiadó szolgáltatás, STS) által kiadott tokennek kell tartoznia. A Media Services a Simple Web Tokens (SWT) és a JSON Web Token (JWT) formátumú tokeneket támogatja. Media Services nem biztosít biztonságos jogkivonat-szolgáltatásokat. Létrehozhat egyéni STS-t is. Az STS-t úgy kell konfigurálni, hogy a megadott kulccsal aláírt tokent hozzon létre, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket. A Media Services Key Delivery Service visszaadja a kért kulcsot (vagy licencet) az ügyfélnek, ha a jogkivonat érvényes, és a jogkivonatban lévő jogcímek egyeznek a kulcshoz (vagy licenchez) konfigurált jogcímekkel.

A jogkivonat korlátozott házirendjének konfigurálásakor meg kell adnia az elsődleges ellenőrző kulcsot, a kiállítót és a célközönség paramétereit. Az elsődleges ellenőrző kulcs tartalmazza azt a kulcsot, amelyhez a jogkivonat be lett jelentkezve, a kibocsátó pedig a tokent kiállító biztonságos jogkivonat-szolgáltatás. A célközönség (más néven hatókör) leírja a jogkivonat célját vagy azt az erőforrást, amelyet a jogkivonat engedélyez a hozzáféréshez. A Media Services Key Delivery Service ellenőrzi, hogy a jogkivonat értékei egyeznek-e a sablon értékeivel.

További információért tekintse át a következő cikkeket:
- [Tartalom – áttekintés](media-services-content-protection-overview.md)
- [Védelem AES-128](media-services-protect-with-aes128.md)
- [Védelem a PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Szállít
### <a name="dynamic-packaging"></a><a name="dynamic_packaging"></a>Dinamikus csomagolás
A Media Services használatakor javasolt a köztes fájlok kódolása adaptív sávszélességű MP4-készletbe, majd a [dinamikus csomagolás](media-services-dynamic-packaging-overview.md)használatával alakítsa át a kívánt formátumra.

### <a name="streaming-endpoint"></a>Streamvégpont
A Streamvégpontok olyan adatfolyam-szolgáltatást jelöl, amely közvetlenül az ügyfél-vagy Content Delivery Network (CDN) számára biztosít tartalmat a további terjesztéshez (Azure Media Services mostantól biztosítja a Azure CDN-integrációt.) A streaming Endpoint szolgáltatás kimenő adatfolyama lehet élő stream vagy igény szerinti video-eszköz a Media Services-fiókban. A Media Services ügyfelei általában egy **standard** szintű streamvégpontot vagy egy vagy több **prémium** szintű streamvégpontot választanak, saját igényeiknek megfelelően. A standard folyamatos átviteli végpont a legtöbb folyamatos átviteli terheléshez megfelelő. 

A szabványos streamvégpont a legtöbb streamelési feladat ellátására alkalmas. A standard folyamatos átviteli végpontok rugalmasan, a HLS, MPEG-DASH és Smooth Streaming, valamint a Microsoft PlayReady, a Google Widevine, az Apple Fairplay és a AES128 dinamikus titkosításával szinte minden eszközön továbbítják a tartalmat.  Az Azure CDN integráción keresztül több ezer egyidejű megjelenítővel is méretezhetők. Ha speciális számítási feladattal rendelkezik, vagy ha a folyamatos átviteli kapacitásra vonatkozó követelmények nem felelnek meg a normál átviteli végpont átviteli sebességének, vagy szeretné szabályozni a Streamvégpontok szolgáltatás kapacitását a növekvő sávszélesség-szükségletek kielégítése érdekében, ajánlott a méretezési egységek (más néven prémium szintű streaming-egységek) lefoglalása.

A dinamikus csomagolás és/vagy a dinamikus titkosítás használata javasolt.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** folyamatos átviteli végpontot. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

További információ [ebben](media-services-portal-manage-streaming-endpoints.md) a témakörben érhető el.

Alapértelmezés szerint legfeljebb 2 folyamatos átviteli végponttal rendelkezhet Media Services-fiókjában. Magasabb korlát igényléséhez lásd: [kvóták és korlátozások](media-services-quotas-and-limitations.md).

Csak akkor számítunk fel díjat, ha a Streamvégpontok fut állapotban van.

### <a name="asset-delivery-policy"></a>Eszköz kézbesítési szabályzata
A Media Services Content Delivery munkafolyamat egyik lépése a [továbbítási szabályzatok](/rest/api/media/operations/assetdeliverypolicy)konfigurálása az adatfolyamként használni kívánt eszközökhöz. Az eszköz kézbesítési házirendje közli Media Services, hogyan szeretné kézbesíteni az eszközét: az adatstream protokollnak (például MPEG DASH, HLS, Smooth Streaming vagy all) az eszközét dinamikusan kell titkosítania, függetlenül attól, hogy szeretné-e dinamikusan titkosítani az eszközt és a (boríték vagy közös titkosítás).

Ha titkosított eszközzel rendelkezik, az eszköz adatfolyamként való továbbítása előtt a streaming-kiszolgáló eltávolítja a tárolási titkosítást, és a megadott kézbesítési házirend használatával továbbítja a tartalmat. Ha például az eszköz titkosítását Advanced Encryption Standard (AES) titkosítási kulccsal szeretné továbbítani, állítsa a házirend típusát DynamicEnvelopeEncryption értékre. A tároló titkosításának eltávolításához és az objektum kiürítésének törléséhez állítsa a házirend típusát NoDynamicEncryption értékre.

### <a name="progressive-download"></a>Progresszív letöltés
A progresszív letöltés lehetővé teszi, hogy a teljes fájl letöltése előtt megkezdje az adathordozók lejátszását. Egy MP4-fájlt csak progresszív módon tölthet le.

>[!NOTE]
>A titkosított eszközöket vissza kell fejteni, ha szeretné, hogy azok elérhetők legyenek a progresszív letöltéshez.

A progresszív letöltési URL-címekkel rendelkező felhasználók számára először létre kell hoznia egy OnDemandOrigin-lokátort. A lokátor létrehozásakor megadja az objektum alap elérési útját. Ezután hozzá kell fűzni az MP4-fájl nevét. Például:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

### <a name="streaming-urls"></a>Streaming URL-címek
Tartalom továbbítása az ügyfeleknek. A streaming URL-címeket használó felhasználók számára először létre kell hoznia egy OnDemandOrigin-lokátort. A lokátor létrehozásakor megadja a továbbítani kívánt tartalmat tartalmazó objektum alap elérési útját. Ahhoz azonban, hogy ezt a tartalmat tudja továbbítani, ezt az elérési utat tovább kell módosítania. A folyamatos átviteli jegyzékfájl fájljának teljes URL-címének létrehozásához össze kell fűzve a lokátor Path értékét és a jegyzékfájl (fájlnév. ISM) fájlnevét. Ezután fűzze hozzá a/manifest és a megfelelő formátumot (ha szükséges) a lokátor elérési útjához.

A tartalmat TLS-kapcsolaton keresztül is továbbíthatja. Ehhez győződjön meg arról, hogy a streaming URL-címek a HTTPS protokollal kezdődnek. Az AMS jelenleg nem támogatja a TLS-t egyéni tartományokkal.  

>[!NOTE]
>Csak akkor továbbíthatja a TLS-t, ha a streaming végpontot, amelyről a tartalmat a 2014 szeptember 10. után hozták létre. Ha a folyamatos átviteli URL-címek a szeptember 10. után létrehozott streaming-végpontokon alapulnak, az URL-cím "streaming.mediaservices.windows.net" (az új formátum) tartalmazza. A "origin.mediaservices.windows.net" (a régi) formátumot tartalmazó streaming URL-címek nem támogatják a TLS-t. Ha az URL-cím a régi formátumban van, és szeretné továbbítani a TLS-t, hozzon létre egy új streaming-végpontot. Az új adatfolyam-végpont alapján létrehozott URL-címek használatával továbbíthatja a tartalmat a TLS protokollon keresztül.

A következő lista különböző streaming formátumokat tartalmaz, és példákat nyújt:

* Smooth Streaming

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

http: \/ /testendpoint-testaccount.streaming.Mediaservices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

* MPEG DASH

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.streaming.Mediaservices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = mpd-Time-CSF)

* Apple HTTP Live Streaming (HLS) v4

{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.streaming.Mediaservices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = m3u8-AAPL)

* Apple HTTP Live Streaming (HLS) v3

{streaming Endpoint Name-Media Services-fiók neve}. streaming. Mediaservices. Windows. net/{kereső azonosítója} ISM/manifest (Format = m3u8-AAPL-v3)

http: \/ /testendpoint-testaccount.streaming.Mediaservices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = m3u8-AAPL-v3)

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
