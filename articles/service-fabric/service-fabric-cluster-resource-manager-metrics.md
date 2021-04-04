---
title: Azure Service Fabric-alkalmazások terhelésének kezelése mérőszámok használatával
description: Ismerje meg, hogyan konfigurálhatja és használhatja a Service Fabric mérőszámait a szolgáltatások erőforrás-felhasználásának kezeléséhez.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a7dedea2937c9cafb4216da3628aa1360ad6993
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92173011"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Az erőforrás-felhasználás és a terhelések kezelése a metrikákkal Service Fabric
A *metrikák* azok az erőforrások, amelyeket a szolgáltatásai törődnek, és amelyeket a fürt csomópontjai biztosítanak. A mérőszám a szolgáltatások teljesítményének javításához vagy figyeléséhez szükséges. Megtekintheti például, hogy a szolgáltatás túlterhelt-e a memóriában. Egy másik lehetőség, hogy kiderítse, hogy a szolgáltatás máshol is mozog-e, ahol a memória kevésbé korlátozott a jobb teljesítmény érdekében.

A memória, a lemez és a CPU-használat például mérőszámokra mutat. Ezek a mérőszámok fizikai mérőszámok, a csomóponton található fizikai erőforrásoknak megfelelő erőforrások, amelyeket kezelni kell. A metrikák is lehetnek (és gyakran) logikai metrikák. A logikai mérőszámok olyan dolgok, mint a "MyWorkQueueDepth" vagy a "MessagesToProcess" vagy a "TotalRecords". A logikai mérőszámok az alkalmazás által definiált és közvetetten egy fizikai erőforrás-felhasználásnak felelnek meg. A logikai mérőszámok gyakoriak, mert nehéz lehet mérni és jelenteni a fizikai erőforrások felhasználását egy szolgáltatás alapján. A saját fizikai mérőszámok mérésének és jelentésének összetettsége azt is jelenti, hogy Service Fabric biztosít néhány alapértelmezett metrikát.

## <a name="default-metrics"></a>Alapértelmezett metrikák
Tegyük fel, hogy meg szeretné kezdeni a szolgáltatás írását és üzembe helyezését. Ezen a ponton nem tudja, hogy milyen fizikai vagy logikai erőforrások használják. Ez rendben van! A Service Fabric fürterőforrás-kezelő néhány alapértelmezett metrikát használ, ha nincs megadva más metrika. Ezek a következők:

  - PrimaryCount – a csomóponton lévő elsődleges replikák száma 
  - ReplicaCount – a csomóponton található állapot-nyilvántartó replikák száma
  - Az összes szolgáltatási objektum (állapot nélküli és állapot-nyilvántartó) száma a csomóponton

| Metric | Állapot nélküli példányok betöltése | Állapot-nyilvántartó másodlagos terhelés | Állapot-nyilvántartó elsődleges terhelés | Tömeg |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Magas |
| ReplicaCount |0 |1 |1 |Közepes |
| Darabszám |1 |1 |1 |Alacsony |


Az alapszintű számítási feladatok esetében az alapértelmezett mérőszámok biztosítják a feladatok tisztességes elosztását a fürtben. Az alábbi példában lássuk, mi történik, ha két szolgáltatást hozunk létre, és az alapértelmezett mérőszámokra támaszkodunk. Az első szolgáltatás egy állapot-nyilvántartó szolgáltatás, amely három partícióval és egy célként megadott replika három mérettel rendelkezik. A második szolgáltatás egy állapot nélküli szolgáltatás, amely egy partícióval és három példányszámmal rendelkezik.

A következő eredményt kapjuk:

<center>

![Fürt elrendezése alapértelmezett metrikákkal][Image1]
</center>

Ügyeljen a következőkre:
  - Az állapot-nyilvántartó szolgáltatás elsődleges replikái több csomópont között oszlanak meg
  - Ugyanahhoz a partícióhoz tartozó replikák különböző csomópontokon találhatók
  - Az elsődleges és a formátumú másodlagos zónák teljes száma a fürtben van elosztva
  - A szolgáltatási objektumok teljes száma egyenletesen van lefoglalva az egyes csomópontokon

jó!

