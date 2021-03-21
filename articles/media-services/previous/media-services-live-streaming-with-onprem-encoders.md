---
title: Élő stream a többszörös átviteli sebességű streameket létrehozó helyszíni kódolókkal – Azure | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan állíthat be egy olyan csatornát, amely több-sávszélességű élő streamet fogad egy helyszíni kódolóból.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 316372f091833519f0479d07355d2845c82743b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014842"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>A helyszíni kódolók többszörös átviteli sebességű streamjét fogadó csatornák használata

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A 2018. május 12. után az élő csatornák már nem támogatják az RTP/MPEG-2 Transport stream betöltési protokollt. Telepítse át a következőt: RTP/MPEG-2 – RTMP vagy darabolt MP4 (Smooth Streaming) betöltési protokollok.

## <a name="overview"></a>Áttekintés
Azure Media Services a *csatorna* az élő közvetítéssel végzett tartalom feldolgozásának folyamatát jelöli. A csatorna az élő bemeneti streameket kétféleképpen fogadja el:

* A helyszíni élő kódoló egy többszörös sávszélességű RTMP vagy Smooth Streaming (töredezett MP4) streamet küld a csatornára, amely nincs engedélyezve az élő kódolás végrehajtásához Media Services. A betöltött adatfolyamok további feldolgozás nélkül haladnak át a csatornákon. Ezt a metódust *áteresztőnek* nevezzük. Egy élő kódoló olyan csatornára is küldhet egysebességű streamet, amely nem engedélyezett az élő kódoláshoz, de ezt nem javasoljuk. Media Services továbbítja a streamet a kérelmező ügyfeleknek.

  > [!NOTE]
  > Az átmenő módszer használata a leggazdaságosabb módja az élő közvetítésnek.


* A helyszíni élő kódoló egy átviteli sebességű streamet küld a csatornának, amely lehetővé teszi, hogy élő kódolást végezzen Media Services az alábbi formátumok valamelyikével: RTMP vagy Smooth Streaming (töredezett MP4). A csatorna ezután a bejövő egyszeri átviteli sebességű adatfolyam élő kódolását egy többszörös sávszélességű (adaptív) videó streambe végzi. Media Services továbbítja a streamet a kérelmező ügyfeleknek.

A Media Services 2,10 kiadástól kezdve a csatorna létrehozásakor megadhatja, hogy a csatorna hogyan kapja meg a bemeneti adatfolyamot. Azt is megadhatja, hogy a csatorna az adatfolyam élő kódolását kívánja-e végrehajtani. Erre két lehetősége van:

* **Továbbítás**: akkor adja meg ezt az értéket, ha olyan helyszíni élő kódolót szeretne használni, amelynek kimenete több sávszélességű stream (egy csatlakoztatott adatfolyam). Ebben az esetben a bejövő adatfolyam kódolás nélkül halad át a kimeneten. Ez egy csatorna viselkedése a 2,10-es kiadás előtt. Ez a cikk részletesen ismerteti az ilyen típusú csatornák használatának részleteit.
* **Live Encoding**: akkor válassza ezt az értéket, ha a Media Services használatával kívánja kódolni az egysebességű élő streamet egy többszörös sávszélességű adatfolyamba. Egy **futó** állapotú élő kódolási csatorna elhagyása a számlázási díjakkal. Javasoljuk, hogy az élő adatfolyam-továbbítási esemény befejezése után azonnal állítsa le a futó csatornákat, hogy elkerülje a felesleges óránkénti díjat. Media Services továbbítja a streamet a kérelmező ügyfeleknek.

> [!NOTE]
> Ez a cikk a csatornák olyan attribútumait ismerteti, amelyek nem engedélyezettek az élő kódolás végrehajtásához. További információ az élő kódolást lehetővé tévő csatornák használatáról: [élő közvetítés Azure Media Services használatával többszörös sávszélességű adatfolyamok létrehozásához](media-services-manage-live-encoder-enabled-channels.md).
>
>További információ a helyszíni kódolókkal kapcsolatban: ajánlott helyszíni [kódolók](media-services-recommended-encoders.md).

A következő ábra egy élő adatfolyam-továbbítási munkafolyamatot jelöl, amely egy helyszíni élő kódolót használ, hogy a többszörös sávszélességű RTMP vagy darabolt MP4 (Smooth Streaming) adatfolyamok legyenek kimenetként.

