---
title: Az Azure Service Fabricban az állapot-nyilvántartó szervezeti egységek tesztelése
description: Ismerje meg az egység tesztelési Service Fabric állapot-nyilvántartó szolgáltatásainak fogalmait és gyakorlatát.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75433915"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Állapot-nyilvántartó Service Fabric egység tesztelése

Ez a cikk az egység tesztelési Service Fabric állapot-nyilvántartó szolgáltatásaival kapcsolatos fogalmakat és gyakorlatokat ismerteti. A Service Fabricon belüli egység tesztelése a saját szempontjait is megőrzi, mivel az alkalmazás kódja aktívan fut több különböző kontextusban. Ez a cikk az alkalmazás kódjának biztosítására szolgáló eljárásokat ismerteti a különböző környezetekben futtatott alkalmazások esetében.

## <a name="unit-testing-and-mocking"></a>Egység tesztelése és modellezése
Az egység tesztelése ebben a cikkben olyan automatizált tesztelést végez, amely egy tesztelési futó (például MSTest vagy NUnit) kontextusában hajtható végre. A jelen cikkben szereplő mértékegység-tesztek nem végeznek műveleteket egy távoli erőforráson, például egy adatbázison vagy REST API-n. Ezeket a távoli erőforrásokat ki kell gúnyolni. Ennek a cikknek a kontextusában hamis, rögzíthető és szabályozható a távoli erőforrások visszatérési értékei.

### <a name="service-fabric-considerations"></a>Service Fabric megfontolások
Service Fabric állapot-nyilvántartó szolgáltatás tesztelési egysége több szempontot tartalmaz. Első lépésként a szolgáltatási kód több csomóponton, de különböző szerepköröknél fut. Az egység-tesztek kiértékelik az egyes szerepkörök kódját a teljes lefedettség eléréséhez. A különböző szerepkörök elsődleges, aktív másodlagos, üresjárati másodlagos és ismeretlenek. A none szerepkörhöz általában nincs szükség különleges lefedettségre, mivel Service Fabric úgy véli, hogy ez a szerepkör érvénytelen vagy NULL értékű szolgáltatás. Másodszor, minden egyes csomópont megváltoztatja a szerepkörét egy adott ponton. A teljes lefedettség elérése érdekében a kód végrehajtásának elérési útját a szerepkör változásaival kell tesztelni.

## <a name="why-unit-test-stateful-services"></a>Miért van az egység tesztelése állapot-nyilvántartó szolgáltatások? 
Az állapot-nyilvántartó szervezeti egységek tesztelésével feltárhatja az olyan gyakori hibákat, amelyeket az alkalmazás vagy a tartomány-specifikus egység tesztelése nem feltétlenül okoz. Ha például az állapot-nyilvántartó szolgáltatás memóriában lévő állapottal rendelkezik, akkor az ilyen típusú tesztelés ellenőrzi, hogy a memóriában tárolt állapot szinkronban van-e az egyes replikák között. Ez a típusú tesztelés azt is ellenőrizheti, hogy egy állapot-nyilvántartó szolgáltatás válaszol-e a Service Fabric-előkészítés által átadott lemondási jogkivonatokra. A lemondások elindításakor a szolgáltatásnak le kell állítania a hosszú ideig futó és/vagy aszinkron műveleteket.  

## <a name="common-practices"></a>Gyakori eljárások

A következő szakasz az állapot-nyilvántartó szolgáltatások tesztelésével kapcsolatos leggyakoribb eljárásokról ad tájékoztatást. Azt is javasolja, hogy a kigúnyoló rétegnek hogyan kell szorosan összehangolni a Service Fabric-előkészítéssel és az állami felügyelettel. A [ServiceFabric. mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) (3.3.0 vagy újabb) egy olyan függvénytár, amely a javasolt modellezési funkciókat biztosítja, és követi az alább vázolt eljárásokat.

### <a name="arrangement"></a>Megállapodás

#### <a name="use-multiple-service-instances"></a>Több szolgáltatási példány használata
Az egységek teszteléséhez egy állapot-nyilvántartó szolgáltatás több példányának kell futnia. Ez szimulálja, hogy mi történik valójában a fürtön, ahol Service Fabric kiépíti több replikát, amely a szolgáltatást futtatja a különböző csomópontok között. Ezek a példányok azonban eltérő kontextusban lesznek végrehajtva. A teszt futtatásakor minden példánynak a fürtben várt szerepkör-konfigurációval kell rendelkeznie. Ha például a szolgáltatásnak meg kell egyeznie a cél replikájának 3. méretével, Service Fabric három replikát fog kiépíteni a különböző csomópontokon. Az egyik az elsődleges, a másik kettő pedig aktív másodlagos.