Az alapértelmezett mérőszámok az indításkor is remekül működnek. Az alapértelmezett mérőszámok azonban csak ennyi ideig lesznek végrehajtva. Például: mi a valószínűsége annak, hogy a kiválasztott particionáló séma tökéletesen egyenletes kihasználtságot eredményez az összes partíción? Mi a valószínűsége annak, hogy egy adott szolgáltatás terhelése az idő múlásával állandó, vagy akár több partíción is ugyanaz van?

A-t csak az alapértelmezett metrikákkal futtathatja. Azonban ez általában azt jelenti, hogy a fürt kihasználtsága alacsonyabb, és még ennél is rosszabb, mint szeretné. Ennek az az oka, hogy az alapértelmezett mérőszámok nem alkalmazkodnak, és feltételezik, hogy minden egyenértékű. Például egy foglalt elsődleges és egy, a PrimaryCount metrikához nem járul hozzá "1". A legrosszabb esetben, ha csak az alapértelmezett mérőszámokat használja, a rendszer a túlütemezett csomópontokat is eredményezi, ami teljesítménnyel kapcsolatos problémákat okoz. Ha szeretné, hogy a lehető legtöbbet hozza ki a fürtből, és elkerülje a teljesítménnyel kapcsolatos problémákat, egyéni metrikákat és dinamikus betöltési jelentéskészítést kell használnia.

## <a name="custom-metrics"></a>Egyéni metrikák
A metrikák a szolgáltatás létrehozásakor egy névvel ellátott, szolgáltatás-példány alapon vannak konfigurálva.

Bármely metrika tartalmaz néhány tulajdonságot, amely leírja azt: egy nevet, egy súlyozást és egy alapértelmezett betöltést.

* Metrika neve: a metrika neve. A metrika neve a fürtben lévő metrika egyedi azonosítója az erőforrás-kezelő szemszögéből.
* Súlyozás: a metrika súlya határozza meg, hogy ez a metrika milyen fontos a szolgáltatáshoz tartozó többi metrikához képest.
* Alapértelmezett betöltés: az alapértelmezett terhelés eltérő módon jelenik meg attól függően, hogy a szolgáltatás állapota vagy állapota nem megfelelő-e.
  * Az állapot nélküli szolgáltatások esetében minden metrika egyetlen, DefaultLoad nevű tulajdonsággal rendelkezik.
  * Az Ön által definiált állapot-nyilvántartó szolgáltatások esetében:
    * PrimaryDefaultLoad: a metrika alapértelmezett értéke, amelyet a szolgáltatás akkor használ, ha az elsődleges
    * SecondaryDefaultLoad: a metrika alapértelmezett értéke, ha a szolgáltatás másodlagosként használja

> [!NOTE]
> Ha egyéni metrikákat ad meg, és az alapértelmezett metrikákat _is_ használni szeretné, _explicit módon_ hozzá kell adnia az alapértelmezett metrikákat, és meg kell határoznia azok súlyozásait és értékeit. Ennek az az oka, hogy meg kell határoznia az alapértelmezett mérőszámok és az egyéni metrikák közötti kapcsolatot. Tegyük fel például, hogy a ConnectionCount vagy a WorkQueueDepth nagyobb mértékben érdekli az elsődleges eloszlás. Alapértelmezés szerint a PrimaryCount metrikájának súlya magas, ezért közepesre szeretné csökkenteni a többi mérőszámot, hogy azok elsőbbséget élvezzenek.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Mérőszámok definiálása a szolgáltatáshoz – példa
Tegyük fel, hogy a következő konfigurációt szeretné használni:

  - A szolgáltatás jelentést készít a "ConnectionCount" nevű metrikáról
  - Az alapértelmezett mérőszámokat is használni szeretné 
  - Elvégezte néhány mérést, és tudja, hogy az adott szolgáltatás elsődleges replikája 20 egységet vesz igénybe a "ConnectionCount"
  - A formátumú másodlagos zónák a "ConnectionCount" 5 egységét használja
  - Tudja, hogy az adott szolgáltatás teljesítményének kezelése szempontjából a legfontosabb mérőszám az "ConnectionCount".
  - Továbbra is érdemes az elsődleges replikákat kiegyensúlyozottan használni. Az elsődleges replikák kiegyensúlyozása általában jó ötlet, függetlenül attól, hogy mi. Ez segít megakadályozni, hogy egyes csomópontok vagy tartalék tartományok elveszítsék az elsődleges replikák többségét. 
  - Ellenkező esetben az alapértelmezett mérőszámok rendben vannak

Az alábbi kód azt írja le, hogy az adott metrikai konfigurációval hozzon létre egy szolgáltatást:

