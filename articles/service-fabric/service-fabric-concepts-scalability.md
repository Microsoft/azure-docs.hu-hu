---
title: Service Fabric szolgáltatások skálázhatósága
description: Ismerje meg az Azure Service Fabric skálázását, valamint az alkalmazások méretezésének különböző módszereit.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 87ac89edc1c9996afc03e7c2bd6743202fdfcb52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786191"
---
# <a name="scaling-in-service-fabric"></a>Méretezés Service Fabric
Az Azure Service Fabric használatával könnyedén méretezhető alkalmazások hozhatók létre egy fürt csomópontjain található szolgáltatások, partíciók és replikák kezelésével. Számos számítási feladat ugyanazon a hardveren való futtatása lehetővé teszi az erőforrások maximális kihasználtságát, azonban rugalmasságot is biztosít a munkaterhelések skálázása szempontjából. Ez a Channel 9 videó azt ismerteti, hogyan hozhat létre méretezhető Service-alkalmazásokat:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

A méretezés Service Fabric számos különböző módon valósítható meg:

1. Skálázás állapot nélküli szolgáltatási példányok létrehozásával vagy eltávolításával
2. Méretezés új nevesített szolgáltatások létrehozásával vagy eltávolításával
3. Méretezés új elnevezett alkalmazási példányok létrehozásával vagy eltávolításával
4. Skálázás particionált szolgáltatások használatával
5. Skálázás a fürt csomópontjainak hozzáadásával és eltávolításával 
6. Skálázás a fürterőforrás-kezelő metrikái használatával

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Skálázás állapot nélküli szolgáltatási példányok létrehozásával vagy eltávolításával
A Service Fabricon belüli méretezésének egyik legegyszerűbb módja az állapot nélküli szolgáltatások. Állapot nélküli szolgáltatás létrehozásakor lehetőség van a definiálására `InstanceCount` . `InstanceCount` meghatározza, hogy az adott szolgáltatás kódjának hány futó példánya legyen létrehozva a szolgáltatás indításakor. Tegyük fel például, hogy a fürtben 100 csomópont található. Azt is tegyük fel, hogy a szolgáltatás 10-mel lett létrehozva `InstanceCount` . A Futtatás során a kód 10 futó példánya túlságosan elfoglalt lehet (vagy nem elég elfoglalt). A számítási feladatok méretezésének egyik módja a példányok számának módosítása. Előfordulhat például, hogy egyes figyelési vagy felügyeleti kód a meglévő példányok számát 50-re vagy 5-re módosítja, attól függően, hogy a munkaterhelés a terhelés alapján kell-e méreteznie vagy kifogynia. 

C#

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Dinamikus példányszám használata
Az állapot nélküli szolgáltatások esetében Service Fabric automatikus módszert kínál a példányszám megváltoztatására. Ez lehetővé teszi, hogy a szolgáltatás dinamikusan méretezhető legyen az elérhető csomópontok számával. Ennek a viselkedésnek a megadására a példányszám =-1 érték állítható be. A InstanceCount =-1 utasítás Service Fabric, amely azt írja, hogy "az állapot nélküli szolgáltatást minden csomóponton futtatja." Ha a csomópontok száma megváltozik, Service Fabric automatikusan módosítja a példányszámot, hogy a szolgáltatás az összes érvényes csomóponton fusson. 

C#

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Méretezés új nevesített szolgáltatások létrehozásával vagy eltávolításával
Az elnevezett szolgáltatás-példány egy szolgáltatástípus egy adott példánya (lásd: [Service Fabric alkalmazás életciklusa](service-fabric-application-lifecycle.md)) a fürt egyes megnevezett alkalmazási példányain belül. 

