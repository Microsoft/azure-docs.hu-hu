---
title: Azure Cloud Services-alkalmazások konvertálása Service Fabric
description: Ez az útmutató összehasonlítja Cloud Services webes és feldolgozói szerepköröket, és Service Fabric állapot nélküli szolgáltatásokat biztosít a Cloud Servicesról a Service Fabricre való Migrálás céljából.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: cf593f793aabf2a0650684ed8d02fe02d756ec2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575737"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Útmutató a webes és feldolgozói szerepkörök Service Fabric állapot nélküli szolgáltatásokhoz való átalakításához
Ez a cikk azt ismerteti, hogyan telepítheti át Cloud Services webes és feldolgozói szerepköreit Service Fabric állapot nélküli szolgáltatások számára. Ez a legegyszerűbb áttelepítési útvonal Cloud Servicesról Service Fabric olyan alkalmazások számára, amelyek általános architektúrája nagyjából azonos marad.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Cloud Service-projekt Service Fabric alkalmazás-projekthez
 A Cloud Service-projekt és a Service Fabric alkalmazás-projekt hasonló struktúrával rendelkezik, és mindkettő az alkalmazás üzembe helyezési egységét képviseli, azaz mindegyiknek meg kell határoznia az alkalmazás futtatásához üzembe helyezett teljes csomagot. A felhőalapú szolgáltatási projekt egy vagy több webes vagy feldolgozói szerepkört tartalmaz. Hasonlóképpen, egy Service Fabric alkalmazás-projekt tartalmaz egy vagy több szolgáltatást. 

A különbség az, hogy a Cloud Service Project a virtuálisgép-telepítéssel párosítja az alkalmazás üzembe helyezését, így a virtuális gép konfigurációs beállításait tartalmazza, míg a Service Fabric alkalmazás-projekt csak olyan alkalmazást határoz meg, amely egy Service Fabric-fürtben lévő meglévő virtuális gépek készletére lesz telepítve. Maga a Service Fabric-fürt csak egyszer lesz telepítve, vagy egy Resource Manager-sablonon vagy a Azure Portalon keresztül, és több Service Fabric alkalmazás is üzembe helyezhető.

![Service Fabric és Cloud Services projekt összehasonlítása][3]

## <a name="worker-role-to-stateless-service"></a>Feldolgozói szerepkör az állapot nélküli szolgáltatáshoz
Elméletileg a feldolgozói szerepkör állapot nélküli munkaterhelést képvisel, ami azt jelenti, hogy a munkaterhelés minden példánya azonos, és a kérések bármikor átirányíthatók bármelyik példányra. Az egyes példányok nem várnak az előző kérelemre. Azt, hogy a munkaterhelést a (z) rendszer felügyeli-e, külső állapotú tároló (például Azure Table Storage vagy Azure Cosmos DB) kezeli. Service Fabric az ilyen típusú számítási feladatokat egy állapot nélküli szolgáltatás jelképezi. A feldolgozói szerepkör Service Fabricre való áttelepítésének legegyszerűbb megközelítése a feldolgozói szerepkör kódjának állapot nélküli szolgáltatásba történő átváltásával hajtható végre.

![Feldolgozói szerepkör az állapot nélküli szolgáltatáshoz][4]

## <a name="web-role-to-stateless-service"></a>Az állapot nélküli szolgáltatás webes szerepköre
A feldolgozói szerepkörhöz hasonlóan a webes szerepkörök állapot nélküli számítási feladatokat is jelentenek, így a fogalmi módon is leképezhetők egy Service Fabric állapot nélküli szolgáltatáshoz. A webes szerepköröktől eltérően azonban a Service Fabric nem támogatja az IIS-t. Webalkalmazás webes szerepkörről állapot nélküli szolgáltatásba való áttelepítéséhez először olyan webes keretrendszerre kell áttérnie, amely saját üzemeltetésű, és nem függ az IIS-től vagy a System. Web-től, például ASP.NET Core 1.

| **Alkalmazás** | **Támogatott** | **Áttelepítési útvonal** |
| --- | --- | --- |
| ASP.NET Web Forms |No |Átalakítás ASP.NET Core 1 MVC-re |
| ASP.NET, MVC |Áttelepítéssel |Frissítés ASP.NET Core 1 MVC-ra |
| ASP.NET, webes API |Áttelepítéssel |Saját üzemeltetésű kiszolgáló használata vagy ASP.NET Core 1 |
| ASP.NET Core 1 |Yes |N/A |

## <a name="entry-point-api-and-lifecycle"></a>Belépési pont API és életciklusa
A feldolgozói szerepkör és a Service Fabric szolgáltatás API-jai hasonló belépési pontokat kínálnak: 

