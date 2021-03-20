---
title: Azure Service Fabric-fürt skálázása
description: Ismerje meg, hogy az Azure Service Fabric-fürtök méretezése ki-vagy bekapcsolható, illetve fel vagy le. Ahogy az alkalmazás igényli a változást, így Service Fabric fürtöket.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 610c43f64f9073aefe8008473209039122cf36d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591798"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Azure Service Fabric-fürtök méretezése
A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. Egy fürt részét képező gépet vagy virtuális gépet csomópontnak nevezzük. A fürtök akár több ezer csomópontot is tartalmazhatnak. Service Fabric-fürt létrehozása után vízszintesen méretezheti a fürtöt (a csomópontok számának módosítása) vagy függőlegesen (a csomópontok erőforrásainak módosítása).  A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön.  A fürt skálázása esetén az alkalmazások is automatikusan méretezhetők.

Miért érdemes méretezni a fürtöt? Az alkalmazás iránti igények időbeli változása.  Előfordulhat, hogy növelnie kell a fürt erőforrásait, hogy megfeleljen az alkalmazások megnövekedett munkaterhelésének vagy a hálózati forgalomnak, vagy csökkentenie kell a fürterőforrások mennyiségét.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Be-és kiskálázás, vagy horizontális skálázás
Megváltoztatja a fürt csomópontjainak számát.  Miután az új csomópontok csatlakoznak a fürthöz, a [fürterőforrás-kezelő](service-fabric-cluster-resource-manager-introduction.md) áthelyezi a szolgáltatásokat, ami csökkenti a meglévő csomópontok terhelését.  Csökkentheti a csomópontok számát is, ha a fürt erőforrásait nem hatékonyan használják.  Mivel a csomópontok elhagyják a fürtöt, a szolgáltatások elmozdulnak a csomópontokon, és a többi csomóponton is növekednek.  Az Azure-ban futó fürtökben lévő csomópontok számának csökkentése révén pénzt takaríthat meg, mivel a felhasznált virtuális gépek számának és a virtuális gépek munkaterhelésének a megfizetését kell fizetnie.  

- Előnyök: végtelen skála, elméletileg.  Ha az alkalmazás méretezhetőségre van tervezve, további csomópontok hozzáadásával engedélyezheti a korlátlan növekedést.  A felhőalapú környezetekben az eszközök megkönnyítik a csomópontok hozzáadását és eltávolítását, így egyszerűen beállíthatja a kapacitást, és csak a felhasznált erőforrásokért kell fizetnie.  
- Hátrányok: az alkalmazásokat [a méretezhetőség érdekében kell tervezni](service-fabric-concepts-scalability.md).  Az alkalmazás adatbázisai és az adatmegőrzés további építészeti munkát is igényelhet a méretezéshez.  Service Fabric állapot-nyilvántartó szolgáltatások [megbízható gyűjteményei](service-fabric-reliable-services-reliable-collections.md) azonban sokkal egyszerűbbé teszik az alkalmazásadatok méretezését.

A virtuálisgép-méretezési csoportok egy olyan Azure-beli számítási erőforrás, amellyel készletként telepítheti és kezelheti a virtuális gépek gyűjteményét. Az Azure-fürtben definiált összes csomópont-típus [külön méretezési csoportként van beállítva](service-fabric-cluster-nodetypes.md). Ezután az egyes csomópont-típusok egymástól függetlenül méretezhetők, különböző portokat nyitnak meg, és rendelkezhetnek különböző kapacitási metrikákkal. 

Egy Azure-fürt skálázásakor tartsa szem előtt a következő irányelveket:
- az éles munkaterheléseket futtató elsődleges csomópontok típusának mindig legalább öt csomóponttal kell rendelkeznie.
- az állapot-nyilvántartó munkaterheléseket futtató nem elsődleges csomópontok esetében mindig legalább öt csomópontnak kell futnia.
- az állapot nélküli éles környezetben futó munkaterheléseket futtató nem elsődleges csomópontok esetében mindig legalább két csomópontnak kell futnia.
- Az arany vagy ezüst [tartóssági szintjének](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) minden csomópont-típusának mindig öt vagy több csomóponttal kell rendelkeznie.
- Ne távolítsa el a véletlenszerű virtuálisgép-példányokat/csomópontokat a csomópont típusától, mindig használja a virtuálisgép-méretezési csoport méretezését a szolgáltatásban. A véletlenszerű virtuálisgép-példányok törlése hátrányosan befolyásolhatja a rendszerek megfelelő terheléselosztásának képességét.
- Ha automatikus skálázási szabályokat használ, állítsa be a szabályokat úgy, hogy a (virtuálisgép-példányok eltávolítása) méretezése egyszerre egy csomóponton történjen. Egyszerre legfeljebb egy példány skálázása nem biztonságos.