A legtöbb esetben a szolgáltatás végrehajtási útvonala némileg eltér az egyes szerepköröknél. Ha például a szolgáltatás nem fogadja el az aktív Másodlagostól érkező kéréseket, akkor előfordulhat, hogy a szolgáltatás egy olyan tájékoztató kivételt ad vissza, amely azt jelzi, hogy a kérést másodlagosan próbálták meg. Több példány esetén a rendszer teszteli a helyzetet.

Emellett több példány is lehetővé teszi a tesztek számára, hogy az egyes példányok szerepköreit átállítsa, hogy a válaszok konzisztensek legyenek a szerepkör változásai ellenére.

#### <a name="mock-the-state-manager"></a>A State Manager kigúnyolása
A State Managert távoli erőforrásként kell kezelni, ezért a rendszer kigúnyolja. A State Manager kiírásakor szükség van néhány, a memóriában tárolt tárterületre, amellyel nyomon követhető, hogy a rendszer milyen adatokat ment az állami kezelőbe, hogy azok olvashatók és ellenőrizhetők legyenek. Ez egy egyszerű módja annak, hogy a megbízható gyűjtemények egyes típusaihoz hozzon létre mintául szolgáló példányokat. Ezekben a modellekben olyan adattípust használjon, amely szorosan igazodik az adott gyűjteményen végrehajtott műveletekhez. Az alábbiakban néhány javasolt adattípust ismertetünk az egyes megbízható gyűjteményekhez

- IReliableDictionary<TKey, TValue>-> System. Collections. párhuzamos. ConcurrentDictionary<TKey, TValue>
- IReliableQueue \<T> -> System. Collections. Generic. üzenetsor\<T>
- IReliableConcurrentQueue \<T> -> System. Collections. párhuzamos. ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Számos State Manager-példány, egyetlen tárterület
Ahogy azt korábban említettük, a State Managert és a megbízható gyűjteményeket távoli erőforrásként kell kezelni. Ezért ezeket az erőforrásokat az egységen belüli tesztek során kell kitalálni. Azonban egy állapot-nyilvántartó szolgáltatás több példányának futtatásakor a rendszer kihívást jelent, hogy minden egyes kigúnyolt állapotú kezelőt szinkronizáljon a különböző állapot-nyilvántartó szolgáltatási példányok között. Ha az állapot-nyilvántartó szolgáltatás a fürtön fut, a Service Fabric gondoskodik az egyes másodlagos replikák állapot-kezelőjének az elsődleges replikával való összhangban tartásáról. Ezért a teszteknek ugyanúgy kell működniük, hogy szimulálják a szerepkör módosításait.

A szinkronizálás egyszerű módja, ha a mögöttes objektumra egyedi mintát használ, amely az egyes megbízható gyűjteményeknek írt adatokat tárolja. Ha például egy állapot-nyilvántartó szolgáltatás a szolgáltatást használja `IReliableDictionary<string, string>` . A kigúnyolt állapot kezelőjének a modelljét kell visszaadnia `IReliableDictionary<string, string>` . A modell a `ConcurrentDictionary<string, string>` segítségével nyomon követheti az írt kulcs/érték párokat. A `ConcurrentDictionary<string, string>` -nek a szolgáltatásnak átadott állami vezetők összes példánya által használt egyedinek kell lennie.

#### <a name="keep-track-of-cancellation-tokens"></a>A lemondási tokenek nyomon követése
A lemondási jogkivonatok az állapot-nyilvántartó szolgáltatások fontos, de gyakran megtekinthető aspektusa. Ha Service Fabric egy állapot-nyilvántartó szolgáltatás elsődleges replikáját indítja el, a rendszer lemondási jogkivonatot biztosít. Ez a lemondási token arra szolgál, hogy jelezze a szolgáltatásnak, amikor a rendszer eltávolítja vagy lefokozza egy másik szerepkört. Az állapot-nyilvántartó szolgáltatásnak le kell állítania a hosszú ideig futó vagy aszinkron műveleteket, hogy a Service Fabric el tudja végezni a szerepkör-módosítási munkafolyamatot.

