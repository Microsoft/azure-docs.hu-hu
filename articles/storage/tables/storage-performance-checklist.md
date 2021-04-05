---
title: A Table Storage teljesítmény-és méretezhetőségi ellenőrzőlistája – Azure Storage
description: A nagy teljesítményű alkalmazások fejlesztéséhez szükséges, a Table Storage szolgáltatással használható bevált eljárások ellenőrzőlistája.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 71b1f3cfa1df86b417c468d56f67cd7fe8d71d73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004704"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>A Table Storage teljesítmény-és méretezhetőségi ellenőrzőlistája

A Microsoft számos bevált gyakorlatot fejlesztett ki a nagy teljesítményű alkalmazások táblázatos tárolással való fejlesztéséhez. Ez az ellenőrzőlista azokat a kulcsfontosságú eljárásokat azonosítja, amelyeket a fejlesztők követhetnek a teljesítmény optimalizálása érdekében. Tartsa szem előtt ezeket a gyakorlatokat az alkalmazás tervezésekor és a folyamat során.

Az Azure Storage méretezhetőségi és teljesítménybeli célokat biztosít a kapacitáshoz, a tranzakciós sebességhez és a sávszélességhez. Az Azure Storage skálázhatósági céljaival kapcsolatos további információkért lásd: [skálázhatósági és teljesítményi célok a standard szintű Storage-fiókok esetében](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) , valamint [a táblázatos tárolás méretezhetőségi és teljesítménybeli céljai](scalability-targets.md).

## <a name="checklist"></a>Ellenőrzőlista

Ez a cikk bevált eljárásokat szervez a teljesítményre vonatkozóan egy olyan ellenőrzőlista keretében, amelyet a Table Storage-alkalmazás fejlesztése során követhet.

