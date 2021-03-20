---
title: Szolgáltatás futtatása a rendszer és a helyi biztonsági fiókok alatt
description: Megtudhatja, hogyan futtathat Service Fabric alkalmazást a rendszer-és a helyi biztonsági fiókokban.  Hozzon létre rendszerbiztonsági tagokat, és alkalmazza a Run-As szabályzatot a szolgáltatások biztonságos futtatásához.
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: 53212f8636602705899834b6db1d3f0d80b5fe4f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75610121"
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Szolgáltatás futtatása helyi felhasználói fiókként vagy helyi rendszerfiókként
Az Azure Service Fabric használatával a fürtben futó alkalmazások különböző felhasználói fiókokban is biztonságossá tehetők. Alapértelmezés szerint Service Fabric alkalmazások azon a fiókon futnak, amelyen a Fabric.exe folyamat fut. A Service Fabric lehetővé teszi az alkalmazások helyi felhasználói vagy rendszerfiókkal történő futtatását is. A helyi rendszerfiókok támogatott típusai a következők: **LocalUser**, **NetworkService**, **LocalService** és **LocalSystem**.  Ha a Service Fabrict egy önálló Windows-fürtön futtatja, akkor a [Active Directory tartományi fiókok](service-fabric-run-service-as-ad-user-or-group.md) vagy [csoportosan felügyelt](service-fabric-run-service-as-gmsa.md)szolgáltatásfiókok területen futtathatja a szolgáltatást.

Az alkalmazás jegyzékfájljában meghatározhatja a szolgáltatások futtatásához szükséges felhasználói fiókokat, illetve az erőforrások biztonságos elérését a **résztvevők** szakaszban. Létrehozhat és létrehozhat felhasználói csoportokat is, hogy egy vagy több felhasználó egyszerre kezelhető legyen. Ez akkor hasznos, ha több felhasználó van a különböző szolgáltatási belépési pontokhoz, és a csoport szintjén elérhető általános jogosultságokra van szükségük.  A felhasználók ezután egy futtató házirendben lesznek hivatkozva, amely egy adott szolgáltatásra vagy az alkalmazás összes szolgáltatására vonatkozik. 

Alapértelmezés szerint a rendszer a fő belépési pontra alkalmazza a RunAs-házirendet.  Egy futtató házirendet is alkalmazhat a telepítési belépési pontra, ha a [rendszerfiókban bizonyos magas szintű jogosultságokkal rendelkező telepítési műveleteket kell futtatnia](service-fabric-run-script-at-service-startup.md), vagy a fő és a telepítési belépési pontokat is.  

> [!NOTE] 
> Ha futtató házirendet alkalmaz egy szolgáltatásra, és a szolgáltatás jegyzékfájlja deklarálja a végponti erőforrásokat a HTTP protokollal, meg kell adnia egy **SecurityAccessPolicy**.  További információ: [biztonsági hozzáférési házirend társítása http-és HTTPS-végpontokhoz](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Szolgáltatás futtatása helyi felhasználóként
Létrehozhat egy helyi felhasználót, amely segítségével biztonságossá teheti a szolgáltatást az alkalmazáson belül. Ha a **LocalUser** -fiók típusa meg van adva az alkalmazás jegyzékfájljának rendszerbiztonsági tagjai szakaszban, Service Fabric helyi felhasználói fiókokat hoz létre azon gépeken, amelyeken az alkalmazás telepítve van. Alapértelmezés szerint ezek a fiókok nem egyeznek meg az alkalmazás jegyzékfájljában megadott névvel (például *Customer3* a következő alkalmazás jegyzékfájljában). Ehelyett dinamikusan jönnek létre, és véletlenszerű jelszavakkal rendelkeznek.

A ServiceManifestImport **RunAsPolicy** szakaszában adja meg a felhasználói fiókot a **rendszerbiztonsági tag** szakaszban a szolgáltatás kódjának futtatásához.  Az alábbi példa bemutatja, hogyan hozhat létre helyi felhasználót, és hogyan alkalmazhat egy futtató házirendet a fő belépési pontra:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Helyi felhasználói csoport létrehozása
Létrehozhat felhasználói csoportokat, és hozzáadhat egy vagy több felhasználót a csoporthoz. Ez akkor hasznos, ha több felhasználó van a különböző szolgáltatási belépési pontokhoz, és szükségük van bizonyos, a csoport szintjén elérhető általános jogosultságokra. A következő Application manifest-példa egy *LocalAdminGroup* nevű helyi csoportot mutat be, amely rendszergazdai jogosultságokkal rendelkezik. Két felhasználó, a *Customer1* és a *Customer2* ennek a helyi csoportnak a tagjai. A **ServiceManifestImport** szakaszban egy futtató házirend lesz alkalmazva a *Stateful1Pkg* -kód *Customer2* való futtatásához.  Egy másik futtató házirendet alkalmaz a *Web1Pkg* -kód *Customer1* való futtatására.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Groups>
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <User Name="Customer1">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <User Name="Customer2">
        <MemberOf>
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Alapértelmezett szabályzat alkalmazása az összes szolgáltatási kód csomagjaira
A **DefaultRunAsPolicy** szakasz segítségével megadhat egy alapértelmezett felhasználói fiókot az összes olyan kódból, amely nem rendelkezik meghatározott **RunAsPolicy** . Ha az alkalmazás által használt szolgáltatás jegyzékfájljában megadott kódok többségét ugyanazon felhasználó alatt kell futtatnia, az alkalmazás csak az alapértelmezett futtató házirendet definiálhatja az adott felhasználói fiókhoz. A következő példa azt adja meg, hogy ha egy programkódhoz nincs megadva **RunAsPolicy** , a csomagnak a rendszerbiztonsági tag szakaszban megadott **MyDefaultAccount** -felhasználó alatt kell futnia.  A támogatott fióktípus a LocalUser, a NetworkService, a LocalSystem és a LocalService.  Ha helyi felhasználót vagy szolgáltatást használ, adja meg a fiók nevét és jelszavát is.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Programkód hibakeresése helyileg a konzol átirányításával
Alkalmanként hibakeresési célokra is hasznos lehet, ha a konzol kimenetét szeretné megtekinteni egy futó szolgáltatásból. A konzol átirányítási házirendjét megadhatja a szolgáltatási jegyzékfájl belépési pontján, amely a kimenetet fájlba írja. A fájl kimenete az alkalmazás központi telepítésére és futtatására szolgáló fürtcsomópont **log** nevű alkalmazás mappájába íródik. 

> [!WARNING]
> Soha ne használja a konzol átirányítási házirendjét az éles környezetben üzembe helyezett alkalmazásokban, mivel ez hatással lehet az alkalmazás feladatátvételére. Ezt *csak* helyi fejlesztési és hibakeresési célokra használhatja.  
> 
> 

A következő szolgáltatási jegyzékfájl szemlélteti a konzol átirányításának engedélyezését egy FileRetentionCount értékkel:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Következő lépések
* [Az alkalmazás modelljének megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása a szolgáltatás jegyzékfájljában](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
