---
title: 'Alkalmazás frissítése: frissítési paraméterek'
description: A Service Fabric alkalmazás frissítésével kapcsolatos paramétereket ismerteti, beleértve a végrehajtandó állapot-ellenőrzéseket, valamint a frissítések automatikus visszavonására vonatkozó házirendeket.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 6b6116bf1188fcf191b2d672e6c698bb3c050e6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018477"
---
# <a name="application-upgrade-parameters"></a>Alkalmazásfrissítési paraméterek
Ez a cikk az Azure Service Fabric-alkalmazások frissítésekor alkalmazandó különböző paramétereket ismerteti. Az alkalmazás-frissítési paraméterek vezérlik a frissítés során alkalmazott időtúllépési és állapot-ellenőrzéseket, és meghatározzák azokat a házirendeket, amelyeket a frissítés sikertelensége esetén alkalmazni kell. Az alkalmazás paramétereinek frissítése a következő használatával:
- PowerShell
- Visual Studio
- SFCTL
- [REST](/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Az alkalmazások frissítései három, felhasználó által választható frissítési mód egyikével indíthatók el. Mindegyik üzemmód saját alkalmazás-paraméterekkel rendelkezik:
- Figyelt
- Nem figyelt automatikus
- Nem figyelt manuális

A szükséges és választható paraméterek az egyes szakaszokban az alábbiak szerint vannak leírva.

## <a name="visual-studio-and-powershell-parameters"></a>A Visual Studio és a PowerShell paraméterei

Service Fabric az alkalmazások PowerShell használatával történő frissítései a [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) parancsot használják. A frissítési mód kiválasztásához a **megfigyelt**, a **UnmonitoredAuto** vagy a **UnmonitoredManual** paramétert kell átadnia a [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade)értékre.

A Visual Studio Service Fabric az alkalmazás frissítési paramétereit a Visual Studio frissítési beállítások párbeszédpanelén állíthatja be. A Visual Studio verziófrissítési módja a **frissítési mód** legördülő lista használatával **figyelt**, **UnmonitoredAuto** vagy **UnmonitoredManual** van kiválasztva. További információ: [Service Fabric alkalmazás frissítésének konfigurálása a Visual Studióban](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Szükséges paraméterek
(PS = PowerShell, VS = Visual Studio)

| Paraméter | Érvényesség | Description |
| --- | --- | --- |
ApplicationName |PS| A frissítendő alkalmazás neve. Példák: Fabric:/VisualObjects, Fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|Annak az alkalmazásnak a verziója, amelyet a frissítési célok határoznak meg. |
FailureAction |PS, VS|Az engedélyezett értékek: **visszaállítás**, **manuális** és **érvénytelen**. Az a kompenzációs művelet, amelyet akkor kell végrehajtani, ha a *figyelt* frissítés figyelési házirendet vagy állapotházirend megsértését tapasztalja. <br>A **visszaállítás** azt jelzi, hogy a frissítés automatikusan visszaállítja a frissítés előtti verziót. <br>A **manuális** beállítás azt jelzi, hogy a frissítés a *UnmonitoredManual* frissítési módjára vált. <br>**Érvénytelen** érték azt jelzi, hogy a hiba művelet érvénytelen.|
Figyelt |PS|Azt jelzi, hogy a frissítési mód figyelt. Miután a parancsmag befejezte a frissítési tartomány frissítését, ha a frissítési tartomány és a fürt állapota megfelel az Ön által meghatározott állapot-szabályzatoknak, Service Fabric frissíti a következő frissítési tartományt. Ha a frissítési tartomány vagy fürt nem teljesíti az állapotfigyelő házirendeket, a frissítés sikertelen lesz, és Service Fabric visszaállítja a frissítési tartomány frissítését, vagy a megadott házirend alapján visszaállítja a manuális üzemmódot. Ez az ajánlott mód az alkalmazások éles környezetben történő verziófrissítésére. |
UpgradeMode | VS | Az engedélyezett értékek **figyelése** (alapértelmezett), **UnmonitoredAuto** vagy **UnmonitoredManual**. A részletekért tekintse meg a jelen cikk minden üzemmódjának PowerShell-paramétereit. |
UnmonitoredAuto | PS | Azt jelzi, hogy a frissítési mód nincs automatikusan figyelve. Service Fabric frissítési tartomány verziófrissítése után Service Fabric a következő frissítési tartományt frissíti, függetlenül az alkalmazás állapotával. Ez a mód éles környezetben nem ajánlott, és csak az alkalmazások fejlesztése során hasznos. |
UnmonitoredManual | PS | Azt jelzi, hogy a frissítési mód a nem figyelt manuális. Miután Service Fabric frissíti a frissítési tartományt, megvárja, hogy frissítse a következő frissítési tartományt a *resume-ServiceFabricApplicationUpgrade* parancsmag használatával. |

### <a name="optional-parameters"></a>Választható paraméterek

Az állapot-értékelési paraméterek megadása nem kötelező. Ha az állapot-értékelési feltételek nem lettek megadva a frissítés indításakor, Service Fabric az alkalmazás példányának ApplicationManifest.xml megadott alkalmazás-állapot házirendjét használja.

> [!div class="mx-tdBreakAll"]
> | Paraméter | Érvényesség | Description |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Megadja az alkalmazás paramétereinek felülbírálásait.<br>A PowerShell-alkalmazás paraméterei szórótábla név/érték párokként vannak megadva. Például: @ {"VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1"}.<br>A Visual Studio alkalmazás paramétereinek megadhatók az **alkalmazás-paraméterek fájl** mezőjében a közzététel Service Fabric alkalmazásban párbeszédpanelen.
> | Jóváhagyás |PS| Az engedélyezett értékek értéke **igaz** és **hamis**. A parancsmag futtatása előtt megerősítést kér. |
> | ConsiderWarningAsError |PS, VS |Az engedélyezett értékek értéke **igaz** és **hamis**. Az alapértelmezett érték **false (hamis**). Az alkalmazás állapotának kiértékelése során hibaként kezelheti az alkalmazáshoz tartozó figyelmeztetési állapottal kapcsolatos eseményeket. Alapértelmezés szerint a Service Fabric nem értékeli ki a figyelmeztetési állapottal kapcsolatos eseményeket (hibák), így a frissítés akkor is folytatódhat, ha figyelmeztető események vannak. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Meghatározza a figyelt frissítéshez használandó alapértelmezett szolgáltatástípus MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices formátumban megadott állapotát. Például az 5, 10, 15 a következő értékeket jelzi: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Az engedélyezett értékek értéke **igaz** és **hamis**. Azt jelzi, hogy a frissítési folyamat kihagyja a figyelmeztető üzenetet, és akkor is kényszeríti a frissítést, ha a verziószám nem módosult. Ez a helyi teszteléshez hasznos, de nem ajánlott éles környezetben használni, mivel a meglévő telepítést el kell távolítani, ami leállítja az időt és a potenciális adatvesztést. |
> | ForceRestart |PS, VS |Ha a szolgáltatási kód frissítése nélkül frissít egy konfigurációs vagy adatcsomagot, a szolgáltatás csak akkor indul újra, ha a ForceRestart tulajdonság értéke TRUE ( **igaz**). Ha a frissítés befejeződött, Service Fabric értesíti a szolgáltatást arról, hogy az új konfigurációs csomag vagy adatcsomag elérhető. A szolgáltatás feladata a módosítások alkalmazása. Ha szükséges, a szolgáltatás újra tud indítani. |
> | HealthCheckRetryTimeoutSec |PS, VS |Az az időtartam (másodpercben), ameddig Service Fabric továbbra is végrehajtja az állapot-értékelést a frissítés sikertelenként való deklarálása előtt. Az alapértelmezett érték 600 másodperc. Ez az időtartam a *HealthCheckWaitDurationSec* elérésekor kezdődik. Ezen a *HealthCheckRetryTimeout* belül Service Fabric az alkalmazás állapotának több állapotát is elvégezheti. Az alapértelmezett érték 10 perc, és megfelelően testre kell szabni az alkalmazáshoz. |
> | HealthCheckStableDurationSec |PS, VS |Az az időtartam (másodpercben), amely alapján ellenőrizhető, hogy az alkalmazás stabil-e, mielőtt továbblép a következő frissítési tartományra, vagy befejezi a frissítést. Ez a várakozási időtartam arra szolgál, hogy megakadályozza az állapotadatok nem észlelhető módosításait az állapot-ellenőrzés elvégzése után. Az alapértelmezett érték 120 másodperc, és megfelelően testre kell szabni az alkalmazáshoz. |
> | HealthCheckWaitDurationSec |PS, VS | Az a várakozási idő (másodpercben), amikor a frissítés befejeződött a frissítési tartományon, mielőtt Service Fabric kiértékeli az alkalmazás állapotát. Ez az időtartam azt is figyelembe veheti, hogy az alkalmazásnak Mikor kell futnia ahhoz, hogy kifogástalannak lehessen tekinteni. Ha az állapot-ellenőrzési művelet megfelel, a frissítési folyamat a következő frissítési tartományba kerül.  Ha az állapot-ellenőrzés sikertelen, Service Fabric várja a [UpgradeHealthCheckInterval](./service-fabric-cluster-fabric-settings.md#clustermanager) , mielőtt újra próbálkozik az állapot-ellenőrzéssel, amíg el nem éri a *HealthCheckRetryTimeoutSec* . Az alapértelmezett és ajánlott érték 0 másodperc. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |Az alapértelmezett és az ajánlott érték 0. Adja meg a telepített alkalmazások maximális számát (lásd az [állapot szakaszt](service-fabric-health-introduction.md)), amely akkor lehet sérült, ha az alkalmazás nem megfelelő állapotba kerül, és a frissítés sikertelen lesz. Ez a paraméter határozza meg az alkalmazás állapotát a csomóponton, és segít észlelni a hibákat a frissítés során. Az alkalmazás replikái jellemzően terheléselosztást kapnak a másik csomópontra, ami lehetővé teszi, hogy az alkalmazás Kifogástalan állapotba kerüljön, ami lehetővé teszi a frissítés folytatását. A szigorú *MaxPercentUnhealthyDeployedApplications* -állapot megadásával a Service Fabric gyorsan észlelheti az alkalmazáscsomag problémáit, és elősegítheti a Fail Fast frissítését. |
> | MaxPercentUnhealthyServices |PS, VS |A *DefaultServiceTypeHealthPolicy* és a *ServiceTypeHealthPolicyMap* paraméter. Az alapértelmezett és az ajánlott érték 0. Adja meg azon szolgáltatások maximális számát az alkalmazás-példányban, amelyek nem Kifogástalan állapotba kerülnek, mielőtt az alkalmazás nem megfelelő állapotba kerül, és a frissítés meghiúsul. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |A *DefaultServiceTypeHealthPolicy* és a *ServiceTypeHealthPolicyMap* paraméter. Az alapértelmezett és az ajánlott érték 0. A szolgáltatásban található partíciók maximális számát határozza meg, amely nem kifogástalan állapotú, mielőtt a szolgáltatás nem kifogástalannak minősül. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |A *DefaultServiceTypeHealthPolicy* és a *ServiceTypeHealthPolicyMap* paraméter. Az alapértelmezett és az ajánlott érték 0. Itt adhatja meg, hogy a partícióban legfeljebb hány replika lehet sérült, mielőtt a partíció nem kifogástalannak minősül. |
> | ServiceTypeHealthPolicyMap | PS, VS | A szolgáltatás típusához tartozó szolgáltatások állapotának kiértékeléséhez használt állapotházirend. A kivonatoló tábla bemenete a következő formátumban történik: @ {"ServiceTypeName": "MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices"} például: @ {"ServiceTypeName01" = "5, 10, 5"; "ServiceTypeName02" = "5, 5, 5"} |
> | TimeoutSec | PS, VS | Meghatározza a művelet időtúllépési időtartamát másodpercben. |
> | UpgradeDomainTimeoutSec |PS, VS |Egyetlen frissítési tartomány verziófrissítésének maximális ideje (másodpercben). Ha elérte ezt az időkorlátot, a frissítés leáll, és a *FailureAction* beállítása alapján folytatódik. Az alapértelmezett érték soha nem (végtelen), és megfelelően testre kell szabni az alkalmazáshoz. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Másodpercben mérve.<br>**Állapot nélküli szolgáltatás**– egyetlen frissítési tartományon belül Service Fabric megpróbálja biztosítani, hogy a szolgáltatás további példányai elérhetők legyenek. Ha a célként megadott példányok száma egynél több, Service Fabric vár több példány rendelkezésre állására, legfeljebb egy maximális időtúllépési értékre. Ez az időkorlát a *UpgradeReplicaSetCheckTimeoutSec* tulajdonsággal van megadva. Ha az időtúllépés lejár, Service Fabric a frissítéssel folytatja a frissítést, a szolgáltatási példányok számától függetlenül. Ha a célként megadott példányok száma egy, Service Fabric nem vár, és azonnal folytatja a frissítést.<br><br>**Állapot-nyilvántartó szolgáltatás**– egyetlen frissítési tartományon belül Service Fabric megkísérli biztosítani, hogy a replikakészlet kvórumtal legyen elfoglalva. Service Fabric várja, amíg a kvórum elérhetővé válik, legfeljebb a *UpgradeReplicaSetCheckTimeoutSec* tulajdonság által megadott maximális időtúllépési értékig. Ha az időtúllépés lejár, Service Fabric a frissítéssel folytatja a frissítést, a kvórumtól függetlenül. Ez a beállítás soha nem (végtelen) értékre van beállítva, ha a továbbítás előre, a 1200 másodperc pedig a visszagörgetéskor történik. |
> | UpgradeTimeoutSec |PS, VS |Időtúllépés (másodpercben), amely a teljes frissítésre vonatkozik. Ha elérte ezt az időtúllépést, a frissítés leáll, és a rendszer elindítja a *FailureAction* . Az alapértelmezett érték soha nem (végtelen), és megfelelően testre kell szabni az alkalmazáshoz. |
> | WhatIf | PS | Az engedélyezett értékek értéke **igaz** és **hamis**. Bemutatja, mi történne a parancsmag futtatásakor. A parancsmag nem fut. |

Az *MaxPercentUnhealthyServices*, a *MaxPercentUnhealthyPartitionsPerService* és a *MaxPercentUnhealthyReplicasPerPartition* feltételek a szolgáltatástípus alapján adhatók meg egy alkalmazás példánya számára. Ezen paraméterek felhasználónkénti beállítása lehetővé teszi, hogy egy alkalmazás különböző értékelési házirendekkel rendelkező különböző szolgáltatásokat tartalmazzon. Például egy állapot nélküli átjáró szolgáltatás típusa rendelkezhet olyan *MaxPercentUnhealthyPartitionsPerService* , amely eltér egy adott alkalmazás-példány állapot-nyilvántartó szolgáltatásának típusától.

## <a name="sfctl-parameters"></a>SFCTL paraméterek

Service Fabric az alkalmazások frissítése a Service Fabric parancssori felülettel a [sfctl alkalmazás frissítése](./service-fabric-sfctl-application.md#sfctl-application-upgrade) paranccsal, a következő kötelező és választható paraméterekkel együtt.

### <a name="required-parameters"></a>Szükséges paraméterek

| Paraméter | Leírás |
| --- | --- |
| alkalmazás-azonosító  |A frissítendő alkalmazás azonosítója. <br> Ez általában az alkalmazás teljes neve a "Fabric:" URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric:/SajátPr/App1", az alkalmazás identitása a korábbi verziókban a következő: "SajátPr \~ App1", 6.0 + és "SajátPr/App1".|
alkalmazás verziója |Annak az alkalmazásnak a verziója, amelyet a frissítési célok határoznak meg.|
parameters  |Az alkalmazás frissítésekor alkalmazandó felülbírálások JSON-kódolású listája.|

### <a name="optional-parameters"></a>Választható paraméterek

| Paraméter | Leírás |
| --- | --- |
alapértelmezett – szolgáltatás – állapot – házirend | Az alapértelmezés szerint a szolgáltatástípus állapotának kiértékeléséhez használt állapotfigyelő [JSON](/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) -kódolású specifikációja. Alapértelmezés szerint a Térkép üres. |
hiba – művelet | Az engedélyezett értékek: **visszaállítás**, **manuális** és **érvénytelen**. Az a kompenzációs művelet, amelyet akkor kell végrehajtani, ha a *figyelt* frissítés figyelési házirendet vagy állapotházirend megsértését tapasztalja. <br>A **visszaállítás** azt jelzi, hogy a frissítés automatikusan visszaállítja a frissítés előtti verziót. <br>A **manuális** beállítás azt jelzi, hogy a frissítés a *UnmonitoredManual* frissítési módjára vált. <br>**Érvénytelen** érték azt jelzi, hogy a hiba művelet érvénytelen.|
kényszerített újraindítás | Ha a szolgáltatási kód frissítése nélkül frissít egy konfigurációs vagy adatcsomagot, a szolgáltatás csak akkor indul újra, ha a ForceRestart tulajdonság értéke TRUE ( **igaz**). Ha a frissítés befejeződött, Service Fabric értesíti a szolgáltatást arról, hogy az új konfigurációs csomag vagy adatcsomag elérhető. A szolgáltatás feladata a módosítások alkalmazása. Ha szükséges, a szolgáltatás újra tud indítani. |
állapot-ellenőrzési-újrapróbálkozás – időtúllépés | Az állapot kiértékelésének ideje, ha az alkalmazás vagy a fürt állapota nem kifogástalan, mielőtt a rendszer végrehajtja a *FailureAction* . A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. Alapértelmezett: PT0H10M0S. |
állapot-ellenőrzési – stabil – időtartam | Az az időtartam, ameddig az alkalmazásnak vagy a fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományba kerül. A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. Alapértelmezett: PT0H2M0S. |
állapot-ellenőrzési várakozás időtartama | Az a várakozási idő, ameddig a frissítési tartomány elvégzése után meg kell várni az Állapotházirendek alkalmazását. A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. Alapértelmezett: 0.|
maximális – nem megfelelő állapotú alkalmazások | Az alapértelmezett és az ajánlott érték 0. Adja meg a telepített alkalmazások maximális számát (lásd az [állapot szakaszt](service-fabric-health-introduction.md)), amely akkor lehet sérült, ha az alkalmazás nem megfelelő állapotba kerül, és a frissítés sikertelen lesz. Ez a paraméter határozza meg az alkalmazás állapotát a csomóponton, és segít észlelni a hibákat a frissítés során. Az alkalmazás replikái jellemzően terheléselosztást kapnak a másik csomópontra, ami lehetővé teszi, hogy az alkalmazás Kifogástalan állapotba kerüljön, ami lehetővé teszi a frissítés folytatását. A szigorú max. nem kifogástalan *állapotú alkalmazások* állapotának megadásával a Service Fabric gyorsan észlelheti az alkalmazáscsomag problémáit, és elősegítheti a Fail Fast frissítését. 0 és 100 közötti számként jelenik meg. |
mód | Az engedélyezett értékek **figyelése**, **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Az alapértelmezett érték a **UnmonitoredAuto**. Ezekhez az értékekhez tekintse meg a Visual Studio és a PowerShell *szükséges paramétereit* ismertető szakaszt.|
replika-set-pipa-időtúllépés |Másodpercben mérve. <br>**Állapot nélküli szolgáltatás**– egyetlen frissítési tartományon belül Service Fabric megpróbálja biztosítani, hogy a szolgáltatás további példányai elérhetők legyenek. Ha a célként megadott példányok száma egynél több, Service Fabric vár több példány rendelkezésre állására, legfeljebb egy maximális időtúllépési értékre. Ezt az időtúllépést a *replika-set-ellenőrzés-időtúllépés* tulajdonsággal határozhatja meg. Ha az időtúllépés lejár, Service Fabric a frissítéssel folytatja a frissítést, a szolgáltatási példányok számától függetlenül. Ha a célként megadott példányok száma egy, Service Fabric nem vár, és azonnal folytatja a frissítést.<br><br>**Állapot-nyilvántartó szolgáltatás**– egyetlen frissítési tartományon belül Service Fabric megkísérli biztosítani, hogy a replikakészlet kvórumtal legyen elfoglalva. Service Fabric várja, amíg a kvórum elérhetővé válik, a maximális időtúllépési értékig (amelyet a *replika-set-timeout* tulajdonság határoz meg). Ha az időtúllépés lejár, Service Fabric a frissítéssel folytatja a frissítést, a kvórumtól függetlenül. Ez a beállítás soha nem (végtelen) értékre van beállítva, ha a továbbítás előre, a 1200 másodperc pedig a visszagörgetéskor történik. |
szolgáltatás – állapot – szabályzat | A JSON-kódolású leképezés a szolgáltatástípus-állapotra vonatkozó házirend-típus alapján. Alapértelmezés szerint a Térkép üres. [Paraméter JSON-formátuma.](/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). Az "érték" részhez tartozó JSON a következőt tartalmazza: **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService** és **MaxPercentUnhealthyReplicasPerPartition**. A paraméterek leírását a Visual Studio és a PowerShell választható paraméterek szakasza tartalmazza.
timeout | Meghatározza a művelet időtúllépési időtartamát másodpercben. Alapértelmezett: 60. |
frissítés – tartomány – időtúllépés | Az egyes frissítési tartományok befejezésének időtartamát a *FailureAction* végrehajtása előtt kell végrehajtani. A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. Az alapértelmezett érték soha nem (végtelen), és megfelelően testre kell szabni az alkalmazáshoz. Alapértelmezett: P10675199DT02H48M 05.4775807 S. |
frissítés – időtúllépés | Az egyes frissítési tartományok befejezésének időtartamát a *FailureAction* végrehajtása előtt kell végrehajtani. A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. Az alapértelmezett érték soha nem (végtelen), és megfelelően testre kell szabni az alkalmazáshoz. Alapértelmezett: P10675199DT02H48M 05.4775807 S.|
Figyelmeztetés – hiba | Az engedélyezett értékek értéke **igaz** és **hamis**. Az alapértelmezett érték **false (hamis**). Jelzőként adható át. Az alkalmazás állapotának kiértékelése során hibaként kezelheti az alkalmazáshoz tartozó figyelmeztetési állapottal kapcsolatos eseményeket. Alapértelmezés szerint a Service Fabric nem értékeli ki a figyelmeztetési állapottal kapcsolatos eseményeket (hibák), így a frissítés akkor is folytatódhat, ha figyelmeztető események vannak. |

## <a name="next-steps"></a>Következő lépések
[Az alkalmazás a Visual Studióval történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítésén a Visual Studióval.

[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti az alkalmazás frissítésén a PowerShell használatával.

Ha a [linuxon Service FABRIC CLI-vel frissíti az alkalmazást](service-fabric-application-lifecycle-sfctl.md#upgrade-application) , az alkalmazás a Service Fabric parancssori felület használatával is frissíthető.

[Az alkalmazás frissítése Service Fabric Eclipse beépülő modullal](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Az alkalmazások frissítését az [adatszerializálás](service-fabric-application-upgrade-data-serialization.md)használatának megismerésével teheti meg.

Ismerje meg, hogyan használhatja a speciális funkciókat az alkalmazás frissítéséhez a [speciális témakörökre](service-fabric-application-upgrade-advanced.md)való hivatkozással.

Az alkalmazások frissítéseinek [hibaelhárításával](service-fabric-application-upgrade-troubleshooting.md)kapcsolatos gyakori problémák elhárítása.