Az RunAsync, a ChangeRoleAsync, a OpenAsync és a CloseAsync számára biztosított törlési jogkivonatokat a tesztek végrehajtása során kell megtartani. Ha ezeket a jogkivonatokat megtartja, a teszt szimulálhatja a szolgáltatás leállítását vagy lefokozását, és ellenőrizheti, hogy a szolgáltatás megfelelően válaszol-e.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Teljes körű tesztelés a kigúnyolt távoli erőforrásokkal
Az egységek teszteléséhez az alkalmazás kódjának nagy részét kell végrehajtani, amely módosíthatja az állapot-nyilvántartó szolgáltatás állapotát. Javasoljuk, hogy a tesztek több végpontok közötti természetű legyenek. Az egyetlen létező modell a távoli erőforrás-interakciók rögzítése, szimulálása és/vagy ellenőrzése. Ez magában foglalja az állapot-kezelővel és a megbízható gyűjteményekkel folytatott interakciókat. Az alábbi kódrészlet egy példa a teljes körű tesztelést bemutató uborka vizsgálatra:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Ez a teszt azt állítja be, hogy az egyik replikán rögzített adattárolók elérhetők legyenek egy másodlagos replika számára, ha az elsődlegesre van előléptetve. Feltételezve, hogy a megbízható gyűjtemény az alkalmazotti adataihoz tartozó tároló, a teszttel esetlegesen megjelenő, AA típusú lehetséges hiba, ha az új alkalmazott mentéséhez az alkalmazás kódja nem lett végrehajtva a `CommitAsync` tranzakcióban. Ebben az esetben az alkalmazottak beszerzésére irányuló második kérelem nem adja vissza az első kérelem által felvett alkalmazottat.

### <a name="acting"></a>Működő
#### <a name="mimic-service-fabric-replica-orchestration"></a>Service Fabric replika összehangolása
Több szolgáltatási példány kezelésekor a teszteknek ugyanúgy kell inicializálnia és lebontaniuk ezeket a szolgáltatásokat, mint a Service Fabrict. Ha például egy új elsődleges replikán hoz létre egy szolgáltatást, Service Fabric meghívja a CreateServiceReplicaListener, a OpenAsync, a ChangeRoleAsync és a RunAsync. Az életciklus eseményei a következő cikkekben vannak dokumentálva:

