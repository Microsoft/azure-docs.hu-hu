---
title: Az alkalmazások frissítéseinek hibaelhárítása
description: Ez a cikk a Service Fabric-alkalmazások frissítésével és megoldásával kapcsolatos gyakori problémákat ismerteti.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009347"
---
# <a name="troubleshoot-application-upgrades"></a>Alkalmazásfrissítések hibaelhárítása

Ez a cikk az Azure Service Fabric-alkalmazások frissítésével és megoldásával kapcsolatos gyakori problémákat ismerteti.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Sikertelen alkalmazások frissítésének hibáinak megoldása

Ha egy frissítés meghiúsul, a **Get-ServiceFabricApplicationUpgrade** parancs kimenete további információkat tartalmaz a hiba hibakereséséhez.  A következő lista meghatározza a további információk használatát:

1. Azonosítsa a hiba típusát.
2. Azonosítsa a hiba okát.
3. Egy vagy több hibás összetevő elkülönítése a további vizsgálathoz.

Ezek az információk akkor érhetők el, ha a Service Fabric észleli a hibát, függetlenül attól, hogy a **FailureAction** visszaállíthatja vagy felfüggesztheti a frissítést.

### <a name="identify-the-failure-type"></a>A hiba típusának azonosítása

A **Get-ServiceFabricApplicationUpgrade** kimenetében a **FailureTimestampUtc** azonosítja azt az időbélyeget (UTC), amelyen a Service Fabric és a **FailureAction** a frissítési hibát észlelte. A **FailureReason** a hiba három lehetséges magas szintű okának egyikét azonosítja:

1. UpgradeDomainTimeout – azt jelzi, hogy egy adott frissítési tartomány túl sokáig tartott a befejezéshez és a **UpgradeDomainTimeout** lejárt.
2. OverallUpgradeTimeout – azt jelzi, hogy a teljes frissítés túl sokáig tartott, és a **UpgradeTimeout** lejárt.
3. HealthCheck – azt jelzi, hogy a frissítési tartomány frissítése után az alkalmazás nem megfelelő állapotú marad a megadott állapotházirendek és **HealthCheckRetryTimeout** szerint.

Ezek a bejegyzések csak akkor jelennek meg a kimenetben, ha a frissítés meghiúsul, és a rendszer elindítja a visszaállítást. A hiba típusától függően további információk jelennek meg.

### <a name="investigate-upgrade-timeouts"></a>Frissítési időtúllépések vizsgálata

A frissítési időtúllépési hibák leggyakrabban a szolgáltatás rendelkezésre állásával kapcsolatos problémák okozzák. A bekezdést követő kimenet jellemző azokra a frissítésekre, amelyekben a szolgáltatás replikái vagy példányai nem indulnak el az új kód verziójában. A **UpgradeDomainProgressAtFailure** mező a hiba időpontjában rögzíti a függőben lévő frissítésre vonatkozó összes művelet pillanatképét.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

Ebben a példában a frissítés nem sikerült a frissítési tartomány *MYUD1* , és a két partíció (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* és *4b43f4d8-b26b-424e-9307-7a7a62e79750*) beragadt. A partíciók elakadtak, mert a futtatókörnyezet nem tudta elsődleges replikákat (*WaitForPrimaryPlacement*) elhelyezni a *Csomópont1* és a *csomópont4*.

A **Get-ServiceFabricNode** parancs használatával ellenőrizheti, hogy ez a két csomópont a frissítési tartomány *MYUD1* van-e. A *UpgradePhase* mondja a *PostUpgradeSafetyCheck*, ami azt jelenti, hogy ezek a biztonsági ellenőrzések a frissítési tartomány összes csomópontjának verziófrissítése után történnek. Az összes információ az alkalmazás kódjának új verziójával kapcsolatos lehetséges problémára mutat. A leggyakoribb problémák a megnyitási vagy előléptetési szolgáltatás hibái az elsődleges kód elérési útjain.

A *PreUpgradeSafetyCheck* *UpgradePhase* azt jelenti, hogy problémák léptek fel a frissítési tartomány előkészítése előtt. Ebben az esetben a leggyakoribb probléma az elsődleges kód elérési útjaiból való Bezárás vagy lefokozás során felmerülő szolgáltatási hibák.

Az aktuális **UpgradeState** *RollingBackCompleted*, ezért az eredeti frissítést egy visszaállítási **FailureAction** kell végrehajtani, amely a hiba miatt automatikusan visszaállítja a frissítést. Ha az eredeti frissítést manuális **FailureAction** hajtották végre, a frissítés Ehelyett felfüggesztett állapotba kerül, hogy lehetővé tegye az alkalmazás élő hibakeresését.

