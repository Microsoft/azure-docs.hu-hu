---
title: Egyéni Service Fabric állapotjelentés hozzáadása
description: Útmutató egyéni állapotjelentés küldéséhez az Azure Service Fabric Health entitások számára. Ajánlásokat nyújt a minőségi állapottal kapcsolatos jelentések tervezéséhez és megvalósításához.
ms.topic: conceptual
ms.date: 2/28/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c87c063ac9d3b4810657f72c46c17725b8899c77
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626169"
---
# <a name="add-custom-service-fabric-health-reports"></a>Egyéni Service Fabric állapotjelentés hozzáadása
Az Azure Service Fabric egy olyan [egészségügyi modellt](service-fabric-health-introduction.md) vezet be, amely a nem kifogástalan állapotú fürtök és az alkalmazások bizonyos entitásokra vonatkozó feltételeit jelöli. Az állapot-modell az állapotfigyelő **jelentéseket** (rendszerösszetevők és watchdogok) használja. A cél egyszerű és gyors diagnosztizálás és javítás. A Service Writers szolgáltatásnak előre kell gondolnia az állapotról. Minden olyan feltételt, amely hatással lehet az állapotra, jelenteni kell, különösen akkor, ha segíthet a gyökérhez közeledő problémák megjelölésében. Az állapotadatok segítségével időt és fáradságot takaríthat meg a hibakeresés és a vizsgálat során. A hasznosság különösen egyértelmű, ha a szolgáltatás a felhőben (magán vagy Azure) nagy léptékben fut.

