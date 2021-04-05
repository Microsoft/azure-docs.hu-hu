---
title: Teljesítmény-és skálázhatósági ellenőrzőlista a blob Storage-hoz – Azure Storage
description: A nagy teljesítményű alkalmazások fejlesztése során a blob Storage-hoz való használatra bevált eljárások ellenőrzőlistája.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 14da8b6cb695703f1881b6b0b9858772bde386c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544751"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>A blob Storage teljesítmény-és méretezhetőségi ellenőrzőlistája

A Microsoft számos bevált gyakorlatot fejlesztett ki a nagy teljesítményű alkalmazások blob Storage-alapú fejlesztéséhez. Ez az ellenőrzőlista azokat a kulcsfontosságú eljárásokat azonosítja, amelyeket a fejlesztők követhetnek a teljesítmény optimalizálása érdekében. Tartsa szem előtt ezeket a gyakorlatokat az alkalmazás tervezésekor és a folyamat során.

Az Azure Storage méretezhetőségi és teljesítménybeli célokat biztosít a kapacitáshoz, a tranzakciós sebességhez és a sávszélességhez. Az Azure Storage skálázhatósági céljaival kapcsolatos további információkért lásd: [skálázhatósági és teljesítményi célok a standard szintű Storage-fiókokhoz](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) , valamint [méretezhetőség és teljesítménybeli célok a blob Storage-](scalability-targets.md)hoz.

## <a name="checklist"></a>Ellenőrzőlista

Ez a cikk bevált eljárásokat szervez a teljesítményre vonatkozóan a blob Storage-alkalmazás fejlesztése során követendő ellenőrzőlistán.