Ritka esetekben előfordulhat, hogy a **UpgradeDomainProgressAtFailure** mező üres, ha a teljes frissítés időtúllépést okoz, ha a rendszer az aktuális frissítési tartomány összes munkafolyamatát befejezi. Ha ez történik, próbálja meg növelni a **UpgradeTimeout** és a **UpgradeDomainTimeout** frissítési paramétereinek értékét, majd próbálja megismételni a frissítést.

### <a name="investigate-health-check-failures"></a>Állapot-ellenőrzési hibák vizsgálata

Az állapot-ellenőrzési hibák a frissítési tartomány összes csomópontjának frissítését és az összes biztonsági ellenőrzés átadását követően különböző problémák léphetnek fel. A bekezdést követő kimenet egy sikertelen állapot-ellenőrzés miatti frissítési hibára jellemző. A **nemmegfelelőállapotúértékelések** mező a frissítés időpontjában sikertelen állapot-ellenőrzéseket tartalmazó pillanatképet rögzít a megadott [állapotházirend](service-fabric-health-introduction.md)alapján.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Az állapot-ellenőrzési hibák kivizsgálása először a Service Fabric Health Model ismeretét igényli. De még a részletes megértés nélkül is láthatjuk, hogy a következő két szolgáltatás állapota nem kifogástalan: *Fabric:/DemoApp/Svc3* és *Fabric:/DemoApp/Svc2*, valamint a hiba állapotáról szóló jelentések ("InjectedFault" ebben az esetben). Ebben a példában a négy szolgáltatás közül kettő nem kifogástalan állapotú, ami nem éri el az alapértelmezett 0%-os sérült (*MaxPercentUnhealthyServices*).

A frissítés a frissítés megkezdése után a **FailureAction** manuális megadásával lett felfüggesztve. Ez a mód lehetővé teszi, hogy a hibás állapotú élő rendszerek vizsgálatát még a művelet megkezdése előtt megvizsgáljuk.

### <a name="recover-from-a-suspended-upgrade"></a>Helyreállítás felfüggesztett frissítésből

Visszaállítási **FailureAction** esetén nincs szükség helyreállításra, mert a frissítés automatikusan Visszagörgeti a hibát. A manuális **FailureAction** számos helyreállítási lehetőség közül választhat:

1.  visszaállítás indítása
2. A frissítés hátralévő részének áthaladása manuálisan
3. A figyelt frissítés folytatása

A **Start-ServiceFabricApplicationRollback** parancs bármikor használható az alkalmazás visszagörgetésének megkezdéséhez. A parancs sikeres visszaküldése után a visszaállítási kérelem regisztrálva van a rendszeren, és hamarosan elindul.

A **resume-ServiceFabricApplicationUpgrade** parancs használatával folytathatja a frissítés fennmaradó részét manuálisan, egyszerre egy frissítési tartományt. Ebben a módban csak a biztonsági ellenőrzéseket hajtja végre a rendszer. Nem végeznek további állapot-ellenőrzést. Ez a parancs csak akkor használható, ha a *UpgradeState* a *RollingForwardPending* mutatja, ami azt jelenti, hogy az aktuális frissítési tartomány befejezte a frissítést, de a következő egy nem indult el (függőben).

Az **Update-ServiceFabricApplicationUpgrade** parancs használatával folytathatja a figyelt frissítést a biztonsági és állapot-ellenőrzésekkel.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

A frissítés az utolsó felfüggesztést követően a frissítési tartományból folytatódik, és ugyanazokat a frissítési paramétereket és állapotházirend-szabályzatokat használja, mint korábban. Ha szükséges, az előző kimenetben látható frissítési paraméterek és állapot-házirendek bármelyike módosítható ugyanabban a parancsban, amikor a frissítés folytatódik. Ebben a példában a frissítés figyelt módban folytatódik, és a paraméterek és az állapotfigyelő házirendek változatlanok maradtak.

## <a name="further-troubleshooting"></a>További hibaelhárítás

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>A Service Fabric nem követi a megadott állapotházirend-szabályzatot

1. lehetséges ok:

Service Fabric lefordítja az összes százalékos arányt a tényleges számú entitásra (például replikák, partíciók és szolgáltatások) az állapot kiértékeléséhez, és mindig teljes entitásokra kerekít. Ha például a maximális *MaxPercentUnhealthyReplicasPerPartition* 21%, és öt replika van, akkor Service Fabric legfeljebb két sérült replikát (azaz) tesz lehetővé `Math.Ceiling (5*0.21)` . Ezért az állapotfigyelő házirendeket ennek megfelelően kell beállítani.

2. lehetséges ok:

