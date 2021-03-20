---
title: Inicializáló CodePackages a Service Fabricban
description: A Service Fabric inicializálási CodePackages ismertetése.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 3be079b97c2660437344f88203fdda06cc6d6740
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86258977"
---
# <a name="initializer-codepackages"></a>Inicializáló kódcsomagok

Az 7,1-es verziótól kezdődően Service Fabric támogatja az **inicializálási CodePackages** a [tárolók][containers-introduction-link] és a [vendég végrehajtható][guest-executables-introduction-link] alkalmazások számára. Az inicializáló CodePackages lehetőséget biztosít az inicializálások végrehajtásához a szervizcsomag hatókörében, mielőtt más CodePackages megkezdi a végrehajtást. A szervizcsomaggal való kapcsolatuk hasonló ahhoz, amit egy [SetupEntryPoint][setup-entry-point-link] egy CodePackage.

A cikk folytatása előtt javasoljuk, hogy ismerkedjen meg a [Service Fabric alkalmazás modelljével][application-model-link] és a [Service Fabric üzemeltetési modellel][hosting-model-link].

> [!NOTE]
> Az inicializáló CodePackages jelenleg nem támogatottak az [Reliable Services][reliable-services-link] programozási modellel írt szolgáltatások esetében.
 
## <a name="semantics"></a>Szemantika

A rendszer inicializáló CodePackage fog futni a **sikeres befejezéshez (kilépési kód: 0)**. A sikertelen inicializálási CodePackage a sikeres befejezésig újraindulnak. Több inicializáló CodePackages is engedélyezve van, és végrehajtása a **sikeres Befejezés** érdekében, **sorrendben**, **egy meghatározott sorrendben** történik, mielőtt más CodePackages is megkezdődik a szervizcsomagok végrehajtásában.

## <a name="specifying-initializer-codepackages"></a>Inicializálási CodePackages megadásához
A CodePackage megadható inicializáló úgy, hogy az **inicializálás** attribútumát **true** értékre állítja a ServiceManifest. Ha több inicializáló CodePackages van, a végrehajtásuk sorrendje a **ExecOrder** attribútumon keresztül adható meg. A **ExecOrder** nem negatív egész számnak kell lennie, és csak inicializáló CodePackages esetén érvényes. Először a **ExecOrder** alacsonyabb értékkel rendelkező inicializáló CodePackages hajtja végre. Ha a **ExecOrder** nincs megadva az inicializálási CodePackage, a rendszer a 0 alapértelmezett értéket feltételezi. A **ExecOrder** azonos értékkel rendelkező inicializáló CodePackages relatív végrehajtási sorrendje nincs meghatározva.

A következő ServiceManifest-kódrészlet három CodePackages tartalmaz, amelyek közül kettőt inicializáló jelöl meg. Ha ez a szervizcsomag aktiválva van, a rendszer először a *InitCodePackage0* hajtja végre, mivel a legalacsonyabb **ExecOrder** értékkel rendelkezik. A *InitCodePackage0* sikeres befejezésekor (0. kilépési kód) a *InitCodePackage1* végrehajtása történik. Végül a *InitCodePackage1* sikeres befejezésekor a rendszer végrehajtja a *WorkloadCodePackage* .

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Példa az inicializálási CodePackages használatával

Nézzük meg a teljes példát az inicializálási CodePackages használatával.

> [!IMPORTANT]
> Az alábbi példa azt feltételezi, hogy a [Windows-tároló alkalmazások Service Fabric és a Docker használatával történő][containers-getting-started-link]létrehozásának ismerete.
>
> Ez a példa mcr.microsoft.com/windows/nanoserver:1809 hivatkozik. A Windows Server-tárolók nem kompatibilisek a gazdagép operációs rendszerének összes verziójával. További információ: a [Windows-tároló verziójának kompatibilitása](/virtualization/windowscontainers/deploy-containers/version-compatibility).

A következő ServiceManifest.xml a korábban ismertetett ServiceManifest-kódrészletre épül fel. A *InitCodePackage0*, a *InitCodePackage1* és a *WorkloadCodePackage* a tárolókat jelképező CodePackages. Aktiváláskor a rendszer először a *InitCodePackage0* hajtja végre. Egy üzenetet naplóz egy fájlba, és kilép. Ezután a *InitCodePackage1* végrehajtja a fájlt, amely egy üzenetet is naplóz egy fájlba, és kilép. Végül a *WorkloadCodePackage* megkezdi a végrehajtást. Egy üzenetet is naplóz egy fájlba, kiírja a fájl tartalmát az **StdOut** -ba, majd örökre Pingeli.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

A következő ApplicationManifest.xml a fent tárgyalt ServiceManifest.xml alapján ismerteti az alkalmazást. Vegye figyelembe, hogy az összes tárolóhoz ugyanazt a **kötet** -csatlakoztatást adja meg, a **C:\WorkspaceOnHost** pedig mindhárom tároló **C:\WorkspaceOnContainer** van csatlakoztatva. A nettó hatás az, hogy minden tároló ugyanarra a naplófájlba írja az aktiválási sorrendben.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
A szervizcsomag sikeres aktiválása után a **C:\WorkspaceOnHost\log.txt** tartalmának a következőnek kell lennie.

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