A Service Fabric Reporter figyeli az azonosított feltételeket. Ezek a feltételek a helyi nézet alapján jelentenek. Az [állapotfigyelő](service-fabric-health-introduction.md#health-store) összesíti az összes riporter által eljuttatott egészségügyi adatokat annak megállapítására, hogy az entitások globálisan kifogástalanok-e. A modell célja, hogy gazdag, rugalmas és könnyen használható legyen. Az állapot-jelentések minősége határozza meg a fürt állapot nézetének pontosságát. A nem kifogástalan állapotú problémák helytelen megjelenítése negatív hatással lehet a frissítésekre vagy más, az állapotadatok használatát használó szolgáltatásokra. Ilyen szolgáltatás például a szolgáltatások és a riasztási mechanizmusok javítása. Ezért bizonyos gondolkodásra van szükség ahhoz, hogy olyan jelentéseket szolgáltasson, amelyek a lehető legjobb módon rögzítik az érdeklődési feltételeket.

Az állapot-jelentéskészítés, a watchdogok és a rendszerösszetevők tervezéséhez és megvalósításához a következőket kell tennie:

* Határozza meg a feltételt, hogy érdeklik, milyen módon legyenek figyelve, és hogy milyen hatással van a fürtre vagy az alkalmazás működésére. Ezen információk alapján döntse el az állapotjelentés tulajdonságot és az állapotot.
* Határozza meg azt az [entitást](service-fabric-health-introduction.md#health-entities-and-hierarchy) , amelyre a jelentés vonatkozik.
* Határozza meg, hogy a jelentéskészítés hol történik, a szolgáltatáson belülről vagy egy belső vagy külső watchdogról.
* A jelentéskészítő azonosítására szolgáló forrás definiálása.
* Válasszon egy jelentéskészítési stratégiát, amely rendszeres időközönként vagy átmeneteken alapul. Az ajánlott módszer rendszeres időközönként, mivel egyszerűbb programkódot igényel, és kevésbé hajlamos a hibákra.
* Határozza meg, hogy mennyi ideig maradnak a jelentés a nem kifogástalan állapotú feltételeknél az állapotfigyelő tárolóban, és hogyan legyenek törölve. Ezen információk alapján döntse el, hogy a jelentés Mikor él és távolítsa el a lejárat viselkedését.

Ahogy említettük, a jelentéskészítés a következő helyről végezhető el:

* A figyelt Service Fabric szolgáltatás replikája.
* Service Fabric szolgáltatásként üzembe helyezett belső watchdogok (például egy Service Fabric állapot nélküli szolgáltatás, amely figyeli a feltételeket és a problémákkal kapcsolatos jelentéseket). A watchdogok minden csomópontot üzembe helyezhetnek, vagy a figyelt szolgáltatáshoz lehetnek affinitással.
* A Service Fabric csomópontokon futó belső watchdogok, de *nem* Service Fabric-szolgáltatásként valósulnak meg.
* Külső watchdogok, amelyek az erőforrást az Service Fabric-fürtön *kívülről* vizsgálják (például: figyelési szolgáltatás, mint a Gomez).

> [!NOTE]
> A fürt a rendszerösszetevők által elküldett állapot-jelentésekkel van feltöltve. További információ: [rendszerállapot-jelentések használata a hibaelhárításhoz](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). A felhasználói jelentéseket a rendszer által már létrehozott [állapot entitásokon](service-fabric-health-introduction.md#health-entities-and-hierarchy) kell elküldeni.
> 
> 

Ha az állapot-jelentési terv egyértelmű, az állapotadatok egyszerűen elküldhetők. A [FabricClient](/dotnet/api/system.fabric.fabricclient) használatával jelenthet állapotot, ha a fürt nem [biztonságos](service-fabric-cluster-security.md) , vagy ha a háló ügyfél rendszergazdai jogosultságokkal rendelkezik. A jelentéskészítés az API-n keresztül végezhető el a [FabricClient. HealthManager. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), a PowerShell vagy a REST használatával. A konfigurációs gombok a Batch-jelentéseket a jobb teljesítmény érdekében.

> [!NOTE]
> A jelentés állapota szinkronban van, és csak az ügyfél oldalán lévő ellenőrzési munkát jelöli. Az a tény, hogy az állapotadatok elfogadják a jelentést, vagy az `Partition` vagy az `CodePackageActivationContext` objektum nem azt jelenti, hogy az a tárolóban lesz alkalmazva. A rendszer aszinkron módon küldi el, és esetleg más jelentésekkel kötegelt feldolgozást készít. A kiszolgálón végzett feldolgozás továbbra is sikertelen lehet: a sorszám elavult lehet, az entitást, amelyre a jelentést alkalmazni kell, törölve lett, stb.
> 
> 

## <a name="health-client"></a>Állapotadatok
Az állapotfigyelő jelentéseket a rendszer az állapotfigyelő ügyfélen keresztül küldi el a Health Managernek. Az Health Manager jelentéseket ment az állapotfigyelő tárolóban. Az állapot-ügyfél a következő beállításokkal konfigurálható:

* **HealthReportSendInterval**: a jelentésnek az ügyfélhez való hozzáadása és a Health Managernek elküldésekor eltelt idő közötti késleltetés. Egyetlen üzenetbe történő kötegelt jelentések küldésére szolgál, és nem küld egyetlen üzenetet az egyes jelentésekhez. A kötegelt feldolgozás javítja a teljesítményt. Alapértelmezett: 30 másodperc.
* **HealthReportRetrySendInterval**: az az időtartam, amikor az állapotfigyelő ügyfél újraküldi a halmozott állapotú jelentéseket az Health Managernek. Alapértelmezett: 30 másodperc, minimum: 1 másodperc.
* **HealthOperationTimeout**: az állapotfigyelő időszaka, amelyet a rendszer a Health Managernek küld. Ha egy üzenet túllépi az időkorlátot, az állapot-ügyfél újrapróbálkozik, amíg az állapotfigyelő nem ellenőrzi, hogy a jelentés feldolgozása megtörtént-e. Alapértelmezett: két perc.

> [!NOTE]
> Ha a jelentések batch-alapúak, a háló ügyfelet legalább a HealthReportSendInterval életben kell tartani, hogy azok el legyenek küldve. Ha az üzenet elvész, vagy a Health Manager átmeneti hibák miatt nem tudja alkalmazni őket, a háló ügyfelet továbbra is életben kell tartani, hogy a rendszer próbálkozzon újra.
> 
> 

Az ügyfélen a pufferelés a jelentések egyediségét veszi figyelembe. Ha például egy adott rossz Reporter egy adott entitás ugyanazon tulajdonságán másodpercenként 100 jelentést küld, a jelentéseket a rendszer a legutóbbi verzióra cseréli le. Legfeljebb egy ilyen jelentés szerepel az ügyfél-várólistában. Ha a kötegelt beállítás konfigurálva van, az állapotfigyelő Managernek küldött jelentések száma csak egy küldési időköz. Ez a jelentés az utolsó hozzáadott jelentés, amely az entitás aktuális állapotát tükrözi.
Adja meg a konfigurációs paramétereket, ha a jön `FabricClient` létre a [FabricClientSettings](/dotnet/api/system.fabric.fabricclientsettings) átadásával az állapottal kapcsolatos bejegyzések kívánt értékeivel.

A következő példa létrehoz egy Fabric-ügyfelet, és megadja, hogy a rendszer a jelentéseket a hozzáadásakor küldje el. Az újrapróbálkozó időtúllépéseken és hibáknál az újrapróbálkozások 40 másodpercenként történnek.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Javasoljuk, hogy az alapértelmezett háló ügyfél-beállításokat állítsa `HealthReportSendInterval` 30 másodpercre. Ez a beállítás biztosítja az optimális teljesítményt a kötegelt feldolgozás miatt. Olyan kritikus fontosságú jelentésekhez, amelyeket a lehető legrövidebb időn belül el kell juttatni, azonnal használhatja a `HealthReportSendOptions` `true` [FabricClient. HealthClient. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API-ban. Az azonnali jelentések megkerülik a kötegelt időközt. Ezt a jelzőt körültekintően használhatja. Ha lehetséges, igény szerint ki kell használni az állapot-ügyfél kötegét. Az azonnali küldés akkor is hasznos, ha a Fabric-ügyfél lezárult (például a folyamat érvénytelen állapotot határozott meg, és le kell állítania, hogy megakadályozza a mellékhatásokat). Ez biztosítja a halmozott jelentések legalkalmasabb küldését. Ha az azonnali jelzővel egy jelentést ad hozzá, az állapot-ügyfél a legutóbbi küldés óta összesíti az összes összesített jelentést.

Ugyanazokat a paramétereket lehet megadni, amikor egy fürthöz csatlakozik a PowerShell használatával. A következő példa egy helyi fürthöz való kapcsolódást indít el:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Az API-hoz hasonlóan a jelentések a `-Immediate` kapcsoló használatával azonnal elküldhetők, az `HealthReportSendInterval` értéktől függetlenül.

A REST esetében a jelentéseket a rendszer a Service Fabric átjárónak küldi el, amely egy belső háló ügyfelet tartalmaz. Ez az ügyfél alapértelmezés szerint 30 másodpercenként kötegelt küldésre van konfigurálva. A Batch-intervallumot megváltoztathatja a fürtkonfiguráció beállításaival `HttpGatewayHealthReportSendInterval` `HttpGateway` . Ahogy említettük, egy jobb lehetőség a jelentések igaz módon történő elküldése `Immediate` . 

> [!NOTE]
> Annak biztosítása érdekében, hogy a jogosulatlan szolgáltatások ne jelentsenek állapotot a fürt entitásai között, konfigurálja úgy a kiszolgálót, hogy csak a biztonságos ügyfelektől fogadja a kérelmeket. A `FabricClient` jelentéskészítéshez használt biztonsági funkciónak engedélyezve kell lennie ahhoz, hogy kommunikálni tudjon a fürttel (például Kerberos-vagy tanúsítványalapú hitelesítéssel). További információ a [fürt biztonságáról](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Az alacsony jogosultsági szintű szolgáltatásokból származó jelentés
Ha Service Fabric szolgáltatásoknak nincs rendszergazdai hozzáférése a fürthöz, a vagy a alkalmazásban az aktuális környezetből bejelentheti az állapotot az entitásokban `Partition` `CodePackageActivationContext` .

* Állapot nélküli szolgáltatások esetén a [IStatelessServicePartition. ReportInstanceHealth](/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) használatával jelentse az aktuális szolgáltatási példányt.
* Állapot-nyilvántartó szolgáltatások esetén a [IStatefulServicePartition. ReportReplicaHealth](/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) használatával jelentse az aktuális replikát.
* A [IServicePartition. ReportPartitionHealth](/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) használatával jelentse az aktuális partíciós entitást.
* A [CodePackageActivationContext. ReportApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) használatával jelentse az aktuális alkalmazást.
* A [CodePackageActivationContext. ReportDeployedApplicationHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) használatával jelentse az aktuális csomóponton üzembe helyezett aktuális alkalmazást.
* A [CodePackageActivationContext. ReportDeployedServicePackageHealth](/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) használatával jelentheti be az aktuális csomóponton üzembe helyezett alkalmazás szolgáltatási csomagját.

> [!NOTE]
> Belsőleg, a `Partition` és az `CodePackageActivationContext` állapotának megtartása az alapértelmezett beállításokkal van konfigurálva. Ahogy azt az állapotfigyelő [ügyfél](service-fabric-report-health.md#health-client)is ismerteti, a jelentések kötegelt és időzítő módon lesznek elküldve. Az objektumokat életben kell tartani, hogy legyen esélye a jelentés elküldésére.
> 
> 

Megadhatja `HealthReportSendOptions` , mikor küldjön jelentéseket a `Partition` és az Health API-k használatával `CodePackageActivationContext` . Ha olyan kritikus fontosságú jelentésekkel rendelkezik, amelyeket a lehető leghamarabb el kell juttatni, `HealthReportSendOptions` azonnal használhatja `true` . Az azonnali jelentések megkerülik a belső állapotfigyelő ügyfél batching intervallumát. Ahogy korábban említettük, használja ezt a jelzőt a Care használatával. Ha lehetséges, igény szerint ki kell használni az állapot-ügyfél kötegét.

## <a name="design-health-reporting"></a>Állapot jelentéskészítésének megtervezése
A kiváló minőségű jelentések létrehozásának első lépéseként azonosíthatók azok a feltételek, amelyek befolyásolhatják a szolgáltatás állapotát. Minden olyan feltételt, amely segíthet a szolgáltatás vagy a fürt hibáinak megjelölésében, vagy akár jobb is, még a probléma előtt – akár több milliárd dollárt is menthet. Az előnyök kevesebb időt vesznek igénybe, és kevesebb órát töltöttek a problémák kivizsgálásával és javításával, valamint a vásárlói elégedettséggel szemben.

A feltételek azonosítása után a watchdog-íróknak meg kell találniuk a legjobb módszert a terhelés és a hasznosság közötti egyensúly figyelésére. Vegyünk például egy olyan szolgáltatást, amely olyan összetett számításokat alkalmaz, amelyek bizonyos ideiglenes fájlokat használnak egy megosztáson. A watchdog figyelheti a megosztást, így biztosítva, hogy elegendő szabad hely álljon rendelkezésre. A fájl-vagy könyvtárbeli változások értesítéseit figyelheti. Figyelmeztetést jelenthet, ha elérte a kezdeti küszöbértéket, és hibát jelez, ha a megosztás megtelt. Figyelmeztetés esetén a javítási rendszer megkezdheti a megosztás régebbi fájljainak tisztítását. Hiba esetén a javítási rendszer áthelyezheti a szolgáltatás replikáját egy másik csomópontra. Figyelje meg, hogy a feltétel állapotait az állapot állapota határozza meg: az állapot, amely kifogástalannak tekinthető (ok) vagy sérült (figyelmeztetés vagy hiba).

A figyelési részletek beállítása után a watchdog-íróknak meg kell állapítaniuk a watchdog megvalósításának módját. Ha a feltételek meghatározhatók a szolgáltatáson belülről, a watchdog maga is lehet a figyelt szolgáltatás része. A szolgáltatási kód például megtekintheti a megosztás használatát, majd jelentést készíthet minden alkalommal, amikor megpróbálja írni a fájlt. Ennek a megközelítésnek az előnye, hogy a jelentéskészítés egyszerű. Ügyelni kell arra, hogy a watchdog hibái ne befolyásolják a szolgáltatás funkcióit.

A felügyelt szolgáltatáson belüli jelentéskészítés nem mindig egy lehetőség. Előfordulhat, hogy a szolgáltatáson belüli watchdog nem fogja tudni felderíteni a feltételeket. Előfordulhat, hogy nem rendelkezik a meghatározáshoz szükséges logikával vagy adattal. A feltételek monitorozásának terhelése magas lehet. A feltételek nem feltétlenül jellemzőek a szolgáltatásra, hanem a szolgáltatások közötti interakciókat is érintik. Egy másik lehetőség, hogy a fürtben lévő watchdogok külön folyamatként legyenek kiválasztva. A watchdog a feltételeket és a jelentést a főbb szolgáltatások bármilyen módon történő befolyásolása nélkül figyeli. Például ezek a watchdogok olyan állapot nélküli szolgáltatásokként valósíthatók meg ugyanabban az alkalmazásban, amelyek az összes csomóponton vagy a szolgáltatással azonos csomópontokon vannak telepítve.

Előfordulhat, hogy a fürtben futó watchdog nem választható. Ha a figyelt feltétel a szolgáltatás rendelkezésre állása vagy funkciója, ahogy a felhasználók látják, a legjobb, ha a watchdogok ugyanazon a helyen vannak, mint a felhasználói ügyfelek. Ott is tesztelheti a műveleteket ugyanúgy, ahogy a felhasználók meghívja őket. Rendelkezhet például egy olyan watchdoggal, amely a fürtön kívül él, és lekérdezi a szolgáltatásra irányuló kéréseket, és ellenőrzi az eredmény késését és helyességét. (A számológép szolgáltatás esetében például a 2 + 2 a 4 értéket ésszerű időn belül visszaküldi?)

A watchdog részleteinek véglegesítése után döntse el, hogy milyen forrás-AZONOSÍTÓval rendelkezik, amely egyedileg azonosítja azt. Ha ugyanaz a típus több házőrző is él a fürtben, különböző entitásokat kell jelenteniük, vagy ha ugyanazon az entitáson jelentenek, más forrás-azonosítót vagy tulajdonságot kell használniuk. Így a jelentéseik is létezhetnek. Az állapotjelentés tulajdonságának rögzítenie kell a figyelt feltételt. (A fenti példában a tulajdonság lehet **ShareSize**.) Ha több jelentés is ugyanarra a feltételre vonatkozik, a tulajdonságnak tartalmaznia kell néhány dinamikus információt, amely lehetővé teszi a jelentések egyazon létezését. Ha például több megosztást kell figyelni, a tulajdonság neve lehet **ShareSize-megosztásnév**.

> [!NOTE]
> Az állapotadatok megőrzéséhez *ne használja a* Health Store-t. Csak az állapottal kapcsolatos információkat kell jelenteni állapotként, mivel ezek az információk hatással vannak az entitások állapotának kiértékelésére. Az állapotfigyelő szolgáltatás nem általános célú tárolóként lett kialakítva. Az állapot kiértékelésének logikája alapján összesíti az összes adatokat az állapotba. Ha az adatok nem kapcsolódnak az állapothoz (például a jelentéskészítés állapota OK állapotával), az nem befolyásolja az összesített állapotot, de negatívan befolyásolhatja az állapotadatok teljesítményét.
> 
> 

A következő döntési pont az az entitás, amelyről jelentést szeretne készíteni. A legtöbb esetben a feltétel egyértelműen azonosítja az entitást. Válassza ki az entitást a lehető legpontosabb részletességgel. Ha egy feltétel a partíción lévő összes replikára hatással van, akkor a (z) partíciót nem a szolgáltatáson keresztül kell jelentenie. Vannak olyan sarokban lévő esetek, ahol további gondolkodásra van szükség. Ha a feltétel hatással van egy entitásra, például egy replikára, de a szándék az, hogy a feltételt a replika élettartamának időtartama meghaladja, akkor azt jelenteni kell a partíción. Ellenkező esetben a replika törlésekor a Health Store minden jelentését törli. A watchdog-íróknak gondolnia kell az entitás és a jelentés élettartamára. Egyértelműnek kell lennie abban az esetben, ha egy jelentést egy tárolóból kell tisztítani (például ha egy entitáson már nem történt hiba).

Lássunk egy példát, amely összefoglalja a leírt pontokat. Vegyünk egy Service Fabric alkalmazást, amely az összes csomóponton üzembe helyezett fő állapot-nyilvántartó állandó szolgáltatásból és másodlagos állapot nélküli szolgáltatásokból áll (az egyes feladatokhoz egy másodlagos szolgáltatástípus). A főkiszolgálónak van egy feldolgozási várólistája, amely a formátumú másodlagos zónák által végrehajtandó parancsokat tartalmazza. A formátumú másodlagos zónák végrehajtja a beérkező kéréseket, és visszaküldi a nyugtázási jeleket. A felügyelhető egyik feltétel a fő feldolgozási várólista hossza. Ha a fő várólista hossza eléri a küszöbértéket, a rendszer figyelmeztetést küld. A figyelmeztetés azt jelzi, hogy a formátumú másodlagos zónák nem tudja kezelni a terhelést. Ha a várólista eléri a maximális hosszt és a parancsokat, a rendszer hibát jelez, mivel a szolgáltatás nem állítható helyre. A jelentések a **QueueStatus** tulajdonsággal rendelkezhetnek. A watchdog a szolgáltatáson belül lakik, és rendszeres időközönként a fő elsődleges replikán küldi el. Az élettartam két perc, és minden esetben 30 másodpercenként küldi el a rendszer. Ha az elsődleges leáll, a jelentés automatikusan törlődik a tárolóból. Ha a szolgáltatás replikája folyamatban van, de holtpontba ütközik, vagy más problémák léptek fel, a jelentés lejár az állapotfigyelő tárolóban. Ebben az esetben a rendszer hiba esetén kiértékeli az entitást.

Egy másik megfigyelhető feltétel a feladat végrehajtási ideje. A főkiszolgáló a feladattípus alapján osztja el a tevékenységeket a formátumú másodlagos zónák. A tervtől függően a főkiszolgáló lekérdezheti a formátumú másodlagos zónák a feladat állapotára vonatkozóan. Azt is megvárhatja, hogy a formátumú másodlagos zónák visszaküldje a nyugtázási jeleket, ha elkészült. A második esetben ügyelni kell arra, hogy olyan helyzetekben lehessen felderíteni a formátumú másodlagos zónák, amelyekben a Die vagy az üzenetek elvesznek. Az egyik lehetőség, hogy a főkiszolgáló egy ping-kérelmet küld ugyanarra a másodlagosra, amely visszaküldi az állapotát. Ha nem érkezik állapot, a főkiszolgáló hibát jelez, és átütemezze a feladatot. Ez a viselkedés feltételezi, hogy a tevékenységek idempotens.

A figyelt feltételt figyelmeztetésként lehet lefordítani, ha a feladat nem egy adott időpontban történik (**T1**, például 10 perc). Ha a feladat nem fejeződött be időben (**T2**, például 20 perc), akkor a figyelt feltétel hibaként fordítható le. A jelentéskészítés több módon is elvégezhető:

* A fő elsődleges replika rendszeres időközönként jelentést készít. Az üzenetsor összes függőben lévő feladatának egy tulajdonsága lehet. Ha legalább egy feladat továbbra is fennáll, a jelentés állapota a **PendingTasks** tulajdonságnál figyelmeztetés vagy hiba, ha szükséges. Ha nincsenek függőben lévő feladatok, vagy az összes feladat végrehajtása megkezdődött, a jelentés állapota OK. A feladatok állandóak. Ha az elsődleges leáll, az újonnan előléptetett elsődleges művelet továbbra is megfelelően tud jelentést készíteni.
* Egy másik watchdog-folyamat (a felhőben vagy a külsőben) ellenőrzi a feladatokat (a kívánt feladat eredménye alapján), hogy megtekintse, hogy azok befejeződtek-e. Ha nem veszik figyelembe a küszöbértékeket, a rendszer egy jelentést továbbít a főkiszolgálón. A rendszer minden olyan feladatra vonatkozó jelentést is továbbít, amely tartalmazza a feladat azonosítóját, például a **PendingTask + taskId**. A jelentéseket csak sérült állapotok esetén kell elküldeni. Állítsa be az időpontot néhány percre, és jelölje ki az eltávolítandó jelentéseket, amikor lejárnak a tisztítás érdekében.
* A feladat-jelentést végrehajtó másodlagos, ha a futtatása a vártnál hosszabb időt vesz igénybe. A **PendingTasks** tulajdonságban található szolgáltatási példányról jelent jelentést. A jelentés kijelöli a problémát okozó szolgáltatási példányt, de nem rögzíti azt a helyzetet, amelyben a példány meghal. Ekkor a jelentések törlődnek. Jelenthet jelentést a másodlagos szolgáltatásról. Ha a másodlagos feladat befejezi a feladatot, a másodlagos példány törli a jelentést az áruházból. A jelentés nem rögzíti azt a helyzetet, amelyben az nyugtázási üzenet elvész, és a feladat nem fejeződött be a főkiszolgáló nézetből.

A jelentéskészítés azonban a fent ismertetett esetekben történik, az állapot kiértékelése során a jelentéseket az alkalmazás állapota rögzíti.

## <a name="report-periodically-vs-on-transition"></a>Rendszeres jelentés és áttérés
Az állapot-jelentési modell használatával a watchdogok rendszeres időközönként vagy átmeneteken keresztül küldhetnek jelentéseket. A watchdog-jelentéskészítés ajánlott módja rendszeres időközönként, mivel a kód sokkal egyszerűbb, és kevésbé hajlamos a hibákra. A watchdogoknak úgy kell törekedniük, hogy a lehető legegyszerűbbek legyenek a helytelen jelentéseket kiváltó hibák elkerülése érdekében. A nem megfelelő *állapotú* jelentések az állapoton alapuló egészségügyi értékeléseket és forgatókönyveket érintik, beleértve a frissítéseket is. Helytelen *kifogástalan* állapotú jelentések a fürtben lévő problémákat elrejtik, ami nem kívánatos.

Rendszeres jelentéskészítés esetén a watchdog egy időzítővel is megvalósítható. Az időzítő visszahívásakor a watchdog megtekintheti az állapotot, és elküldheti a jelentést az aktuális állapot alapján. Nem kell megtekinteni, hogy melyik jelentést küldték korábban, vagy az üzenetkezelési feltételeit. Az állapotfigyelő ügyfélnek kötegelt logikája van, hogy segítse a teljesítményt. Amíg az állapotfigyelő ügyfél életben marad, a rendszer belsőleg próbálkozik addig, amíg az állapotfigyelő nem ismeri a jelentést, vagy a watchdog egy újabb jelentést hoz létre ugyanazzal az entitással, tulajdonsággal és forrással.

Az áttérésekről szóló jelentésekhez körültekintően kell kezelni az állapotot. A watchdog csak a feltételek megváltozásakor figyeli a feltételeket és a jelentéseket. Ennek a megközelítésnek a célja, hogy kevesebb jelentésre van szükség. A hátránya pedig az, hogy a watchdog logikája összetett. A watchdognak meg kell őriznie a feltételeket vagy a jelentéseket, hogy meg lehessen vizsgálni az állapot változásainak megállapítását. Feladatátvételkor körültekintően kell eljárni, de az állapotfigyelő szolgáltatásba még nem küldi el a jelentéseket. A sorozatszámnak egyre nagyobbnak kell lennie. Ha nem, a rendszer elutasítja a jelentéseket elavultként. Azon ritka esetekben, amikor adatvesztés keletkezik, szükség lehet a riporter és az állapotfigyelő állapot közötti szinkronizálásra.

Az áttérésekről szóló jelentés a szolgáltatások jelentéskészítésére is ésszerű, a vagy a rendszeren keresztül `Partition` `CodePackageActivationContext` . Ha a helyi objektum (replika vagy telepített szervizcsomag/telepített alkalmazás) el lett távolítva, a rendszer az összes jelentését is eltávolítja. Ez az automatikus tisztítás ellazítja a riporter és az állapotfigyelő közötti szinkronizálás szükségességét. Ha a jelentés szülő-vagy szülő-alkalmazásra vonatkozik, ügyelni kell a feladatátvételre, hogy elkerülje az elavult jelentéseket a Health Store-ban. A megfelelő állapot fenntartásához hozzá kell adni a logikát, és törölni kell a jelentést a tárolóból, ha már nincs rá szükség.

## <a name="implement-health-reporting"></a>Állapot-jelentéskészítés megvalósítása
Ha az entitás és a jelentés részletei egyértelműek, az állapotadatok küldését az API, a PowerShell vagy a REST használatával végezheti el.

### <a name="api"></a>API
Az API-n keresztül történő jelentéskészítéshez létre kell hoznia egy, az entitás típusára vonatkozó állapotjelentést, amelyre jelentést szeretne készíteni. Adja meg a jelentést az állapot-ügyfélnek. Alternatív megoldásként létrehozhat egy állapotadatokat, és átadhatja azt a jelentéskészítési módszerek és `Partition` `CodePackageActivationContext` az aktuális entitások jelentésének megfelelő állapotáról.

Az alábbi példa egy watchdog rendszeres jelentéskészítését mutatja be a fürtön belül. A watchdog ellenőrzi, hogy egy külső erőforrás elérhető-e egy csomóponton belülről. Az erőforrásra az alkalmazáson belüli szolgáltatási jegyzékfájl szükséges. Ha az erőforrás nem érhető el, az alkalmazáson belüli egyéb szolgáltatások továbbra is megfelelően működhetnek. A jelentést ezért 30 másodpercenként elküldjük a telepített szolgáltatáscsomag entitásban.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Állapotjelentés küldése a **Send-ServiceFabric *EntityType* állapotjelentés**.

Az alábbi példa egy csomóponton lévő CPU-értékekkel kapcsolatos rendszeres jelentéskészítést mutatja be. A jelentéseket 30 másodpercenként el kell juttatni, és két percen belül meg kell élniük. Ha lejárnak, a jelentéskészítő problémákba ütközik, így hiba esetén a csomópont kiértékelése megtörténik. Ha a processzor egy küszöbérték felett van, a jelentés állapota figyelmeztetés állapotú. Ha a processzor a beállított időpontnál továbbra is meghaladja a küszöbértéket, a rendszer hibát jelez. Ellenkező esetben a jelentéskészítő az OK állapotot küldi el.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

Az alábbi példa egy átmeneti figyelmeztetést jelent a replikán. Először beolvassa a partíció-azonosítót, majd annak a szolgáltatásnak a repliká-AZONOSÍTÓját, amely érdekli. Ezután jelentést küld a **PowershellWatcher** a **ResourceDependency** tulajdonságban. A jelentés csupán két percet vesz igénybe, és a rendszer automatikusan eltávolítja az áruházból.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
Állapotjelentés küldése a REST használatával a kívánt entitáshoz tartozó POST-kérésekkel, és az állapotjelentés leírását a törzsben kell megtenni. Tekintse meg például a REST- [fürt állapotáról szóló jelentések](/rest/api/servicefabric/report-the-health-of-a-cluster) vagy a [szolgáltatás állapotáról](/rest/api/servicefabric/report-the-health-of-a-service)szóló jelentések küldését ismertető témakört. Minden entitás támogatott.

## <a name="next-steps"></a>Következő lépések
Az állapotadatok alapján a Service Writers és a cluster/Application Administrators az információk felhasználásának módját is meggondolhatja. Például riasztásokat állíthatnak be az állapot alapján, hogy súlyos problémákba tudják fogni az kimaradások kiesése előtt. A rendszergazdák a javítási rendszerek beállításával automatikusan is kijavíthatják a problémákat.

[Az Service Fabric Health monitoring bemutatása](service-fabric-health-introduction.md)

[Service Fabric állapottal kapcsolatos jelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

[A szolgáltatás állapotának jelentése és ellenõrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Rendszerállapot-jelentések használata a hibaelhárításhoz](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[A szolgáltatások helyi figyelése és diagnosztikája](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric alkalmazás frissítése](service-fabric-application-upgrade.md)