- [Állapot-nyilvántartó szolgáltatás indítása](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Állapot-nyilvántartó szolgáltatás leállítása](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Állapot-nyilvántartó szolgáltatás elsődleges felcserélése](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Replika szerepkör módosításainak futtatása
Az egység tesztek a szolgáltatási példányok szerepköreit ugyanúgy kell megváltoztatni, mint a Service Fabric-előkészítést. A szerepkör állapotára szolgáló számítógép a következő cikkben van dokumentálva:

[Replika szerepkör állapotának gépe](service-fabric-concepts-replica-lifecycle.md#replica-role)

A szerepkör-változások szimulálása a tesztelés kritikus szempontjainak egyike, és képes felfedni azokat a problémákat, amelyekben a replika állapota nem konzisztens egymással. A replika állapotának inkonzisztensnek kell lennie, mert a memóriában lévő állapot tárolása statikus vagy osztály szintű példány változókban történik. Ilyen lehet például a lemondási tokenek, az enumerálások és a konfigurációs objektumok/értékek. Ez azt is biztosítja, hogy a szolgáltatás tiszteletben tartsa a RunAsync során megadott törlési jogkivonatokat, hogy engedélyezze a szerepkör módosítását. A szerepkör-változások szimulálása olyan problémákat is felfedi, amelyek felmerülhetnek, ha a kód nem íródik, hogy a RunAsync többször is meghívást lehessen tenni.

#### <a name="cancel-cancellation-tokens"></a>Visszavonási tokenek megszakítása
Léteznie kell olyan egység-teszteknek, amelyekben a RunAsync megadott törlési tokent megszakították. Ez lehetővé teszi a teszt számára, hogy ellenőrizze, hogy a szolgáltatás szabályosan leáll-e. A rendszer leállítja a hosszú ideig futó vagy aszinkron műveleteket. Egy olyan hosszú ideig futó folyamat, amely egy szolgáltatáson létezhet, egy megbízható várólistán lévő üzenetek figyelésére szolgál. Ez közvetlenül a RunAsync vagy egy háttérbeli szálon belül létezhet. A megvalósításnak tartalmaznia kell a művelet kilépéséhez szükséges logikát, ha a megszakítási tokent megszakították.

Ha az állapot-nyilvántartó szolgáltatások olyan gyorsítótár-vagy memórián kívüli állapotot használnak, amelynek csak az elsődlegesen kell szerepelnie, akkor azt el kell végezni. Ezzel a beállítással biztosíthatja, hogy ez az állapot konzisztens legyen, ha a csomópont később válik újra. A lemondás tesztelése lehetővé teszi, hogy a teszt megfelelően ellenőrizze ezt az állapotot.

#### <a name="execute-requests-against-multiple-replicas"></a>Kérelmek végrehajtása több replikán
Az állítási teszteknek ugyanazt a kérelmet kell végrehajtaniuk a különböző replikák ellen. A szerepkör változásaival párosítva a konzisztencia-problémák feloldhatók. Egy példa teszt a következő lépéseket hajthatja végre:
1. Írási kérelem végrehajtása az aktuális elsődleges
2. Olyan olvasási kérelem végrehajtása, amely az 1. lépésben írt adatok beolvasása az aktuális elsődleges értékre
3. Másodlagos elsődlegesre előléptetése. Az aktuális elsődlegesről másodlagosra is le kell fokozni
4. Hajtsa végre ugyanazt az olvasási kérést a 2. lépésben az új másodlagosra vonatkozóan.

Az utolsó lépésben a teszt képes érvényesíteni a visszaadott adathalmazt. Lehetséges, hogy ez a probléma feltárhatja, hogy a szolgáltatás által visszaadott adatmennyiség a memóriában, de végül egy megbízható gyűjtemény által. Előfordulhat, hogy a memóriában tárolt adatmennyiségek nem megfelelően szinkronizálva vannak a megbízható gyűjteményben találhatókkal.

A memóriában tárolt adatmennyiségeket általában másodlagos indexek vagy a megbízható gyűjteményben található adatösszesítések létrehozására használják.

### <a name="asserting"></a>Megkér
#### <a name="ensure-responses-match-across-replicas"></a>A válaszok egyezésének biztosítása a replikák között
Az egységbeli teszteknek azt kell megadniuk, hogy egy adott kérelemre adott válasz konzisztens legyen több replikán az elsődlegesre való áttérés után. Ez felszíni lehetséges problémákat okozhat, ha a válaszban megadott adatokat nem egy megbízható gyűjtemény támogatja, vagy a memóriában tárolva van, és nincs olyan mechanizmus, amely az adatokat replikák között szinkronizálja. Ezzel biztosíthatja, hogy a szolgáltatás konzisztens válaszokat küldjön a Service Fabric újraelosztása után, vagy feladatátvételt hajt végre egy új elsődleges replikán.

#### <a name="verify-service-respects-cancellation"></a>A szolgáltatás tiszteletben tartásának ellenőrzése
Hosszú ideig futó vagy aszinkron folyamatok, amelyeket meg kell szüntetni, ha a lemondási tokent megszakították, ellenőrizni kell, hogy a lemondás után ténylegesen le lettek-e állítva. Ezzel biztosíthatja, hogy a replika megváltozása ellenére a nem elsődleges replikán futó folyamatok ne maradjanak le az áttérés befejeződése előtt. Ezzel a megoldással olyan problémák is felhasználhatók, amelyekben a folyamat letilt egy szerepkör-módosítási vagy leállítási kérést a Service Fabricból.

#### <a name="verify-which-replicas-should-serve-requests"></a>A kérelmeket kézbesítő replikák ellenőrzése
A teszteknek meg kell tenniük a várt viselkedést, ha egy kérés nem elsődleges replikához van irányítva. A Service Fabric lehetővé teszi a másodlagos replikák kérések kiszolgálását. A megbízható gyűjteményekbe való írás azonban csak az elsődleges replikából történhet. Ha az alkalmazás csak elsődleges replikákat szándékozik kézbesíteni a kérések kiszolgálásához, vagy csak a kérelmek egy részhalmazát tudja kezelni egy másodlagos, akkor a tesztek a pozitív és a negatív esetek esetében is a várt viselkedést fogják érvényesíteni. A rendszer egy olyan replikára irányítja át a negatív esetet, amely nem kezeli a kérést, és a pozitív az ellenkezője.

## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan [tesztelheti az állapot-nyilvántartó szolgáltatásokat](service-fabric-how-to-unit-test-stateful-services.md).
