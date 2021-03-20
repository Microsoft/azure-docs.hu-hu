---
title: Azure Media Services töredezett MP4 Live betöltési specifikáció | Microsoft Docs
description: Ez a specifikáció leírja a Azure Media Services-hoz tartozó töredezett MP4-alapú élő adatfolyamok betöltésének protokollját és formátumát. Ez a dokumentum a nagy mértékben redundáns és robusztus élő betöltési mechanizmusok létrehozásával kapcsolatos ajánlott eljárásokat is tárgyalja.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 7323ae611431e1d91fd1a8471914be388fcc4712
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92019511"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services darabolt MP4 élő betöltési specifikáció 

Ez a specifikáció leírja a Azure Media Services-hoz tartozó töredezett MP4-alapú élő adatfolyamok betöltésének protokollját és formátumát. A Media Services élő adatfolyam-szolgáltatást biztosít, amellyel az ügyfelek valós időben továbbítják az élő eseményeket és közvetítik a tartalmakat, ha az Azure-t felhő platformként használják. Ez a dokumentum a nagy mértékben redundáns és robusztus élő betöltési mechanizmusok létrehozásával kapcsolatos ajánlott eljárásokat is tárgyalja.

## <a name="1-conformance-notation"></a>1. megfelelőségi jelölés
A (z) "MUST," "nem kötelező", "REQUIREd", "", "" nem "," "nem", "" ajánlott "," "lehet, hogy" és "nem kötelező" szövegeket kell értelmezni a dokumentumban, az RFC 2119-es dokumentumban leírtak szerint.

## <a name="2-service-diagram"></a>2. szolgáltatási diagram
Az alábbi ábrán a Media Services élő streaming szolgáltatásának magas szintű architektúrája látható:

1. Az élő kódoló élő hírcsatornákat küld a Azure Media Services SDK-val létrehozott és kiépített csatornákra.
1. A Media Services csatornák, programok és folyamatos átviteli végpontok kezelik az élő közvetítés funkcióit, beleértve a betöltést, a formázást, a felhőalapú DVR-t, a biztonságot, a méretezhetőséget és a redundanciát.
1. Igény szerint az ügyfelek dönthetnek úgy is, hogy egy Azure Content Delivery Network réteget helyeznek üzembe a folyamatos átviteli végpont és az ügyfél-végpontok között.
1. Az ügyfél-végpontok adatfolyamot továbbítanak a streaming végpontról a HTTP Adaptív átviteli protokollok használatával. Ilyenek például a Microsoft Smooth Streaming, a dinamikus adaptív átvitel HTTP-n keresztül (DASH vagy MPEG-DASH) és az Apple HTTP Live Streaming (HLS).

