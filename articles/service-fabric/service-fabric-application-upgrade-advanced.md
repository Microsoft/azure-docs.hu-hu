---
title: Speciális alkalmazás-frissítési témakörök
description: Ez a cikk a Service Fabric alkalmazások frissítésével kapcsolatos néhány speciális témakört ismerteti.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6604300328f2d243077ba341a9028221438dce9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98792048"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric alkalmazás frissítése: speciális témakörök

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Szolgáltatások típusának hozzáadása vagy eltávolítása az alkalmazás frissítése során

Ha új szolgáltatástípus van hozzáadva egy közzétett alkalmazáshoz egy frissítés részeként, akkor a rendszer hozzáadja az új szolgáltatás típusát az üzembe helyezett alkalmazáshoz. Egy ilyen frissítés nem befolyásolja az alkalmazás részét képező szolgáltatási példányok egyikét sem, de a hozzáadott szolgáltatástípus egy példányát létre kell hozni ahhoz, hogy az új szolgáltatástípus aktív legyen (lásd: [New-ServiceFabricService](/powershell/module/servicefabric/new-servicefabricservice)).

Hasonlóképpen, a szolgáltatások típusai a frissítés részeként eltávolíthatók az alkalmazásokból. A frissítés folytatása előtt azonban el kell távolítani az összes szolgáltatás összes szolgáltatási példányát (lásd: [Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice)).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>Az állapot nélküli szolgáltatás tervezett leállási idejének elkerülése

A tervezett állapot nélküli leállások esetében, például az alkalmazás/fürt frissítése vagy a csomópont inaktiválása esetén a kapcsolatok eltűnnek, mivel a rendszer eltávolítja a megjelenő végpontot a példány leállása után, ami erőteljes kapcsolat bezárását eredményezi.

Ennek elkerüléséhez konfigurálja a *RequestDrain* szolgáltatást úgy, hogy a szolgáltatás konfigurációjában egy *példányhoz tartozó késleltetési idő* hozzáadásával engedélyezi a meglévő kérelmeket a fürtből a kihelyezett végpontokon való leeresztéshez. Ez úgy érhető el, mert a rendszer eltávolítja az állapot nélküli példány által hirdetett végpontot, *mielőtt* a késleltetés a példány bezárása előtt elindul. Ez a késleltetés lehetővé teszi, hogy a meglévő kérések zökkenőmentesen le legyenek ürítve, mielőtt a példány ténylegesen leáll. Az ügyfelek értesítést kapnak a végpont változásáról egy visszahívási függvény által a késés megkezdésének időpontjában, hogy újra fel tudják oldani a végpontot, és ne küldjön új kéréseket a lekérdezett példányra. Ezek a kérelmek olyan ügyfelektől származnak, amelyek [fordított proxyt](./service-fabric-reverseproxy.md) használnak, vagy a szolgáltatási végpont feloldási API-ját az értesítési modellel ([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)) használják a végpontok frissítéséhez.

### <a name="service-configuration"></a>Szolgáltatáskonfiguráció

A késést többféleképpen is konfigurálhatja a szolgáltatás oldalán.

 * **Új szolgáltatás létrehozásakor** a következőket kell megadnia `-InstanceCloseDelayDuration` :

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * A **szolgáltatásnak az alkalmazás jegyzékfájljának Alapértelmezések szakaszában megadott meghatározása során** rendelje hozzá a (z) `InstanceCloseDelayDurationSeconds` tulajdonságot:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Meglévő szolgáltatás frissítésekor** a következőket kell megadnia `-InstanceCloseDelayDuration` :

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **Meglévő szolgáltatás ARM-sablonon keresztüli létrehozásakor vagy frissítésekor** a következőt kell megadnia: `InstanceCloseDelayDuration` (a támogatott API-verzió minimális verziója: 2019-11-01-előnézet):

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2019-11-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>Ügyfél-konfiguráció