| Kész | Kategória | Tervezési szempont |
| --- | --- | --- |
| &nbsp; |Méretezhetőségi célok |[Megtervezheti, hogy az alkalmazás ne legyen több, mint a Storage-fiókok maximális száma?](#maximum-number-of-storage-accounts) |
| &nbsp; |Méretezhetőségi célok |[Elkerüli a kapacitást és a tranzakciós korlátokat?](#capacity-and-transaction-targets) |
| &nbsp; |Méretezhetőségi célok |[Közeledik az entitások skálázhatósági céljaihoz másodpercenként?](#targets-for-data-operations) |
| &nbsp; |Hálózatkezelés |[A szükséges teljesítmény elérése érdekében az ügyféloldali eszközök megfelelően nagy sávszélességgel és kis késéssel rendelkeznek?](#throughput) |
| &nbsp; |Hálózatkezelés |[Az ügyféloldali eszközök magas színvonalú hálózati kapcsolattal rendelkeznek?](#link-quality) |
| &nbsp; |Hálózatkezelés |[Az ügyfélalkalmazás ugyanabban a régióban található, mint a Storage-fiók?](#location) |
| &nbsp; |Közvetlen ügyfél-hozzáférés |[Közös hozzáférésű aláírásokat (SAS) és több eredetű erőforrás-megosztást (CORS) használ az Azure Storage-hoz való közvetlen hozzáférés engedélyezéséhez?](#sas-and-cors) |
| &nbsp; |Kötegelés |[Az alkalmazás batch-tranzakciói az Entity Group Transactions használatával frissülnek?](#batch-transactions) |
| &nbsp; |.NET-konfiguráció |[A .NET Core 2,1-as vagy újabb verzióját használja az optimális teljesítmény érdekében?](#use-net-core) |
| &nbsp; |.NET-konfiguráció |[Konfigurálta az ügyfelet az egyidejű kapcsolatok megfelelő számának használatára?](#increase-default-connection-limit) |
| &nbsp; |.NET-konfiguráció |[A .NET-alkalmazások esetében megfelelő számú szál használatára konfigurálta a .NET-et?](#increase-minimum-number-of-threads) |
| &nbsp; |Párhuzamosság |[Gondoskodott arról, hogy a párhuzamosságok megfelelően legyenek kötve, hogy ne legyenek túlterhelve az ügyfél képességei, vagy közelítse meg a méretezhetőségi célokat?](#unbounded-parallelism) |
| &nbsp; |Eszközök |[A Microsoft által biztosított ügyféloldali kódtárak és eszközök legújabb verzióit használja?](#client-libraries-and-tools) |
| &nbsp; |Újrapróbálkozások |[Újrapróbálkozási szabályzatot használ egy exponenciális leállítási a hibák és időtúllépések szabályozásához?](#timeout-and-server-busy-errors) |
| &nbsp; |Újrapróbálkozások |[Az alkalmazás elkerüli a nem újrapróbálkozást okozó hibák újrapróbálkozását?](#non-retryable-errors) |
| &nbsp; |Konfiguráció |[JSON-t használ a táblázatos kérelmekhez?](#use-json) |
| &nbsp; |Konfiguráció |[Kikapcsolta a Nyéki algoritmust a kis kérelmek teljesítményének javítása érdekében?](#disable-nagle) |
| &nbsp; |Táblák és partíciók |[Megfelelően particionálta az adatait?](#schema) |
| &nbsp; |Gyakori partíciók |[Elkerüli a csak Hozzáfűzés és a csak előtag mintázatot?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Gyakori partíciók |[A lapkák/frissítések több partíció között oszlanak meg?](#high-traffic-data) |
| &nbsp; |Lekérdezési hatókör |[Úgy tervezte a sémát, hogy lehetővé tegye a pontok lekérdezéseit a legtöbb esetben használni, és a táblázatos lekérdezéseket takarékosan kell használni?](#query-scope) |
| &nbsp; |Lekérdezési sűrűség |[A lekérdezések általában csak az alkalmazás által használt sorok vizsgálatát és visszaküldését végzik?](#query-density) |
| &nbsp; |Visszaadott adatértékek korlátozása |[A szűrést használja a nem szükséges entitások visszaküldésének elkerüléséhez?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Visszaadott adatértékek korlátozása |[Kivetítést használ a nem szükséges tulajdonságok visszaadásának elkerülésére?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalizáció |[Leválasztotta az adatait úgy, hogy elkerülje a nem hatékony lekérdezéseket vagy több olvasási kérést az adatlekérdezés során?](#denormalization) |
| &nbsp; |INSERT, Update és DELETE |[Olyan kérelmeket dolgoz fel, amelyeknek tranzakciós igényeknek kell lenniük, vagy egyidejűleg is elvégezhető a ciklikus utak csökkentése?](#batching) |
| &nbsp; |INSERT, Update és DELETE |[Elkerüli az entitások beolvasását, hogy megtörténjen a Beszúrás vagy a frissítés meghívása?](#upsert) |
| &nbsp; |INSERT, Update és DELETE |[Vannak olyan adatsorozatok, amelyek gyakran egy entitásban lesznek beolvasva több entitás helyett tulajdonságokként?](#storing-data-series-in-a-single-entity) |
| &nbsp; |INSERT, Update és DELETE |[Azokat az entitásokat, amelyek mindig együtt lesznek lekérdezve, és kötegekben (például idősorozat-adatsorokban) is írhatók, a táblázatok helyett blobokat használtak?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Méretezhetőségi célok

Ha az alkalmazás megközelíti vagy túllépi a méretezhetőségi célokat, akkor a tranzakciós késések vagy a szabályozás nagyobb mértékben merülhet fel. Amikor az Azure Storage szabályozza az alkalmazást, a szolgáltatás megkezdi a 503 (foglalt kiszolgáló) vagy a 500 (művelet időtúllépése) hibakódok visszaadását. A méretezhetőségi célok korlátain belül maradó hibák elkerülésének fontos része az alkalmazás teljesítményének növelése.

A Table service skálázhatósági céljaival kapcsolatos további információkért lásd: a [táblázatos tárolás skálázhatósági és teljesítményi céljai](scalability-targets.md).

### <a name="maximum-number-of-storage-accounts"></a>Storage-fiókok maximális száma

Ha egy adott előfizetés/régió kombináció számára engedélyezett maximális számú Storage-fiókot használ, több Storage-fiókot is használhat a beléptetés, a kimenő forgalom, az I/O műveletek másodpercenkénti (IOPS) vagy kapacitásának növelésére? Ebben a forgatókönyvben a Microsoft azt javasolja, hogy a Storage-fiókok megnövekedett korlátainak kihasználásával csökkentse a munkaterhelés számára szükséges tárolási fiókok számát, ha lehetséges. Vegye fel a kapcsolatot az [Azure támogatási szolgálatával](https://azure.microsoft.com/support/options/) , és kérjen nagyobb korlátokat a Storage-fiókjához. További információ: [nagyobb méretű, magasabb szintű Storage-fiókok bejelentése](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapacitás-és tranzakciós célok

Ha az alkalmazása közeledik egyetlen Storage-fiók skálázhatósági céljaihoz, vegye figyelembe az alábbi módszerek egyikét:  

- Gondolja át a számítási feladatokat, amelyek hatására az alkalmazás megközelíti vagy túllépi a méretezhetőségi célt. Másképpen is megtervezhető, hogy kevesebb sávszélességet vagy kapacitást vagy kevesebb tranzakciót használjon?
- Ha az alkalmazásnak meg kell haladnia a méretezhetőségi célok valamelyikét, hozzon létre több Storage-fiókot, és particionálja az alkalmazásadatok körét a több Storage-fiók között. Ha ezt a mintát használja, ügyeljen arra, hogy tervezze meg az alkalmazását, hogy a jövőben további Storage-fiókokat lehessen hozzáadni a terheléselosztáshoz. A Storage-fiókok önmagukban a tárolt adatok, a tranzakciók vagy az átvitt adatok alapján nem számítanak bele a használati díjakba.
- Ha az alkalmazás közeledik a sávszélesség-célokhoz, érdemes lehet az ügyfél oldalán lévő adatok tömörítését az adatok Azure Storage-ba való küldéséhez szükséges sávszélesség csökkentése érdekében.
    Az adatok tömörítése csökkentheti a sávszélességet, és javíthatja a hálózati teljesítményt, továbbá negatív hatással lehet a teljesítményre. Értékelje ki a további feldolgozási követelmények teljesítményére gyakorolt hatást az adattömörítés és a kibontás az ügyféloldali oldalon. Ne feledje, hogy a tömörített adattárolók megnehezítik a hibaelhárítást, mivel a szabványos eszközök használatával nagyobb kihívást jelenthet az adatmegtekintés.
- Ha az alkalmazás közeledik a méretezhetőségi célokhoz, akkor győződjön meg arról, hogy exponenciális leállítási használ az újrapróbálkozásokhoz. A legjobb megoldás, ha a jelen cikkben ismertetett javaslatok végrehajtásával el szeretné kerülni a méretezhetőségi célok elérését. Az újrapróbálkozások exponenciális leállítási használata azonban megakadályozza, hogy az alkalmazás gyorsan újrapróbálkozjon, ami még rosszabb szabályozást eredményezhet. További információ: az [időtúllépés és a kiszolgáló foglalt hibák](#timeout-and-server-busy-errors)című szakasza.

### <a name="targets-for-data-operations"></a>Az adatműveletek céljai

Az Azure Storage-beli terhelési egyenlegek a Storage-fiók forgalmának növekedésével növekednek, de ha a forgalom hirtelen kitörést mutat, előfordulhat, hogy nem tudja azonnal lekérni ezt a kötetet. A rendszer a burst és/vagy időtúllépést tekinti meg a folyamat során, mivel az Azure Storage automatikusan betölti a tábla egyenlegét. A lassú indítás általában jobb eredményeket biztosít, mivel a rendszeren elegendő idő van a terheléselosztás megfelelő elosztására.

#### <a name="entities-per-second-storage-account"></a>Entitások másodpercenként (Storage-fiók)

A táblák elérésének skálázhatósági korlátja legfeljebb 20 000 entitás (egyenként 1 KB), egy fiók esetében másodpercenként. Általánosságban elmondható, hogy minden beszúrt, frissített, törölt vagy beolvasott entitás beleszámít a cél felé. Így az 100 entitásokat tartalmazó batch-Beszúrás 100 entitásnak számít. Az 1000 entitások vizsgálatára és az 5 értéket visszaadó lekérdezések 1000 entitásnak számítanak.

#### <a name="entities-per-second-partition"></a>Entitások másodpercenként (partíció)

Egyetlen partíción belül a táblák elérésének méretezhetőségi célja 2 000 entitás (1 KB másodpercenként), az előző szakaszban leírtak szerint.

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

## <a name="sas-and-cors"></a>SAS és CORS

Tegyük fel, hogy engedélyezni kell a kódot, például a JavaScriptet, amely egy felhasználó webböngészőjében vagy egy mobiltelefon-alkalmazásban fut az Azure Storage-ban tárolt adateléréshez. Az egyik módszer egy olyan szolgáltatásalkalmazás létrehozása, amely proxyként működik. A felhasználó eszköze hitelesíti magát a szolgáltatással, amely viszont engedélyezi az Azure Storage-erőforrásokhoz való hozzáférést. Így elkerülhető, hogy a Storage-fiók kulcsa nem biztonságos eszközökön legyen kitéve. Ez a megközelítés azonban jelentős terhelést jelent a szolgáltatásalkalmazás számára, mivel a felhasználó eszközén és az Azure Storage-ban továbbított összes adatmennyiségnek továbbítania kell a szolgáltatásalkalmazás alkalmazásán.

A megosztott hozzáférési aláírások (SAS) használatával elkerülhető, hogy a szolgáltatásalkalmazás proxyként legyen használatban az Azure Storage-ban. A SAS használatával engedélyezheti a felhasználó eszközének, hogy közvetlenül az Azure Storage-ba irányuló kéréseket hozzon igénybe korlátozott hozzáférési jogkivonat használatával. Ha például egy felhasználó egy fényképet szeretne feltölteni az alkalmazásba, a szolgáltatásalkalmazás létrehozhat egy SAS-t, és elküldheti azt a felhasználó eszközére. Az SAS-jogkivonat engedélyezheti az Azure Storage-erőforrásoknak adott időtartamra való írást, amely után az SAS-jogkivonat lejár. Az SAS-vel kapcsolatos további információkért lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférési aláírások (SAS) használatával](../common/storage-sas-overview.md).  

A webböngésző általában nem engedélyezi a JavaScript használatát egy olyan oldalon, amelyet egy adott tartomány webhelye futtat bizonyos műveletek, például írási műveletek végrehajtásához egy másik tartományba. Ez a házirend megakadályozza, hogy az egyik lapon lévő rosszindulatú parancsfájlok hozzáférjenek egy másik weblapon lévő adatokhoz. Ugyanakkor a felhőben a megoldás kiépítésekor is megadható korlátozás. A több eredetű erőforrás-megosztás (CORS) egy böngésző-szolgáltatás, amely lehetővé teszi a célként megadott tartomány számára, hogy kommunikáljon a forrás tartományból származó kérelmeket megbízható böngészővel.

Tegyük fel például, hogy egy Azure-ban futó webalkalmazás egy Azure Storage-fiókhoz kér egy erőforrást. A webalkalmazás a forrástartomány, a Storage-fiók pedig a célként megadott tartomány. A CORS bármely Azure Storage-szolgáltatáshoz beállíthatja úgy, hogy az Azure Storage által megbízhatónak tekintse azt a böngészőt, amelyet a forrástartománytól érkező kérések biztosítanak. A CORS kapcsolatos további információkért lásd: [Az Azure Storage-hoz készült több eredetű erőforrás-megosztás (CORS) támogatása](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Az SAS és a CORS is segíthet elkerülni a webalkalmazás szükségtelen terhelését.  

## <a name="batch-transactions"></a>Kötegelt tranzakciók

A Table service támogatja a Batch-tranzakciókat olyan entitásokon, amelyek ugyanabban a táblában találhatók, és ugyanahhoz a partíciós csoporthoz tartoznak. További információ: [Entity Transactions-tranzakciók végrehajtása](/rest/api/storageservices/performing-entity-group-transactions).

## <a name="net-configuration"></a>.NET-konfiguráció

A .NET-keretrendszer használata esetén ez a szakasz több gyors konfigurációs beállítást tartalmaz, amelyek segítségével jelentős teljesítmény-növelést végezhet.  Ha más nyelveket használ, ellenőrizze, hogy a választott nyelven a hasonló fogalmak érvényesek-e.  

### <a name="use-net-core"></a>A .NET Core használata

Fejlessze Azure Storage-alkalmazásait a .NET Core 2,1-es vagy újabb verziójával, hogy kihasználhassa a teljesítménnyel kapcsolatos fejlesztéseket. Ha lehetséges, a .NET Core 3. x használata javasolt.

A .NET Core teljesítményével kapcsolatos újdonságokról a következő blogbejegyzésekben olvashat bővebben:

- [Teljesítménnyel kapcsolatos újdonságok a .NET Core 3,0-ben](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Teljesítménnyel kapcsolatos újdonságok a .NET Core 2,1-ben](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Az alapértelmezett kapcsolatok korlátjának megemelése

A .NET-ben a következő kód növeli az alapértelmezett kapcsolódási korlátot (amely általában 2 egy ügyfél-környezetben, vagy 10 a kiszolgálói környezetben) 100-re. Az értéket általában az alkalmazás által használt szálak számának körülbelül értékre kell állítani.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

A kapcsolatok megnyitása előtt állítsa be a kapcsolati korlátot.  

Más programozási nyelvek esetében az adott nyelv dokumentációjában megtudhatja, hogyan állíthatja be a kapcsolódási korlátot.  

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

## <a name="configuration"></a>Konfiguráció

Ez a szakasz több olyan gyors konfigurációs beállítást sorol fel, amelyek segítségével jelentős teljesítménybeli tökéletesítéseket végezhet a Table serviceban:

### <a name="use-json"></a>JSON használata

A Storage Service 2013-08-15-es verziójától kezdve a Table service támogatja a JSON használatát az XML-alapú AtomPub formátum helyett a tábla adatai átviteléhez. A JSON használatával csökkentheti a hasznos adatok méretét a 75%-kal, és jelentősen növelheti az alkalmazás teljesítményét.

További információ [: post Microsoft Azure Tables](/archive/blogs/windowsazurestorage/windows-azure-tables-introducing-json) (a Table [Service műveleteinek](/rest/api/storageservices/Payload-Format-for-Table-Service-Operations)JSON-és hasznos adatokkal kapcsolatos formátumának bemutatása).

### <a name="disable-nagle"></a>A nyár letiltása

A Nyéki algoritmusa széles körben implementálva van a TCP/IP-hálózatokon a hálózati teljesítmény javítása érdekében. Ez azonban nem minden esetben optimális (például a nagyon interaktív környezetekben). A Nyéki algoritmusa negatív hatással van az Azure Table service érkező kérések teljesítményére, és ha lehetséges, le kell tiltania.

## <a name="schema"></a>Séma

Az adatfeldolgozási és-lekérdezési folyamat a legnagyobb egyetlen tényező, amely hatással van a Table service teljesítményére. Habár minden alkalmazás eltérő, ez a szakasz néhány, a következőhöz kapcsolódó általános bevált gyakorlatot ismertet:

- Tábla kialakítása
- Hatékony lekérdezések
- Hatékony adatfrissítések

### <a name="tables-and-partitions"></a>Táblák és partíciók

A táblák felosztva vannak osztva. A partíciókban tárolt összes entitás ugyanazzal a partíciós kulccsal osztozik, és egyedi sorral rendelkezik az adott partíción belüli azonosításához. A partíciók előnyöket biztosítanak, de a méretezhetőségi korlátokat is bevezetik.

- Előnyök: ugyanazon a partíción lévő entitásokat egyetlen, atomi, kötegelt tranzakcióban frissítheti, amely akár 100 különálló tárolási műveletet is tartalmaz (legfeljebb 4 MB teljes méret). Ha ugyanazokat a számú entitást szeretné lekérdezni, az egyes partíciókban lévő adatlekérdezések hatékonyabbak lesznek, mint a partíciókat átölelő adatmennyiségek (bár a táblázatos adatlekérdezéssel kapcsolatos további javaslatokról olvashat bővebben).
- Skálázhatósági korlát: egyetlen partíción tárolt entitásokhoz való hozzáférés nem tölthető be, mert a partíciók támogatják az Atomic batch-tranzakciókat. Emiatt a különálló tábla partíciójának skálázhatósági célja alacsonyabb, mint a Table service egésze számára.

A táblák és partíciók jellemzői miatt a következő tervezési alapelveket kell elfogadnia:

- Megkeresheti az ügyfélalkalmazás által gyakran frissített vagy lekérdezett adatait ugyanabban a logikai egységben, amely ugyanabban a partícióban működik. Ha például az alkalmazás az írásokat összesíti, vagy atomi batch-műveleteket végez, keresse meg az adatok ugyanabban a partícióban. Emellett az egyetlen partíción lévő adatlekérdezések hatékonyabban kérhetők le egyetlen lekérdezésben, mint az adatpartíciók között.
- Keresse meg azokat az adatmennyiségeket, amelyeket az ügyfélalkalmazás nem szúr be, nem frissít vagy kérdez ugyanabban a logikai egységben (azaz egyetlen lekérdezésben vagy batch-frissítésben) külön partíciókban. Ne feledje, hogy egyetlen táblában sincs korlátozva a partíciós kulcsok száma, így több millió partíciós kulcs nem jelent problémát, és nem befolyásolja a teljesítményt. Ha például az alkalmazás egy olyan népszerű webhely, amely felhasználói bejelentkezést használ, a felhasználói azonosító használatával a partíciós kulcs jó választás lehet.

#### <a name="hot-partitions"></a>Gyakori partíciók

Egy olyan gyakori partíció, amely egy fiók felé irányuló forgalom aránytalan százalékát kapja, és nem lehet terheléselosztást végezni, mert egyetlen partíció. Általánosságban elmondható, hogy a gyakori partíciók kétféleképpen jönnek létre:

#### <a name="append-only-and-prepend-only-patterns"></a>Csak hozzáfűzési és csak hozzáfűzési minták

A "csak Hozzáfűzés" minta egy adott partíciós kulcshoz tartozó összes (vagy majdnem az összes) adatforgalom növekedése, és az aktuális idő függvényében csökken. Tegyük fel például, hogy az alkalmazás az aktuális dátumot használja partíciós kulcsként a naplózási adatokhoz. Ez a kialakítás azt eredményezi, hogy az összes Beszúrás a tábla utolsó partícióján történik, és a rendszeren nem lehet megfelelően betölteni a terheléselosztást. Ha az adott partícióra irányuló forgalom mennyisége meghaladja a partíciós skálázhatósági célt, akkor a rendszer a szabályozást eredményezi. Jobb, ha biztosítani szeretné, hogy a rendszer több partícióra küldje a forgalmat, így lehetővé téve a terhelés elosztását a táblán lévő kérelmek között.

#### <a name="high-traffic-data"></a>Nagy forgalmú adatok

Ha a particionálási séma egy olyan partíciót eredményez, amely csak a többi partíciónál sokkal nagyobb mennyiségű adattal rendelkezik, akkor is megtekintheti a szabályozást, mivel a partíció megközelíti a méretezhetőségi célt egy adott partícióhoz. Érdemes meggyőződni arról, hogy a partíciós séma egyetlen partíción sem közelíti meg a méretezhetőségi célokat.

### <a name="querying"></a>Lekérdezés

Ez a szakasz a Table service lekérdezésének bevált gyakorlatait ismerteti.

#### <a name="query-scope"></a>Lekérdezési hatókör

A lekérdezni kívánt entitások köre több módon is meghatározható. Az alábbi lista a lekérdezési hatókör minden beállítását ismerteti.

- **Pontok lekérdezései:**– a pont lekérdezése pontosan egy entitást kérdez le a lekérdezni kívánt entitás partíciós kulcsának és a sor kulcsának megadásával. Ezek a lekérdezések hatékonyak, és lehetőleg ezeket kell használniuk.
- **Partíciós lekérdezések:** A partíciós lekérdezés egy olyan lekérdezés, amely közös partíciós kulcsot használó adathalmazt kérdez le. A lekérdezés általában megadja a sor kulcsának értékét vagy az egyes entitások tulajdonságának tartományát a partíciós kulcs mellett. Ezek a lekérdezések kevésbé hatékonyak, mint a pontszerű lekérdezések, és érdemes takarékosan használni őket.
- **Táblák lekérdezései:** A tábla lekérdezése egy olyan lekérdezés, amely nem közös partíciós kulccsal rendelkező entitásokat kérdez le. Ezek a lekérdezések nem hatékonyak, és ha lehetséges, el kell kerülnie őket.

Általánosságban elkerülheti, hogy a vizsgálatok ne legyenek (egyetlen entitásnál nagyobb lekérdezések), de ha vizsgálatra van szüksége, próbálja meg megszervezni az adatait, hogy a vizsgálatok beolvassák a szükséges adatait anélkül, hogy jelentős mennyiségű entitást kellene beolvasni vagy visszaadni.

#### <a name="query-density"></a>Lekérdezési sűrűség

A lekérdezés hatékonyságának egy másik kulcsfontosságú tényezője a visszaküldött entitások száma a visszaadott készlet kereséséhez képest. Ha az alkalmazás egy tábla-lekérdezést hajt végre egy olyan tulajdonságérték alapján, amely csak az adatmegosztások 1%-át tartalmazza, akkor a lekérdezés minden visszaadott entitás 100 entitását vizsgálja. A korábban tárgyalt táblázat-méretezhetőségi célok a beolvasott entitások számával és nem a visszaadott entitások számával kapcsolatosak: az alacsony lekérdezési sűrűség egyszerűen okozhatja Table service az alkalmazás szabályozását, mivel a rendszernek sok entitást kell beolvasnia a keresett entitás lekéréséhez. A szabályozás elkerülésével kapcsolatos további információkért tekintse meg a [denormalizálás](#denormalization)című szakaszt.

#### <a name="limiting-the-amount-of-data-returned"></a>A visszaadott adatmennyiség korlátozása

Ha tudja, hogy egy lekérdezés olyan entitásokat ad vissza, amelyekre nincs szüksége az ügyfélalkalmazás számára, érdemes lehet szűrőt használni a visszaadott készlet méretének csökkentéséhez. Amíg az ügyfélnek nem visszaadott entitások még a méretezhetőségi korlátok felé is beleszámítanak, az alkalmazás teljesítménye a hálózati hasznos adatok méretének csökkentése és az ügyfélalkalmazás által feldolgozható entitások számának csökkenése miatt javulni fog. Ne feledje, hogy a méretezhetőségi célok a beolvasott entitások számához kapcsolódnak, így a sok entitást kiszűrő lekérdezés továbbra is szabályozást eredményezhet, még akkor is, ha kevés entitást ad vissza. A lekérdezések hatékony létrehozásával kapcsolatos további információkért lásd a [lekérdezési sűrűség](#query-density)című szakaszt.

Ha az ügyfélalkalmazás csak korlátozott tulajdonságokat igényel a tábla entitásai közül, a kivetítés használatával korlátozhatja a visszaadott adathalmaz méretét. A szűréshez hasonlóan a kivetítés is segít csökkenteni a hálózati terhelést és az ügyfelek feldolgozását.

#### <a name="denormalization"></a>Denormalizáció

A kapcsolati adatbázisokkal való együttműködéstől eltérően bevált eljárások a Table-adatforrások hatékony lekérdezéséhez az adataik denormalizálása érdekében. Ez azt eredményezi, hogy ugyanazokat az adatokkal duplikálja több entitásban (egyet az egyes kulcsokhoz, amelyeket az adatok kereséséhez használhat), hogy csökkentse azon entitások számát, amelyeket a lekérdezésnek meg kell vizsgálnia az ügyfél által igényelt adatok megkereséséhez, és nem kell nagy számú entitást beolvasnia az alkalmazás által igényelt adatok megtalálásához. Egy e-kereskedelmi webhelyen például érdemes lehet megrendelést keresni az ügyfél-azonosító alapján (adja meg az ügyfél megrendeléseit) és a dátumot (dátummal megadhatja a rendeléseket). A Table Storageban az entitást (vagy az arra mutató hivatkozást) kétszer kell tárolnia – egyszer a Table Name, a PK és a RK használatával, hogy megkönnyítse az ügyfél-azonosító alapján történő keresését  

### <a name="insert-update-and-delete"></a>INSERT, Update és DELETE

Ez a szakasz a Table serviceban tárolt entitások módosítására vonatkozó bevált eljárásokat ismerteti.  

#### <a name="batching"></a>Kötegelés

A Batch-tranzakciók az Azure Storage-beli Entity Group-tranzakciók néven ismertek. Az Entity (entitás) csoport tranzakcióján belüli összes műveletnek egyetlen partíción kell lennie egyetlen táblában. Ahol lehetséges, az Entity Transactions használatával szúrhat be, frissítéseket és törölheti a kötegeket. Az entitás-csoportok tranzakcióinak használata csökkenti az ügyfélalkalmazás és a kiszolgáló közötti átutazások számát, csökkentve a számlázható tranzakciók számát (az entitás-csoport tranzakciói egyetlen tranzakciónak számítanak a számlázási célokra, és akár 100 tárolási műveletet is tartalmazhatnak), és lehetővé teszi az atomi frissítések használatát (az összes művelet sikeres vagy az Entity Group tranzakción belüli összes hiba). A nagy késéssel rendelkező környezetek, például a mobileszközök nagy mértékben kihasználják az Entity Group tranzakcióit.  

#### <a name="upsert"></a>Beszúrás és frissítés

Ha csak lehet, használja a Table **Upsert** -műveleteket. Kétféle **Upsert** létezik, amelyek közül mindkettő hatékonyabb lehet, mint egy hagyományos **beszúrási** és **frissítési** művelet:  

- **InsertOrMerge**: akkor használja ezt a műveletet, ha az entitás tulajdonságainak egy részhalmazát szeretné feltölteni, de nem biztos benne, hogy az entitás már létezik-e. Ha az entitás létezik, a hívás frissíti a **Upsert** műveletben foglalt tulajdonságokat, és az összes meglévő tulajdonságot elhagyja, ha az entitás nem létezik, beszúrja az új entitást. Ez hasonló a lekérdezésekben a leképezések használatához, hogy csak a változó tulajdonságokat kell feltöltenie.
- **InsertOrReplace**: akkor használja ezt a műveletet, ha egy teljesen új entitást szeretne feltölteni, de nem biztos benne, hogy már létezik-e. Akkor használja ezt a műveletet, ha tudja, hogy az újonnan feltöltött entitás teljesen helyes, mert teljesen felülírja a régi entitást. Például szeretné frissíteni a felhasználó aktuális helyét tároló entitást, függetlenül attól, hogy az alkalmazás korábban tárolta-e a felhasználó tartózkodási helyét. az új hely entitás elkészült, és semmilyen információt nem kell megadnia az előző entitásokról.

#### <a name="storing-data-series-in-a-single-entity"></a>Adatsorozatok tárolása egyetlen entitásban

Előfordulhat, hogy egy alkalmazás egy adatsorozatot tárol, amelyet gyakran kell egyszerre lekérnie: például egy alkalmazás nyomon követheti a CPU-használatot az idő múlásával, hogy az elmúlt 24 órában ábrázolja az adatok diagramját. Az egyik módszer az, hogy egy tábla entitás/óra legyen, és minden entitás egy adott órát jelképez, és tárolja a CPU-használatot az adott órában. Az adatok ábrázolásához az alkalmazásnak le kell kérnie az adatait tároló entitásokat a 24 legutóbbi órában.  

Azt is megteheti, hogy az alkalmazás minden órában a CPU-használatot különálló tulajdonságként tárolja: az óránkénti frissítéshez az alkalmazás egyetlen **InsertOrMerge Upsert** hívást használhat a legutóbbi óra értékének frissítéséhez. Az adatábrázoláshoz az alkalmazásnak csak egyetlen entitást kell lekérnie 24 helyett, amely hatékony lekérdezést tesz elérhetővé. A lekérdezés hatékonyságával kapcsolatos további információkért lásd a [lekérdezési hatókör](#query-scope)című szakaszt.

#### <a name="storing-structured-data-in-blobs"></a>Strukturált adattárolók tárolása a blobokban

Ha batch-beszúrásokat hajt végre, majd az entitások tartományait is beolvassa, érdemes a táblázatok helyett blobokat használni. Jó példa egy naplófájl. Több percből is készíthet naplókat, és beillesztheti őket, majd egyszerre több perces naplót is beolvashat. Ebben az esetben a teljesítmény jobb, ha táblázatok helyett blobokat használ, mivel jelentősen csökkentheti a beolvasott vagy olvasott objektumok számát, valamint az esetlegesen igényelt kérelmek számát is.  

## <a name="next-steps"></a>Következő lépések

- [A Table Storage méretezhetőségi és teljesítménybeli céljai](scalability-targets.md)
- [A standard szintű Storage-fiókok méretezhetősége és teljesítménybeli céljai](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Állapot-és hibakódok](/rest/api/storageservices/Status-and-Error-Codes2)