![folyamat betöltése][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream formátum – ISO 14496-12 darabolt MP4
A jelen dokumentumban tárgyalt élő adatfolyamok átviteli formátuma az [ISO-14496-12] alapján történik. A töredezett MP4 formátum és bővítmények részletes ismertetése a videó igény szerinti fájlokhoz és az élő adatfolyamok betöltéséhez: [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251).

### <a name="live-ingest-format-definitions"></a>Élő betöltés formátumának definíciói
Az alábbi lista a Azure Media Servicesba való élő betöltésre vonatkozó speciális formátum-definíciókat ismerteti:

1. A **ftyp**, a **Live Server manifest Box** és a **Moov** MEZŐKET minden kérelemmel (http post) kell elküldeni. Ezeket a mezőket a stream elején kell elküldeni, és a kódolónak újra kell csatlakoznia a stream betöltésének folytatásához. További információ: 6. szakasz, [1].
1. Az [1] 3.3.2. szakasza az élő betöltéshez egy **StreamManifestBox** nevű opcionális mezőt határoz meg. Az Azure Load Balancer útválasztási logikája miatt ez a mező elavult. A mező nem lehet jelen a Media Servicesba való betöltéskor. Ha ez a mező megtalálható, Media Services csendben hagyja figyelmen kívül.
1. Az egyes töredékek esetében az [1] 3.2.3.2-ben definiált **TrackFragmentExtendedHeaderBox** -mezőnek jelen kell lennie.
1. A **TrackFragmentExtendedHeaderBox** mező 2. verzióját kell használni a több adatközpontban azonos URL-címekkel rendelkező adathordozó-szegmensek létrehozásához. Az index-alapú folyamatos átviteli formátumok, például az Apple HLS és az index-alapú MPEG-kötőjel közötti adatközpont feladatátvételéhez a töredék index mező szükséges. Az adatközpontok feladatátvételének engedélyezéséhez a töredék-indexet több kódolón kell szinkronizálni, és minden egyes egymást követő adathordozó-töredék esetében 1 értékkel kell növelni, még a kódoló újraindítása vagy meghibásodása között is.
1. A (z) [1] 3.3.6 szakasza a **MovieFragmentRandomAccessBox** (**mfra**) nevű mezőt definiálja, amelyet az élő betöltés végén lehet elküldeni, hogy az adatfolyamot (EOS) a csatornához adja. Media Services betöltési logikája miatt az EOS elavult, és nem lehet elküldeni az élő betöltéshez használt **mfra** mezőt. Ha elküldésre kerül, Media Services csendben hagyja figyelmen kívül. A betöltési pont állapotának alaphelyzetbe állításához a [csatorna alaphelyzetbe állítását](/rest/api/media/operations/channel#reset_channels)ajánlott használni. Azt is javasoljuk, hogy a [program leállítása](/rest/api/media/operations/program#stop_programs) paranccsal fejezze be a bemutatót és a streamet.
1. Az MP4-töredék időtartamának állandónak kell lennie az ügyfél-jegyzékfájlok méretének csökkentése érdekében. Az MP4-töredékek állandó időtartama Emellett javítja az ügyfél letöltési heurisztikus használatát az ismételt címkék használatával. Az időtartam VÁLTOZHAT, hogy kompenzálja a nem egész számok díjszabását.
1. Az MP4-töredék időtartamának körülbelül 2 – 6 másodpercnek kell lennie.
1. Az MP4-töredékek időbélyegei és indexei (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` és `fragment_index` ) növekvő sorrendben érkeznek. Bár a Media Services az ismétlődő töredékek esetében is rugalmas, a töredékek átrendezése a média idővonalának megfelelően korlátozott.

## <a name="4-protocol-format--http"></a>4. protokoll formátuma – HTTP
A Media Services ISO-alapú, MP4-alapú élő betöltési szolgáltatása szabványos, hosszú ideig futó HTTP POST-kérést használ, amely a töredezett MP4 formátumba csomagolt kódolt adathordozó-adatokat továbbítja a szolgáltatásnak. Az egyes HTTP-bejegyzések teljes töredezett MP4-Bitstream ("Stream") küldenek, kezdve a fejléctől kezdve (**ftyp**, **Live Server manifest Box** és **Moov** box), és továbbra is a töredékek sorozatából (**moof** és **mdat** mezők). A HTTP POST kérelem URL-szintaxisát lásd: 9,2. szakasz, [1]. Példa a POST URL-címre: 

`http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)`

### <a name="requirements"></a>Követelmények
A részletes követelmények a következők:

1. A kódolónak el kell indítania a szórást egy olyan HTTP POST-kérelem küldésével, amely üres "szövegtörzs" (nulla tartalom hossza) használatával ugyanazzal a betöltési URL-lel rendelkezik. Ez segíthet a kódolónak gyorsan észlelni, hogy az élő betöltési végpont érvényes-e, és hogy van-e hitelesítés vagy egyéb szükséges feltétel. HTTP protokollon keresztül a kiszolgáló nem tud HTTP-választ küldeni, amíg a teljes kérelem nem érkezik, beleértve a POST törzset is. Az élő esemény hosszan futó természete miatt ebben a lépésben a kódoló nem képes észlelni a hibát, amíg az összes adat küldésének véget nem ér.
1. A kódolónak az (1) miatti hibákat és hitelesítési kihívásokat kell kezelnie. Ha (1) a 200-es választal sikeres, folytassa a következővel:.
1. A kódolónak egy új HTTP POST-kérelmet kell elindítania a töredezett MP4 streamtel. A hasznos adatnak a fejléc mezővel kell kezdődnie, amelyet töredékek követnek. Vegye figyelembe, hogy az **ftyp**, az **élő kiszolgáló jegyzékfájlja** és a **Moov** (ebben a sorrendben) mezőben szerepelnie kell minden kérelemnek, még akkor is, ha a kódolónak újra kell kapcsolódnia, mert az előző kérést a stream vége előtt megszakították. 
1. A kódolónak a feltöltéshez darabolásos adatátviteli kódolást kell használnia, mivel nem lehet előre jelezni az élő esemény teljes tartalmának hosszát.
1. Ha az esemény véget ért, az utolsó részlet elküldése után a kódolónak szabályosan le kell végződnie a darabolásos átvitel kódolási üzenetének sorozatát (a legtöbb HTTP-ügyfél automatikusan kezeli azt). A kódolónak várnia kell, hogy a szolgáltatás visszaadja a végső válasz kódját, majd megszakítsa a kapcsolódást. 
1. A kódoló nem használhatja az 9,2-ben `Events()` leírtak szerint a következőt: [1], Media Servicesba való élő BEtöltéshez.
1. Ha a HTTP POST kérelem leáll, vagy időtúllépést okoz a TCP-hibánál az adatfolyam vége előtt, akkor a kódolónak új bejegyzéssel kell kiállítania egy új bejegyzést, és követnie kell az előző követelményeket. Emellett a kódolónak újra el kell küldenie az előző két MP4-töredéket az adatfolyamban lévő egyes sávokhoz, és nem kell folytatnia a folytonosságot a média idővonalán. Az utolsó két MP4-töredék Újraküldés az egyes sávok esetében biztosítja, hogy nincs adatvesztés. Más szóval, ha egy stream egy hang-és egy videó-nyomkövetést is tartalmaz, és a jelenlegi POST kérelem meghiúsul, a kódolónak újra kell csatlakoznia, majd újra el kell küldenie a hangsáv utolsó két töredékét, amelyeket korábban sikeresen Elküldöttek, és a videó nyomon követésének utolsó két töredékét, amelyeket korábban sikeresen elküldtek, hogy ne legyen adatvesztés. A kódolónak meg kell őriznie egy "Forward" puffert, amely az újracsatlakozáskor újraküldi az adathordozó töredékeit.

## <a name="5-timescale"></a>5. időskála
[[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) a **SmoothStreamingMedia** (szakasz 2.2.2.1), a **StreamElement** (szakasz 2.2.2.3), a **StreamFragmentElement** (szakasz 2.2.2.6) és a **LiveSMIL** (szakasz: 2.2.7.3.1) időkeretének használatát ismerteti. Ha az időskála értéke nem létezik, az alapértelmezett érték 10 000 000 (10 MHz). Bár a Smooth Streaming formátum specifikációja nem blokkolja más időskála-értékek használatát, a legtöbb kódoló implementációja ezt az alapértelmezett értéket (10 MHz-et) használja a Smooth Streaming adatok betöltéséhez. Az [Azure Media Dynamic csomagolási](./previous/media-services-dynamic-packaging-overview.md) funkciója miatt javasoljuk, hogy a videó streamek és a 44,1 khz vagy 48,1 kHz esetében használjon 90-kHz-es időskálát a hangstreamek számára. Ha különböző időkeret-értékeket használ a különböző adatfolyamokhoz, az adatfolyam-szintű időkeretet el kell juttatni. További információ: [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251).     

## <a name="6-definition-of-stream"></a>6. a "Stream" definíciója
A stream az élő bemutatók előkészítésének, a folyamatos átvitel feladatátvételének és a redundancia-forgatókönyveknek a működésének alapvető működési egysége. Az adatfolyam egy egyedi, töredékes MP4-Bitstream van definiálva, amely egyetlen vagy több zeneszámot tartalmazhat. A teljes élő bemutató egy vagy több streamet tartalmazhat, az élő kódolók konfigurációjától függően. Az alábbi példák a streamek egy teljes élő bemutató összeállításához való használatának különböző lehetőségeit szemléltetik.

**Példa** 

Az ügyfél egy élő adatfolyam-bemutatót szeretne létrehozni, amely a következő hang/videó bitrátát tartalmazza:

Videó – 3000 kbps, 1500 kbps, 750 kbps

Hang – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>1. lehetőség: egy adatfolyam összes nyomon követése
Ebben a beállításban egyetlen kódoló hozza létre az összes hang/videó sávot, majd egy darabolt MP4-Bitstream csomagolja őket. Ezután a töredezett MP4-Bitstream egyetlen HTTP POST-kapcsolatban küldi el a rendszer. Ebben a példában csak egy stream érhető el ehhez az élő bemutatóhoz.

![Streamek – egy pálya][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>2. lehetőség: az egyes sávok egy külön streamben
Ebben a beállításban a kódoló az egyes töredékek MP4-Bitstream egy számot helyez el, majd külön HTTP-kapcsolatokon keresztül könyveli az összes adatfolyamot. Ez egy kódolóval vagy több kódolóval is elvégezhető. Az élő betöltés a négy streamből álló élő bemutatót látja.

![Streamek – külön sávok][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>3. lehetőség: a legalacsonyabb sávszélességű videó követése egyetlen streambe
Ebben a beállításban az ügyfél úgy dönt, hogy a legalacsonyabb sávszélességű videofelvételt használja az egyik töredék MP4-Bitstream, és a másik két videó nyomon követi a különböző adatfolyamokat. 

![Streamek – hang-és video-zeneszámok][image4]

### <a name="summary"></a>Összefoglalás
Ez a példa nem tartalmazza az összes lehetséges betöltési beállítást. Valójában a zeneszámok adatfolyamként történő csoportosítását az élő betöltés támogatja. Az ügyfelek és a kódoló-szállítók a mérnöki komplexitás, a kódoló kapacitása, valamint a redundancia és a feladatátvételi megfontolások alapján választhatják ki a saját implementációkat. A legtöbb esetben azonban csak egyetlen hangsáv van a teljes élő bemutatóhoz. Ezért fontos, hogy gondoskodjon arról, hogy a zeneszámot tartalmazó betöltési stream egészséges legyen. Ez a megfontolás gyakran azt eredményezi, hogy a hangsávot a saját streamben helyezi üzembe (a 2. lehetőségnek megfelelően), vagy a legalacsonyabb sávszélességű videóval (a 3. lehetőséggel) közvetíti. Emellett a jobb redundancia és hibatűrés érdekében ugyanazt a hangsávot kell elküldeni két különböző streamben (2. lehetőség redundáns hangsávokkal), vagy a hangfelvételt legalább két, a legalacsonyabb sávszélességű videó (3. lehetőség, legalább két videó streamben található hang) esetében ajánlott a Media Servicesba való élő betöltéshez.

## <a name="7-service-failover"></a>7. szolgáltatás feladatátvétele
Az élő közvetítés jellegéből adódóan a jó feladatátvételi támogatás elengedhetetlen a szolgáltatás rendelkezésre állásának biztosításához. Media Services a különböző típusú hibák kezelésére szolgál, beleértve a hálózati hibákat, a kiszolgálói hibákat és a tárolási problémákat. Ha a megfelelő feladatátvételi logikával együtt használja az élő kódoló oldalról, az ügyfelek egy nagyon megbízható élő adatfolyam-szolgáltatást érhetnek el a felhőből.

Ebben a szakaszban a szolgáltatás feladatátvételi forgatókönyveit tárgyaljuk. Ebben az esetben a hiba valahol a szolgáltatáson belül történik, és hálózati hibaként nyilvánul meg. Íme néhány javaslat a szolgáltatás feladatátvételének kezelésére szolgáló kódoló megvalósításához:

1. A TCP-kapcsolat létrehozásához használjon 10 másodperces időkorlátot. Ha a kapcsolat megállapítására tett kísérlet 10 másodpercnél hosszabb időt vesz igénybe, szakítsa meg a műveletet, és próbálkozzon újra. 
1. Rövid időtúllépéssel küldje el a HTTP-kérelmek üzenetének adattömböket. Ha a cél MP4-töredék időtartama N másodperc, a küldési időkorlát N és 2 N másodperc között legyen. Ha például az MP4-töredék időtartama 6 másodperc, akkor a 6 és 12 másodperc közötti időkorlátot használja. Ha időtúllépés történik, állítsa alaphelyzetbe a kapcsolatokat, nyisson meg egy új kapcsolaton, és folytassa az adatfolyam betöltését az új kapcsolaton. 
1. Tartson fenn egy olyan működés közbeni puffert, amely az utolsó két töredéktel rendelkezik minden olyan nyomon követéshez, amelyet sikeresen és teljes mértékben elküldtek a szolgáltatásnak.  Ha az adatfolyamhoz tartozó HTTP POST-kérelem leáll, vagy az adatfolyam végét megelőzően időtúllépés történik, nyisson meg egy új kapcsolódást, és kezdjen el egy másik HTTP POST-kérelmet, küldje el újra az adatfolyam-fejléceket, küldje el újra az utolsó két töredéket az egyes nyomon követésekhez, és folytassa az adatfolyamot anélkül, hogy az adathordozó idővonalán el kellene végezni Ez csökkenti az adatvesztés esélyét.
1. Javasoljuk, hogy a kódoló ne korlátozza az újrapróbálkozások számát a kapcsolat létesítéséhez vagy a folyamatos átvitel folytatásához TCP-hiba esetén.
1. TCP-hiba után:
  
    a. A jelenlegi kapcsolatokat be kell zárni, és egy új HTTP POST-kérelemhez új kapcsolatokat kell létrehozni.

    b. Az új HTTP POST URL-címnek meg kell egyeznie a kezdeti bejegyzés URL-címével.
  
    c. Az új HTTP-POSTnak tartalmaznia kell egy stream-fejlécet (**ftyp**, **Live Server manifest Box** és **Moov** ), amelyek megegyeznek a kezdeti bejegyzésben szereplő stream-fejlécekkel.
  
    d. Az egyes műsorszámok utolsó két töredékét újra kell elküldeni, és a folyamatos átvitelnek folytatnia kell a folytonosságot a média idővonalán. Az MP4-töredék időbélyegének folyamatosan kell növekedni, még a HTTP POST kérelmek között is.
1. A kódolónak le kell fejeznie a HTTP POST kérelmet, ha az adatforgalom az MP4-töredék időtartamával arányos sebességgel történik.  Egy HTTP POST-kérelem, amely nem küldi el az adatküldést, megakadályozhatja, hogy az Media Services gyorsan lekapcsolódjanak a kódolóból egy szolgáltatás frissítése esetén. Emiatt a ritka (ad-jel) sávok HTTP-BEJEGYZÉSének rövid életűnek kell lennie, és azonnal le kell zárnia a ritka töredékek elküldésekor.

## <a name="8-encoder-failover"></a>8. kódoló feladatátvétele
A kódoló feladatátvétele a második típusú feladatátvételi forgatókönyv, amelyet a végpontok közötti élő adatfolyam-továbbításhoz kell kezelni. Ebben az esetben a hiba feltétele a kódoló oldalán történik. 

![kódoló feladatátvétele][image5]

A következő elvárások érvényesek az élő betöltési végpontról, amikor kódoló feladatátvétel történik:

1. A folyamatos átvitel folytatásához új kódoló példányt kell létrehozni, ahogy az ábrán látható (a 3000k videó továbbítása szaggatott vonallal).
1. Az új kódolónak ugyanazt az URL-címet kell használnia a HTTP POST kérelmek esetében, mint a sikertelen példány.
1. Az új kódoló POST-kérelmének ugyanazokat a töredezett MP4-fejléceket kell tartalmaznia, mint a sikertelen példány.
1. Az új kódolónak megfelelően szinkronizálva kell lennie az összes többi futó kódolóval ugyanahhoz az élő bemutatóhoz, hogy szinkronizált hang-/video-mintákat lehessen összehangolni az illesztett töredékek határaival.
1. Az új adatfolyamnak szemantikailag egyenértékűnek kell lennie az előző adatfolyammal, és a fejléc és a töredék szintjén is felcserélhető lehet.
1. Az új kódolónak meg kell próbálnia csökkenteni az adatvesztést. A `fragment_absolute_time` és a `fragment_index` média töredékeit abban a PONTban kell megnövekedni, ahol a kódoló utoljára leállt. A `fragment_absolute_time` és a `fragment_index` folyamatosan növekszik, de szükség esetén a folytonosságot is lehetővé teszi. Media Services figyelmen kívül hagyja a már kapott és feldolgozott töredékeket, így jobb, ha a töredékek újraküldésének oldalára kerül sor, mint hogy bevezesse a folytonosságot a média idővonalán. 

## <a name="9-encoder-redundancy"></a>9. kódoló redundancia
Az olyan kritikus élő események esetében, amelyek még magasabb rendelkezésre állást és minőséget igényelnek, ajánlott aktív-aktív redundáns kódolókat használni az adatvesztés nélküli zökkenőmentes feladatátvétel érdekében.

![kódoló redundancia][image6]

Ahogy az ábrán is látható, a kódolók két csoportja küld egyszerre két példányt az egyes streamekről az élő szolgáltatásba. Ez a beállítás akkor támogatott, ha a Media Services az adatfolyam-azonosító és a töredék timestamp alapján képes ismétlődő töredékek kiszűrésére. Az eredményül kapott élő adatfolyam és archiválás a két forrásból származó lehető legjobb összesítést biztosító streamek egyetlen másolata. Például egy feltételezett szélsőséges esetben, ha egy kódoló (nem feltétlenül azonos) fut az egyes adatfolyamok adott időpontjában, akkor a szolgáltatásból származó élő stream folyamatos adatvesztés nélkül történik. 

A forgatókönyv követelményei szinte megegyeznek a "kódoló feladatátvétele" eset követelményeivel, azzal a kivétellel, hogy a második kódoló az elsődleges kódolóval megegyező időpontban fut.

## <a name="10-service-redundancy"></a>10. szolgáltatás-redundancia
A nagymennyiségű globális terjesztéshez időnként a regionális katasztrófák kezeléséhez a régiók közötti biztonsági mentés szükséges. A "kódoló redundancia" topológiával bővülő ügyfelek kiválaszthatják, hogy a redundáns szolgáltatás központi telepítése egy másik régióban, amely a második kódolóhoz van csatlakoztatva. Az ügyfelek az Content Delivery Network-szolgáltatóval is dolgozhatnak, hogy a két szolgáltatás központi telepítése előtt üzembe helyezhetnek egy globális Traffic Manager, hogy zökkenőmentesen irányítsák az ügyfelek forgalmát. A kódolók követelményei megegyeznek a "kódoló redundancia" esetével. Az egyetlen kivétel, hogy a második kódolónak egy másik élő betöltési végpontra kell mutatnia. A következő ábra a telepítőt mutatja be:

![szolgáltatás-redundancia][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. a betöltési formátumok speciális típusai
Ez a szakasz az élő betöltési formátumok speciális típusait ismerteti, amelyeket az adott forgatókönyvek kezelésére terveztek.

### <a name="sparse-track"></a>Ritka nyomon követés
Ha élő adatfolyam-bemutatót ad meg, amely gazdag ügyfél-élményt nyújt, gyakran szükség van az időszinkronizált események vagy a sávon belüli jelek továbbítására a fő adathordozó-adatokat. Erre példa a dinamikus élő ad-Beszúrás. Az ilyen típusú események jelzése eltér a normál hang-és video-adatfolyamtól, a ritka természet miatt. Ez azt jelenti, hogy az adatjelzések általában nem folyamatosan történnek, és az intervallum nehéz előre jelezni. A ritka nyomon követés fogalma a sávon kívüli adatjelzések betöltésére és szórására lett tervezve.

A következő lépések a ritka számok betöltését szolgáló ajánlott implementációk:

1. Hozzon létre egy különálló, töredékes MP4-Bitstream, amely csak ritka számokat tartalmaz, hang-és video-zeneszámok nélkül.
1. A (z) [1] 6. szakaszában meghatározott **Live Server manifest (élő kiszolgáló jegyzékfájlja) mezőben** adja meg a *parentTrackName* paramétert a szülő sáv nevének megadásához. További információ: szakasz 4.2.1.2.1.2 [1].
1. Az **élő kiszolgáló jegyzékfájlja mezőben** a **manifestOutput** **true** értékre kell állítani.
1. A jelzési esemény ritka természete miatt a következőket javasoljuk:
   
    a. Az élő esemény elején a kódoló elküldi a kezdeti fejléceket a szolgáltatásnak, amely lehetővé teszi, hogy a szolgáltatás regisztrálja a ritka nyomon követést az ügyfél jegyzékfájljában.
   
    b. A kódolónak le kell fejeznie a HTTP POST kérelmet, ha nem küldi el az adatküldést. Egy hosszú ideig futó HTTP-bejegyzés, amely nem küldi az adatküldést, megakadályozhatja, hogy Media Services gyorsan lekapcsolódjon a kódolóból egy szolgáltatás frissítése vagy a kiszolgáló újraindítása esetén. Ezekben az esetekben a Media-kiszolgáló átmenetileg blokkolva van egy fogadási műveletben a szoftvercsatornán.
   
    c. Amikor az adatjelzés nem érhető el, a kódolónak be kell zárulnia a HTTP POST kérelemben. Amíg a POST kérelem aktív, a kódolónak el kell küldenie az adatküldést.

    d. Ritka töredékek küldésekor a kódoló explicit Content-Length fejlécet állíthat be, ha az elérhető.

    e. Ha ritka töredékeket küld egy új csatlakozással, a kódolónak el kell kezdenie a fejléceket, majd az új töredékeket kell elküldenie. Ez olyan esetekben fordul elő, amikor feladatátvétel történik a között, és az új ritka kapcsolat létrejön egy olyan új kiszolgáló számára, amely még nem látta a ritka nyomon követést.

    f. A ritka követési töredék akkor válik elérhetővé az ügyfél számára, amikor az ügyfél számára elérhetővé válnak az azonos vagy nagyobb időbélyeg-értékkel rendelkező megfelelő fölérendelt követési töredékek. Ha például a ritka töredék a t = 1000 időbélyeggel rendelkezik, akkor a várt érték azt eredményezi, hogy az ügyfél a "videó" kifejezést látja (feltéve, hogy a szülő követés neve "videó") az időbélyeg 1000-as vagy újabb töredékét, letöltheti a ritka töredék t = 1000-et. Vegye figyelembe, hogy a tényleges jel felhasználható egy másik pozícióra a bemutató idővonalán a kijelölt célra. Ebben a példában lehetséges, hogy a t = 1000 ritka töredéke XML-adattartalommal rendelkezik, amely egy ad egy olyan pozícióba való beszúrására szolgál, amely néhány másodperccel később van.

    : A ritka nyomkövetési töredékek a forgatókönyvtől függően különböző formátumokban (például XML, szöveg vagy bináris) lehetnek.

### <a name="redundant-audio-track"></a>Redundáns hangfelvétel
Egy tipikus HTTP adaptív streaming-forgatókönyvben (például Smooth Streaming vagy DASH) gyakran csak egy hangsáv található a teljes bemutatóban. A video tracks szolgáltatástól eltérően, amelyek több minőségi szintet biztosítanak az ügyfél számára a hibák kiválasztásához, a hangsáv lehet egy meghibásodási pont, ha a hangsávokat tartalmazó adatfolyam betöltése megszakadt. 

A probléma megoldásához Media Services támogatja a redundáns hangsávok élő betöltését. Az elképzelés az, hogy ugyanazt a hangsávot többször is el lehet juttatni különböző streamekben. Bár a szolgáltatás csak egyszer regisztrálja a hangfelvételt az ügyfél jegyzékfájljában, a redundáns hangsávokat használhat a hangtöredékek lekérésére, ha az elsődleges hangsávok problémái vannak. A redundáns hangsávok betöltéséhez a kódolónak a következőket kell tennie:

1. Hozza létre ugyanazt a hangsávot több töredék MP4-bitstreams. A redundáns hangsávoknak szemantikailag egyenértékűnek kell lenniük, ugyanazzal a töredék időbélyeggel, és a fejléc és a töredék szintjén is felcserélhetők.
1. Győződjön meg arról, hogy az élő kiszolgáló jegyzékfájljában (6. szakasz, [1]) szereplő "hang" bejegyzés ugyanaz, mint az összes redundáns hangsávot.

A redundáns hangsávok esetében a következő implementáció javasolt:

1. Az egyes egyedi hangsávokat saját maga is elküldheti egy adatfolyamban. Továbbá küldjön egy redundáns streamet az egyes hangsávokhoz, ahol a második adatfolyam csak a HTTP POST URL-cím ({Protocol}://{Server cím}/{Publishing pont elérési útja})/Streams ({Identifier}) esetében az elsővel eltér.
1. A két legalacsonyabb videó-bitrátát külön streamek használatával küldheti el. Ezen adatfolyamok mindegyikének tartalmaznia kell az egyes egyedi hangsávok másolatát is. Ha például több nyelv is támogatott, a streameknek az egyes nyelvekhez tartozó hangsávokat kell tartalmazniuk.
1. Használjon külön kiszolgáló-(kódoló-) példányokat a (1) és a (2) bekezdésben említett redundáns streamek kódolásához és elküldéséhez. 

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png