Az új elnevezett szolgáltatási példányok létrehozhatók (vagy eltávolíthatók), mivel a szolgáltatások egyre vagy kevesebben vannak elfoglalva. Ez lehetővé teszi, hogy a kérelmek több szolgáltatási példányon is elterjednek, ami általában lehetővé teszi a meglévő szolgáltatások terhelésének csökkenését. Szolgáltatások létrehozásakor a Service Fabric fürterőforrás-kezelő elosztott módon helyezi el a szolgáltatásokat a fürtben. A pontos döntéseket a fürt [metrikái](service-fabric-cluster-resource-manager-metrics.md) és egyéb elhelyezési szabályok szabályozzák. A szolgáltatások számos különböző módon hozhatók létre, de a leggyakoribbak vagy olyan rendszergazdai műveletek, mint például valaki [`New-ServiceFabricService`](/powershell/module/servicefabric/new-servicefabricservice) , vagy a kód meghívásával [`CreateServiceAsync`](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) . `CreateServiceAsync` akár a fürtben futó más szolgáltatásokból is meghívható.

A szolgáltatások dinamikusan hozhatók létre a különféle helyzetekben, és egy közös minta. Vegyünk például egy olyan állapot-nyilvántartó szolgáltatást, amely egy adott munkafolyamatot jelöl. A munkát képviselő hívások megjelennek a szolgáltatásban, és ez a szolgáltatás végrehajtja a munkafolyamat lépéseit és a rekord előrehaladását. 

Hogyan teszi ezt a szolgáltatási léptéket? Előfordulhat, hogy a szolgáltatás több-bérlős lehet valamilyen formában, és fogadja a hívásokat, és az ugyanazon munkafolyamat számos különböző példányán egyszerre hívja meg a lépéseket. Azonban a kód összetettebbvé teheti a kódot, mivel mostantól az azonos munkafolyamat számos különböző példányán kell aggódnia, mindezt különböző fázisokban és különböző ügyfelektől. Emellett a több munkafolyamat egyidejű kezelésére nem oldja meg a skálázási problémát. Ennek az az oka, hogy a szolgáltatás egy adott gépen túl sok erőforrást fog használni. Számos olyan szolgáltatás, amely nem ehhez a mintához lett létrehozva, az első helyen is nehézségekbe ütközik, mivel a programkódban rejlő szűk keresztmetszet vagy lassulás történt. Az ilyen típusú problémák miatt a szolgáltatás nem működik, ha a nyomon követett egyidejű munkafolyamatok száma nagyobb lesz.  

Egy megoldás a szolgáltatás egy példányának létrehozása a nyomon követni kívánt munkafolyamat minden különböző példánya számára. Ez egy nagyszerű minta, és működik, hogy a szolgáltatás állapota vagy állapota megfelelő-e. Ennek a mintának a működéséhez általában egy másik szolgáltatás, amely a "munkaterhelés-kezelő szolgáltatás" néven működik. A szolgáltatás feladata a kérelmek fogadása és a kérelmek más szolgáltatásokhoz való továbbítása. A kezelő dinamikusan létre tudja hozni a munkaterhelés szolgáltatás egy példányát, amikor megkapja az üzenetet, majd továbbítja a kérelmeket ezekre a szolgáltatásokra. A kezelő szolgáltatás visszahívásokat is fogadhat, ha egy adott munkafolyamat-szolgáltatás befejezi a feladatot. Ha a kezelő megkapja ezeket a visszahívásokat, törölheti a munkafolyamat-szolgáltatás ezen példányát, vagy ha további hívások várhatóak, akkor hagyja. 

Az ilyen típusú kezelő speciális verziói akár az általa kezelt szolgáltatások készleteit is létrehozhatják. A készlet segít biztosítani, hogy ha új kérés érkezik, nem kell várnia, amíg a szolgáltatás fel nem merül. Ehelyett a kezelő csak olyan munkafolyamat-szolgáltatást választhat ki, amely jelenleg nem foglalt a készletből, vagy véletlenszerűen irányítja át őket. A rendelkezésre álló szolgáltatások készletének megőrzése gyorsabbá teszi az új kérések kezelését, mivel ez kevésbé valószínű, hogy a kérésnek várnia kell, amíg egy új szolgáltatás megpördült. Az új szolgáltatások létrehozása gyors, de nem ingyenes vagy azonnali. A készlet segít minimálisra csökkenteni azt az időtartamot, ameddig a kérésnek a kiszolgálás előtt várnia kell. Ezt a kezelőt és a készletet gyakran akkor láthatja, amikor a legtöbbet válaszol. A kérés és a szolgáltatás létrehozása a háttérben, _majd_ az átadása egy népszerű kezelői minta is, mint a szolgáltatások létrehozása és törlése a szolgáltatás által jelenleg függőben lévő munka mennyiségének nyomon követése alapján. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Méretezés új elnevezett alkalmazási példányok létrehozásával vagy eltávolításával
A teljes alkalmazás-példányok létrehozása és törlése hasonló a szolgáltatások létrehozásának és törlésének mintázatához. Ebben a mintában van néhány Manager-szolgáltatás, amely a megjelenő kérések alapján dönti el, és a fürtön belüli többi szolgáltatástól kapott információkat. 

