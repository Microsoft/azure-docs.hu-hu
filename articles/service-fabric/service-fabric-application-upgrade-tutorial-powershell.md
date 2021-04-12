---
title: Az alkalmazás frissítése Service Fabric a PowerShell használatával
description: Ez a cikk végigvezeti egy Service Fabric alkalmazás üzembe helyezésének és a kód módosításának, valamint a PowerShell használatával történő frissítésének folyamatán.
ms.topic: conceptual
ms.date: 8/5/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b038f4a41ee02960a02c4445b65eb2360a75761
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98792031"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Service Fabric alkalmazás frissítése a PowerShell használatával
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

A leggyakrabban használt és ajánlott frissítési módszer a figyelt működés közbeni frissítés.  Az Azure Service Fabric állapot-szabályzatok alapján figyeli az alkalmazás frissítésének állapotát. A frissítési tartomány (UD) frissítése után Service Fabric kiértékeli az alkalmazás állapotát, és vagy a következő frissítési tartományba kerül, vagy az állapotfigyelő házirendektől függően sikertelen lesz a frissítés.

A figyelt alkalmazások frissítése a felügyelt vagy a natív API-k, a PowerShell, az Azure CLI, a Java vagy a REST használatával végezhető el. A Visual Studióval végzett frissítéssel kapcsolatos utasításokért lásd: [az alkalmazás frissítése a Visual Studióval](service-fabric-application-upgrade-tutorial.md).

Service Fabric figyelt működés közbeni frissítésekkel az alkalmazás rendszergazdája beállíthatja, hogy a Service Fabric milyen állapot-értékelési házirendet használ annak megállapításához, hogy az alkalmazás kifogástalan-e. Emellett a rendszergazda konfigurálhatja az állapot kiértékelésének sikertelensége esetén végrehajtandó műveletet (például automatikus visszaállítást hajt végre). Ez a szakasz végigvezeti a PowerShellt használó SDK-minták egyikének figyelt frissítésén. 

> [!NOTE]
> Az [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s nem őrződnek meg az alkalmazás verziófrissítése során. Az aktuális alkalmazás paramétereinek megőrzése érdekében a felhasználónak először le kell kérnie a paramétereket, és át kell adnia őket a frissítési API-híváshoz, például az alábbihoz:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>1. lépés: a vizuális objektumok mintájának létrehozása és üzembe helyezése
Hozza létre és tegye közzé az alkalmazást úgy, hogy a jobb gombbal az alkalmazás projektre, a **VisualObjectsApplication,** majd a **közzétételi** parancsra kattint.  További információ: [Service Fabric alkalmazás-frissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md).  Azt is megteheti, hogy a PowerShell használatával helyezi üzembe az alkalmazást.

> [!NOTE]
> Mielőtt bármelyik Service Fabric parancs használható a PowerShellben, először a parancsmag használatával kell csatlakoznia a fürthöz `Connect-ServiceFabricCluster` . Hasonlóképpen feltételezhető, hogy a fürt már be van állítva a helyi gépen. Tekintse meg a [Service Fabric fejlesztői környezet beállítását](service-fabric-get-started.md)ismertető cikket.
> 
> 

Miután létrehozta a projektet a Visual Studióban, használhatja a [copy-ServiceFabricApplicationPackage PowerShell-](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) parancsot az alkalmazáscsomag másolásához a lemezképtárolóba. Ha az alkalmazáscsomag helyileg szeretné ellenőrizni, használja a [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) parancsmagot. A következő lépés az alkalmazás regisztrálása a Service Fabric futtatókörnyezetben a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) parancsmag használatával. A következő lépés az alkalmazás egy példányának elindítása a [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication) parancsmag használatával.  Ez a három lépés hasonló ahhoz, hogy a Visual Studióban a **Deploy** menüpontot használja.  Miután a kiépítés befejeződött, törölje a másolt alkalmazáscsomag a rendszerkép-tárból a felhasznált erőforrások csökkentése érdekében.  Ha már nincs szükség az alkalmazás típusára, a regisztrációt ugyanezen okból törölni kell. További információ: [alkalmazások telepítése és eltávolítása a PowerShell használatával](service-fabric-application-upgrade-tutorial-powershell.md) .