![Élő munkafolyamat][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Gyakori élő közvetítés forgatókönyve
A következő lépések ismertetik a gyakori élő adatfolyam-alkalmazások létrehozásával kapcsolatos feladatokat.

1. Csatlakoztasson a számítógéphez egy videokamerát. Egy olyan helyszíni élő kódoló elindítása és konfigurálása, amelynek kimenete többszörös sávszélességű RTMP vagy töredezett MP4 (Smooth Streaming) Stream. További tájékoztatást az [Azure Media Services RTMP Support and Live Encoders](https://go.microsoft.com/fwlink/?LinkId=532824) (Az Azure Media Services RTMP-támogatása és az élő kódolók) című cikk nyújt.

    Ezt a lépést a csatorna létrehozása után is elvégezheti.
2. Csatorna létrehozása és elindítása.

3. A csatorna betöltési URL-címének beolvasása.

    Az élő kódoló a betöltési URL-cím használatával küldi el a streamet a csatornának.
4. A csatorna előnézeti URL-címének beolvasása.

    Ezen az URL-címen győződhet meg róla, hogy a csatorna rendben megkapja-e az élő streamet.
5. Hozzon létre egy programot.

    A Azure Portal használatakor a program létrehoz egy eszközt is.

    A .NET SDK vagy a REST használata esetén létre kell hoznia egy eszközt, és meg kell adnia, hogy a program létrehozásakor használja ezt az eszközt.
6. A programhoz társított eszköz közzététele.   

    >[!NOTE]
    >A Azure Media Services-fiók létrehozásakor a rendszer egy **alapértelmezett** folyamatos átviteli végpontot ad hozzá a fiókhoz a **leállított** állapotban. A tartalom-továbbításhoz használt streamvégpontnak **Fut** állapotban kell lennie.

7. Indítsa el a programot, amikor készen áll a folyamatos átvitelre és archiválásra.

8. Ha kívánja, a kódolólónak küldött jelzéssel hirdetést is elindíthat. A hirdetés a kimeneti adatfolyamba lesz beszúrva.

9. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.

10. Törölje a programot (és opcionálisan törölje az eszközt).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>A csatorna és a hozzá kapcsolódó összetevők leírása
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Csatorna bemeneti (betöltési) konfigurációi
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Streaming Protocol betöltése
Media Services támogatja az élő hírcsatornák betöltését a többszörös sávszélességű töredezett MP4 és a többszörös sávszélességű RTMP használatával streaming protokollokként. Ha ki van választva az RTMP betöltésének folyamatos átviteli protokollja, a rendszer két betöltési (bemeneti) végpontot hoz létre a csatornához:

* **Elsődleges URL-cím**: megadja a csatorna elsődleges RTMP betöltési végpontjának teljes URL-címét.
* **Másodlagos URL-cím** (nem kötelező): megadja a csatorna másodlagos RTMP betöltési végpontjának teljes URL-címét.

Használja a másodlagos URL-címet, ha javítani szeretné a betöltési adatfolyam tartósságát és hibatűrését (valamint a kódoló feladatátvételét és hibatűrését), különösen a következő esetekben:

- Egykódolós dupla küldés mind az elsődleges, mind a másodlagos URL-címekre:

    Ennek a forgatókönyvnek a fő célja, hogy nagyobb rugalmasságot biztosítson a hálózati ingadozások és a vibrálás terén. Néhány RTMP-kódoló nem kezeli a hálózat leválasztását. Hálózati kapcsolat bontásakor egy kódoló leállíthatja a kódolást, és nem küldheti el a pufferelt adatkapcsolatot, amikor újracsatlakozás történik. Ez a megszakításokat és az adatvesztést eredményezi. A hálózat leválasztása az Azure-oldal rossz hálózata vagy karbantartása miatt fordulhat elő. Az elsődleges/másodlagos URL-címek csökkentik a hálózati problémákat, és biztosítanak egy vezérelt frissítési folyamatot. Minden alkalommal, amikor egy ütemezett hálózat kapcsolata megtörténik, Media Services kezeli az elsődleges és a másodlagos leválasztásokat, és késleltetett leválasztást biztosít a kettő között. A kódolók ezután időt biztosítanak az adatok küldésére és újbóli újrakapcsolódására. A leválasztások sorrendje véletlenszerű lehet, de az elsődleges/másodlagos vagy másodlagos/elsődleges URL-címek között mindig megmaradnak a késések. Ebben az esetben a kódoló még mindig az egyetlen meghibásodási pont.

- Több kódoló, és mindegyik kódoló dedikált pontra irányul:

    Ez a forgatókönyv mindkét kódolót és a redundancia betöltését teszi lehetővé. Ebben a forgatókönyvben a encoder1 leküldi az elsődleges URL-címet, és a encoder2 leküldi a másodlagos URL-címre. Ha egy kódoló meghibásodik, a másik kódoló megtarthatja az adatok küldését. Az adatredundancia karbantartható, mert Media Services nem bontja le egyszerre az elsődleges és a másodlagos URL-címeket. Ez a forgatókönyv feltételezi, hogy a kódolók időben szinkronizálva vannak, és pontosan ugyanazokat az adategységeket adják meg.  

- Több kódoló az elsődleges és a másodlagos URL-címekre is duplán törekszik:

    Ebben a forgatókönyvben mindkét kódoló az elsődleges és a másodlagos URL-címekre küldi az adatküldést. Ez biztosítja a legjobb megbízhatóságot és hibatűrést, valamint az adatredundanciát. Ez a forgatókönyv képes a kódoló meghibásodására és a leválasztásra, még akkor is, ha az egyik kódoló működése leáll. Azt feltételezi, hogy az idő szinkronizálja a kódolókat, és pontosan ugyanazokat az adategységeket adja meg.  

További információ a RTMP élő kódolókkal kapcsolatban: [Azure Media Services RTMP-támogatás és élő kódolók](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Betöltési URL-címek (végpontok)
A csatorna egy bemeneti végpontot (betöltési URL-címet) biztosít az élő kódolóban, így a kódoló leküldheti a streameket a csatornákra.   

A betöltési URL-címek a csatorna létrehozásakor szerezhetők be. Az URL-címek megszerzéséhez a csatornának nem kell **futó** állapotban lennie. Amikor készen áll arra, hogy elindítsa az adattovábbítást a csatornára, a csatornának **futó** állapotban kell lennie. Miután a csatorna elkezdi az adatfeldolgozást, az előzetes verziójú URL-címen megtekintheti az adatfolyamot.

Lehetősége van feldarabolt MP4 (Smooth Streaming) élő streamek betöltésére TLS-kapcsolaton keresztül. A TLS-kapcsolat betöltéséhez frissítse a betöltési URL-címet HTTPS-re. Jelenleg nem lehet betöltést a TLS protokollon keresztül.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Kulcsképek intervalluma
Ha helyszíni élő kódolót használ a többszörös átviteli sebességű streamek létrehozásához, a kulcsképek intervalluma határozza meg, hogy az adott külső kódoló milyen hosszú ideig használja a képcsoportot (GOP). Miután a csatorna megkapja ezt a bejövő adatfolyamot, a következő formátumok bármelyikében elérhetővé teheti az élő streamet az ügyfelek lejátszási alkalmazásaihoz: Smooth Streaming, dinamikus adaptív átvitel HTTP-n keresztül (DASH) és HTTP Live Streaming (HLS). Ha élő adatfolyamot használ, a HLS mindig dinamikusan van csomagolva. Alapértelmezés szerint a Media Services automatikusan kiszámítja a HLS szegmens csomagolási arányát (szegmensek szerinti töredékek) az élő kódoló által fogadott kulcsképek-intervallum alapján.

A következő táblázat a szegmens időtartamának kiszámítását mutatja be:

| Kulcsképek intervalluma | HLS szegmens csomagolási aránya (FragmentsPerSegment) | Példa |
| --- | --- | --- |
| Kisebb vagy egyenlő, mint 3 másodperc |3:1 |Ha a KeyFrameInterval (vagy a GOP) 2 másodperc, az alapértelmezett HLS-szegmens csomagolási aránya 3 – 1. Ez egy 6 másodperces HLS-szegmenst hoz létre. |
| 3 – 5 másodperc |2:1 |Ha a KeyFrameInterval (vagy GOP) 4 másodperc, az alapértelmezett HLS-szegmens csomagolási aránya 2 – 1. Ez egy 8 másodperces HLS-szegmenst hoz létre. |
| 5 másodpercnél nagyobb |1:1 |Ha a KeyFrameInterval (vagy a GOP) 6 másodperc, az alapértelmezett HLS-szegmens csomagolási aránya 1 és 1 közötti. Ez egy 6 másodperces HLS-szegmenst hoz létre. |

A töredékek szegmensek arányát a csatorna kimenetének konfigurálásával és a ChannelOutputHls FragmentsPerSegment beállításával módosíthatja.

A kulcsképek intervallumának értékét a ChannelInput KeyFrameInterval tulajdonságának beállításával is módosíthatja. Ha explicit módon beállította a KeyFrameInterval-t, a HLS szegmens csomagolási arányának FragmentsPerSegment a korábban leírt szabályok alapján számítjuk ki.  

Ha explicit módon beállította a KeyFrameInterval és a FragmentsPerSegment is, a Media Services a beállított értékeket használja.

#### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja azokat az IP-címeket, amelyek számára engedélyezett a videó közzététele a csatornán. Az engedélyezett IP-címek az alábbiak egyike adhatók meg:

* Egyetlen IP-cím (például 10.0.0.1)
* IP-címet és CIDR alhálózati maszkot használó IP-címtartomány (például 10.0.0.1/22)
* IP-címet és pontozott decimális alhálózati maszkot használó IP-címtartomány (például 10.0.0.1 (255.255.252.0))

Ha nincs megadva IP-cím, és nincs szabály definíciója, akkor nem engedélyezett az IP-cím. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.

### <a name="channel-preview"></a>Csatorna előnézete
#### <a name="preview-urls"></a>Előnézeti URL-címek
A csatornák olyan előzetes verziójú végpontot (előzetes verziójú URL-címet) biztosítanak, amelyet a további feldolgozás és a továbbítás előtt a stream előzetes verziójának megtekintéséhez és ellenőrzéséhez használ.

A csatorna létrehozásakor az előnézeti URL-címet is beolvashatja. Az URL-cím lekéréséhez a csatornának nem kell **futó** állapotban lennie. Miután a csatorna elkezdi az adatfeldolgozást, megtekintheti az adatfolyamot.

Jelenleg az előzetes verziójú adatfolyam csak töredezett MP4 (Smooth Streaming) formátumban továbbítható, a megadott bemeneti típustól függetlenül. A Smooth stream teszteléséhez használhatja a [Smooth streaming Health Monitor](https://playready.directtaps.net/smoothstreaming/) -lejátszót. Az adatfolyam megtekintéséhez használhatja a Azure Portalban üzemeltetett lejátszót is.

#### <a name="allowed-ip-addresses"></a>Engedélyezett IP-címek
Megadhatja azokat az IP-címeket, amelyek számára engedélyezett az előnézeti végponthoz való kapcsolódás. Ha nincs megadva IP-cím, a rendszer minden IP-címet engedélyez. Az engedélyezett IP-címek az alábbiak egyike adhatók meg:

* Egyetlen IP-cím (például 10.0.0.1)
* IP-címet és CIDR alhálózati maszkot használó IP-címtartomány (például 10.0.0.1/22)
* IP-címet és pontozott decimális alhálózati maszkot használó IP-címtartomány (például 10.0.0.1 (255.255.252.0))

### <a name="channel-output"></a>Csatorna kimenete
További információ a csatorna kimenetéről: a [kulcsképek intervallumának](#keyframe_interval) szakasza.

### <a name="channel-managed-programs"></a>Csatorna által felügyelt programok
A csatornák olyan programokhoz vannak társítva, amelyek segítségével szabályozható az élő adatfolyamban lévő szegmensek közzététele és tárolása. A csatornákat a programok kezelik. A csatorna és a program kapcsolata hasonló a hagyományos adathordozóhoz, ahol a csatornán állandó tartalom található, és a program hatóköre az adott csatornán futó eseményekre vonatkozik.

Az **Archive Window** (Archiválás időtartama) beállításnál megadhatja, hogy hány órára szeretné megőrizni a program felvett tartalmát. Ez az érték 5 perc és 25 óra közötti lehet. Az archiválási ablak hossza azt is megköveteli, hogy az ügyfelek legfeljebb hány időt tölthetnek vissza az aktuális élő pozícióból. Az események hosszabbak lehetnek a megadott időtartamnál, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a megadott időtartamnál. Ennek a tulajdonságnak az értéke határozza meg azt is, hogy milyen hosszúra nőhetnek az ügyfél jegyzékfájljai.

Minden program egy olyan objektumhoz van társítva, amely a továbbított tartalmat tárolja. Az eszköz az Azure Storage-fiókban lévő blokk blob-tárolóra van leképezve, és az objektumban található fájlok blobként tárolódnak a tárolóban. A program közzétételéhez, hogy az ügyfelek megtekinthessék a streamet, létre kell hoznia egy OnDemand-lokátort a társított objektumhoz. Ezt a lokátort használhatja az ügyfelek számára megadható streaming URL-cím létrehozásához.

Egy csatorna legfeljebb három párhuzamosan futó programot támogat, így több archívumot is létrehozhat ugyanahhoz a bejövő adatfolyamhoz. Egy esemény különböző részeit szükség szerint teheti közzé és archiválhatja. Tegyük fel például, hogy az üzleti követelmény egy program 6 órányi archiválása, de csak az utolsó 10 percet kell közvetíteni. Ezt két egyidejűleg zajló program létrehozásával érheti el. Az egyik program az esemény hat órájának archiválására van beállítva, de a program nincs közzétéve. A másik program 10 perces archiválásra van beállítva, és ez a program közzé van téve.

A meglévő programokat nem szabad új eseményekhez ismét felhasználni. Ehelyett hozzon létre egy új programot az egyes eseményekhez. Indítsa el a programot, amikor készen áll a folyamatos átvitelre és archiválásra. Állítsa le a programot, ha szeretné megállítani az adatfolyam-továbbítást, és archiválni kívánja az eseményt.

Az archivált tartalom törléséhez állítsa le és törölje a programot, majd törölje a hozzá tartozó eszközt. Egy eszköz nem törölhető, ha egy program azt használja. Először törölni kell a programot.

Még a program leállítása és törlése után is igény szerint továbbíthatja az archivált tartalmat videóként, amíg nem törli az eszközt. Ha meg szeretné őrizni az archivált tartalmat, de nem érhető el a streaming számára, törölje a folyamatos átviteli lokátort.

## <a name="channel-states-and-billing"></a><a id="states"></a>Csatorna állapotok és számlázás
A csatorna aktuális állapotának lehetséges értékei a következők:

* **Leállítva**: Ez a csatorna kezdeti állapota a létrehozás után. Ebben az állapotban a csatorna tulajdonságai frissíthetők, de a folyamatos átvitel nem engedélyezett.
* **Indítás**: a csatorna indítása folyamatban van. Ebben az állapotban nem engedélyezett a frissítés vagy a folyamatos átvitel. Ha hiba történik, a csatorna **visszaállított** állapotba tér vissza.
* **Fut**: a csatorna képes az élő streamek feldolgozására.
* **Leállítás**: a csatorna leállítása folyamatban van. Ebben az állapotban nem engedélyezett a frissítés vagy a folyamatos átvitel.
* **Törlés**: a csatorna törlése folyamatban van. Ebben az állapotban nem engedélyezett a frissítés vagy a folyamatos átvitel.

Az alábbi táblázat azt mutatja be, hogy a csatorna állapotai hogyan képezhetők le a számlázási módra.

| Csatorna állapota | A portál felhasználói felületének kijelzői | Számlázása? |
| --- | --- | --- |
| **Indítás** |**Indítás** |Nem (átmeneti állapot) |
| **Futó** |**Kész** (nincs futó program)<p><p>vagy<p>**Streaming** (legalább egy futó program) |Yes |
| **Leállítás** |**Leállítás** |Nem (átmeneti állapot) |
| **Leállítva** |**Leállítva** |No |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Kódolt feliratok és ad-Beszúrás
Az alábbi táblázat a kódolt feliratozás és az ad-Beszúrás támogatott szabványait mutatja be.

| Standard | Jegyzetek |
| --- | --- |
| CEA-708 és EIA-608 (708/608) |A CEA-708 és a KHV-608 a Egyesült Államok és Kanada esetében bezárt feliratozási szabványok.<p><p>Jelenleg a feliratozás csak akkor támogatott, ha a kódolt bemeneti adatfolyamban történik. Olyan élő adathordozó-kódolót kell használnia, amely 608 vagy 708 feliratot szúr be a Media Servicesba küldött kódolt adatfolyamba. Media Services a tartalmat beszúrt feliratokkal látja el a nézők számára. |
| TTML belül. ismt (Smooth Streaming szöveges számok) |Media Services dinamikus csomagolás lehetővé teszi, hogy az ügyfelek a következő formátumok bármelyikében továbbítsák a tartalmakat: DASH, HLS vagy Smooth Streaming. Ha azonban a. ismt (Smooth Streaming Text tracks) feliratokkal rendelkező töredezett MP4 (Smooth Streaming) betöltést végez, az adatfolyamot csak Smooth Streaming ügyfeleknek továbbíthatja. |
| SCTE-35 |A SCTE-35 egy digitális jelzőrendszer, amely a hirdetés beszúrására szolgál. Az alsóbb rétegbeli fogadók a jelet használva összekapcsolják a hirdetést a streamben a rendelkezésre álló időn belül. A SCTE-35-et ritka számként kell elküldeni a bemeneti adatfolyamban.<p><p>Jelenleg az ad-jeleket tartalmazó támogatott bemeneti adatfolyam formátuma töredékes MP4 (Smooth Streaming). Az egyetlen támogatott kimeneti formátum is Smooth Streaming. |

## <a name="considerations"></a><a id="considerations"></a>Megfontolandó szempontok
Ha helyszíni élő kódolót használ a többszörös átviteli sebességű stream egy csatornába való küldéséhez, a következő korlátozások érvényesek:

* Győződjön meg arról, hogy elegendő szabad internetkapcsolattal rendelkezik a betöltési pontokra történő adatküldéshez.
* A másodlagos betöltési URL-cím használata további sávszélességet igényel.
* A bejövő többszörös átviteli sebességű adatfolyam legfeljebb 10 videó minőségi szintet (réteget) és legfeljebb 5 hangsávot tartalmazhat.
* A videó minőségi szintjeinek legnagyobb átlagos bitrátájának 10 Mbps-nál kisebbnek kell lennie.
* A videó-és hangadatfolyamok átlagos bitrátájának összesített értékének 25 Mbps-nál kisebbnek kell lennie.
* A bemeneti protokoll nem módosítható, amíg a csatorna vagy a hozzá tartozó programok futnak. Ha más protokollt szeretne használni, hozzon létre külön-külön csatornákat az egyes bemeneti protokollokhoz.
* A csatornán egyetlen bitráta is betölthető. Mivel azonban a csatorna nem dolgozza fel a streamet, az ügyfélalkalmazások egyetlen bitráta-adatfolyamot is kapnak. (Ezt a beállítást nem javasoljuk.)

A csatornákkal és a kapcsolódó összetevőkkel kapcsolatos további szempontok:

* Minden alkalommal, amikor újrakonfigurálja az élő kódolót, hívja meg az **alaphelyzetbe állítási** módszert a csatornán. A csatorna alaphelyzetbe állítása előtt le kell állítania a programot. A csatorna alaphelyzetbe állítása után indítsa újra a programot.

  > [!NOTE]
  > A program újraindításakor hozzá kell rendelnie egy új objektumhoz, és létre kell hoznia egy új lokátort. 
  
* Egy csatornát csak akkor lehet leállítani, ha **fut** állapotban van, és a csatornán lévő összes program le lett állítva.
* Alapértelmezés szerint csak öt csatornát vehet fel Media Services-fiókjába. További információ: [kvóták és korlátozások](media-services-quotas-and-limitations.md).
* A számlázás csak akkor történik meg, ha a csatorna **fut** állapotban van. További információkért lásd a [csatorna állapotának és számlázásának](media-services-live-streaming-with-onprem-encoders.md#states) szakaszát.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="suggestions-and-feedback"></a>Javaslatok és visszajelzés

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Helyszíni kódolók esetén ajánlott](media-services-recommended-encoders.md)

[Azure Media Services darabolt MP4-él a specifikáció betöltése](../media-services-fmp4-live-ingest-overview.md)

[Azure Media Services áttekintése és gyakori forgatókönyvek](media-services-overview.md)

[Media Services fogalmak](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