Ha értesítést szeretne kapni egy végpont megváltozásakor, az ügyfeleknek vissza kell regisztrálniuk a visszahívást, lásd: [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
A változási értesítés arra utal, hogy a végpontok megváltoztak, az ügyfélnek újra fel kell oldania a végpontokat, és nem szabad azokat a végpontokat használni, amelyek nem jelennek meg többé, mert hamarosan leállnak.

### <a name="optional-upgrade-overrides"></a>Választható frissítési felülbírálások

A szolgáltatás alapértelmezett késleltetési időtartamának beállítása mellett a késést is felülbírálhatja az alkalmazás/fürt frissítése során ugyanazzal a ( `InstanceCloseDelayDurationSec` ) kapcsolóval:

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

A felülbírált késleltetési időtartam csak a meghívott frissítési példányra vonatkozik, és más módon nem változtatja meg az egyes szolgáltatás-késleltetési konfigurációkat. Ezzel a beállítással például késleltetheti az `0` előre konfigurált frissítési késések kihagyása érdekében.

> [!NOTE]
> * A lecsapolt kérések beállításai nem fogják tudni megakadályozni, hogy az Azure Load Balancer új kéréseket küldjön a leeresztő végpontoknak.
> * A panasz-alapú megoldási mechanizmus nem eredményezi a kérések zökkenőmentes kiürítését, mivel hiba után kivált egy szolgáltatás feloldására. A korábban leírtaknak megfelelően a [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)használatával kell kijavítani a végpontok módosítására vonatkozó értesítésekre való előfizetést.
> * A beállítások nem teljesülnek, ha a frissítés egy negatív hatással van, azaz Ha a rendszer nem indít le replikákat a frissítés során.
>
>

> [!NOTE]
> Ez a funkció konfigurálható a meglévő szolgáltatásokban Update-ServiceFabricService parancsmag vagy a fentiekben említett ARM-sablon használatával, ha a 7.1.XXX vagy újabb verzió van megadva.
>
>

## <a name="manual-upgrade-mode"></a>Manuális frissítési mód

> [!NOTE]
> A *figyelt* frissítési mód használata minden Service Fabric frissítéshez ajánlott.
> A *UnmonitoredManual* frissítési módját csak a sikertelen vagy felfüggesztett verziófrissítések esetében érdemes figyelembe venni. 
>
>

*Figyelt* módban Service Fabric alkalmazza az állapot-szabályzatokat, hogy az alkalmazás kifogástalan állapotú legyen a frissítés folyamata során. Ha az Állapotházirendek megsértették, a rendszer felfüggeszti a frissítést, vagy automatikusan visszaállítja a megadott *FailureAction* függően.

*UnmonitoredManual* módban az alkalmazás rendszergazdája teljes mértékben szabályozhatja a frissítés előrehaladását. Ez a mód akkor hasznos, ha egyéni állapot-értékelési házirendeket alkalmaz, vagy nem hagyományos frissítéseket végez az állapot figyelésének mellőzéséhez (például az alkalmazás már adatvesztésben van). Az ebben a módban futó frissítés az egyes UD befejezését követően felfüggeszti magát, és a [resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade)használatával explicit módon folytatnia kell azt. Ha a frissítés fel van függesztve, és készen áll a felhasználó folytatására, a frissítési állapota *RollforwardPending* fog megjelenni (lásd: [UpgradeState](/dotnet/api/system.fabric.applicationupgradestate)).

Végezetül a *UnmonitoredAuto* mód hasznos lehet a gyors verziófrissítési iterációk végrehajtásához a szolgáltatás fejlesztése vagy tesztelése során, mivel nincs szükség felhasználói beavatkozásra, és nincs kiértékelve az alkalmazás állapotára vonatkozó házirend.

## <a name="upgrade-with-a-diff-package"></a>Frissítés diff csomaggal

A teljes alkalmazáscsomag kiépítés helyett a verziófrissítések olyan diff csomagok kitelepítésével is elvégezhetők, amelyek csak a frissített Code/config/adatcsomagokat tartalmazzák, valamint a teljes alkalmazás-jegyzékfájlt és a teljes szolgáltatási jegyzékfájlt. Az alkalmazáscsomag teljes telepítéséhez csak az alkalmazás fürtbe történő telepítésére van szükség. A következő frissítések lehetnek teljes alkalmazáscsomag vagy diff csomagok.  

A rendszer automatikusan lecseréli az alkalmazáscsomag egyik olyan diff-csomagjának az alkalmazási jegyzékfájljában vagy a szolgáltatás jegyzékfájljában szereplő hivatkozást, amely nem található az alkalmazás-csomagban.

A diff csomagok használatának forgatókönyvei a következők:

* Ha nagyméretű alkalmazáscsomag van, amely több szolgáltatás-jegyzékfájlra és/vagy több kódra, konfigurációs csomagra vagy adatcsomagra hivatkozik.
* Ha van olyan központi telepítési rendszer, amely közvetlenül az alkalmazás-létrehozási folyamatból hozza létre az összeállítási elrendezést. Ebben az esetben annak ellenére, hogy a kód nem változott, az újonnan létrehozott szerelvények eltérő ellenőrzőösszeget kapnak. A teljes alkalmazáscsomag használatához frissítenie kell a verziót az összes programkód-csomagon. A diff csomag használata esetén csak a módosított fájlok és a verziószámot tartalmazó jegyzékfájlok adhatók meg.

Ha egy alkalmazás a Visual Studióval lett frissítve, a rendszer automatikusan közzétesz egy diff-csomagot. Ha manuálisan szeretne létrehozni egy diff csomagot, az alkalmazás-jegyzékfájlt és a szolgáltatás-jegyzékfájlokat frissíteni kell, de csak a módosított csomagokat kell belefoglalni a végső alkalmazáscsomagba.

Tegyük fel például, hogy a következő alkalmazással (a könnyű megértéshez megadott verziószámokkal) kezdődik:

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Tegyük fel, hogy a service1 csak a kód csomagját szeretné frissíteni a diff csomag használatával. A frissített alkalmazás a következő verziót módosítja:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Ebben az esetben frissíti az alkalmazás-jegyzékfájlt a 2.0.0 és a service1 szolgáltatás jegyzékfájlját, hogy tükrözze a csomag frissítését. Az alkalmazáscsomag mappája a következő szerkezettel rendelkezhet:

```text
app1/
  service1/
    code/
```

Más szóval hozzon létre egy teljes alkalmazáscsomag-csomagot, majd távolítsa el az összes olyan Code/config/adatcsomag-mappát, amelyhez a verzió nem változott.

## <a name="upgrade-application-parameters-independently-of-version"></a>Alkalmazás paramétereinek frissítése a verziótól függetlenül

Időnként érdemes módosítani egy Service Fabric alkalmazás paramétereit a jegyzékfájl verziójának módosítása nélkül. Ez kényelmesen elvégezhető a **Start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShell-parancsmaggal a **-ApplicationParameter** jelző használatával. Tegyük fel, hogy Service Fabric alkalmazás a következő tulajdonságokkal rendelkezik:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Most frissítse az alkalmazást a **Start-ServiceFabricApplicationUpgrade** parancsmag használatával. Ez a példa egy figyelt frissítést mutat be, de a nem figyelt frissítés is használható. Ha meg szeretné tekinteni a parancsmag által elfogadott jelzők teljes leírását, tekintse meg az [Azure Service Fabric PowerShell-modul referenciáját](/powershell/module/servicefabric/start-servicefabricapplicationupgrade#parameters) .

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

A frissítés után ellenőrizze, hogy az alkalmazás rendelkezik-e a frissített paraméterekkel és ugyanazzal a verzióval:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Alkalmazások verziófrissítésének visszaállítása

A frissítések a három mód (*figyelt*, *UnmonitoredAuto* vagy *UnmonitoredManual*) egyikében továbbíthatók, de csak *UnmonitoredAuto* vagy *UnmonitoredManual* módban állíthatók vissza. A *UnmonitoredAuto* mód visszaállítása ugyanúgy működik, mint a *UpgradeReplicaSetCheckTimeout* alapértelmezett értéke – lásd az [alkalmazás frissítési paramétereit](service-fabric-application-upgrade-parameters.md). A *UnmonitoredManual* mód visszaállítása ugyanúgy működik, mint a továbbítás – a visszaállítás az összes UD befejezése után felfüggeszti magát, és a [resume-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade) használatával explicit módon folytatnia kell a visszaállítást.

A visszaállítások automatikusan indíthatók, ha a *figyelt* módban lévő, a *FailureAction* *visszaállítással* rendelkező frissítés állapot-házirendjei megsérülnek (lásd az [alkalmazás frissítési paramétereit](service-fabric-application-upgrade-parameters.md)), vagy explicit módon használják a [Start-ServiceFabricApplicationRollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback).

A visszaállítás során a *UpgradeReplicaSetCheckTimeout* és a mód értéke bármikor módosítható a [Update-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/update-servicefabricapplicationupgrade)használatával.

## <a name="next-steps"></a>Következő lépések
[Az alkalmazás a Visual Studióval történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítésén a Visual Studióval.

[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti az alkalmazás frissítésén a PowerShell használatával.

Annak szabályozása, hogy az alkalmazás hogyan legyen [frissítve a frissítési paraméterek](service-fabric-application-upgrade-parameters.md)használatával.

Az alkalmazások frissítését az [adatszerializálás](service-fabric-application-upgrade-data-serialization.md)használatának megismerésével teheti meg.

Az alkalmazások frissítéseinek [hibaelhárításával](service-fabric-application-upgrade-troubleshooting.md)kapcsolatos gyakori problémák elhárítása.
