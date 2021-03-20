---
title: A megbízható szolgáltatás programozási modelljének áttekintése
description: Ismerje meg Service Fabric megbízható szolgáltatás programozási modelljét, és kezdje el saját szolgáltatásainak megírását.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 50b8cae11cef5ee8670e2e0270fd8506b1dad4d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576536"
---
# <a name="reliable-services-overview"></a>A Reliable Services áttekintése

Az Azure Service Fabric egyszerűsíti az állapot nélküli és állapot-nyilvántartó szolgáltatások írását és kezelését. Ez a témakör a következőkkel foglalkozik:

* Az állapot nélküli és állapot-nyilvántartó szolgáltatások Reliable Services programozási modellje.
* A megbízható szolgáltatás írásakor szükséges döntéseket.
* Néhány forgatókönyv és példa arra, hogy mikor kell használni a Reliable Servicest, és hogyan írják őket.

*Reliable Services* a Service Fabric elérhető programozási modellek egyike. Egy másik a *megbízható Actors* programozási modell, amely egy [virtuális színészi](https://research.microsoft.com/en-us/projects/orleans/) alkalmazási keretrendszert biztosít a Reliable Services modellen felül. További információ a Reliable Actorsről: [Service Fabric Reliable Actors bemutatása](service-fabric-reliable-actors-introduction.md).

Service Fabric kezeli a szolgáltatások élettartamát, a kiépítés és a telepítés révén a frissítés és törlés segítségével [Service Fabric az alkalmazások felügyeletén](service-fabric-deploy-remove-applications.md)keresztül.

## <a name="what-are-reliable-services"></a>Mik a Reliable Services

A Reliable Services egy egyszerű, hatékony és legfelső szintű programozási modellt kínál, amely segít kipróbálni, hogy mi a fontos az alkalmazás számára. A Reliable Services programozási modellel a következőket kapja:

* Hozzáférés Service Fabric API-khoz. Az Service Fabric [vendégként](service-fabric-guest-executables-introduction.md)modellezett szolgáltatásokkal ellentétben Reliable Services a Service Fabric API-kat közvetlenül is használhatják. A szolgáltatások a következőket teszik lehetővé:
  * A rendszer lekérdezése
  * A fürtben található entitások állapotának jelentése
  * Értesítések fogadása a konfigurációval és a kód változásaival kapcsolatban
  * Megkeresheti és kommunikálhat más szolgáltatásokkal,
  * A [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md) használata
  * Számos más képességhez férhet hozzá, mindezt egy, az első osztályú programozási modellből számos programozási nyelven.
* Egy egyszerű modell a saját programkód futtatásához, amely más ismerős programozási modellekhez hasonlóan működik. A kód jól definiált belépési ponttal és könnyen kezelhető életciklussal rendelkezik.
* Egy csatlakoztatható kommunikációs modell. Az Ön által választott átvitelt használhatja, például a HTTP-t [webes API](./service-fabric-reliable-services-communication-aspnetcore.md)-val, websocketekkel, egyéni TCP protokollokkal vagy bármi mással. Reliable Services rendelkezésére áll néhány remek lehetőség, amelyet használhat, vagy megadhatja a sajátját.
* Az állapot-nyilvántartó szolgáltatások esetében az Reliable Services programozási modell lehetővé teszi, hogy [megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md)használatával következetesen és megbízhatóan tárolja az állapotát a szolgáltatáson belül. A megbízható gyűjtemények olyan jól elérhető és megbízható gyűjteményi osztályok, amelyek a C#-gyűjteményeket használó bárki számára ismerősek lesznek. A szolgáltatásoknak hagyományosan külső rendszerekre van szükségük a megbízható állami felügyelethez. Megbízható gyűjtemények esetén az állapotát a számítási kapacitás mellett tárolhatja, és a magas rendelkezésre állású külső üzletekből várhatóan magasabb rendelkezésre állást és megbízhatóságot is igénybe vehet. Ez a modell Emellett javítja a késést, mivel Ön a számítás és az állapot működéséhez szükséges.

## <a name="what-makes-reliable-services-different"></a>Mi teszi Reliable Services különböző

A Reliable Services a korábban írt szolgáltatásokból különböznek, mivel a Service Fabric a következőket biztosítja:

* **Megbízhatóság** – a szolgáltatás olyan megbízhatatlan környezetekben marad, ahol a gépek meghibásodnak vagy hálózati problémákba ütköznek, illetve olyan esetekben, amikor a szolgáltatások magukban foglalják a hibákat és összeomlást vagy meghibásodást. Az állapot-nyilvántartó szolgáltatások esetében a rendszer a hálózat vagy más hibák jelenléte esetén is megőrzi az állapotát.
* **Rendelkezésre állás** – a szolgáltatás elérhető és rugalmas. Service Fabric megtartja a futó másolatok kívánt számát.
* **Skálázhatóság** – a szolgáltatások egy adott hardvertől vannak leválasztva, és a hardver vagy más erőforrások hozzáadásával vagy eltávolításával szükség szerint növelhetik vagy csökkenthetik azokat. A szolgáltatások egyszerűen particionálva vannak (különösen az állapot-nyilvántartó esetekben) annak biztosítása érdekében, hogy a szolgáltatás képes legyen a részleges meghibásodások skálázására és kezelésére. A szolgáltatások a kódok használatával dinamikusan hozhatók létre és törölhetők, így a további példányok szükség szerint megadhatók, például az ügyfelek kéréseire adott válaszként. Végezetül Service Fabric a szolgáltatások egyszerűvé való kihasználása. Service Fabric lehetővé teszi, hogy több ezer szolgáltatást lehessen kiépíteni egyetlen folyamaton belül, ahelyett, hogy teljes operációsrendszer-példányt vagy-folyamatokat kellene a szolgáltatás egyetlen példányára kiépítenie.
* **Konzisztencia** – a megbízható szolgáltatásokban tárolt összes információ konzisztens lehet. Ez a szolgáltatáson belüli több megbízható gyűjtemény esetében is igaz. A szolgáltatásokon belüli gyűjtemények változásai tranzakciós szempontból atomi módon is elvégezhető.

## <a name="service-lifecycle"></a>Szolgáltatás életciklusa

Az, hogy a szolgáltatás állapota állapotú vagy állapot nélküli-e, Reliable Services adjon meg egy egyszerű életciklust, amely lehetővé teszi a kód gyors csatlakoztatását és az első lépéseket.  Az új szolgáltatás megkezdéséhez két módszert kell megvalósítani:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** – ez a módszer, ahol a szolgáltatás meghatározza a használni kívánt kommunikációs verem (eke) t. A kommunikációs verem (például a [webes API](./service-fabric-reliable-services-communication-aspnetcore.md)) meghatározza a szolgáltatás figyelő végpontját vagy végpontját (az ügyfelek hogyan érik el a szolgáltatást). Azt is meghatározza, hogy a megjelenő üzenetek hogyan működnek együtt a szolgáltatás kódjával.
* **RunAsync** – ez a módszer azt adja meg, hogy a szolgáltatás hogyan futtatja az üzleti logikát, és hol indítson el minden olyan háttérben futó feladatot, amelyet a szolgáltatás élettartama során futtatni kell. A megadott lemondási token egy jel, amikor a munkafolyamat leáll. Ha például a szolgáltatásnak üzeneteket kell kihúznia egy megbízható várólistából, és fel kell dolgoznia azokat, akkor ez a feladat.

Ha első alkalommal tanul a megbízható szolgáltatásokkal kapcsolatban, olvassa el a következőt:. Ha a megbízható szolgáltatások életciklusának részletes ismertetését keresi, tekintse meg [Reliable Services életciklus áttekintését](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Példa szolgáltatások

Ismerkedjen meg közelebbről, hogyan működik együtt a Reliable Services modell állapot nélküli és állapot-nyilvántartó szolgáltatásokkal.

### <a name="stateless-reliable-services"></a>Állapot nélküli Reliable Services

Az *állapot nélküli szolgáltatások* olyanok, amelyekben a szolgáltatáson belül nem tart fenn állapotot a hívások között. A jelen lévő állapotok teljes mértékben rendelkezésre állnak, és nem igényel szinkronizálást, replikálást, adatmegőrzést vagy magas rendelkezésre állást.

Vegyünk például egy olyan számológépet, amely nem rendelkezik memóriával, és fogadja az összes feltételt és műveletet egyszerre történő végrehajtásra.

Ebben az esetben a `RunAsync()` szolgáltatás (C#) vagy `runAsync()` (Java) üres, mert nincs olyan háttérbeli feladat-feldolgozás, amelyet a szolgáltatásnak el kell végeznie. A számológép szolgáltatás létrehozásakor egy `ICommunicationListener` (C#) vagy `CommunicationListener` (Java) (például [webes API](./service-fabric-reliable-services-communication-aspnetcore.md)) értéket ad vissza, amely egy figyelő végpontot nyit meg egy bizonyos porton. Ez a figyelő végpont a számológép nyilvános API-ját definiáló különböző számítási módszerekhez (például: "Add (N1, N2)") csatlakoztatható.

Amikor egy ügyfél hívást kezdeményez, a rendszer meghívja a megfelelő metódust, és a számológép szolgáltatás végrehajtja a műveleteket a megadott adatokon, és visszaadja az eredményt. Nem tárol semmilyen állapotot.

A belső állapot tárolásának mellőzése egyszerűvé teszi ezt a példát. A legtöbb szolgáltatás azonban nem igazán állapot nélküli. Ehelyett Externalize az állapotukat egy másik áruházba. (Például minden olyan webalkalmazás, amely a munkamenet-állapot megőrzésére támaszkodik egy tárolóban vagy gyorsítótárban nem állapot nélküli.)

Gyakori példa arra, hogy az állapot nélküli szolgáltatások hogyan használhatók az Service Fabricban, amely egy webalkalmazás nyilvános API-ját teszi elérhetővé. Az előtér-szolgáltatás ezután megbeszéli az állapot-nyilvántartó szolgáltatásokat a felhasználói kérések végrehajtásához. Ebben az esetben az ügyfelektől érkező hívások egy ismert portra vannak irányítva, például 80, ahol az állapot nélküli szolgáltatás figyel. Ez az állapot nélküli szolgáltatás fogadja a hívást, és meghatározza, hogy a hívás egy megbízható féltől származik-e, és hogy melyik szolgáltatásra van szánva.  Ezután az állapot nélküli szolgáltatás továbbítja a hívást az állapot-nyilvántartó szolgáltatás megfelelő partíciójának, és megvárja a választ. Ha az állapot nélküli szolgáltatás választ kap, az az eredeti ügyfélre válaszol. Ilyen szolgáltatás például a *Service Fabric első lépések* minta ([C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)  /  [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)), az adott tárházban található egyéb Service Fabric minták között.

### <a name="stateful-reliable-services"></a>Állapot-nyilvántartó Reliable Services

Egy *állapot-nyilvántartó szolgáltatásnak olyan állapotban* kell lennie, amelynek meg kell felelnie az állapotnak, és meg kell jelen lennie ahhoz, hogy a szolgáltatás működőképes legyen. Vegyünk egy olyan szolgáltatást, amely folyamatosan kiszámítja az egyes értékek mozgóátlagát a kapott frissítések alapján. Ehhez meg kell adnia a feldolgozáshoz szükséges bejövő kérelmek aktuális készletét és az aktuális átlagot. Bármely szolgáltatás, amely adatokat kér le, dolgoz fel és tárol egy külső tárolóban (például az Azure Blob vagy a Table Store-ban) állapotú. Csak a külső állapot tárolójában tartja meg az állapotát.

A legtöbb szolgáltatás jelenleg kívülről tárolja az állapotukat, mivel a külső tároló, amely az adott állapot megbízhatóságát, rendelkezésre állását, méretezhetőségét és egységességét biztosítja. Service Fabric nem szükséges, hogy a szolgáltatások kívülről tárolják az állapotukat. Service Fabric a szolgáltatás kódjára és a szolgáltatás állapotára vonatkozó követelményekről gondoskodik.

Tegyük fel, hogy olyan szolgáltatást szeretnénk írni, amely képeket dolgoz fel. Ennek elvégzéséhez a szolgáltatás egy rendszerképet és az adott képen végrehajtandó konverziók sorozatát veszi igénybe. Ez a szolgáltatás egy kommunikációs figyelőt ad vissza (tegyük fel, hogy ez egy WebAPI), amely egy hasonló API-t tesz elérhetővé `ConvertImage(Image i, IList<Conversion> conversions)` . Amikor kérelmet kap, a szolgáltatás tárolja azt a-ben `IReliableQueue` , és visszaad egy azonosítót az ügyfélnek, hogy nyomon tudja követni a kérést.

Ebben a szolgáltatásban `RunAsync()` összetettebb lehet. A szolgáltatás olyan hurkot tartalmaz, `RunAsync()` amely a kérelmeket lekéri, `IReliableQueue` és végrehajtja a kért konverziókat. Az eredményeket úgy tárolja a rendszer, `IReliableDictionary` hogy az ügyfél mikor kapja vissza a konvertált lemezképeit. Annak biztosítása érdekében, hogy ha valami meghibásodik, a rendszerkép nem vész el, ez a megbízható szolgáltatás kihúzza a várólistát, végrehajtja a konverziót, és az eredményt egyetlen tranzakcióban tárolja. Ebben az esetben a rendszer eltávolítja az üzenetet a várólistából, és az eredményeket csak akkor tárolja az eredmény-szótárban, ha a konverziók befejeződik. Azt is megteheti, hogy a szolgáltatás lekéri a rendszerképet a sorból, és azonnal tárolja azt egy távoli tárolóban. Ez csökkenti a szolgáltatás által kezelt állapot mennyiségét, de növeli a bonyolultságot, mivel a szolgáltatásnak meg kell őriznie a szükséges metaadatokat a távoli tároló kezeléséhez. Ha bármelyik megközelítéssel nem sikerül a közepén, a kérelem a várólistán marad, és a feldolgozásra vár.

Bár ez a szolgáltatás egy jellemző .NET-szolgáltatáshoz hasonlóan hangzik, a különbség az, hogy a használt adatstruktúrákat ( `IReliableQueue` és `IReliableDictionary` ) a Service Fabric biztosítják, és megbízható, elérhető és konzisztensek.

## <a name="when-to-use-reliable-services-apis"></a>Mikor kell használni a Reliable Services API-kat

Vegye fontolóra Reliable Services API-kat, ha:

* Azt szeretné, hogy a szolgáltatás kódja (és opcionálisan állapotú) legyen nagyon elérhető és megbízható.
* A tranzakciós garanciák több egységre is kiterjednek (például megrendelések és rendelési sorok).
* Az alkalmazás állapota természetesen megbízható Szótárakként és várólistákként is modellezhető.
* Az alkalmazások kódjának vagy állapotának nagyon alacsony olvasási és írási műveletekkel kell rendelkeznie.
* Az alkalmazásnak meg kell határoznia a tranzakciós műveletek párhuzamosságát vagy részletességét egy vagy több megbízható gyűjteményen belül.
* Felügyelni kívánja a kommunikációt, vagy szabályozni szeretné a szolgáltatás particionálási sémáját.
* A kódnak szabad szálon futó futásidejű környezetre van szüksége.
* Az alkalmazásnak dinamikusan kell létrehoznia vagy megsemmisítenie megbízható szótárakat vagy várólistákat vagy teljes szolgáltatásokat futásidőben.
* A szolgáltatás állapotához programozott módon kell vezérelni Service Fabric által biztosított biztonsági mentési és visszaállítási funkciókat.
* Az alkalmazásnak meg kell őriznie a változási előzményeket az egysége számára.
* Harmadik féltől származó, egyéni állami szolgáltatókat szeretne fejleszteni vagy használni.

## <a name="next-steps"></a>Következő lépések

* [Reliable Services rövid útmutató](service-fabric-reliable-services-quick-start.md)
* [Megbízható gyűjtemények](service-fabric-reliable-services-reliable-collections.md)
* [Az Reliable Actors programozási modell](service-fabric-reliable-actors-introduction.md)