Mivel a fürtben lévő Service Fabric csomópontok a háttérbeli virtuálisgép-méretezési csoportokból állnak, beállíthatja az [automatikus méretezési szabályokat, vagy manuálisan méretezheti](service-fabric-cluster-scale-in-out.md) az egyes csomópontok típusát/virtuálisgép-méretezési csoportját.

### <a name="programmatic-scaling"></a>Programozott méretezés
Számos esetben a [fürt manuális méretezése vagy az automatikus skálázási szabályok](service-fabric-cluster-scale-in-out.md) használata jó megoldás. A speciális forgatókönyvek esetében azonban előfordulhat, hogy nem a megfelelő illeszkedés. A következő megközelítések lehetséges hátrányai a következők:

- A manuális skálázáshoz be kell jelentkeznie, és explicit módon kell lekérnie a skálázási műveleteket. Ha a skálázási műveletek gyakran vagy előre nem látható időpontokban is szükségesek, akkor ez a megközelítés nem jó megoldás.
- Ha az automatikus skálázási szabályok eltávolítanak egy példányt egy virtuálisgép-méretezési csoportból, a csomópont nem távolítja el automatikusan a kapcsolódó Service Fabric fürt ismereteit, kivéve, ha a csomópont típusa nem rendelkezik ezüst vagy arany tartóssági szinttel. Mivel az automatikus skálázási szabályok a méretezési csoport szintjén működnek (nem a Service Fabric szinten), az automatikus skálázási szabályok el tudják távolítani Service Fabric csomópontokat anélkül, hogy a rendszer szabályosan leállítja őket. Ez a durva csomópont-Eltávolítás a "Ghost" Service Fabric a csomópont állapotát a skálázási műveletek után elhagyja. Egy személynek (vagy szolgáltatásnak) rendszeresen törölnie kell az eltávolított csomópont-állapotot a Service Fabric-fürtben.
- Az arany vagy ezüst tartóssági szinttel rendelkező csomópontok automatikusan törlik az eltávolított csomópontokat, így nincs szükség további tisztításra.
- Bár az automatikus skálázási szabályok [számos mérőszámot](../azure-monitor/autoscale/autoscale-common-metrics.md) támogatnak, még mindig korlátozott a készlet. Ha a forgatókönyv a készletben nem szereplő egyes mérőszámok alapján meghívja a méretezést, akkor előfordulhat, hogy az automatikus skálázási szabályok nem megfelelőek.

A Service Fabric skálázás megközelítése a forgatókönyvtől függ. Ha a skálázás nem gyakori, a csomópontok manuális hozzáadása vagy eltávolítása valószínűleg elegendő. Az összetettebb forgatókönyvek esetében az automatikus méretezési szabályok és SDK-k lehetővé teszik a programozott, hatékony alternatívák méretezését.

Léteznek olyan Azure API-k, amelyek lehetővé teszik, hogy az alkalmazások programozott módon működjenek együtt a virtuálisgép-méretezési csoportokkal és a Service Fabric fürtökkel. Ha a meglévő automatikus Méretezési lehetőségek nem működnek a forgatókönyvben, ezek az API-k lehetővé teszik az egyéni skálázási logika megvalósítását. 

A "Home-Made" automatikus skálázási funkció megvalósításának egyik módja, ha új állapot nélküli szolgáltatást ad hozzá a Service Fabric alkalmazáshoz a skálázási műveletek kezeléséhez. A saját méretezési szolgáltatás létrehozása biztosítja a legmagasabb fokú irányítást és testreszabhatóság az alkalmazás skálázási viselkedése során. Ez olyan helyzetekben lehet hasznos, amikor pontosan meg kell határozni, hogy mikor vagy hogyan méretezi be vagy ki egy adott alkalmazást. Ez a vezérlő azonban a kód bonyolultságának kompromisszumával jár. Ennek a módszernek a használata azt jelenti, hogy saját méretezési kódot kell használnia, amely nem triviális. A szolgáltatás `RunAsync` metódusán belül az eseményindítók készlete meghatározhatja, hogy szükség van-e a skálázásra (beleértve az olyan paraméterek ellenőrzését is, mint például a fürt maximális mérete és a hűtők skálázása).   