| **Belépési pont** | **Feldolgozói szerepkör** | **Service Fabric szolgáltatás** |
| --- | --- | --- |
| Feldolgozás |`Run()` |`RunAsync()` |
| Virtuális gép indítása |`OnStart()` |N/A |
| Virtuális gép leállítása |`OnStop()` |N/A |
| Ügyfél-kérelmekhez tartozó figyelő megnyitása |N/A |<ul><li> `CreateServiceInstanceListener()` állapot nélküli</li><li>`CreateServiceReplicaListener()` állapot-nyilvántartó</li></ul> |

### <a name="worker-role"></a>Feldolgozói szerepkör
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Állapot nélküli szolgáltatás Service Fabric
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Mindkettő rendelkezik egy elsődleges "Futtatás" felülbírálással, amelyben megkezdheti a feldolgozást. A Service Fabric- `Run` szolgáltatások `Start` `Stop` egyetlen belépési ponttal kombinálják a-t és a-t `RunAsync` . A szolgáltatásnak a kezdéskor kell megkezdenie a munkát `RunAsync` , és a `RunAsync` metódus CancellationToken jelzése szerint leáll. 

A feldolgozói szerepkörök és a Service Fabric szolgáltatások életciklusa és élettartama között számos lényeges különbség van:

* **Életciklus:** A legnagyobb különbség az, hogy a feldolgozói szerepkör egy virtuális gép, ezért életciklusa a virtuális GÉPHEZ van kötve, amely a virtuális gép indításakor és leállításakor bekövetkező eseményeket tartalmazza. A Service Fabric szolgáltatásnak van olyan életciklusa, amely elkülönül a virtuális gép életciklusával, így nem tartalmazza azokat az eseményeket, amikor a gazdagép virtuális gépe vagy a gép leáll, mert nem kapcsolódnak egymáshoz.
* **Élettartam:** Egy feldolgozói szerepkör példánya újraindul, ha a `Run` metódus kilép. A `RunAsync` Service Fabric szolgáltatás metódusa azonban a befejezéshez is futtatható, és a szolgáltatási példány marad. 

Service Fabric egy opcionális kommunikációs beállítási pontot biztosít az ügyfelek kéréseinek figyelésére szolgáló szolgáltatásokhoz. A RunAsync és a kommunikáció belépési pontja nem kötelező felülbírálások Service Fabric-szolgáltatásokban – a szolgáltatás dönthet úgy, hogy csak az ügyfelek kéréseinek figyelését, vagy csak feldolgozási ciklust futtat, vagy mindkettőt, ezért a RunAsync metódus a szolgáltatás példányának újraindítása nélkül is lehetséges, mivel az ügyfelek kéréseinek figyelése továbbra is lehetséges.

## <a name="application-api-and-environment"></a>Alkalmazás API és környezet
A Cloud Services környezeti API az aktuális virtuálisgép-példányra vonatkozó információkat és funkciókat, valamint a többi virtuálisgép-szerepköri példányra vonatkozó információkat tartalmaz. Service Fabric a futtatókörnyezetével kapcsolatos információkat biztosít, és néhány információt arról, hogy a szolgáltatás jelenleg melyik csomóponton fut. 