Kód:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> A fenti példák és a jelen dokumentum többi része leírja a metrikák felügyeletét egy névvel ellátott szolgáltatás alapján. A szolgáltatások mérőszámait a szolgáltatás _típusa_ szintjén is meghatározhatja. Ennek megvalósításához meg kell adni őket a szolgáltatás jegyzékfájljában. A típus szintű mérőszámok meghatározása több okból nem ajánlott. Ennek első oka, hogy a metrikák nevei gyakran a környezetre jellemzőek. Ha nincs határozott szerződés, nem biztos benne, hogy az egyik környezetben lévő "magok" mérőszám nem "MiliCores" vagy "magok". Ha a metrikák definiálva vannak a jegyzékfájlban, új jegyzékfájlokat kell létrehoznia egy adott környezetben. Ez általában olyan különböző jegyzékfájlok elterjedését eredményezi, amelyek csak kisebb eltérésekkel járnak, ami felügyeleti problémákhoz vezethet.  
>
> A metrikák terhelését általában egy névvel ellátott szolgáltatás-példány alapján kell hozzárendelni. Tegyük fel például, hogy a szolgáltatás egy példányát hozza létre a Customera számára, aki csak kis mennyiségű használatot tervez. Azt is tegyük fel, hogy létrehoz egy másikat a nagyobb számítási feladattal rendelkező CustomerB. Ebben az esetben valószínűleg a szolgáltatások alapértelmezett terhelését szeretné felhangolni. Ha olyan mérőszámokkal és terhelésekkel rendelkezik, amelyek a jegyzékfájlok segítségével vannak meghatározva, és támogatni szeretné ezt a forgatókönyvet, az egyes ügyfelekhez különböző alkalmazás-és szolgáltatási típusok szükségesek. A szolgáltatás-létrehozási időpontban definiált értékek felülbírálják a jegyzékfájlban definiált értékeket, így az adott alapértelmezett beállítások megadására is használható. Ez azonban azt eredményezi, hogy a jegyzékfájlokban deklarált értékek nem egyeznek meg a szolgáltatás által ténylegesen futtatott értékekkel. Ez zavart okozhat. 
>

Emlékeztető: Ha csak az alapértelmezett mérőszámokat szeretné használni, nem kell többé megérintenie a metrikák gyűjteményét, vagy bármit külön kell tennie a szolgáltatás létrehozásakor. Az alapértelmezett mérőszámok automatikusan használatban lesznek, ha nincs más definiálva. 

Most nézzük át ezeket a beállításokat részletesebben, és beszéljünk az általa befolyásolt viselkedésről.

## <a name="load"></a>Betöltés
A metrikák definiálásának lényege, hogy bizonyos terhelést képviselnek. A *betöltési* érték azt adja meg, hogy egy adott metrika mekkora részét használja egy adott csomópont egyes szolgáltatási példányai vagy replikái. A betöltés szinte bármilyen ponton konfigurálható. Például:

  - A terhelést a szolgáltatás létrehozásakor lehet meghatározni. Ezt a típusú betöltési konfigurációt _alapértelmezett betöltésnek_ nevezzük.
  - A szolgáltatáshoz tartozó metrikai információk, beleértve az alapértelmezett terheléseket is, a szolgáltatás létrehozása után frissíthetők. Ez a metrikák frissítése _egy szolgáltatás frissítésével_ történik.
  - Egy adott partíció terhelése visszaállítható a szolgáltatás alapértelmezett értékeire. Ezt a metrikát a _partíció betöltésének visszaállítására_ hívják.
  - A betöltést szolgáltatási objektum alapján, dinamikusan, futásidőben lehet jelenteni. Ezt a metrikai frissítést _jelentési betöltésnek_ nevezzük.
  - A partíció replikáinak vagy példányainak betöltését a Fabric API-híváson keresztül a betöltési értékek jelentésével is frissítheti. Ezt a metrikai frissítést _egy partíció jelentési terhelésének_ nevezzük.

Az összes ilyen stratégia az adott szolgáltatáson belül is felhasználható élettartama alatt.