Az állapotfigyelő házirendek a teljes szolgáltatások és nem meghatározott szolgáltatási példányok százalékos arányában vannak meghatározva. Például a frissítés előtt, ha egy alkalmazásnak négy szolgáltatási példánya van, B, C és D, ahol a D szolgáltatás nem kifogástalan állapotú, de kevés hatással van az alkalmazásra. Szeretnénk figyelmen kívül hagyni az ismert, nem kifogástalan állapotú szolgáltatást a frissítés során, és a *MaxPercentUnhealthyServices* paramétert 25%-ra kell beállítani, feltéve, hogy csak az a, B és C értéknek kell kifogástalannak lennie.

A frissítés során azonban a D állapota Kifogástalan lehet, amíg a C nem megfelelő állapotú lesz. A frissítés még sikeres lenne, mert a szolgáltatások csak 25%-a nem kifogástalan állapotú. Előfordulhat azonban, hogy váratlan hibákat okoz, mert a C nem várt állapotú a D helyett. Ebben az esetben a D-t az A, B és C típustól eltérő szolgáltatástípusként kell modellezni. Mivel az állapot-szabályzatok szolgáltatási típus szerint vannak megadva, a különböző nem kifogástalan állapotú százalékos küszöbértékek eltérő szolgáltatásokra alkalmazhatók. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Nem határoztam meg az alkalmazás frissítésére vonatkozó állapotfigyelő szabályzatot, de a frissítés továbbra is meghiúsul egy olyan időtúllépés miatt, amely soha nem volt megadva

Ha a frissítési kérelem nem ad meg állapot-szabályzatokat, azok az alkalmazás aktuális verziójának *ApplicationManifest.xml* származnak. Ha például az X alkalmazást az 1,0-es verzióról az 2,0-es verzióra frissíti, a rendszer az 1,0-as verzióban megadott Application Health-szabályzatokat használja. Ha a frissítéshez másik állapotházirend használata szükséges, akkor a szabályzatot az alkalmazás-frissítési API-hívás részeként kell megadni. Az API-hívás részeként megadott szabályzatok csak a frissítés során érvényesek. A frissítés befejezése után a rendszer a *ApplicationManifest.xmlban* megadott házirendeket használja.

### <a name="incorrect-time-outs-are-specified"></a>Helytelen időtúllépés van megadva

Lehet, hogy már megértette, mi történik, ha az időtúllépés beállítása inkonzisztens. Előfordulhat például, hogy egy *UpgradeTimeout* kisebb, mint a *UpgradeDomainTimeout*. A válasz az, hogy a rendszer hibaüzenetet ad vissza. A rendszer hibákat ad vissza, ha a *UpgradeDomainTimeout* kisebb, mint a *HealthCheckWaitDuration* és a *HealthCheckRetryTimeout* összege, vagy ha a *UpgradeDomainTimeout* értéke kisebb, mint a *HealthCheckWaitDuration* és a *HealthCheckStableDuration* összege.

### <a name="my-upgrades-are-taking-too-long"></a>A frissítések túl sokáig tartanak

A frissítés befejezésének ideje a megadott állapot-ellenőrzéstől és időtúllépéstől függ. Az állapot-ellenőrzés és az időtúllépés attól függ, hogy mennyi ideig tart az alkalmazás másolása, üzembe helyezése és stabilizálása. Ha túl agresszív az időtúllépés, akkor a frissítés nem jelent nagyobb hibát, ezért javasoljuk, hogy a konzervatív és a hosszabb időkorláttal kezdjen el.

Íme egy gyors frissítő, hogy az időtúllépés hogyan működjön együtt a frissítési időpontokkal:

A frissítési tartomány frissítései nem hajthatók végre gyorsabban, mint a *HealthCheckWaitDuration*  +  *HealthCheckStableDuration*.

A frissítési hiba nem fordulhat elő gyorsabban, mint a *HealthCheckWaitDuration*  +  *HealthCheckRetryTimeout*.

A frissítési tartomány frissítési idejét a *UpgradeDomainTimeout* korlátozza.  Ha a *HealthCheckRetryTimeout* és a *HealthCheckStableDuration* értéke nem nulla, és az alkalmazás állapota továbbra is folyamatosra vált, a frissítés végül a *UpgradeDomainTimeout*-on történik. A *UpgradeDomainTimeout* megkezdi az aktuális frissítési tartomány verziófrissítésének megkezdését.

## <a name="next-steps"></a>Következő lépések

[Az alkalmazás a Visual Studióval történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítésén a Visual Studióval.

[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti az alkalmazás frissítésén a PowerShell használatával.

Annak szabályozása, hogy az alkalmazás hogyan legyen [frissítve a frissítési paraméterek](service-fabric-application-upgrade-parameters.md)használatával.

Az alkalmazások frissítését az [adatszerializálás](service-fabric-application-upgrade-data-serialization.md)használatának megismerésével teheti meg.

Ismerje meg, hogyan használhatja a speciális funkciókat az alkalmazás frissítéséhez a [speciális témakörökre](service-fabric-application-upgrade-advanced.md)való hivatkozással.