| **Környezeti feladat** | **Felhőszolgáltatások** | **Service Fabric** |
| --- | --- | --- |
| Konfigurációs beállítások és változási értesítés |`RoleEnvironment` |`CodePackageActivationContext` |
| Helyi tárterület |`RoleEnvironment` |`CodePackageActivationContext` |
| Végpont adatai |`RoleInstance` <ul><li>Aktuális példány: `RoleEnvironment.CurrentRoleInstance`</li><li>Egyéb szerepkörök és példányok: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` aktuális csomópont-címnek</li><li>`FabricClient` és `ServicePartitionResolver` a szolgáltatás végpontjának felderítéséhez</li> |
| Környezeti emuláció |`RoleEnvironment.IsEmulated` |N/A |
| Egyidejű módosítási esemény |`RoleEnvironment` |N/A |

## <a name="configuration-settings"></a>Konfigurációs beállítások
A Cloud Services konfigurációs beállításai virtuálisgép-szerepkörre vannak beállítva, és az adott virtuálisgép-szerepkör összes példányára érvényesek. Ezek a beállítások a ServiceConfiguration. *. cscfg-fájlokban beállított kulcs-érték párok, amelyek közvetlenül a RoleEnvironment keresztül érhetők el. Service Fabric a beállítások a virtuális gépek helyett az egyes szolgáltatásokra és az egyes alkalmazásokra vonatkoznak, mivel egy virtuális gép több szolgáltatást és alkalmazást is képes tárolni. A szolgáltatás három csomagból áll:

* **Kód:** tartalmazza a szolgáltatás végrehajtható fájljait, bináris fájljait, DLL-fájljait és minden más, a szolgáltatás futtatásához szükséges fájlt.
* **Config:** egy szolgáltatás összes konfigurációs fájlja és beállítása.
* **Adatai:** a szolgáltatáshoz társított statikus adatfájlok.

Ezek a csomagok egymástól függetlenül telepíthetők és frissíthetők. A Cloud Serviceshoz hasonlóan a konfigurációs csomag programozott módon, API-n keresztül érhető el, és az események elérhetők, hogy értesítsék a konfigurációs csomag megváltozásának szolgáltatásáról. Egy Settings.xml fájl használható a kulcs-érték konfigurációhoz és a programozott eléréshez, hasonlóan egy App.config fájl Alkalmazásbeállítások szakaszához. A Cloud Servicestól eltérően azonban egy Service Fabric konfigurációs csomag bármilyen formátumú konfigurációs fájlt tartalmazhat, legyen az XML, JSON, YAML vagy egyéni bináris formátum. 

### <a name="accessing-configuration"></a>Hozzáférés a konfigurációhoz
#### <a name="cloud-services"></a>Cloud Services
A ServiceConfiguration. *. cscfg konfigurációs beállításai a használatával érhetők el `RoleEnvironment` . Ezek a beállítások globálisan elérhetők az összes szerepkör-példány számára ugyanabban a felhőalapú szolgáltatás-telepítésben.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Minden szolgáltatás saját egyéni konfigurációs csomaggal rendelkezik. A fürtben található összes alkalmazás által elérhető globális konfigurációs beállításokhoz nincs beépített mechanizmus. Ha Service Fabric speciális Settings.xml konfigurációs fájlját használja egy konfigurációs csomagban, a Settings.xml értékei felül is írhatók az alkalmazás szintjén, így lehetséges az alkalmazás szintű konfigurációs beállítások végrehajtása.

A konfigurációs beállítások az egyes szolgáltatási példányokon belül a szolgáltatáson keresztül érhetők el `CodePackageActivationContext` .

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Konfigurációs frissítési események
#### <a name="cloud-services"></a>Cloud Services
Az `RoleEnvironment.Changed` esemény az összes szerepkör-példány értesítésére szolgál, ha változás történik a környezetben, például a konfiguráció módosításakor. Ez a konfigurációs frissítések felhasználására szolgál a szerepkör-példányok újrahasznosítása vagy a munkavégző folyamat újraindítása nélkül.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
A szolgáltatási kód, a konfiguráció és az adattípusok mindhárom csomagja tartalmaz olyan eseményeket, amelyek értesítik a szolgáltatási példányt a csomagok frissítésekor, hozzáadásakor vagy eltávolításakor. A szolgáltatás több különböző típusú csomagot is tartalmazhat. Előfordulhat például, hogy egy szolgáltatás több konfigurációs csomaggal rendelkezik, és mindegyik külön verzióban és frissíthető. 

Ezek az események elérhetők a szolgáltatási csomagok változásainak a szolgáltatási példány újraindítása nélkül történő felhasználásához.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Indítási feladatok
Az indítási feladatok olyan műveletek, amelyeket az alkalmazás elindítása előtt hajtanak végre. A telepítési parancsfájlok emelt szintű jogosultságokkal történő futtatására általában egy indítási feladat használatos. Mindkét Cloud Services és Service Fabric támogatja az indítási feladatokat. A fő különbség az, hogy Cloud Servicesban az indítási feladat egy virtuális géphez van kötve, mert egy szerepkör-példány része, míg Service Fabric az indítási feladat egy olyan szolgáltatáshoz van kötve, amely nem kötődik egy adott virtuális géphez.

| Service Fabric | Cloud Services |
| --- | --- |
| Konfigurációs hely |ServiceDefinition. csdef |
| Jogosultságok |"Limited" vagy "emelt" |
| Alkalmazás-előkészítés |"egyszerű", "háttér", "előtér" |

### <a name="cloud-services"></a>Cloud Services
Cloud Services egy indítási belépési pont szerepkört konfigurál a ServiceDefinition. csdef. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
Service Fabric az indítási belépési pont konfigurálása szolgáltatásként ServiceManifest.xmlban:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Megjegyzés a fejlesztési környezetről
A Cloud Services és a Service Fabric egyaránt integrálva van a Visual Studióval és a Project sablonjaival, és támogatja a helyi és az Azure-beli hibakeresést, konfigurálást és üzembe helyezést. A Cloud Services és a Service Fabric is biztosít helyi fejlesztési futtatókörnyezetet. A különbség az, hogy míg a Cloud Service fejlesztői futtatókörnyezet emulálja az Azure-környezetet, amelyen fut, Service Fabric nem használ emulátort – a teljes Service Fabric futtatókörnyezetet használja. A helyi fejlesztési gépen futtatott Service Fabric környezet ugyanaz a környezet, amely éles környezetben fut.

## <a name="next-steps"></a>Következő lépések
További információ a Service Fabric Reliable Servicesről és az Cloud Services és Service Fabric alkalmazás-architektúra közötti alapvető különbségekről, hogy megtudja, hogyan használhatja ki a Service Fabric funkciók teljes készletét.

* [Első lépések a Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Elméleti útmutató a Cloud Services és Service Fabric közötti különbségekhez](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