Mikor kell létrehozni egy új elnevezett alkalmazás-példányt, és nem kell új elnevezett szolgáltatási példányt létrehoznia egy már meglévő alkalmazásban? Néhány esetben:

  * Az új alkalmazás-példány olyan ügyfélre vonatkozik, amelynek a kódnak bizonyos identitás-vagy biztonsági beállításokkal kell futnia.
    * Service Fabric lehetővé teszi, hogy különböző kódokat adjon meg adott identitások futtatásához. Ha ugyanazt a kódot szeretné elindítani a különböző identitások alatt, az aktiválásoknak különböző alkalmazás-példányokban kell történniük. Vegye fontolóra azt az esetet, amikor egy meglévő ügyfél munkaterhelése telepítve van. Ezek egy adott identitás alatt futhatnak, így a más erőforrásokhoz, például a távoli adatbázisokhoz vagy más rendszerekhez való hozzáférését nyomon követheti és szabályozhatja. Ebben az esetben, amikor egy új ügyfél regisztrál, valószínűleg nem kívánja aktiválni a kódját ugyanabban a kontextusban (a folyamat területét). Előfordulhat, hogy ez megnehezíti, hogy a szolgáltatási kód egy adott identitás kontextusában járjon el. Általában nagyobb biztonsággal, elkülönítéssel és Identity Management kóddal kell rendelkeznie. Ahelyett, hogy ugyanazon az alkalmazás-példányon belül különböző nevesített szolgáltatási példányokat kellene használnia, és így ugyanaz a folyamat tér el, különböző elnevezett Service Fabric alkalmazás-példányokat használhat. Ez megkönnyíti a különböző identitási környezetek definiálását.
  * Az új alkalmazás-példány konfigurációs eszközként is szolgál.
    * Alapértelmezés szerint egy adott szolgáltatástípus összes nevesített szolgáltatási példánya ugyanabban a folyamatban fog futni egy adott csomóponton. Ez azt jelenti, hogy az egyes szolgáltatási példányok különböző módon konfigurálhatók, így bonyolult. A szolgáltatásoknak rendelkeznie kell bizonyos jogkivonatokkal, amelyek segítségével megkeresik a konfigurációját egy konfigurációs csomagban. Ez általában csak a szolgáltatás neve. Ez jól működik, de a konfigurációt az adott alkalmazás-példányon belüli egyes elnevezett szolgáltatási példányok neveire párosítja. Ez zavaró lehet és nehezen kezelhető, mivel a konfiguráció általában egy, az alkalmazás-példányra jellemző értékeket tartalmazó tervezési idő. A további szolgáltatások létrehozása mindig azt jelenti, hogy az alkalmazás frissíti a konfigurációs csomagok adatait, vagy újakat helyez üzembe, így az új szolgáltatások megkereshetik az adott információkat. Gyakran egyszerűbb létrehozni egy teljesen új elnevezett Application-példányt. Ezután az alkalmazás paramétereinek használatával beállíthatja, hogy milyen konfigurációra van szükség a szolgáltatásokhoz. Így az elnevezett alkalmazási példányon belül létrehozott összes szolgáltatás örökölheti az adott konfigurációs beállításokat. Például ahelyett, hogy egyetlen konfigurációs fájlra lenne szüksége az összes ügyfél, például a Secrets vagy a felhasználónkénti erőforrásokra vonatkozó beállításokkal és testreszabásokkal, a beállítások felülbírálják az egyes ügyfelek eltérő alkalmazás-példányát. 
  * Az új alkalmazás frissítési határként szolgál
    * Service Fabric belül különböző elnevezett alkalmazás-példányok a frissítéshez határként szolgálnak. Egy elnevezett Application példány frissítése nem befolyásolja azt a kódot, amelyet egy másik elnevezett alkalmazás-példány futtat. A különböző alkalmazások ugyanazon kódok különböző verzióit futtatják ugyanazon a csomópontokon. Ez olyan tényező lehet, amikor méretezési döntésre van szükség, mert kiválaszthatja, hogy az új kódnak ugyanazokat a frissítéseket kell-e követnie, mint egy másik szolgáltatás. Tegyük fel például, hogy egy hívás érkezik a Manager szolgáltatásba, amely a szolgáltatások dinamikus létrehozásával és törlésével felelős egy adott ügyfél munkaterhelésének méretezéséhez. Ebben az esetben azonban a hívás egy _új_ ügyféllel társított munkaterheléshez tartozik. A legtöbb ügyfél, amely nem csupán a korábban felsorolt biztonsági és konfigurációs okok miatt izolálja egymástól, de nagyobb rugalmasságot biztosít a szoftver adott verzióinak futtatásához és a frissítésük időpontjának kiválasztásához. Létrehozhat egy új alkalmazás-példányt is, és létrehozhatja a szolgáltatást úgy, hogy az egy frissítés által érintett szolgáltatások mennyiségét Tovább particionálja. A különböző alkalmazási példányok részletesebb részletességet biztosítanak az alkalmazások frissítésekor, valamint A/B tesztelés és A kék/zöld környezetek engedélyezését is. 
  * A meglévő alkalmazás-példány megtelt
    * Service Fabric az [alkalmazás kapacitása](service-fabric-cluster-resource-manager-application-groups.md) egy olyan fogalom, amellyel szabályozható az adott alkalmazás-példányokhoz rendelkezésre álló erőforrások mennyisége. Dönthet például úgy, hogy egy adott szolgáltatásnak egy másik példányt kell létrehoznia a skálázás érdekében. Ez az alkalmazás-példány azonban egy bizonyos metrika esetében nem áll rendelkezésre kapacitással. Ha az adott ügyfélnek vagy munkaterhelésnek továbbra is több erőforrást kell biztosítania, akkor növelheti az alkalmazás meglévő kapacitását, vagy létrehozhat egy új alkalmazást. 