Most [a Service Fabric Explorer használatával megtekintheti a fürtöt és az alkalmazást](service-fabric-visualizing-your-cluster.md). Az alkalmazás rendelkezik egy webszolgáltatással, amely az Internet Explorerben megnyitható a címsorba való beírásával `http://localhost:8081/visualobjects` .  Látnia kell néhány lebegő vizuális objektumot a képernyőn.  Emellett a [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) használatával is megtekintheti az alkalmazás állapotát.

## <a name="step-2-update-the-visual-objects-sample"></a>2. lépés: a vizuális objektumok mintájának frissítése
Észreveheti, hogy az 1. lépésben üzembe helyezett verziónál a vizualizációs objektumok nem forognak. Frissítjük az alkalmazást arra a szintre, ahol a vizualizáció objektumok is forognak.

Válassza ki a VisualObjects. ActorService projektet a VisualObjects-megoldáson belül, majd nyissa meg a StatefulVisualObjectActor. cs fájlt. A fájlon belül navigáljon a metódushoz `MoveObject` , a megjegyzésekhez `this.State.Move()` és a megjegyzésekhez `this.State.Move(true)` . Ez a módosítás a szolgáltatás frissítése után elforgatja az objektumokat.

Emellett frissítenie kell a **VisualObjects. ActorService** projekthez tartozó *ServiceManifest.xml* fájlt (a PackageRoot alatt). Frissítse a *CodePackage* és a szolgáltatás verzióját 2,0-re, valamint a *ServiceManifest.xml* fájl megfelelő soraira.
A jegyzékfájl szerkesztése lehetőségre kattintva használhatja a Visual Studio- *fájlok* módosítása lehetőséget, miután a jobb gombbal a megoldásra kattint, hogy megváltoztassa a jegyzékfájlt.

A módosítások elvégzése után a jegyzékfájlnak a következőhöz hasonlóan kell kinéznie (a Kiemelt részek a módosításokat mutatják):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Most a **VisualObjects** -megoldás alatt található **VisualObjects** -projekt alatt található *ApplicationManifest.xml* fájl frissítve lett a **VisualObjects. ActorService** projekt 2,0-es verziójára. Emellett az alkalmazás verziószáma a 1.0.0.0 2.0.0.0 frissül. A *ApplicationManifest.xmlnak* a következő kódrészlethez hasonlóan kell kinéznie:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Most hozza létre a projektet úgy, hogy kiválasztja a **ActorService** projektet, majd a jobb gombbal kattint, és kiválasztja a **Létrehozás** lehetőséget a Visual Studióban. Ha az **összes Újraépítés** lehetőséget választja, akkor frissítenie kell az összes projekt verzióját, mivel a kód módosult volna. Ezután csomagolja a frissített alkalmazást úgy, hogy a jobb gombbal a **_VisualObjectsApplication_ _ elemre kattint *, és kiválasztja a Service Fabric menüt, és kiválasztja az _* csomagot**. Ez a művelet létrehoz egy üzembe helyezhető alkalmazáscsomag-csomagot.  A frissített alkalmazás készen áll a telepítésre.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>3. lépés: az állapotfigyelő házirendek és a frissítési paraméterek kiválasztása
Ismerkedjen meg az [alkalmazás frissítési paramétereivel](service-fabric-application-upgrade-parameters.md) és a [frissítési folyamattal](service-fabric-application-upgrade.md) , hogy jól megértse a különböző frissítési paramétereket, időtúllépéseket és az alkalmazott állapotra vonatkozó kritériumokat. Ebben az útmutatóban a szolgáltatás állapotának kiértékelése feltétel az alapértelmezett (és az ajánlott) értékre van állítva, ami azt jelenti, hogy az összes szolgáltatásnak és példánynak *kifogástalannak* kell lennie a frissítés után.  

Azonban növeljük a *HealthCheckStableDuration* 180 másodpercre (így a szolgáltatások kifogástalanak legalább 120 másodpercig, mielőtt a frissítés továbblép a következő frissítési tartományra).  Állítsuk be a *UpgradeDomainTimeout* 1200 másodpercre, míg a *UpgradeTimeout* értéke 3000 másodperc.

Végül állítsa be a *UpgradeFailureAction* a visszaállításhoz. Ehhez a beállításhoz Service Fabric szükséges az alkalmazás korábbi verzióra való visszavonása, ha a frissítés során problémák merülnek fel. Így a frissítés indításakor (a 4. lépésben) a következő paraméterek vannak megadva:

FailureAction = visszaállítás

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>4. lépés: az alkalmazás előkészítése a frissítéshez
Az alkalmazás már elkészült, és készen áll a frissítésre. Ha rendszergazdaként nyit meg egy PowerShell-ablakot, és beírja a [Get-ServiceFabricApplication-](/powershell/module/servicefabric/get-servicefabricapplication)t, akkor azt is tudnia kell, hogy az alkalmazás típusa 1.0.0.0 a telepített **VisualObjects** .  

Az alkalmazáscsomag a következő relatív elérési úton tárolódik, ahol kibonthatja a Service Fabric SDK- *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Meg kell keresnie egy "csomag" mappát abban a könyvtárban, ahol az alkalmazáscsomag tárolva van. Ellenőrizze az időbélyegeket, és győződjön meg arról, hogy a legújabb build (Előfordulhat, hogy az elérési utakat is megfelelően kell módosítania).

Most másolja át a frissített alkalmazáscsomag Service Fabric Lemezképtárolóba (ahol az alkalmazáscsomag tárolása Service Fabric). A *ApplicationPackagePathInImageStore* paraméter tájékoztatja Service Fabric, hogy hol található az alkalmazáscsomag. A frissített alkalmazást a "VisualObjects v2"-ben tesszük a \_ következő paranccsal (Előfordulhat, hogy az elérési utakat megfelelően újra módosítani kell).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

A következő lépés az alkalmazás regisztrálása a Service Fabric használatával, amely a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) paranccsal végezhető el:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Ha az előző parancs nem sikerül, valószínű, hogy az összes szolgáltatás újraépítésére van szükség. A 2. lépésben említetteknek megfelelően előfordulhat, hogy frissítenie kell a webszolgáltatás verzióját is.

Javasoljuk, hogy az alkalmazás sikeres regisztrálása után távolítsa el az alkalmazáscsomag-csomagot.  A rendszerkép-tárolóban lévő alkalmazáscsomag törlése a rendszererőforrásokat felszabadítja.  A nem használt alkalmazáscsomag megőrzése a lemezes tárolást és az alkalmazások teljesítményével kapcsolatos problémákat eredményez.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>5. lépés: az alkalmazás frissítésének elindítása
Most már készen áll az alkalmazás frissítésének elindítására a [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) parancs használatával:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Az alkalmazás neve megegyezik a *ApplicationManifest.xml* fájlban leírt névvel. Service Fabric ezt a nevet használja a frissítendő alkalmazás azonosítására. Ha úgy állítja be az időtúllépést, hogy túl rövidek legyenek, a probléma állapotára utaló hibaüzenet jelenhet meg. Tekintse át a hibaelhárítási szakaszt, vagy növelje az időtúllépést.

Most, ahogy az alkalmazás frissítése folytatódik, a Service Fabric Explorer használatával vagy a [Get-ServiceFabricApplicationUpgrade PowerShell-](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) paranccsal figyelheti azt: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Néhány percen belül az előző PowerShell-paranccsal kapott állapotnak meg kell határoznia, hogy az összes frissítési tartomány frissítve lett (befejezve). Azt is meg kell találni, hogy a böngészőablakban lévő vizuális objektumok elforgatása megkezdődött!

A 2. verzióról a 3-as verzióra vagy a 2. verzióra való frissítést gyakorlatként is kipróbálhatja. A 2. verzióról az 1-es verzióra való áttérés is frissítésnek tekintendő. Az időkorláttal és az állapottal foglalkozó szabályzatokkal megismerheti őket. Az Azure-fürtön történő üzembe helyezéskor a paramétereket megfelelően be kell állítani. A konzervatív időkorlátot érdemes beállítani.

## <a name="next-steps"></a>Következő lépések
[Az alkalmazás a Visual Studióval történő frissítése](service-fabric-application-upgrade-tutorial.md) végigvezeti egy alkalmazás frissítésén a Visual Studióval.

Annak szabályozása, hogy az alkalmazás hogyan legyen [frissítve a frissítési paraméterek](service-fabric-application-upgrade-parameters.md)használatával.

Az alkalmazások frissítését az [adatszerializálás](service-fabric-application-upgrade-data-serialization.md)használatának megismerésével teheti meg.

Ismerje meg, hogyan használhatja a speciális funkciókat az alkalmazás frissítéséhez a [speciális témakörökre](service-fabric-application-upgrade-advanced.md)való hivatkozással.

Az alkalmazások frissítéseinek [hibaelhárításával](service-fabric-application-upgrade-troubleshooting.md)kapcsolatos gyakori problémák elhárítása.