## <a name="default-load"></a>Alapértelmezett betöltés
Az *alapértelmezett terhelés* azt adja meg, hogy az egyes szolgáltatási objektumok (a szolgáltatás állapot nélküli példányai vagy állapot-nyilvántartó replikái) milyen mérőszámot használnak. A fürterőforrás-kezelő ezt a számot használja a szolgáltatási objektum terheléséhez, amíg más adatokat nem kap, például dinamikus betöltési jelentést. Az egyszerűbb szolgáltatások esetében az alapértelmezett terhelés statikus definíció. Az alapértelmezett betöltés soha nem frissül, és a szolgáltatás élettartamára szolgál. Az alapértelmezett terhelések az egyszerű kapacitás-tervezési forgatókönyvek esetében remekül működnek, ahol bizonyos mennyiségű erőforrás a különböző számítási feladatokhoz van hozzárendelve, és nem változik.

> [!NOTE]
> A kapacitások kezelésével és a fürt csomópontjainak kapacitásának meghatározásával kapcsolatos további információkért tekintse meg [ezt a cikket](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

A fürterőforrás-kezelő lehetővé teszi, hogy az állapot-nyilvántartó szolgáltatások egy másik alapértelmezett terhelést adjanak ki az elsődleges és a formátumú másodlagos zónák. Az állapot nélküli szolgáltatások csak egyetlen értéket adhatnak meg, amely az összes példányra érvényes. Az állapot-nyilvántartó szolgáltatások esetében az elsődleges és a másodlagos replikák alapértelmezett terhelése általában eltérő, mivel a replikák különböző típusú munkát végeznek az egyes szerepkörökben. Az elsődlegesek például az olvasást és az írást is kiszolgálják, és a számítási terhek többségét kezelik, míg a formátumú másodlagos zónák nem. Az elsődleges replika alapértelmezett terhelése általában magasabb, mint a másodlagos replikák alapértelmezett terhelése. A valós számok a saját mértéktől függenek.

## <a name="dynamic-load"></a>Dinamikus betöltés
Tegyük fel, hogy egy ideig futtatta a szolgáltatást. Némi figyeléssel észrevette, hogy:

1. Egy adott szolgáltatás egyes partíciói vagy példányai több erőforrást használnak, mint mások
2. Egyes szolgáltatások olyan terheléssel rendelkeznek, amely az idő múlásával változhat.

Számos dolog lehet, ami az ilyen típusú terhelések ingadozását okozhatja. A különböző szolgáltatások vagy partíciók például különböző ügyfelekkel vannak társítva, eltérő követelményekkel. A Load is változhat, mert a szolgáltatás által végzett munka mennyisége a nap folyamán változik. Az ok miatt általában nincs egyetlen szám, amelyet alapértelmezettként használhat. Ez különösen akkor igaz, ha ki szeretné próbálni a legtöbbet a fürtből. Az alapértelmezett betöltéshez kiválasztott értékek közül néhányat nem megfelelőnek tekint. Helytelenek az alapértelmezett betöltések a fürterőforrás-kezelőben vagy az erőforrások lefoglalása során. Ennek eredményeképpen olyan csomópontok vannak, amelyek túl vannak vagy a kihasználtság alatt vannak, bár a fürterőforrás-kezelő úgy gondolja, hogy a fürt egyensúlyban van. Az alapértelmezett betöltések még mindig jók, mivel a kezdeti elhelyezéshez bizonyos információkat biztosítanak, de nem teljes körűek a valós számítási feladatok elvégzéséhez. Az erőforrás-követelmények módosításának pontos rögzítése érdekében a fürterőforrás-kezelő lehetővé teszi, hogy az egyes szolgáltatási objektumok saját terhelést frissítsenek a futtatókörnyezet során. Ezt nevezzük dinamikus betöltési jelentéskészítésnek.

A dinamikus betöltési jelentések lehetővé teszik a replikák vagy példányok számára, hogy az élettartamuk során módosítsák a kiosztott/jelentett terhelést. Egy olyan szolgáltatás-replika vagy-példány, amely hideg volt, és nem végez munkát, általában azt jelenti, hogy az adott metrika alacsony mennyiségét használta. Egy foglalt replika vagy példány azt jelenti, hogy több használatban van.

A jelentési betöltés replikán vagy példányon lehetővé teszi, hogy a fürterőforrás-kezelő átszervezzék a fürtben lévő egyes szolgáltatási objektumokat. A szolgáltatások átrendezése segít biztosítani, hogy megkapják a számukra szükséges erőforrásokat. A foglalt szolgáltatások hatékonyan hozzáférhetnek más replikák vagy olyan példányok erőforrásairól, amelyek jelenleg nem működnek, vagy amelyek kevésbé működnek.

Reliable Services belül a betöltésre szolgáló kód dinamikusan a következőképpen néz ki:

Kód:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

A szolgáltatás a létrehozáskor meghatározott mérőszámok bármelyikén jelentést tud készíteni. Ha a szolgáltatás egy olyan metrika betöltését jelenti, amelyet nincs konfigurálva a használatára, Service Fabric figyelmen kívül hagyja ezt a jelentést. Ha más mérőszámok is érvényesek, akkor ezek a jelentések elfogadva lesznek. A szolgáltatási kód mérhetővé és jelentést készít az összes, az általa ismert mérőszámról, és a kezelők a szolgáltatás kódjának módosítása nélkül is meghatározhatják a használni kívánt metrikai konfigurációt. 

## <a name="reporting-load-for-a-partition"></a>Partíciók jelentési terhelése
Az előző szakasz azt ismerteti, hogyan töltődnek be a szolgáltatás-replikák vagy-példányok jelentése. További lehetőség a betöltés dinamikus jelentésére a FabricClient használatával. Egy partíció terhelésének jelentésekor egyszerre több partíciót is jelenteni lehet.

Ezeket a jelentéseket pontosan ugyanúgy fogjuk használni, mint a replikák vagy a példányokból érkező betöltési jelentéseket. A jelentett értékek érvényesek lesznek, amíg az új betöltési értékeket nem jelentik a replika vagy a példány, vagy egy partíció új betöltési értékének bejelentésével.

Ezzel az API-val több módon is frissítheti a fürt betöltését:

  - Az állapot-nyilvántartó szolgáltatás partíciója frissítheti az elsődleges replika terhelését.
  - Az állapot nélküli és az állapot-nyilvántartó szolgáltatások is frissíthetik a másodlagos replikák vagy példányok terhelését.
  - Az állapot nélküli és az állapot-nyilvántartó szolgáltatások is frissíthetik egy adott replika vagy példány terhelését egy csomóponton.

Az is lehetséges, hogy a frissítéseket egy adott időpontban egyszerre kell kombinálni.

Egy API-hívással több partíció frissítése is lehetséges, ebben az esetben a kimenet egy partíción alapuló választ fog tartalmazni. Abban az esetben, ha a partíció frissítését valamilyen okból nem sikerült alkalmazni, a rendszer kihagyja az adott partíció frissítéseit, és megadja a megfelelő hibakódot a célként megadott partícióhoz:

  - PartitionNotFound – a megadott partíció-azonosító nem létezik.
  - ReconfigurationPending – a partíció jelenleg újra van konfigurálva.
  - InvalidForStatelessServices – kísérlet történt egy állapot nélküli szolgáltatáshoz tartozó partíció elsődleges replikájának betöltésének módosítására.
  - ReplicaDoesNotExist – a másodlagos replika vagy példány nem létezik a megadott csomóponton.
  - InvalidOperation – két esetben fordulhat elő: a rendszeralkalmazáshoz tartozó vagy az előre jelzett terhelést nem tartalmazó partíciók terhelésének frissítése nincs engedélyezve.

Ha a visszaadott hibák némelyikét visszaadja, akkor frissítheti egy adott partíció bemenetét, és megpróbálkozhat egy adott partíció frissítésével.

Kód:

```csharp
Guid partitionId = Guid.Parse("53df3d7f-5471-403b-b736-bde6ad584f42");
string metricName0 = "CustomMetricName0";
List<MetricLoadDescription> newPrimaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 100)
};

string nodeName0 = "NodeName0";
List<MetricLoadDescription> newSpecificSecondaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 200)
};

OperationResult<UpdatePartitionLoadResultList> updatePartitionLoadResults =
    await this.FabricClient.UpdatePartitionLoadAsync(
        new UpdatePartitionLoadQueryDescription
        {
            PartitionMetricLoadDescriptionList = new List<PartitionMetricLoadDescription>()
            {
                new PartitionMetricLoadDescription(
                    partitionId,
                    newPrimaryReplicaLoads,
                    new List<MetricLoadDescription>(),
                    new List<ReplicaMetricLoadDescription>()
                    {
                        new ReplicaMetricLoadDescription(nodeName0, newSpecificSecondaryReplicaLoads)
                    })
            }
        },
        this.Timeout,
        cancellationToken);
```

Ebben a példában egy partíció _53df3d7f-5471-403b-b736-bde6ad584f42_ tartozó utolsó jelentett terhelés frissítését fogja végrehajtani. A metrikus _CustomMetricName0_ elsődleges replikájának terhelése a 100 értékkel lesz frissítve. Ugyanakkor a csomópont _NodeName0_ található, egy adott másodlagos replika esetében az azonos metrikához tartozó terhelést a 200 értékkel frissíti a rendszer.

### <a name="updating-a-services-metric-configuration"></a>A szolgáltatás metrika-konfigurációjának frissítése
A szolgáltatáshoz társított mérőszámok listája és a metrikák tulajdonságai dinamikusan frissíthetők a szolgáltatás élő állapotában. Ez lehetővé teszi a kísérletezést és a rugalmasságot. Néhány példa, ha ez hasznos:

  - egy adott szolgáltatáshoz tartozó hibás jelentéssel rendelkező metrika letiltása
  - a mérőszámok súlyozásának újrakonfigurálása a kívánt viselkedés alapján
  - új metrika engedélyezése csak azt követően, hogy a kód már üzembe lett helyezve és érvényesítve más mechanizmusokon keresztül
  - egy szolgáltatás alapértelmezett terhelésének módosítása a megfigyelt viselkedés és a felhasználás alapján

A metrikák konfigurációjának módosítására szolgáló fő API `FabricClient.ServiceManagementClient.UpdateServiceAsync` -k a C# és a PowerShell-ben találhatók `Update-ServiceFabricService` . Az ezekkel az API-kkal megadott információk azonnal lecserélik a szolgáltatás meglévő metrikai információit. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Alapértelmezett betöltési értékek és dinamikus betöltési jelentések keverése
Az alapértelmezett terhelés és a dinamikus terhelések is használhatók ugyanahhoz a szolgáltatáshoz. Ha egy szolgáltatás az alapértelmezett betöltési és dinamikus betöltési jelentéseket is használja, az alapértelmezett terhelés becslésként szolgál a dinamikus jelentések megjelenítése előtt. Az alapértelmezett betöltés jó, mert a fürterőforrás-kezelőhöz való munkavégzést biztosít. Az alapértelmezett betöltés lehetővé teszi, hogy a fürterőforrás-kezelő a létrehozáskor megfelelő helyen helyezze el a szolgáltatási objektumokat. Ha nincs megadva alapértelmezett betöltési információ, a szolgáltatások elhelyezése gyakorlatilag véletlenszerű. Ha a betöltési jelentések később érkeznek, a kezdeti véletlenszerű elhelyezés gyakran helytelen, és a fürterőforrás-kezelőnek át kell helyeznie a szolgáltatásokat.

Vegyük át az előző példát, és nézzük meg, mi történik, ha hozzáadunk néhány egyéni metrikát és dinamikus betöltési jelentést. Ebben a példában a "MemoryInMb" kifejezést használjuk példaként mérőszámként.

> [!NOTE]
> A memória az a rendszermetrikák egyike, amely Service Fabric az [erőforrás-szabályozást](service-fabric-resource-governance.md), és a jelentés készítése általában nehéz feladat. Valójában nem várjuk, hogy jelentést készítsen a memória használatáról; A memóriát itt találja a fürterőforrás-kezelő képességeinek megismerésére szolgáló támogatásként.
>

Tételezzük fel, hogy kezdetben a következő paranccsal hoztuk létre az állapot-nyilvántartó szolgáltatást:

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Emlékeztetőként ez a szintaxis a következő: ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Lássuk, hogyan nézhet ki egy lehetséges fürtcsomópont:

<center>

![Fürt kiegyensúlyozott, alapértelmezett és egyéni metrikákkal][Image2]
</center>

Néhány dolog, amit érdemes megjegyezni:

* A partíción belüli másodlagos replikák rendelkezhetnek saját terheléssel
* Összességében a mérőszámok kiegyensúlyozottnak tűnnek. A memória esetében a maximális és minimális terhelés közötti arány 1,75 (a legtöbb terheléssel rendelkező csomópont N3, a legkevésbé az N2 és a 28/16 = 1,75).

Még néhány dolgot meg kell magyarázni:

* Mi határozta meg, hogy a 1,75-os arány ésszerű vagy sem? Honnan tudhatja, hogy a fürterőforrás-kezelő tudja-e, hogy elég jó-e, vagy hogy van-e több tennivaló?
* Mikor történik a kiegyensúlyozás?
* Mit jelent az, hogy a memória súlya "magas"?

## <a name="metric-weights"></a>Metrikus súlyok
A különböző szolgáltatások által használt mérőszámok nyomon követése fontos. Ez a globális nézet lehetővé teszi, hogy a fürterőforrás-kezelő nyomon követhesse a fürtben való használatot, kiegyenlítse a felhasználást a csomópontok között, és gondoskodjon arról, hogy a csomópontok ne lépjék át Azonban előfordulhat, hogy a szolgáltatások eltérő nézetekkel rendelkeznek, mint az azonos metrika fontossága. Emellett a sok mérőszámot és sok szolgáltatást tartalmazó fürtben nem léteznek tökéletesen kiegyensúlyozott megoldások az összes metrika esetében. Hogyan kezeli ezeket a helyzeteket a fürterőforrás-kezelő?

A metrikai súlyok lehetővé teszik a fürterőforrás-kezelő számára, hogy eldöntse, hogyan kell kiegyenlíteni a fürtöt, ha nincs tökéletes válasz. A metrikák súlyozása azt is lehetővé teszi, hogy a fürterőforrás-kezelő eltérő szolgáltatásokat egyenlít ki. A metrikák négy különböző súlyozási szinten lehetnek: nulla, alacsony, közepes és magas. A nulla súlyú mérőszámok nem járulnak hozzá, ha figyelembe vesszük, hogy a dolgok kiegyensúlyozottak-e. A terhelés azonban továbbra is hozzájárul a kapacitások kezeléséhez. A nulla súlyozású mérőszámok továbbra is hasznosak, és gyakran használják a szolgáltatás működésének és a teljesítmény monitorozásának részeként. [Ez a cikk](service-fabric-diagnostics-event-generation-infra.md) további információkat nyújt a szolgáltatások monitorozásához és diagnosztizálásához használt mérőszámok használatáról. 

A különböző metrikák súlyozásának valódi hatása a fürtben az, hogy a fürterőforrás-kezelő különböző megoldásokat hoz létre. A metrikus súlyok közlik a fürterőforrás-kezelővel, hogy bizonyos mérőszámok fontosabbak a többinél. Ha nincs tökéletes megoldás, a fürterőforrás-kezelő olyan megoldásokat részesít előnyben, amelyek jobban kiegyensúlyozzák a magasabb súlyozású mérőszámokat. Ha egy szolgáltatás azt gondolja, hogy egy adott metrika nem fontos, akkor az adott metrika kiegyensúlyozatlan használatát tapasztalhatja. Ez lehetővé teszi, hogy egy másik szolgáltatás még a fontos mérőszámok eloszlását is beolvassa.

Lássunk egy példát néhány betöltési jelentésre, és azt, hogy a különböző metrikai súlyozások hogyan eredményeznek különböző foglalásokat a fürtben. Ebben a példában azt láthatjuk, hogy a metrikák relatív súlyozásának váltásakor a fürterőforrás-kezelő különböző szolgáltatási szabályokat hoz létre.

<center>

![A mérőszám súlyozásának példája és a megoldásokra gyakorolt hatása][Image3]
</center>

Ebben a példában négy különböző szolgáltatás létezik, amelyek mindegyike különböző értékeket jelent két különböző metrika, metrika és MetricB esetében. Egy esetben az összes szolgáltatás határozza meg a metrikát a legfontosabb (súlyozás = magas) és a MetricB (súly = alacsony). Ennek eredményeképpen láthatjuk, hogy a fürterőforrás-kezelő úgy helyezi el a szolgáltatásokat, hogy a metrika jobban kiegyensúlyozott legyen, mint a MetricB. A "jobb kiegyensúlyozott" kifejezés azt jelenti, hogy a metrika alacsonyabb szórású, mint a MetricB. A második esetben fordítottuk a metrikák súlyozását. Ennek eredményeképpen a fürterőforrás-kezelő felcseréli az A és A B szolgáltatást, hogy olyan foglalást dolgozzon ki, amelyben a MetricB jobban kiegyensúlyozott, mint a metrikus.

> [!NOTE]
> A metrikai súlyok határozzák meg, hogy a fürterőforrás-kezelő hogyan kerüljön egyensúlyba, de nem, ha az egyensúly megtörténne. A kiegyensúlyozással kapcsolatos további információkért tekintse meg [ezt a cikket](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globális mérőszámok súlyozása
Tegyük fel, hogy a Servicea nagy súlyú mérőszámot határoz meg, a ServiceB pedig alacsony vagy nulla értékűre állítja a metrikát. Mi a tényleges súlyozás, amely a használat során ér véget?

Az összes metrika esetében több súlyozás is nyomon követhető. Az első súlyozás a szolgáltatás létrehozásakor a metrika számára meghatározott. A másik súlyozás egy globális súlyozás, amelyet a rendszer automatikusan kiszámít. A fürterőforrás-kezelő mindkét súlyozást használja a pontozási megoldásoknál. A súlyok figyelembe vétele fontos. Ez lehetővé teszi, hogy a fürterőforrás-kezelő a saját prioritásainak megfelelően kiegyenlítse az egyes szolgáltatásokat, és gondoskodjon arról, hogy a fürt teljes kiosztása megfelelő legyen.

Mi történne, ha a fürterőforrás-kezelő nem törődik a globális és a helyi egyenleggel? Nos, könnyen létrehozhat globálisan kiegyensúlyozott megoldásokat, azonban az egyes szolgáltatások esetében az erőforrás-egyensúlyt is okoz. Az alábbi példában egy csak az alapértelmezett metrikákkal konfigurált szolgáltatást nézzük meg, és nézzük meg, mi történik, ha a rendszer csak a globális egyenleget veszi figyelembe:

<center>

![Egy globális megoldás hatása][Image4]
</center>

A legfelső példában kizárólag globális egyensúlyon alapuló, a teljes fürt valóban kiegyensúlyozott. Az összes csomópont azonos számú elsődleges és azonos számú replikával rendelkezik. Ha azonban megtekinti a kiosztás tényleges hatását, nem annyira jó: a csomópontok elvesztése aránytalanul hatással van egy adott számítási feladatra, mivel az összes elsődleges adatát kiveszi. Ha például az első csomópont nem sikerül, akkor a kör-szolgáltatás három különböző partíciójának három alapadata is elvész. Ezzel szemben a Triangle és a Hexagon szolgáltatás partíciói elveszítik a replikát. Ez nem okoz fennakadást, mert nem kell helyreállítani a le-replikát.

Az alsó példában a fürterőforrás-kezelő a globális és a szolgáltatási egyenlegen alapuló elosztotta a replikákat. A megoldás pontszámának kiszámításakor a legnagyobb súlyt adja a globális megoldásnak, és egy (konfigurálható) részét az egyes szolgáltatásoknak. A metrika globális egyenlegét az egyes szolgáltatásokból származó metrikai súlyok átlaga alapján számítjuk ki. Az egyes szolgáltatások a saját meghatározott mérőszám-súlyozása szerint vannak összefoglalva. Ez biztosítja, hogy a szolgáltatások saját igényeiknek megfelelően kiegyensúlyozottak legyenek. Ennek eredményeképpen, ha ugyanaz az első csomópont meghibásodik, a rendszer az összes szolgáltatás összes partícióján elosztja a hibát. Az egyesekre gyakorolt hatás ugyanaz.

## <a name="next-steps"></a>Következő lépések
- A szolgáltatások konfigurálásával kapcsolatos további információkért [tekintse meg a szolgáltatások konfigurálásával](service-fabric-cluster-resource-manager-configure-services.md)foglalkozó témakört (Service-Fabric-cluster-resource-Manager-configure-Services.MD).
- A Lemeztöredezettség-mentesítő mérőszámok meghatározása az egyik módszer a csomópontok terhelésének konszolidálására a kiterjedésük helyett. A töredezettségmentesítés konfigurálásának megismeréséhez tekintse meg [ezt a cikket.](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Ha szeretné megtudni, hogy a fürterőforrás-kezelő hogyan kezeli és kiegyenlíti a fürt terhelését, tekintse meg a [terhelés kiegyensúlyozásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
- Kezdje a kezdetektől, és [Ismerkedjen meg a Service Fabric fürterőforrás-kezelővel](service-fabric-cluster-resource-manager-introduction.md)
- A szállítási költség az egyik módja annak, hogy a fürterőforrás-kezelőnek jelezze, hogy bizonyos szolgáltatások drágábbak a többinél. Ha többet szeretne megtudni a szállítási díjakról, tekintse meg [ezt a cikket](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