A virtuálisgép-méretezési csoportokkal való interakcióhoz használt API (mindkettő a virtuálisgép-példányok aktuális számának és módosításának vizsgálatához) a [Fluent Azure felügyeleti számítási függvénytár](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). A Fluent számítási függvénytár egy könnyen használható API-t biztosít a virtuálisgép-méretezési csoportokkal való interakcióhoz.  Ha magát a Service Fabric-fürtöt szeretné használni, használja a [System. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient).

A skálázási kódnak azonban nem kell a fürtben lévő szolgáltatásként futtatnia a méretezést. Mindkettő `IAzure` `FabricClient` a kapcsolódó Azure-erőforrásokhoz távolról is kapcsolódhat, így a skálázási szolgáltatás könnyen lehet egy, a Service Fabric alkalmazáson kívül futó konzol-alkalmazás vagy Windows-szolgáltatás.

Ezen korlátozások alapján érdemesebb a [testre szabott automatikus skálázási modelleket megvalósítani](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Felfelé és lefelé skálázás, vagy vertikális skálázás 
Módosítja a fürt csomópontjainak erőforrásait (processzor, memória vagy tároló).
- Előnyök: a szoftverek és az alkalmazások architektúrája ugyanaz marad.
- Hátrányok: véges skála, mivel korlátozva van, hogy mennyit növelheti az erőforrások az egyes csomópontokon. A leállás miatt a fizikai vagy virtuális gépeket offline állapotba kell helyezni, hogy erőforrásokat lehessen hozzáadni vagy eltávolítani.

A virtuálisgép-méretezési csoportok egy olyan Azure-beli számítási erőforrás, amellyel készletként telepítheti és kezelheti a virtuális gépek gyűjteményét. Az Azure-fürtben definiált összes csomópont-típus [külön méretezési csoportként van beállítva](service-fabric-cluster-nodetypes.md). Ezután mindegyik csomópont-típust külön lehet kezelni.  A csomópontok típusának felfelé vagy lefelé történő skálázása új csomópont-típus (frissített VM SKU) hozzáadásával és a régi csomópont típusának eltávolításával jár.

Egy Azure-fürt skálázásakor tartsa szem előtt az alábbi útmutatást:
- Az elsődleges csomópontok típusának leskálázása esetén soha ne méretezheti azt tovább, mint ami a [megbízhatósági szintet](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) lehetővé teszi.

A csomópont-típusok felfelé vagy lefelé történő skálázásának folyamata eltérő attól függően, hogy nem elsődleges vagy elsődleges csomópont típusú-e.

### <a name="scaling-non-primary-node-types"></a>Nem elsődleges csomópontok típusának skálázása
Hozzon létre egy új csomópont-típust a szükséges erőforrásokkal.  Frissítse a futó szolgáltatások elhelyezési korlátozásait, hogy tartalmazza az új csomópont típusát.  Fokozatosan (egy alkalommal) csökkentse a régi csomópont típusú példányok példányszámát nulla értékre, hogy a fürt megbízhatósága ne legyen hatással.  A szolgáltatások fokozatosan lesznek áttelepítve az új csomópont típusára, mivel a régi csomópont-típust leszerelték.

### <a name="scaling-the-primary-node-type"></a>Az elsődleges csomópont típusának skálázása
Helyezzen üzembe egy új elsődleges csomópont-típust a frissített VM SKU-val, majd tiltsa le az eredeti elsődleges csomópont típusú példányokat egy időben, hogy a rendszerszolgáltatások áttérjenek az új méretezési csoportra. Ellenőrizze, hogy a fürt és az új csomópontok állapota Kifogástalan-e, majd távolítsa el a törölt csomópontok eredeti méretezési csoportját és csomópontjának állapotát.

Ha ez nem lehetséges, létrehozhat egy új fürtöt, és [visszaállíthatja az alkalmazás állapotát](service-fabric-reliable-services-backup-restore.md) (ha van ilyen) a régi fürtből. Nincs szükség a rendszerszolgáltatások állapotának visszaállítására, ezeket a rendszer újból létrehozza, amikor telepíti az alkalmazásokat az új fürtre. Ha csak az állapot nélküli alkalmazásokat futtatta a fürtön, akkor csak az alkalmazásait telepíti az új fürtre, nincs szükség a visszaállításra.

## <a name="next-steps"></a>Következő lépések
* Az [alkalmazások méretezhetőségének](service-fabric-concepts-scalability.md)megismerése.
* [Azure-fürt méretezése vagy](service-fabric-tutorial-scale-cluster.md)kibontása.
* Az [Azure-fürtöket programozott módon méretezheti](service-fabric-cluster-programmatic-scaling.md) a Fluent Azure számítási SDK használatával.
* [Önálló fürt méretezése vagy](service-fabric-cluster-windows-server-add-remove-nodes.md)kibontása.

