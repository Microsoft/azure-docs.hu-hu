---
title: RunToCompletion szemantikai Service Fabric
description: A Service Fabric RunToCompletion szemantikai leírását ismerteti.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000266"
---
# <a name="runtocompletion"></a>Futtatás befejezésig

Az 7,1-es verziótól kezdődően a Service Fabric támogatja a [tárolók][containers-introduction-link] és a [vendég végrehajtható][guest-executables-introduction-link] alkalmazások **RunToCompletion** szemantikai használatát. Ezek a szemantika lehetővé teszik az olyan alkalmazások és szolgáltatások számára, amelyek egy adott feladatot és kilépést végeznek, ezzel szemben mindig az alkalmazásokat és szolgáltatásokat futtatják.

A cikk folytatása előtt javasoljuk, hogy ismerkedjen meg a [Service Fabric alkalmazás modelljével][application-model-link] és a [Service Fabric üzemeltetési modellel][hosting-model-link].

> [!NOTE]
> A RunToCompletion szemantikai használata jelenleg nem támogatott a [Reliable Services][reliable-services-link] programozási modellel írt szolgáltatások esetében.
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion szemantika és specifikáció
A RunToCompletion szemantikai **ExecutionPolicy** [a ServiceManifest importálásakor][application-and-service-manifests-link]is megadhatók. A megadott házirendet a rendszer a ServiceManifest alkotó összes CodePackages örökli. A következő ApplicationManifest.xml kódrészlet példaként szolgál.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
A **ExecutionPolicy** a következő két attribútumot teszi lehetővé:
* **Típus:** a **RunToCompletion** jelenleg csak az attribútum megengedett értéke.
* **Újraindítás:** Ez az attribútum határozza meg a szervizcsomagot tartalmazó CodePackages alkalmazott újraindítási szabályzatot, meghibásodás esetén. A **nem nulla kilépési kóddal** kilépő CodePackage sikertelennek tekintendő. Az attribútum megengedett értékei a **OnFailure** , a **OnFailure** pedig **soha nem** az alapértelmezett érték.

Ha az újraindítási szabályzat **OnFailure** értékre van állítva, ha bármelyik CodePackage meghibásodik **(nullától eltérő kilépési kód)**, a rendszer újraindul, és az ismételt hibák között visszakerül. Ha a **CodePackage nem sikerül**, az újraindítási házirend beállítása sikertelen, a DeployedServicePackage telepítési állapota **meghiúsult** , de más CodePackages is engedélyezett a végrehajtás folytatásához. Ha a szervizcsomagot tartalmazó összes CodePackages sikeresen befejeződött **(kilépési kód: 0)**, akkor a DeployedServicePackage telepítési állapota **RanToCompletion** lesz megjelölve. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Példa RunToCompletion szemantika használatával

Nézzük meg a teljes példát a RunToCompletion szemantika használatával.

> [!IMPORTANT]
> Az alábbi példa azt feltételezi, hogy a [Windows-tároló alkalmazások Service Fabric és a Docker használatával történő][containers-getting-started-link]létrehozásának ismerete.
>
> Ez a példa mcr.microsoft.com/windows/nanoserver:1809 hivatkozik. A Windows Server-tárolók nem kompatibilisek a gazdagép operációs rendszerének összes verziójával. További információ: a [Windows-tároló verziójának kompatibilitása](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Az alábbi ServiceManifest.xml a tárolókat jelképező két CodePackages álló szervizcsomagot ismerteti. A *RunToCompletionCodePackage1* csak egy üzenetet naplóz az **StdOut** -ba, és kilép. A *RunToCompletionCodePackage2* egy ideig Pingeli a visszacsatolási címeket, majd a **0**, **1** vagy **2** kilépési kóddal kilép.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

A következő ApplicationManifest.xml a fent tárgyalt ServiceManifest.xml alapján ismerteti az alkalmazást. Meghatározza a **OnFailure** újraindítási szabályzatával rendelkező *WindowsRunToCompletionServicePackage* **RunToCompletion** **ExecutionPolicy** . A *WindowsRunToCompletionServicePackage* aktiválása után a rendszer megkezdi az összetevők CodePackages. Az első aktiváláskor a *RunToCompletionCodePackage1* sikeresen ki kell lépnie. A *RunToCompletionCodePackage2* azonban sikertelen lehet **(nullától eltérő kilépési kóddal)**, ebben az esetben a rendszer újraindul, mert az újraindítási szabályzat **OnFailure**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>DeployedServicePackage telepítési állapotának lekérdezése
A DeployedServicePackage üzembe helyezési állapota lekérdezhető a PowerShellből a [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] vagy a C# használatával a [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (karakterlánc, URI, string) használatával.][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Megfontolások RunToCompletion szemantika használatakor

A jelenlegi RunToCompletion-támogatáshoz a következő szempontokat kell figyelembe venni.
* Ezek a szemantika csak a [tárolók][containers-introduction-link] és a [vendég végrehajtható][guest-executables-introduction-link] alkalmazások esetében támogatott.
* A RunToCompletion szemantikai feladatokkal rendelkező alkalmazások frissítési forgatókönyvei nem engedélyezettek. A felhasználóknak szükség esetén törölnie kell és újra létre kell hozniuk ezeket az alkalmazásokat.
* A feladatátvételi események hatására a CodePackages a sikeres befejezést követően újra végrehajtható, ugyanazon a csomóponton vagy a fürt más csomópontjain. Többek között a feladatátvételi események, a csomópontok újraindítása és Service Fabric futtatókörnyezet frissítése egy csomóponton.

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkról a következő cikkekben olvashat.

* [Service Fabric és tárolók.][containers-introduction-link]
* [Service Fabric és vendég végrehajtható fájlok.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