| Kész | Kategória | Tervezési szempont |
| --- | --- | --- |
| &nbsp; |Méretezhetőségi célok |[Megtervezheti, hogy az alkalmazás ne legyen több, mint a Storage-fiókok maximális száma?](#maximum-number-of-storage-accounts) |
| &nbsp; |Méretezhetőségi célok |[Elkerüli a kapacitást és a tranzakciós korlátokat?](#capacity-and-transaction-targets) |
| &nbsp; |Méretezhetőségi célok |[Egyszerre több ügyfél fér hozzá egyetlen blobhoz?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Méretezhetőségi célok |[Az alkalmazása a méretezhetőségi célokon belül marad egyetlen blob esetében?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Particionálás |[Az elnevezési konvenció úgy lett kialakítva, hogy jobb terheléselosztást lehessen kialakítani?](#partitioning) |
| &nbsp; |Hálózatkezelés |[A szükséges teljesítmény elérése érdekében az ügyféloldali eszközök megfelelően nagy sávszélességgel és kis késéssel rendelkeznek?](#throughput) |
| &nbsp; |Hálózatkezelés |[Az ügyféloldali eszközök magas színvonalú hálózati kapcsolattal rendelkeznek?](#link-quality) |
| &nbsp; |Hálózatkezelés |[Az ügyfélalkalmazás ugyanabban a régióban található, mint a Storage-fiók?](#location) |
| &nbsp; |Közvetlen ügyfél-hozzáférés |[Közös hozzáférésű aláírásokat (SAS) és több eredetű erőforrás-megosztást (CORS) használ az Azure Storage-hoz való közvetlen hozzáférés engedélyezéséhez?](#sas-and-cors) |
| &nbsp; |Gyorsítótárazás |[Az alkalmazás gyorsítótárazza a gyakran használt és ritkán módosított adatait?](#reading-data) |
| &nbsp; |Gyorsítótárazás |[Az alkalmazás batch-frissítése az ügyfélen végzett gyorsítótárazással történik, majd feltölti őket nagyobb készletekbe?](#uploading-data-in-batches) |
| &nbsp; |.NET-konfiguráció |[A .NET Core 2,1-as vagy újabb verzióját használja az optimális teljesítmény érdekében?](#use-net-core) |
| &nbsp; |.NET-konfiguráció |[Konfigurálta az ügyfelet az egyidejű kapcsolatok megfelelő számának használatára?](#increase-default-connection-limit) |
| &nbsp; |.NET-konfiguráció |[A .NET-alkalmazások esetében megfelelő számú szál használatára konfigurálta a .NET-et?](#increase-minimum-number-of-threads) |
| &nbsp; |Párhuzamosság |[Gondoskodott arról, hogy a párhuzamosságok megfelelően legyenek kötve, hogy ne legyenek túlterhelve az ügyfél képességei, vagy közelítse meg a méretezhetőségi célokat?](#unbounded-parallelism) |
| &nbsp; |Eszközök |[A Microsoft által biztosított ügyféloldali kódtárak és eszközök legújabb verzióit használja?](#client-libraries-and-tools) |
| &nbsp; |Újrapróbálkozások |[Újrapróbálkozási szabályzatot használ egy exponenciális leállítási a hibák és időtúllépések szabályozásához?](#timeout-and-server-busy-errors) |
| &nbsp; |Újrapróbálkozások |[Az alkalmazás elkerüli a nem újrapróbálkozást okozó hibák újrapróbálkozását?](#non-retryable-errors) |
| &nbsp; |Blobok másolása |[A lehető leghatékonyabb módon másolja a blobokat?](#blob-copy-apis) |
| &nbsp; |Blobok másolása |[A AzCopy legújabb verzióját használja a tömeges másolási műveletekhez?](#use-azcopy) |
| &nbsp; |Blobok másolása |[A Azure Data Box családot használja nagy adatmennyiségek importálására?](#use-azure-data-box) |
| &nbsp; |Tartalom terjesztése |[CDN-t használ a tartalom terjesztéséhez?](#content-distribution) |
| &nbsp; |Metaadatok használata |[Tárolja a Blobok gyakran használt metaadatait a metaadatokban?](#use-metadata) |
| &nbsp; |Gyors feltöltés |[Amikor egy blob gyors feltöltését kísérli meg, párhuzamosan kell feltölteni a blokkokat?](#upload-one-large-blob-quickly) |
| &nbsp; |Gyors feltöltés |[Sok blob gyors feltöltésének megkísérlésekor a blobokat párhuzamosan kell feltölteni?](#upload-many-blobs-quickly) |
| &nbsp; |Blobtípus |[Az oldal blobokat használja, vagy ha szükséges, a Blobok blokkolása?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Méretezhetőségi célok

Ha az alkalmazás megközelíti vagy túllépi a méretezhetőségi célokat, akkor a tranzakciós késések vagy a szabályozás nagyobb mértékben merülhet fel. Amikor az Azure Storage szabályozza az alkalmazást, a szolgáltatás megkezdi a 503 (foglalt kiszolgáló) vagy a 500 (művelet időtúllépése) hibakódok visszaadását. A méretezhetőségi célok korlátain belül maradó hibák elkerülésének fontos része az alkalmazás teljesítményének növelése.

További információ a Queue szolgáltatás skálázhatósági céljairól: az [Azure Storage skálázhatósági és teljesítményi céljai](../queues/scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Storage-fiókok maximális száma

Ha egy adott előfizetés/régió kombináció számára engedélyezett tárolási fiókok maximális számát keresi, értékelje ki a forgatókönyvet, és állapítsa meg, hogy az alábbi feltételek bármelyike teljesül-e:

- A Storage-fiókok segítségével tárolja a nem felügyelt lemezeket, és hozzáadja ezeket a lemezeket a virtuális gépekhez? Ebben az esetben a Microsoft a felügyelt lemezek használatát javasolja. A felügyelt lemezek méretezése automatikusan történik, és nincs szükség egyéni Storage-fiókok létrehozására és felügyeletére. További információ: [Bevezetés az Azure Managed Disks](../../virtual-machines/managed-disks-overview.md) szolgáltatásba
- Felhasználónként használ egy Storage-fiókot az adatelkülönítés érdekében? Ebben a forgatókönyvben a Microsoft azt javasolja, hogy minden ügyfélhez BLOB-tárolót használjon, a teljes Storage-fiók helyett. Az Azure Storage mostantól lehetővé teszi, hogy az Azure-szerepköröket tároló alapján rendeljen hozzá. További információ: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-adatokhoz való hozzáféréshez](../common/storage-auth-aad-rbac-portal.md).
- Több Storage-fiókot használ a szilánkok számára a bejövő forgalom, a kimenő I/O-műveletek másodpercenkénti (IOPS) vagy kapacitásának növelésére? Ebben a forgatókönyvben a Microsoft azt javasolja, hogy a Storage-fiókok megnövekedett korlátainak kihasználásával csökkentse a munkaterhelés számára szükséges tárolási fiókok számát, ha lehetséges. Vegye fel a kapcsolatot az [Azure támogatási szolgálatával](https://azure.microsoft.com/support/options/) , és kérjen nagyobb korlátokat a Storage-fiókjához. További információ: [nagyobb méretű, magasabb szintű Storage-fiókok bejelentése](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapacitás-és tranzakciós célok

Ha az alkalmazása közeledik egyetlen Storage-fiók skálázhatósági céljaihoz, vegye figyelembe az alábbi módszerek egyikét:  

- Ha az alkalmazás eléri a tranzakció célját, érdemes lehet blokkolni a blob Storage-fiókokat, amelyek magas tranzakciós sebességre és alacsony és konzisztens késésre vannak optimalizálva. További információkat az [Azure Storage-fiókok áttekintésében](../common/storage-account-overview.md) találhat.
- Gondolja át a számítási feladatokat, amelyek hatására az alkalmazás megközelíti vagy túllépi a méretezhetőségi célt. Másképpen is megtervezhető, hogy kevesebb sávszélességet vagy kapacitást vagy kevesebb tranzakciót használjon?
- Ha az alkalmazásnak meg kell haladnia a méretezhetőségi célok valamelyikét, hozzon létre több Storage-fiókot, és particionálja az alkalmazásadatok körét a több Storage-fiók között. Ha ezt a mintát használja, ügyeljen arra, hogy tervezze meg az alkalmazását, hogy a jövőben további Storage-fiókokat lehessen hozzáadni a terheléselosztáshoz. A Storage-fiókok önmagukban a tárolt adatok, a tranzakciók vagy az átvitt adatok alapján nem számítanak bele a használati díjakba.
- Ha az alkalmazás közeledik a sávszélesség-célokhoz, érdemes lehet az ügyfél oldalán lévő adatok tömörítését az adatok Azure Storage-ba való küldéséhez szükséges sávszélesség csökkentése érdekében.
    Az adatok tömörítése csökkentheti a sávszélességet, és javíthatja a hálózati teljesítményt, továbbá negatív hatással lehet a teljesítményre. Értékelje ki a további feldolgozási követelmények teljesítményére gyakorolt hatást az adattömörítés és a kibontás az ügyféloldali oldalon. Ne feledje, hogy a tömörített adattárolók megnehezítik a hibaelhárítást, mivel a szabványos eszközök használatával nagyobb kihívást jelenthet az adatmegtekintés.
- Ha az alkalmazás közeledik a méretezhetőségi célokhoz, akkor győződjön meg arról, hogy exponenciális leállítási használ az újrapróbálkozásokhoz. A legjobb megoldás, ha a jelen cikkben ismertetett javaslatok végrehajtásával el szeretné kerülni a méretezhetőségi célok elérését. Az újrapróbálkozások exponenciális leállítási használata azonban megakadályozza, hogy az alkalmazás gyorsan újrapróbálkozjon, ami még rosszabb szabályozást eredményezhet. További információ: az [időtúllépés és a kiszolgáló foglalt hibák](#timeout-and-server-busy-errors)című szakasza.

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Egyszerre több ügyfél fér hozzá egyetlen blobhoz

Ha nagy számú ügyfél fér hozzá egyszerre egy blobhoz, akkor a blobok és a tárolási fiókok méretezhetőségére vonatkozó célokat is figyelembe kell venni. Az egyetlen blobhoz hozzáférő ügyfelek pontos száma attól függően változhat, hogy milyen tényezők, például a blobot egyidejűleg kérő ügyfelek száma, a blob mérete és a hálózati feltételek.

Ha a blob olyan CDN-en keresztül terjeszthető, mint például a webhelyek által szolgáltatott képek vagy videók, akkor használhat egy CDN-t is. További információt a [Tartalom terjesztése](#content-distribution)című szakaszban talál.

Más forgatókönyvekben, például olyan tudományos szimulációkban, amelyekben az adatok bizalmasak, két lehetőség közül választhat. Az első a munkaterhelések hozzáférésének felosztása úgy, hogy a blob egy adott időtartamon keresztül legyen elérhető, és egyidejűleg legyen elérhető. Azt is megteheti, hogy ideiglenesen átmásolja a blobot több Storage-fiókba, hogy növelje a Blobok teljes IOPS és a különböző tárolási fiókok számát. Az eredmények az alkalmazás működésének függvényében változnak, ezért ügyeljen arra, hogy a kialakítás során tesztelje a párhuzamossági mintákat.

### <a name="bandwidth-and-operations-per-blob"></a>Sávszélesség és műveletek blobon

Egyetlen blob másodpercenként legfeljebb 500 kérelmet támogat. Ha több ügyféllel is el kell olvasnia ugyanazt a blobot, és előfordulhat, hogy túllépi ezt a korlátot, érdemes lehet egy blokk blob Storage-fiókot használni. A blokk blob Storage-fiók nagyobb kérelmeket vagy I/O-műveleteket biztosít másodpercenként (IOPS).

A blobon keresztüli műveletek terjesztéséhez egy Content Delivery Network (CDN) (például Azure CDN) is használható. További információ a Azure CDNről: [Azure CDN áttekintése](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Particionálás

Annak megismerése, hogy az Azure Storage hogyan particionálja a blob adatait a teljesítmény növelése érdekében. Az Azure Storage-ban a több partícióra kiterjedő adattárolók gyorsabban, egyetlen partíción belül is kiszolgálják az adategységeket. A Blobok megfelelő elnevezésével növelheti az olvasási kérelmek hatékonyságát.

A blob Storage egy tartományon alapuló particionálási sémát használ a skálázáshoz és a terheléselosztáshoz. Minden blobhoz tartozik egy partíciós kulcs, amely tartalmazza a blob teljes nevét (fiók + tároló + blob). A partíciós kulcs a blob-adattartományokra való particionálásra szolgál. A tartományok ezután terheléselosztást kapnak a blob Storage-ban.

A tartományon alapuló particionálás azt jelenti, hogy a lexikális sorrendet (például *mypayroll*, *myperformance*, *myemployees* stb.) vagy időbélyegeket (*log20160101*, *log20160102*, *log20160102* stb.) használó elnevezési konvenciók nagyobb valószínűséggel eredményezik, hogy a partíciók ugyanazon a partíció-kiszolgálón helyezkednek el. , amíg a megnövekedett terhelésnek kisebb tartományokra kell bontania. A Blobok ugyanazon a partíciós kiszolgálón való közös elhelyezése javítja a teljesítményt, ezért a teljesítmény fokozásának fontos része a Blobok a leghatékonyabb módon történő megszervezését is magában foglalja.

Egy tárolóban lévő összes blobot például egyetlen kiszolgáló is kiszolgálhat, amíg a Blobok terhelése a partíció-tartományok további kiegyensúlyozását igényli. Hasonlóképpen, a lexikális sorrendben elrendezett fiókokat tartalmazó csoportokat egyetlen kiszolgáló is kiszolgálhatja, amíg az egyik vagy az összes fiók terhelését meg kell osztani több partíciós kiszolgáló között.

Minden terheléselosztási művelet hatással lehet a tárolási hívások késésére a művelet során. A szolgáltatásnak a partícióra irányuló hirtelen adatforgalmának kezelésére korlátozódik egy partíciós kiszolgáló skálázhatósága, amíg a terheléselosztási művelet befejeződik, és kiegyenlíti a partíciós kulcs tartományát.

Az ilyen műveletek gyakoriságának csökkentéséhez kövesse az ajánlott eljárásokat.  

- Ha lehetséges, használjon 4 MiB-nél nagyobb blob-vagy blokkolási méretet a standard szintű Storage-fiókok esetében, és a Premium Storage-fiókoknál nagyobb, mint 256 KiB. A nagyobb blob-vagy blokk-méretek automatikusan aktiválja a nagy átviteli sebességű blokk blobokat. A nagy átviteli sebességű blokk Blobok nagy teljesítményű betöltést biztosítanak, amelyet a partíciók elnevezése nem érint.
- Vizsgálja meg a fiókok, tárolók, blobok, táblák és várólisták elnevezési konvencióját. Vegye fontolóra a fiók, a tároló vagy a blob nevének előjavítását egy háromjegyű kivonattal, amely az igényeinek leginkább megfelelő kivonatoló függvényt használja.
- Ha időbélyegek vagy numerikus azonosítók használatával rendezi az adatokat, ügyeljen arra, hogy ne használjon csak Hozzáfűzés (vagy csak előtag) típusú forgalmi mintát. Ezek a minták nem alkalmasak tartomány alapú particionálási rendszerekhez. Ezek a minták az egyetlen partícióra irányuló összes forgalomhoz vezethetnek, és a rendszernek a hatékony terheléselosztással való korlátozását eredményezhetik.

    Ha például olyan napi műveletekkel rendelkezik, amelyek a blobot egy időbélyeggel (például *ééééhhnn*) használják, akkor az adott napi művelet minden forgalmát egyetlen blobra irányítja a rendszer, amelyet egyetlen partíciós kiszolgáló szolgáltat. Gondolja át, hogy a blob-korlátok és a partíciós korlátok megfelelnek-e az igényeinek, és szükség esetén vegye fontolóra a művelet több blobba való megtörését. Hasonlóképpen, ha a táblázatokban tárolja az idősoros adatokat, a rendszer az összes forgalmat a kulcs névterének utolsó részére irányítja. Ha numerikus azonosítókat használ, az azonosítót egy háromjegyű kivonattal kell előtagja. Ha timestamps-t használ, az időbélyeget a másodperc értékkel, például a *ssyyyymmdd* előtaggal kell elválasztani. Ha az alkalmazás rendszeresen végrehajtja a listázási és lekérdezési műveleteket, akkor válasszon egy kivonatoló függvényt, amely korlátozza a lekérdezések számát. Bizonyos esetekben elegendő lehet egy véletlenszerű előtag.
  
- Az Azure Storage-ban használt particionálási sémával kapcsolatos további információkért lásd [: Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)használatával.

## <a name="networking"></a>Hálózatkezelés

Az alkalmazás fizikai hálózati korlátai jelentős hatással lehetnek a teljesítményre. A következő szakaszok ismertetik néhány korlátozást a felhasználók számára.  

### <a name="client-network-capability"></a>Ügyfél hálózati funkciója

A sávszélesség és a hálózati kapcsolat minősége fontos szerepet játszik az alkalmazás teljesítményében, az alábbi szakaszokban leírtak szerint.

#### <a name="throughput"></a>Teljesítmény

A sávszélesség miatt a probléma gyakran az ügyfél képességei. A nagyobb méretű Azure-példányok nagyobb kapacitású hálózati adapterekkel rendelkeznek, ezért érdemes nagyobb méretű virtuális gépeket használni, ha egy gépről nagyobb hálózati korlátokra van szüksége. Ha egy helyszíni alkalmazásból fér hozzá az Azure Storage-hoz, ugyanez a szabály vonatkozik rá: Ismerje meg az ügyféleszközök hálózati képességeit és a hálózati kapcsolatot az Azure Storage-beli helyhez, vagy javítsa azokat igény szerint, vagy tervezze meg az alkalmazását a képességein belül.

#### <a name="link-quality"></a>Kapcsolat minősége

Ahogy bármilyen hálózati használat esetében, ne feledje, hogy a hibákat eredményező hálózati feltételek és a csomagok elvesztése lassú működést eredményez.  A WireShark vagy a NetMon használata segíthet a probléma diagnosztizálásában.  

### <a name="location"></a>Hely

Minden elosztott környezetben, a-ügyfél közel a kiszolgálóhoz, a legjobb teljesítményt nyújtja. Ha az Azure Storage-t a legalacsonyabb késéssel szeretné elérni, az ügyfél számára a legjobb hely ugyanazon az Azure-régión belül van. Ha például van egy Azure-webalkalmazása, amely az Azure Storage szolgáltatást használja, akkor mindkettőt egyetlen régióban, például az USA nyugati régiójában vagy Délkelet-Ázsiában is megtalálhatja. Az erőforrások közös elhelyezése csökkenti a késést és a költségeket, mivel az egyetlen régión belüli sávszélesség-használat ingyenes.  

Ha az ügyfélalkalmazások hozzáférnek az Azure Storage-hoz, de nem az Azure-ban, például a mobileszköz-alkalmazásokban vagy a helyszíni vállalati szolgáltatásokban vannak tárolva, akkor a Storage-fiók az ügyfelek közelében lévő régiókban is csökkentheti a késést. Ha az ügyfeleket széles körben terjesztik (például néhány Észak-Amerika és néhány európai), akkor érdemes lehet régiónként egy Storage-fiókot használni. Ez a megközelítés könnyebben megvalósítható, ha az alkalmazás által tárolt adattárolók egyediek az egyes felhasználók számára, és nem igénylik az adatreplikálást a Storage-fiókok között.

A Blobok tartalmának széles körű elosztásához használjon egy tartalmat a hálózaton, például Azure CDN. További információ a Azure CDNről: [Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS és CORS

Tegyük fel, hogy engedélyezni kell a kódot, például a JavaScriptet, amely egy felhasználó webböngészőjében vagy egy mobiltelefon-alkalmazásban fut az Azure Storage-ban tárolt adateléréshez. Az egyik módszer egy olyan szolgáltatásalkalmazás létrehozása, amely proxyként működik. A felhasználó eszköze hitelesíti magát a szolgáltatással, amely viszont engedélyezi az Azure Storage-erőforrásokhoz való hozzáférést. Így elkerülhető, hogy a Storage-fiók kulcsa nem biztonságos eszközökön legyen kitéve. Ez a megközelítés azonban jelentős terhelést jelent a szolgáltatásalkalmazás számára, mivel a felhasználó eszközén és az Azure Storage-ban továbbított összes adatmennyiségnek továbbítania kell a szolgáltatásalkalmazás alkalmazásán.

A megosztott hozzáférési aláírások (SAS) használatával elkerülhető, hogy a szolgáltatásalkalmazás proxyként legyen használatban az Azure Storage-ban. A SAS használatával engedélyezheti a felhasználó eszközének, hogy közvetlenül az Azure Storage-ba irányuló kéréseket hozzon igénybe korlátozott hozzáférési jogkivonat használatával. Ha például egy felhasználó egy fényképet szeretne feltölteni az alkalmazásba, a szolgáltatásalkalmazás létrehozhat egy SAS-t, és elküldheti azt a felhasználó eszközére. Az SAS-jogkivonat engedélyezheti az Azure Storage-erőforrásoknak adott időtartamra való írást, amely után az SAS-jogkivonat lejár. Az SAS-vel kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférési aláírások (SAS) használatával](../common/storage-sas-overview.md).  

A webböngésző általában nem engedélyezi a JavaScript használatát egy olyan oldalon, amelyet egy adott tartomány webhelye futtat bizonyos műveletek, például írási műveletek végrehajtásához egy másik tartományba. Ez a házirend megakadályozza, hogy az egyik lapon lévő rosszindulatú parancsfájlok hozzáférjenek egy másik weblapon lévő adatokhoz. Ugyanakkor a felhőben a megoldás kiépítésekor is megadható korlátozás. A több eredetű erőforrás-megosztás (CORS) egy böngésző-szolgáltatás, amely lehetővé teszi a célként megadott tartomány számára, hogy kommunikáljon a forrás tartományból származó kérelmeket megbízható böngészővel.

Tegyük fel például, hogy egy Azure-ban futó webalkalmazás egy Azure Storage-fiókhoz kér egy erőforrást. A webalkalmazás a forrástartomány, a Storage-fiók pedig a célként megadott tartomány. A CORS bármely Azure Storage-szolgáltatáshoz beállíthatja úgy, hogy az Azure Storage által megbízhatónak tekintse azt a böngészőt, amelyet a forrástartománytól érkező kérések biztosítanak. A CORS kapcsolatos további információkért lásd: [Az Azure Storage-hoz készült több eredetű erőforrás-megosztás (CORS) támogatása](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Az SAS és a CORS is segíthet elkerülni a webalkalmazás szükségtelen terhelését.  

## <a name="caching"></a>Gyorsítótárazás

A gyorsítótárazás fontos szerepet játszik a teljesítményben. Az alábbi fejezetek a gyorsítótárazási ajánlott eljárásokat tárgyalják.

### <a name="reading-data"></a>Adatok beolvasása

Általánosságban elmondható, hogy az adatolvasás egyszer érdemes kétszer beolvasni. Vegyünk például egy olyan webalkalmazás példáját, amely egy 50 MiB-blobot adott vissza az Azure Storage-ból, hogy tartalmat szolgáltasson a felhasználó számára. Ideális esetben az alkalmazás helyi gyorsítótárba helyezi a blobot a lemezre, majd lekéri a gyorsítótárazott verziót a későbbi felhasználói kérésekhez.

A blob lekérésének egyik módja, ha még nem módosították, mert a gyorsítótárba került, hogy a lekérési művelet a módosítási idő feltételes fejlécével legyen érvényes. Ha az utolsó módosítás időpontja a BLOB gyorsítótárba helyezésének időpontja után történik, akkor a rendszer beolvassa és újra gyorsítótárazza a blobot. Ellenkező esetben a rendszer lekéri a gyorsítótárazott blobot az optimális teljesítmény érdekében.

Dönthet úgy is, hogy úgy tervezi az alkalmazást, hogy azt feltételezi, hogy a blob a beolvasás után rövid ideig változatlan marad. Ebben az esetben az alkalmazásnak nem kell ellenőriznie, hogy a blob módosult-e az adott intervallumban.

A konfigurációs, a keresési és az alkalmazás által gyakran használt egyéb adatértékek a gyorsítótárazásra alkalmasak.  

További információ a feltételes fejlécek használatáról: [feltételes fejlécek megadása blob Service műveletekhez](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Adatfeltöltés kötegekben

Bizonyos esetekben az adatokat helyileg összesítheti, majd időnként feltöltheti egy kötegbe ahelyett, hogy az egyes adatmennyiségeket azonnal fel kellene töltenie. Tegyük fel például, hogy egy webalkalmazás megtartja a tevékenységek naplófájlját. Az alkalmazás feltöltheti az összes tevékenység részleteit egy olyan táblára (amely sok tárolási műveletet igényel), vagy mentheti a tevékenység részleteit egy helyi naplófájlba, majd rendszeres időközönként feltölti a tevékenység részleteit egy blobba. Ha minden naplóbejegyzés 1 KB méretű, egyetlen tranzakcióban több ezer bejegyzést is feltölthet. Egyetlen tranzakció támogatja a Blobok feltöltését akár 64 MiB méretekben. Az alkalmazás fejlesztőinek meg kell tervezniük az ügyféleszközök vagy a feltöltési hibák lehetőségét. Ha a tevékenységre vonatkozó adatmennyiséget nem egyetlen tevékenység esetében kell letöltenie, és a blob Storage használata ajánlott a Table Storage szolgáltatásban.

## <a name="net-configuration"></a>.NET-konfiguráció

A .NET-keretrendszer használata esetén ez a szakasz több gyors konfigurációs beállítást tartalmaz, amelyek segítségével jelentős teljesítmény-növelést végezhet.  Ha más nyelveket használ, ellenőrizze, hogy a választott nyelven a hasonló fogalmak érvényesek-e.  

### <a name="use-net-core"></a>A .NET Core használata

Fejlessze Azure Storage-alkalmazásait a .NET Core 2,1-es vagy újabb verziójával, hogy kihasználhassa a teljesítménnyel kapcsolatos fejlesztéseket. Ha lehetséges, a .NET Core 3. x használata javasolt.

A .NET Core teljesítményével kapcsolatos újdonságokról a következő blogbejegyzésekben olvashat bővebben:

- [Teljesítménnyel kapcsolatos újdonságok a .NET Core 3,0-ben](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Teljesítménnyel kapcsolatos újdonságok a .NET Core 2,1-ben](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Az alapértelmezett kapcsolatok korlátjának megemelése

A .NET-ben a következő kód növeli az alapértelmezett kapcsolódási korlátot (amely általában kettő egy ügyfél-környezetben, vagy tíz a kiszolgálói környezetben) a 100-re. Az értéket általában az alkalmazás által használt szálak számának körülbelül értékre kell állítani. A kapcsolatok megnyitása előtt állítsa be a kapcsolati korlátot.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Más programozási nyelvek esetében tekintse meg a dokumentációt, amely meghatározza, hogyan kell beállítani a kapcsolódási korlátot.  

További információ [: a webszolgáltatások közzétételét ismertető webszolgáltatások: egyidejű kapcsolatok](/archive/blogs/darrenj/web-services-concurrent-connections).  

### <a name="increase-minimum-number-of-threads"></a>A szálak minimális számának megemelése

Ha a szinkron hívásokat aszinkron feladatokkal együtt használja, érdemes megnövelni a szálak számát a szál készletében:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

További információ: [szálkészlet munkaszála belépett. SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) metódus.  

## <a name="unbounded-parallelism"></a>Nem kötött párhuzamosság

Míg a párhuzamosság kiválóan használható a teljesítmény szempontjából, ügyeljen a nem korlátozott párhuzamosságok használatára, ami azt jelenti, hogy a szálak vagy a párhuzamos kérelmek száma nem kényszerített. Ügyeljen arra, hogy korlátozza a párhuzamos kérelmek feltöltését vagy letöltését, hogy ugyanazon a Storage-fiókban több partíciót lehessen elérni, vagy ugyanabban a partícióban több elemhez is hozzáférhessen. Ha a párhuzamosság feloldva, az alkalmazás túllépheti az ügyféleszközök képességeit vagy a tárolási fiók skálázhatósági céljait, ami hosszabb késést és szabályozást eredményez.  

## <a name="client-libraries-and-tools"></a>Ügyféloldali kódtárak és eszközök

A legjobb teljesítmény érdekében mindig használja a Microsoft által biztosított legújabb ügyféloldali kódtárakat és eszközöket. Az Azure Storage ügyféloldali kódtárai különböző nyelveken érhetők el. Az Azure Storage a PowerShellt és az Azure CLI-t is támogatja. A Microsoft aktívan fejleszti ezeket az ügyfélszoftvereket és eszközöket a teljesítmény szem előtt tartásával, naprakészen tartja a legújabb verziókat, és gondoskodik arról, hogy a bevált teljesítményekkel kapcsolatos gyakorlatokat belsőleg kezeljék.

## <a name="handle-service-errors"></a>Szolgáltatás hibáinak kezelése

Az Azure Storage hibát jelez, ha a szolgáltatás nem tud feldolgozni egy kérést. Az Azure Storage által az adott forgatókönyvben visszaadott hibák megismerése hasznos lehet a teljesítmény optimalizálása érdekében.

### <a name="timeout-and-server-busy-errors"></a>Időtúllépés és a kiszolgáló foglalt hibái

Az Azure Storage szabályozhatja az alkalmazást, ha az megközelíti a méretezhetőségi korlátokat. Bizonyos esetekben előfordulhat, hogy az Azure Storage bizonyos átmeneti feltételek miatt nem tudja kezelni a kérést. A szolgáltatás mindkét esetben egy 503 (foglalt kiszolgáló) vagy 500 (időtúllépési) hibát adhat vissza. Ezek a hibák akkor is előfordulhatnak, ha a szolgáltatás az adatpartíciók újrakiegyensúlyozását teszi lehetővé a magasabb átviteli sebesség érdekében. Az ügyfélalkalmazás általában próbálja megismételni a műveletet, amely a hibák egyikét okozza. Ha azonban az Azure Storage szabályozza az alkalmazást, mert az meghaladja a skálázhatósági célokat, vagy ha a szolgáltatás nem tudta kiszolgálni a kérelmet valamilyen más okból, az agresszív újrapróbálkozások rosszabbul tehetik a problémát. Az exponenciális visszahívási újrapróbálkozási házirend használata ajánlott, és az ügyféloldali kódtárak ezt a viselkedést használják. Előfordulhat például, hogy az alkalmazás 2 másodperc, 4 másodperc, 10 másodperc, majd 30 másodperc múlva újra próbálkozik. Így az alkalmazása jelentősen csökkenti a szolgáltatás terhelését, és nem súlyosbítja a szabályozáshoz vezethető viselkedést.  

A kapcsolódási hibákat azonnal újra lehet próbálni, mert nem a szabályozás eredménye, és a várt átmeneti állapotú.  

### <a name="non-retryable-errors"></a>Nem újrapróbálkozást lehetővé tevő hibák

Az ügyféloldali kódtárak kezelik az újrapróbálkozásokat, és megismerik, hogy mely hibákat lehet újrapróbálni, és melyek nem. Ha azonban közvetlenül hívja meg az Azure Storage-REST API, néhány hiba miatt nem kell újrapróbálkoznia. Például egy 400-as (hibás kérelem) hiba azt jelzi, hogy az ügyfélalkalmazás olyan kérelmet küld, amely nem dolgozható fel, mert nem volt a várt formában. A kérelem újraküldése minden alkalommal ugyanazt a választ eredményezi, így a rendszer nem próbálkozik újra. Ha közvetlenül hívja meg az Azure Storage-REST API, vegye figyelembe a lehetséges hibákat, valamint azt, hogy újra kell-e próbálni.

Az Azure Storage-hibakódokkal kapcsolatos további információkért lásd: [állapot-és hibakódok](/rest/api/storageservices/status-and-error-codes2).

## <a name="copying-and-moving-blobs"></a>Blobok másolása és áthelyezése

Az Azure Storage számos megoldást kínál a Blobok másolására és áthelyezésére egy Storage-fiókban, a Storage-fiókok, valamint a helyszíni rendszerek és a felhő között. Ez a szakasz a teljesítményre gyakorolt hatásuk alapján ismerteti ezeket a lehetőségeket. További információ a blob Storage-ból vagy-ból való adatátvitelről: [Azure-megoldás kiválasztása adatátvitelhez](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="blob-copy-apis"></a>BLOB-másolási API-k

Ha a blobokat a Storage-fiókok között szeretné másolni, használja a [put blokkot az URL-](/rest/api/storageservices/put-block-from-url) címről művelettel. A művelet szinkron módon másolja az adatforrásokat bármely URL-címről egy blokk-blobba. A `Put Block from URL` művelet használata jelentősen csökkentheti a szükséges sávszélességet, ha a Storage-fiókok között telepíti át az adatforgalmat. Mivel a másolási művelet a szolgáltatás oldalán zajlik, nem kell letöltenie és újra feltöltenie az adatfájlokat.

Ha ugyanazon a Storage-fiókon belül szeretne Adatmásolást készíteni, használja a [blob másolása](/rest/api/storageservices/Copy-Blob) műveletet. Az adatok ugyanabban a Storage-fiókban való másolása általában gyorsan elvégezhető.  

### <a name="use-azcopy"></a>Az AzCopy használata

A AzCopy parancssori segédprogram egyszerű és hatékony megoldás a Blobok tömeges átvitelére a, a-ból és a különböző Storage-fiókokba. A AzCopy erre a forgatókönyvre van optimalizálva, és magas adatátviteli sebességet érhet el. A AzCopy 10-es verziója a `Put Block From URL` művelettel másolja át a blob-fájlokat a Storage-fiókok között. További információ: [adatok másolása vagy áthelyezése az Azure Storage-ba az AzCopy v10 használatával](../common/storage-use-azcopy-v10.md).  

### <a name="use-azure-data-box"></a>Azure Data Box használata

Nagyméretű adatmennyiségek blob Storage-ba történő importálásához érdemes lehet a Azure Data Box családot használni az offline átvitelekhez. A Microsoft által biztosított Data Box eszközök használata jó választás a nagy mennyiségű adatforgalom Azure-ba történő áthelyezéséhez, ha az idő, a hálózat rendelkezésre állása vagy a költségek korlátozottak. További információt az [Azure DataBox dokumentációjában](../../databox/index.yml)talál.

## <a name="content-distribution"></a>Tartalom terjesztése

Előfordulhat, hogy egy alkalmazásnak ugyanazokat a tartalmakat kell kiszolgálnia sok felhasználó számára (például egy, a webhely kezdőlapján használt termék-bemutató videó), amely a vagy a több régióban található. Ebben a forgatókönyvben egy Content Delivery Network (CDN), például Azure CDN használatával terjesztheti a Blobok tartalmát földrajzilag. Az egyetlen régióban található Azure Storage-fiókkal ellentétben, amely nem tud a tartalmat alacsony késéssel kézbesíteni más régiók számára, Azure CDN több adatközpontban lévő kiszolgálókat használ a világ különböző pontjain. Emellett a CDN általában sokkal magasabb kimenő forgalomra vonatkozó korlátozásokat is támogat, mint egyetlen Storage-fiók.  

További információ a Azure CDNről: [Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Metaadatok használata

A Blob service támogatja a fő kérelmeket, amelyek tartalmazhatnak blob-tulajdonságokat vagy-metaadatokat is. Ha például az alkalmazásnak egy fényképből származó EXIF (képformátumos) adatokra van szüksége, lekérheti a fényképet, és kinyerheti azt. A sávszélesség mentéséhez és a teljesítmény javításához az alkalmazás képes tárolni az EXIF-adatokat a blob metaadataiban, amikor az alkalmazás feltölti a fényképet. Ezután csak egy HEAD kérelem használatával kérheti le az EXIF-adatokat a metaadatokban. A csak metaadatok beolvasása és a blob teljes tartalma nem ment jelentős sávszélességet, és csökkenti az EXIF-adatok kinyeréséhez szükséges feldolgozási időt. Ne feledje, hogy blobban 8 KiB-nál több metaadat is tárolható.  

## <a name="upload-blobs-quickly"></a>Blobok gyors feltöltése

A Blobok gyors feltöltéséhez először el kell döntenie, hogy egy blobot vagy sok mást tölt fel. Az alábbi útmutatás segítségével határozza meg a forgatókönyvtől függően a helyes módszert.  

### <a name="upload-one-large-blob-quickly"></a>Egy nagyméretű blob gyors feltöltése

Egy nagyméretű blob gyors feltöltéséhez az ügyfélalkalmazások párhuzamosan tölthetik fel a blokkokat vagy lapokat, szem előtt tartva az egyes Blobok méretezhetőségi célkitűzéseit és a Storage-fiók egészét. Az Azure Storage ügyféloldali kódtárai támogatják a párhuzamos feltöltést. A következő tulajdonságokkal például megadhatja a .NET-ben vagy a Java-ban engedélyezett egyidejű kérelmek számát. A más támogatott nyelvekhez készült ügyféloldali kódtárak hasonló lehetőségeket nyújtanak.

- A .NET esetében állítsa be a [BlobRequestOptions. ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) tulajdonságot.
- A Java/Android esetében hívja meg a [BlobRequestOptions. setConcurrentRequestCount (Final Integer concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount) metódust.

### <a name="upload-many-blobs-quickly"></a>Sok blob feltöltése gyorsan

Sok blob gyors feltöltéséhez töltse fel a blobokat párhuzamosan. Az egyidejű feltöltés párhuzamosan gyorsabb, mint a párhuzamos blokkok feltöltésével töltött egyazon Blobok feltöltése, mert a feltöltés a Storage szolgáltatás több partícióján keresztül terjed. A AzCopy alapértelmezés szerint párhuzamosan hajtja végre a feltöltéseket, és ezt a forgatókönyvet javasolja. További információ: Ismerkedés [a AzCopy](../common/storage-use-azcopy-v10.md)szolgáltatással.  

## <a name="choose-the-correct-type-of-blob"></a>A megfelelő típusú blob kiválasztása

Az Azure Storage támogatja a Blobok blokkolását, a Blobok hozzáfűzését és az oldal blobokat. Egy adott használati forgatókönyv esetén a kiválasztott blob-típus hatással lesz a megoldás teljesítményére és méretezhetőségére.

A blokk-Blobok akkor megfelelőek, ha nagy mennyiségű adatok hatékony feltöltését szeretné. Például egy olyan ügyfélalkalmazás, amely fényképeket vagy videót tölt fel a blob Storage-ba, a blokk blobokat célozza meg.

A hozzáfűzési Blobok hasonlók a blokkokból álló Blobok blokkolásához. Hozzáfűző blob módosításakor a rendszer csak a blob végéhez adja hozzá a blokkokat. A hozzáfűzési Blobok olyan forgatókönyvek esetén hasznosak, mint a naplózás, amikor egy alkalmazásnak hozzá kell adnia egy meglévő blobot.

Az oldal Blobok akkor megfelelőek, ha az alkalmazásnak véletlenszerű írást kell végeznie az adatokon. Az Azure-beli virtuális gépek lemezeit például blobként tárolja a rendszer. További információkért lásd: a [Blobok, a blobok és a Blobok hozzáfűzésének ismertetése](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Következő lépések

- [A blob Storage méretezhetőségi és teljesítménybeli céljai](scalability-targets.md)
- [A standard szintű Storage-fiókok méretezhetősége és teljesítménybeli céljai](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Állapot-és hibakódok](/rest/api/storageservices/Status-and-Error-Codes2)