## <a name="scaling-at-the-partition-level"></a>Skálázás a partíció szintjén
Service Fabric támogatja a particionálást. A particionálás több logikai és fizikai szakaszra osztja szét a szolgáltatásokat, amelyek mindegyike egymástól függetlenül működik. Ez az állapot-nyilvántartó szolgáltatások esetében hasznos, mivel egyetlen replikának sem kell kezelnie az összes hívást, és egyszerre módosítania kell az összes állapotot. A [particionálás áttekintése](service-fabric-concepts-partitioning.md) a támogatott particionálási sémák típusairól nyújt információkat. Az egyes partíciók replikái a fürt csomópontjai között oszlanak el, a szolgáltatás terhelésének elosztásával és annak biztosításával, hogy sem a szolgáltatás egészének, sem partíciójának egyetlen meghibásodási pontja legyen. 

Vegyünk fontolóra egy olyan szolgáltatást, amely egy 0. alacsony kulccsal rendelkező, tartományhoz tartozó particionálási sémát használ, a 99 magas kulcsát és a partíciók számát 4. Egy három csomópontot tartalmazó fürtben a szolgáltatás négy replikával van meghatározva, amelyek az egyes csomópontokon lévő erőforrásokat osztják meg az itt látható módon:

<center>

![Partíciós elrendezés három csomóponttal](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Ha növeli a csomópontok számát, Service Fabric áthelyezi a meglévő replikákat. Tegyük fel például, hogy a csomópontok száma négyre nő, és a replikák újraelosztásra kerülnek. A szolgáltatás most már három replikát futtat az egyes csomópontokon, amelyek mindegyike különböző partíciókhoz tartozik. Ez lehetővé teszi a jobb erőforrás-használatot, mivel az új csomópont nem hideg. A teljesítmény általában úgy is javítja a teljesítményt, mivel az egyes szolgáltatásokhoz több erőforrás áll rendelkezésre.

<center>

![Partíciós elrendezés négy csomóponttal](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Skálázás a Service Fabric fürterőforrás-kezelő és metrikák használatával
A [metrikák](service-fabric-cluster-resource-manager-metrics.md) azt ismertetik, hogy a szolgáltatások hogyan fejezik ki az erőforrások felhasználását Service Fabric. A metrikák használatával a fürterőforrás-kezelő lehetővé teszi a fürt elrendezésének átszervezését és optimalizálását. Előfordulhat például, hogy sok erőforrás van a fürtben, de előfordulhat, hogy a jelenleg munkát végző szolgáltatásoknak nem vannak lefoglalva. A metrikák használatával a fürterőforrás-kezelő átrendezheti a fürtöt, így biztosítva, hogy a szolgáltatások hozzáférhessenek a rendelkezésre álló erőforrásokhoz. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Skálázás a fürt csomópontjainak hozzáadásával és eltávolításával 
A Service Fabric méretezésének egy másik lehetősége a fürt méretének módosítása. A fürt méretének módosítása azt jelenti, hogy a fürt egy vagy több csomópont-típusához hozzáad vagy eltávolít egy csomópontot. Vegyünk például egy olyan esetet, amelyben a fürt összes csomópontja meleg. Ez azt jelenti, hogy a fürt erőforrásai szinte teljesen felhasználva vannak. Ebben az esetben a legjobb méretezés érdekében a fürthöz több csomópont hozzáadására van lehetőség. Miután az új csomópontok csatlakoznak a fürthöz, a Service Fabric fürterőforrás-kezelő áthelyezi a szolgáltatásokat, így kevesebb teljes terhelést eredményez a meglévő csomópontokon. A példányok száma =-1 értékkel rendelkező állapot nélküli szolgáltatások esetében a rendszer több szolgáltatási példányt hoz létre automatikusan. Ez lehetővé teszi, hogy bizonyos hívások áttérjenek a meglévő csomópontokról az új csomópontokra. 

További információ: [fürtök skálázása](service-fabric-cluster-scaling.md).

## <a name="choosing-a-platform"></a>Platform kiválasztása

Az operációs rendszerek közötti implementációs különbségek miatt a Windows vagy Linux rendszerű Service Fabric használatának alapvető része lehet az alkalmazás skálázásának. Az egyik lehetséges akadály a szakaszos naplózás végrehajtása. Service Fabric a Windows rendszeren egy kernel-illesztőprogramot használ egy számítógépenkénti naplóhoz, amely az állapot-nyilvántartó szolgáltatás replikái között van megosztva. Ez a napló körülbelül 8 GB-ot foglal le. A Linux azonban egy 256 MB-os átmeneti naplót használ az egyes replikák számára, így kevésbé ideális olyan alkalmazások számára, amelyek maximalizálni szeretnék az adott csomóponton futó Lightweight Service-replikák számát. Az ideiglenes tárolási követelményekkel kapcsolatos eltérések a kívánt platformot tájékoztatják Service Fabric fürt üzembe helyezéséhez.

## <a name="putting-it-all-together"></a>Végső összeállítás
Nézzük meg az itt ismertetett ötleteket, és ismerkedjen meg egy példával. Vegye figyelembe a következő szolgáltatást: olyan szolgáltatást próbál létrehozni, amely címjegyzékként működik, és a neveket és a kapcsolattartási adatokat használja. 

A jobb oldalon van egy csomó kérdés a skálával kapcsolatban: hány felhasználóra lesz szüksége? Hány partner fog tárolni az egyes felhasználók? Ha az első alkalommal nem áll készen a szolgáltatás kiértékelésére, akkor nehéz lenne megállapítani. Tegyük fel, hogy egy adott partíciók számával rendelkező, egyetlen statikus szolgáltatással fog járni. A helytelen partíciók számának kiválasztásának következményei miatt később méretezési problémák merülhetnek fel. Hasonlóképpen, még akkor is, ha kiválasztja a megfelelő számú információt, előfordulhat, hogy nem rendelkezik a szükséges információkkal. Azt is megteheti, hogy a fürt alapértékét a csomópontok számának és méretének megfelelően kell eldöntenie. Általában nehéz megjósolni, hogy hány erőforrást fog használni a szolgáltatás az élettartama során. Azt is nehéz megismerni, hogy a szolgáltatás ténylegesen milyen forgalmi mintát lát. Előfordulhat például, hogy az emberek csak az első dologban veszik fel és távolítják el a kapcsolataikat, vagy lehet, hogy a nap folyamán egyenletesen oszlanak el. Ennek alapján lehetséges, hogy dinamikusan kell felskálázást végeznie. Lehet, hogy megtudhatja, hogy mikor kell felskálázást végeznie, de az is előfordulhat, hogy a szolgáltatás által használt erőforrás-felhasználás változására kell reagálnia. Ez magában foglalhatja a fürt méretének módosítását, hogy több erőforrást biztosítson, ha a meglévő erőforrások használatának átszervezése nem elég. 

De miért is érdemes egyetlen partíciós sémát kiválasztania az összes felhasználó számára? Miért korlátozza magát egy szolgáltatásra és egy statikus fürtre? A valós helyzet általában dinamikusabb. 

A skála kialakításakor vegye figyelembe a következő dinamikus mintát. Előfordulhat, hogy módosítania kell a helyzetet:

1. Ahelyett, hogy kiválasszon egy particionálási sémát mindenki számára, hozzon létre egy "Manager-szolgáltatást".
2. A Manager szolgáltatás feladata, hogy a szolgáltatásra való feliratkozáskor megtekintse a vásárlói adatokat. Ezután attól függően, hogy a kezelő szolgáltatás a _tényleges_ Contact-Storage szolgáltatás egy példányát hozza létre, _csak az adott ügyfél számára_. Ha speciális konfigurációra, elkülönítésre vagy frissítésre van szükség, dönthet úgy is, hogy egy alkalmazás-példányt indít el ehhez az ügyfélhez. 

Ez a dinamikus létrehozási minta számos előnnyel jár:

  - Nem próbálja meg kitalálni a megfelelő partíciók darabszámát az összes felhasználó számára, vagy hozzon létre egy olyan szolgáltatást, amely a teljes mértékben skálázható. 
  - A különböző felhasználóknak nem kell megegyezniük a partíciók számával, a replika számával, az elhelyezési korlátozásokkal, a metrikákkal, az alapértelmezett terhelésekkel, a szolgáltatás nevével, a DNS-beállításokkal, illetve a szolgáltatás vagy az alkalmazás szintjén megadott egyéb tulajdonságokkal. 
  - További adatszegmentálást nyerhet. Minden ügyfél saját másolattal rendelkezik a szolgáltatásról
    - Minden ügyfél-szolgáltatás eltérő módon konfigurálható, és több vagy kevesebb erőforrást is engedélyezhet, a várt lépték alapján több vagy kevesebb partíciót vagy replikát kell biztosítania.
      - Tegyük fel például, hogy az ügyfél a "Gold" szinten fizetett, több replikát vagy nagyobb példányszámot, valamint a szolgáltatásaik számára a metrikák és az alkalmazási kapacitások alapján dedikált erőforrásokat is kaphat.
      - Vagy azt is adja meg, hogy a szükséges kapcsolatok száma "kicsi" volt – ezek csak néhány partíciót kapnak, vagy akár egy megosztott szolgáltatási készletbe is helyezhetők más ügyfelekkel.
  - Nem futtat olyan szolgáltatási példányokat vagy replikákat, amelyek az ügyfelek számára való várakozás közben vannak
  - Ha egy ügyfél még mindig elhagyja a szolgáltatást, a szolgáltatásból eltávolíthatja az adatait, és a kezelő törli az általa létrehozott szolgáltatást vagy alkalmazást.

## <a name="next-steps"></a>Következő lépések
Service Fabric fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Service Fabric szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
* [Service Fabric szolgáltatások particionálása](service-fabric-concepts-partitioning.md)
