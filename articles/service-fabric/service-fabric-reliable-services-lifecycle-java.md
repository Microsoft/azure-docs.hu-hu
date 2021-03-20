---
title: Az Azure Service Fabric Reliable Services életciklusa
description: Ismerkedjen meg az Azure Service Fabric Reliable Services alkalmazásban a Java használatával állapot-nyilvántartó és állapot nélküli szolgáltatásokkal kapcsolatos életciklus-eseményekkel.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: b22c78a0259e4430ac6bfae1c0a9379c4a832cd4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87324607"
---
# <a name="reliable-services-lifecycle"></a>A Reliable Services életciklusa
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-lifecycle.md)
> * [Java Linuxon](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services az Azure Service Fabric-ban elérhető programozási modellek egyike. A Reliable Services életciklusának megismerése során a legfontosabb az alapszintű életciklus eseményeinek megismerése. Az események pontos sorrendje a konfigurációs adatoktól függ. 

Általánosságban elmondható, hogy a Reliable Services életciklusa a következő eseményeket tartalmazza:

* Indításkor:
  * A szolgáltatások építése megtörténik.
  * A szolgáltatásokból lehetőség van nulla vagy több figyelő létrehozására és visszaadására.
  * A szolgáltatással folytatott kommunikációhoz minden visszaadott figyelő meg van nyitva.
  * A szolgáltatás `runAsync` metódusának neve, így a szolgáltatás hosszan futó vagy háttérbeli munkát végezhet.
* Leállítás közben:
  * A rendszer megszakította az átadott törlési jogkivonatot `runAsync` , és a figyelők le vannak zárva.
  * Maga a szolgáltatási objektum megsemmisült.

A Reliable Services eseményeinek sorrendje némileg változhat attól függően, hogy a megbízható szolgáltatás állapota vagy állapota nem megfelelő-e. 

Az állapot-nyilvántartó szolgáltatások esetében az elsődleges swap-forgatókönyvet is meg kell címezni. Ebben a sorozatban az elsődleges szerepkört egy másik replikába (vagy visszatérve) helyezi át a szolgáltatás leállítása nélkül. 

Végül a hiba-vagy meghibásodási feltételeket kell meggondolni.

## <a name="stateless-service-startup"></a>Állapot nélküli szolgáltatás indítása
Az állapot nélküli szolgáltatások életciklusa meglehetősen egyszerű. Az események sorrendje:

1. A szolgáltatás építése megtörténik.
2. Ezek az események párhuzamosan történnek:
    - `StatelessService.createServiceInstanceListeners()` meghívása megtörtént, és a visszaadott figyelők meg lesznek nyitva. `CommunicationListener.openAsync()` minden figyelőre meghívva.
    - A szolgáltatás `runAsync` metódusa ( `StatelessService.runAsync()` ) meghívása.
3. Ha van ilyen, a szolgáltatás saját `onOpenAsync` metódusát hívja meg. Pontosabban `StatelessService.onOpenAsync()` a neve. Ez egy nem gyakori felülbírálás, de elérhető.

Fontos megjegyezni, hogy a figyelők létrehozásához és megnyitásához, illetve a hívásának meghívásához nincs megrendelés `runAsync` . Előfordulhat, hogy a figyelők megnyílnak a `runAsync` megkezdése előtt. Hasonlóképpen lehet `runAsync` meghívni a kommunikációs figyelők megnyitása előtt, vagy még a megépítésük előtt. Ha bármilyen szinkronizálásra van szükség, azt a végrehajtónak kell elvégeznie. Íme néhány gyakori megoldás:

* Néha a figyelők nem működhetnek addig, amíg más adatokat nem hoznak létre, vagy más munkát nem végeznek. Az állapot nélküli szolgáltatások esetében általában a szolgáltatás konstruktorában végezhető el a munka. A `createServiceInstanceListeners()` hívás során vagy maga a figyelő felépítésének részeként is elvégezhető.
* Néha a kód `runAsync` nem indul el, amíg a figyelők meg nem nyílnak. Ebben az esetben további koordinációra van szükség. Egy gyakori megoldás egy jelző hozzáadása a figyelőkhöz. A jelző jelzi, hogy mikor fejeződött be a figyelők. A `runAsync` metódus ellenőrzi ezt, mielőtt folytatná a tényleges munkát.

## <a name="stateless-service-shutdown"></a>Állapot nélküli szolgáltatás leállítása
Állapot nélküli szolgáltatás leállításakor ugyanezt a mintát követjük, de fordítva:

1. Ezek az események párhuzamosan történnek:
    - Minden nyitott figyelő le van zárva. `CommunicationListener.closeAsync()` minden figyelőre meghívva.
    - A rendszer megszakította az átadott törlési jogkivonatot `runAsync()` . A lemondási token tulajdonságának visszaadásának ellenőrzése `isCancelled` `true` , és ha az hívása, a jogkivonat `throwIfCancellationRequested` metódusa `CancellationException` .
2. Amikor `closeAsync()` befejezi az egyes figyelőket, és `runAsync()` befejezi a szolgáltatást, a szolgáltatás `StatelessService.onCloseAsync()` metódusa is meghívja, ha van ilyen. Ez nem egy gyakori felülbírálás, de az erőforrások biztonságos bezárásához, a háttérben történő feldolgozás befejezéséhez, a külső állapot mentéséhez vagy a meglévő kapcsolatok bezárásához használható.
3. `StatelessService.onCloseAsync()`A befejezést követően a szolgáltatás objektum megsemmisül.

## <a name="stateful-service-startup"></a>Állapot-nyilvántartó szolgáltatás indítása
Az állapot-nyilvántartó szolgáltatások olyan mintázattal rendelkeznek, amely hasonló az állapot nélküli szolgáltatásokhoz, néhány módosítással.  Itt látható az állapot-nyilvántartó szolgáltatás indításához szükséges események sorrendje:

1. A szolgáltatás építése megtörténik.
2. `StatefulServiceBase.onOpenAsync()` hívása. Ez a hívás általában nincs felülbírálva a szolgáltatásban.
3. Ezek az események párhuzamosan történnek:
    - `StatefulServiceBase.createServiceReplicaListeners()` hívása megtörtént. 
      - Ha a szolgáltatás elsődleges szolgáltatás, a rendszer minden visszaadott figyelőt megnyit. `CommunicationListener.openAsync()` minden figyelőre meghívva.
      - Ha a szolgáltatás másodlagos szolgáltatás, csak a `listenOnSecondary = true` megnyitott figyelők láthatók. A formátumú másodlagos zónák megnyitott figyelők kevésbé gyakoriak.
    - Ha a szolgáltatás jelenleg elsődleges, a rendszer a szolgáltatás `StatefulServiceBase.runAsync()` metódusát hívja meg.
4. A replika-figyelő összes hívásának befejezése után a rendszer meghívja a metódust `openAsync()` `runAsync()` `StatefulServiceBase.onChangeRoleAsync()` . Ez a hívás általában nincs felülbírálva a szolgáltatásban.

Az állapot nélküli szolgáltatások esetében az állapot-nyilvántartó szolgáltatáshoz hasonlóan nincs koordináció a figyelők létrehozási és megnyitási sorrendje és a `runAsync` meghívása között. Ha koordinációra van szüksége, a megoldások sokkal ugyanazok. De van még egy további eset az állapot-nyilvántartó szolgáltatáshoz. Tegyük fel, hogy a kommunikációs figyelőkre érkező hívások esetében szükséges, hogy az információk bizonyos [megbízható gyűjteményekben](service-fabric-reliable-services-reliable-collections.md)legyenek tárolva. Mivel előfordulhat, hogy a kommunikációs figyelők megnyílnak, mielőtt a megbízható gyűjtemények olvashatók vagy írhatók, és mielőtt `runAsync` elindul, további koordinációra van szükség. A legegyszerűbb és leggyakoribb megoldás, ha a kommunikációs figyelők hibakódot adnak vissza. Az ügyfél a hibakód használatával tudja megismételni a kérést.

## <a name="stateful-service-shutdown"></a>Állapot-nyilvántartó szolgáltatás leállítása
Az állapot nélküli szolgáltatásokhoz hasonlóan a leállítás során az életciklus eseményei megegyeznek az indítás során, de fordítva. Állapot-nyilvántartó szolgáltatás leállításakor a következő események történnek:

1. Ezek az események párhuzamosan történnek:
    - Minden nyitott figyelő le van zárva. `CommunicationListener.closeAsync()` minden figyelőre meghívva.
    - A rendszer megszakította az átadott törlési jogkivonatot `runAsync()` . A lemondási token `isCancelled()` metódusának hívása visszaadja a függvényt `true` , és ha meghívja, a jogkivonat `throwIfCancellationRequested()` metódusa a következőt jeleníti meg: `OperationCanceledException` .
2. Miután `closeAsync()` befejezte az egyes figyelőket `runAsync()` , és befejezi a szolgáltatást, a szolgáltatás `StatefulServiceBase.onChangeRoleAsync()` hívása is megtörténik. Ez a hívás általában nincs felülbírálva a szolgáltatásban.

   > [!NOTE]  
   > A `runAsync` befejezésre való várakozás csak akkor szükséges, ha a replika elsődleges replika.

3. A `StatefulServiceBase.onChangeRoleAsync()` metódus befejeződése után `StatefulServiceBase.onCloseAsync()` meghívja a metódust. Ez a hívás nem gyakori felülbírálás, de elérhető.
3. `StatefulServiceBase.onCloseAsync()`A befejezést követően a szolgáltatás objektum megsemmisül.

## <a name="stateful-service-primary-swaps"></a>Állapot-nyilvántartó szolgáltatás elsődleges felcserélése
Ha egy állapot-nyilvántartó szolgáltatás fut, a rendszer megnyit egy kommunikációs figyelőt, és a `runAsync` metódust csak az állapot-nyilvántartó szolgáltatások elsődleges replikái számára hívja meg. A másodlagos replikák létre vannak építve, de nem találhatók további hívások. Egy állapot-nyilvántartó szolgáltatás fut, a jelenleg az elsődleges replika is változhat. Az állapot-nyilvántartó replika által megtekinthető életciklus-események attól függnek, hogy a replika le van-e lefokozva vagy előléptetve a swap során.

### <a name="for-the-demoted-primary"></a>A lefokozott elsődleges
Service Fabric szüksége van az elsődleges replikára, amely le van fokozni az üzenetek feldolgozásának leállításához és a háttérbeli munka leállításához. Ez a lépés hasonló a szolgáltatás leállításakor. Az egyik különbség, hogy a szolgáltatás nincs elpusztulva vagy lezárva, mert másodlagosként marad. Az alábbi események történnek:

1. Ezek az események párhuzamosan történnek:
    - Minden nyitott figyelő le van zárva. `CommunicationListener.closeAsync()` minden figyelőre meghívva.
    - A rendszer megszakította az átadott törlési jogkivonatot `runAsync()` . A lemondási token `isCancelled()` metódusának beolvasása `true` . Ha az hívása megtörtént, a jogkivonat `throwIfCancellationRequested()` metódusa egy elemet mutat be `OperationCanceledException` .
2. Miután `closeAsync()` befejezte az egyes figyelőket `runAsync()` , és befejezi a szolgáltatást, a szolgáltatás `StatefulServiceBase.onChangeRoleAsync()` hívása is megtörténik. Ez a hívás általában nincs felülbírálva a szolgáltatásban.

### <a name="for-the-promoted-secondary"></a>A előléptetett másodlagos
Hasonlóképpen Service Fabric szüksége van arra a másodlagos másodpéldányra, amelyet a rendszer a hálózati üzenetek figyelésének megkezdéséhez, valamint a befejezéshez szükséges összes háttér-feladat elindításához előléptet. Ez a folyamat hasonló a szolgáltatás létrehozásakor. A különbség az, hogy maga a replika már létezik. Az alábbi események történnek:

1. Ezek az események párhuzamosan történnek:
    - `StatefulServiceBase.createServiceReplicaListeners()` meghívásra kerül, és a visszaadott figyelők meg lesznek nyitva. `CommunicationListener.openAsync()` minden figyelőre meghívva.
    - A szolgáltatás `StatefulServiceBase.runAsync()` metódusának neve.
2. A replika-figyelő összes hívásának befejezése után a rendszer meghívja a metódust `openAsync()` `runAsync()` `StatefulServiceBase.onChangeRoleAsync()` . Ez a hívás általában nincs felülbírálva a szolgáltatásban.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Gyakori problémák az állapot-nyilvántartó szolgáltatás leállítása és az elsődleges lefokozás során
Service Fabric több okból is megváltoztatja az állapot-nyilvántartó szolgáltatás elsődleges állapotát. A leggyakoribb okok a [fürtök újraelosztása](service-fabric-cluster-resource-manager-balancing.md) és az [alkalmazások frissítése](service-fabric-application-upgrade.md). A műveletek során fontos, hogy a szolgáltatás tiszteletben tartsa a szolgáltatást `cancellationToken` . Ez a szolgáltatás normál leállításakor is érvényes, például ha törölték a szolgáltatást.

A megszakítást nem kezelő szolgáltatások számos problémát tapasztalhatnak. Ezek a műveletek lassúak, mert Service Fabric megvárja, amíg a szolgáltatások szabályosan leállnak. Ez végső soron sikertelen frissítésekhez vezethet, amely időtúllépést és visszaállítást eredményezett. A lemondási token betartásának elmulasztása miatt kiegyensúlyozatlan fürtök is lehetnek. A fürtök kiegyensúlyozatlan állapotba kerülnek, mivel a csomópontok melegek lesznek. A szolgáltatásokat azonban nem lehet kiegyensúlyozni, mert túl sokáig tart ahhoz, hogy máshová helyezze őket. 

Mivel a szolgáltatások állapot-nyilvántartó, az is valószínű, hogy a szolgáltatások [megbízható gyűjteményeket](service-fabric-reliable-services-reliable-collections.md)használnak. Service Fabric az elsődleges lefokozása után az egyik első dolog, ami megtörténik, az írási hozzáférés az alapul szolgáló állapothoz visszavonva. Ez egy második olyan problémát eredményez, amely hatással lehet a szolgáltatás életciklusára. A gyűjtemények az időzítés alapján adják vissza a kivételeket, és azt, hogy a replika áthelyezése vagy leállítása folyamatban van-e. Fontos, hogy megfelelően kezelje ezeket a kivételeket. 

Service Fabric által kiváltott kivételek állandó [( `FabricException` )](/java/api/system.fabric.exception) vagy átmeneti [( `FabricTransientException` )](/java/api/system.fabric.exception.fabrictransientexception)értékűek. A végleges kivételeket naplózni és dobni kell. Az újrapróbálkozási logikán alapuló átmeneti kivételek is megismételhetők.

A Reliable Services tesztelésének és érvényesítésének fontos része a `ReliableCollections` szolgáltatás-életciklussal kapcsolatos eseményekkel együtt a-től származó kivételek kezelése. Javasoljuk, hogy mindig a terhelés alatt futtassa a szolgáltatást. Az éles környezetbe való üzembe helyezés előtt frissítéseket és [Chaos-teszteket](service-fabric-controlled-chaos.md) is végre kell hajtania. Ezek az alapszintű lépések biztosítják a szolgáltatás megfelelő megvalósítását, valamint az életciklus-események megfelelő kezelését.

## <a name="notes-on-service-lifecycle"></a>A szolgáltatás életciklusával kapcsolatos megjegyzések
* A `runAsync()` metódus és a `createServiceInstanceListeners/createServiceReplicaListeners` hívások is opcionálisak. Egy szolgáltatás lehet egy, mindkettő vagy egyik sem. Ha például a szolgáltatás a felhasználói hívásokra adott válaszként működik, nincs szükség a megvalósítására `runAsync()` . Csak a kommunikációs figyelők és a hozzájuk tartozó kódok szükségesek.  Hasonlóképpen, a kommunikációs figyelők létrehozása és visszaküldése nem kötelező. Lehetséges, hogy a szolgáltatás csak a háttérben dolgozik, így csak a szükséges `runAsync()` .
* A szolgáltatás sikeres befejezéséhez `runAsync()` és visszaküldéséhez érvényes. Ez nem minősül meghibásodási feltételnek. A szolgáltatás befejezésének hátterét képviseli. Állapot-nyilvántartó Reliable Services esetén `runAsync()` újra kell hívni, ha a szolgáltatás le van lefokozva az elsődlegesről, majd az elsődlegesre lett előléptetve.
* Ha egy szolgáltatás kilép egy `runAsync()` váratlan kivételből, ez a hiba. A szolgáltatási objektum le van állítva, és a rendszer állapottal kapcsolatos hibát jelez.
* Bár az ilyen metódusokból való visszatéréshez nincs időkorlát, azonnal elveszíti az írás lehetőségét. Ezért nem végezheti el a valódi munkát. Javasoljuk, hogy a törlési kérés fogadásakor a lehető leggyorsabban térjen vissza. Ha a szolgáltatás ésszerű időn belül nem válaszol ezekre az API-hívásokra, Service Fabric kényszerítheti a szolgáltatás bezárását. Ez általában csak az alkalmazások frissítése vagy a szolgáltatás törlésekor történik. Alapértelmezés szerint ez az időkorlát 15 perc.
* Az `onCloseAsync()` elérési úton történt hibák `onAbort()` meghívásának eredménye. Ez a hívás egy utolsó esélyes, legjobb lehetőség arra, hogy a szolgáltatás kiürítse és felszabadítsa az általuk igényelt erőforrásokat. Ez általában akkor fordul elő, ha a csomóponton állandó hibát észlel, vagy ha Service Fabric belső meghibásodások miatt nem tudja megbízhatóan kezelni a szolgáltatási példány életciklusát.
* `OnChangeRoleAsync()` akkor lesz meghívva, amikor az állapot-nyilvántartó szolgáltatás replikája megváltoztatja a szerepkört, például az elsődleges vagy a másodlagos. Az elsődleges replikák írási állapotot kapnak (a megbízható gyűjtemények létrehozásához és írásához engedélyezett). A másodlagos replikák olvasási állapotot kapnak (csak a meglévő megbízható gyűjteményekből olvashatók be). Egy állapot-nyilvántartó szolgáltatásban a legtöbb munka az elsődleges replikán történik. A másodlagos replikák csak olvasási ellenőrzés, jelentéskészítés, adatbányászat vagy más írásvédett feladatok végrehajtására használhatók.

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Reliable Servicesba](service-fabric-reliable-services-introduction.md)
* [Reliable Services rövid útmutató](service-fabric-reliable-services-quick-start-java.md)
