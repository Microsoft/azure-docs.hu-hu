---
title: A megbízható szolgáltatások alkalmazásának jegyzékfájlja – példák
description: Megtudhatja, hogyan konfigurálhatja az alkalmazások és szolgáltatások jegyzékfájljának beállításait egy megbízható szolgáltatások Service Fabric alkalmazáshoz.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701098"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Példák Reliable Services-alkalmazások és szolgáltatások jegyzékeire
A következő példák a Service Fabric alkalmazás alkalmazás-és szolgáltatási jegyzékfájlait ASP.NET Core webes kezelőfelülettel és állapot-nyilvántartó háttérrel rendelkeznek. A példák célja, hogy megmutassa, milyen beállítások érhetők el, és hogyan használhatja őket. Ezek az alkalmazások és szolgáltatások jegyzékfájli a [Service Fabric .net](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) gyors üzembe helyezési jegyzékeken alapulnak.

A következő funkciók jelennek meg:

|Jegyzék|Funkciók|
|---|---|
|[Alkalmazásjegyzék](#application-manifest)| [erőforrás-szabályozás](service-fabric-resource-governance.md), [helyi rendszergazdai fiókkal futtatott szolgáltatás](service-fabric-application-runas-security.md), [alapértelmezett szabályzat alkalmazása minden szolgáltatási kód csomagra](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [felhasználó és csoport létrehozása](service-fabric-application-runas-security.md), adatcsomag megosztása a szolgáltatási példányok között, a [szolgáltatási végpontok felülbírálása](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService szolgáltatás jegyzékfájlja| [Parancsfájl futtatása a szolgáltatás indításakor](service-fabric-run-script-at-service-startup.md), [Definiáljon egy https-végpontot](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService szolgáltatás jegyzékfájlja| [Konfigurációs csomag deklarálása](service-fabric-application-and-service-manifests.md), [adatcsomag deklarálása](service-fabric-application-and-service-manifests.md), [végpont konfigurálása](service-fabric-service-manifest-resources.md)| 

Az adott XML-elemekkel kapcsolatos további információkért lásd: az [alkalmazás jegyzékfájljának elemei](#application-manifest-elements), a [VotingWeb](#votingweb-service-manifest-elements)és a [VotingData szolgáltatás jegyzékfájljának](#votingdata-service-manifest-elements) elemei.

## <a name="application-manifest"></a>Alkalmazásjegyzék

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>VotingWeb szolgáltatás jegyzékfájlja

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>VotingData szolgáltatás jegyzékfájlja

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>Az alkalmazás jegyzékfájljának elemei
### <a name="applicationmanifest-element"></a>ApplicationManifest elem
A deklaratív leírja az alkalmazás típusát és verzióját. A rendszer egy vagy több szolgáltatási jegyzékfájlt hivatkozik az alkalmazás típusának összeállítására. Az összetevő-szolgáltatások konfigurációs beállításai felülbírálják a paraméteres Alkalmazásbeállítások használatát. Az alapértelmezett szolgáltatások, a szolgáltatási sablonok, a rendszerbiztonsági tag, a házirendek, a diagnosztika beállítása és a tanúsítványok az alkalmazás szintjén is deklarálva lehetnek. További információ: [ApplicationManifest elem](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Paraméterek elem
Az alkalmazás jegyzékfájljában használt paraméterek deklarálása. Ezeknek a paramétereknek az értéke megadható az alkalmazás példányának létrehozásakor, és az alkalmazás vagy szolgáltatás konfigurációs beállításainak felülbírálására használható. További információ: [Paraméterek elem](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Paraméter elem
A jegyzékfájlban használandó alkalmazás-paraméter. A paraméter értéke megváltoztatható az alkalmazás létrehozásakor, vagy ha nincs megadva érték, a rendszer az alapértelmezett értéket használja. További információ: [paraméter elem](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport elem
A szolgáltatás-fejlesztő által létrehozott szolgáltatási jegyzékfájl importálása. Az alkalmazás egyes összetevőihez szolgáltatásbeli jegyzékfájlt kell importálni. A konfigurációs felülbírálások és házirendek deklarálása a szolgáltatás jegyzékfájljában lehetséges. További információ: [ServiceManifestImport elem](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef elem
A szolgáltatás jegyzékfájljának importálása hivatkozással. Jelenleg a szolgáltatás jegyzékfájljának (ServiceManifest.xml) jelen kell lennie a Build csomagban. További információ: [ServiceManifestRef elem](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>ResourceOverrides elem
Meghatározza a szolgáltatás jegyzékfájljának erőforrásaiban deklarált végpontok erőforrás-felülbírálásait. További információ: [ResourceOverrides elem](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Végpontok eleme
A felülbírálni kívánt végpont (ok). További információ: [végpontok eleme](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Végpont eleme
A felülbíráláshoz a szolgáltatás jegyzékfájljában deklarált végpont. További információ: [végpont elem](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Szabályzatok elem
A szabályzatok (a végponti kötés, a csomagok megosztása, a futtatási és a biztonsági hozzáférés) ismertetése az importált szolgáltatás jegyzékfájlján. További információ: [szabályzatok elem](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy elem
Meghatározza a teljes szolgáltatáscsomag szintjén alkalmazott erőforrás-irányítási házirendet. További információ: [ServicePackageResourceGovernancePolicy elem](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy elem
Meghatározza az codepackage erőforrás-korlátozásait. További információ: [ResourceGovernancePolicy elem](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>Packagesharingpolicy objektumnak elem
Azt jelzi, hogy meg kell-e osztani egy kódot, egy konfigurációt vagy egy adatcsomagot ugyanazon szolgáltatástípus szolgáltatási példányai között. További információ: [Packagesharingpolicy objektumnak elem](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy elem
Hozzáférési engedélyeket ad egy rendszerbiztonsági tag számára egy szolgáltatási jegyzékfájlban definiált erőforráshoz (például egy végponthoz). A biztonsági kockázatok csökkentése érdekében általában nagyon hasznos a szolgáltatások különböző erőforrásokhoz való hozzáférésének szabályozása és korlátozása. Ez különösen akkor fontos, ha az alkalmazást egy olyan piactérből származó szolgáltatásból építették, amelyet különböző fejlesztők fejlesztettek ki. További információ: [SecurityAccessPolicy elem](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>RunAsPolicy elem
Azt a helyi felhasználót vagy helyi rendszerfiókot határozza meg, amelyet a szolgáltatási kód csomagja futtat. A tartományi fiókok olyan Windows Server-példányokon támogatottak, ahol Azure Active Directory érhető el. Alapértelmezés szerint az alkalmazások a Fabric.exe folyamat alatt futó fiók alatt futnak. Az alkalmazások más fiókokként is futtathatók, amelyeket a rendszerbiztonsági tag szakaszban kell deklarálni. Ha futtat egy futtató házirendet egy szolgáltatásra, és a szolgáltatás jegyzékfájlja deklarálja a végponti erőforrásokat a HTTP protokollal, meg kell adnia egy SecurityAccessPolicy annak biztosításához, hogy az ezekhez a végpontokhoz lefoglalt portok helyesen férhessenek hozzá a futtató felhasználói fiókhoz, amelyen a szolgáltatás fut. HTTPS-végpont esetén meg kell határoznia egy EndpointBindingPolicy, amely jelzi a tanúsítvány nevét, hogy visszatérjen az ügyfélhez. További információ: [RunAsPolicy elem](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices elem
Azokat a szolgáltatási példányokat deklarálja, amelyek automatikusan létrejönnek, amikor egy alkalmazás ezen az alkalmazási típuson jön létre. További információ: [DefaultServices elem](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Szolgáltatási elem
Az alkalmazás példányainak létrehozásakor automatikusan létrehozandó szolgáltatás deklarálása. További információ: [szolgáltatási elem](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>StatefulService elem
Meghatároz egy állapot-nyilvántartó szolgáltatást. További információ: [StatefulService elem](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>StatelessService elem
Állapot nélküli szolgáltatást definiál. További információ: [StatelessService elem](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Rendszerbiztonsági tag elem
Azokat a rendszerbiztonsági tagokat (felhasználókat, csoportokat) ismerteti, amelyek szükségesek ahhoz, hogy az alkalmazás a szolgáltatásokat és a biztonságos erőforrásokat futtassa. A rendszerbiztonsági tag hivatkozásokat tartalmaz a szabályzatok szakaszban. További információ: [principals elem](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Csoportok elem
Csoportokat deklarál rendszerbiztonsági tagként, amely a házirendekben hivatkozhat. A csoportok akkor hasznosak, ha több felhasználó van a különböző szolgáltatási belépési pontokhoz, és szükségük van bizonyos, a csoport szintjén elérhető általános jogosultságokra. További információ: [groups Element](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Csoport elem
Egy csoport deklarálása rendszerbiztonsági tagként, amely hivatkozhat a szabályzatokban. További információ: [csoport elem](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Tagsági elem
 További információ: [tagsági elem](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>SystemGroup elem
 További információ: [SystemGroup elem](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Felhasználói elem
Felhasználók csoportjának deklarálása rendszerbiztonsági tagként, amely hivatkozhat a szabályzatokra. További információ: [Users (felhasználók) elem](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Felhasználói elem
Deklarál egy felhasználót rendszerbiztonsági tagként, amely a házirendekben hivatkozhat. További információ: [felhasználói elem](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf elem
A felhasználók hozzáadhatók bármely meglévő tagsági csoporthoz, így az adott tagsági csoport összes tulajdonságát és biztonsági beállítását örökölheti. A tagsági csoport a különböző szolgáltatások vagy ugyanazon szolgáltatás (egy másik gépen) által elérhető külső erőforrások biztonságossá tételére használható. További információ: [memberof elem](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>SystemGroup elem
Az a Rendszercsoport, amelyhez hozzá kívánja adni a felhasználót.  A rendszercsoportot a csoportok szakaszban kell meghatározni. További információ: [SystemGroup elem](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Csoport elem
A csoport, amelyhez hozzá kívánja adni a felhasználót.  A csoportot a csoportok szakaszban kell meghatározni. További információ: [csoport elem](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Szabályzatok elem
Ismerteti a szabályzatokat (a naplózási gyűjteményt, az alapértelmezett futtatást, az állapotot és a biztonsági hozzáférést) az alkalmazás szintjén. További információ: [szabályzatok elem](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy elem
Olyan alapértelmezett felhasználói fiókot határozzon meg minden olyan szolgáltatási kód csomaghoz, amely nem rendelkezik meghatározott RunAsPolicy a ServiceManifestImport szakaszban. További információ: [DefaultRunAsPolicy elem](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>VotingWeb szolgáltatás jegyzékfájljának elemei
### <a name="servicemanifest-element"></a>ServiceManifest elem
A szolgáltatás típusának és verziójának deklaratív leírása. Felsorolja a különállóan frissíthető kód-, konfigurációs és adatcsomagokat, amelyek együtt alkotnak egy szervizcsomagot egy vagy több szolgáltatás típusának támogatásához. Az erőforrások, a diagnosztikai beállítások és a szolgáltatás metaadatainak, például a szolgáltatástípus, az állapot tulajdonságai és a terheléselosztás metrikái is meg vannak adva. További információ: [ServiceManifest elem](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes elem
Meghatározza, hogy a CodePackage milyen szolgáltatási típusokat támogat a jegyzékfájlban. Ha egy szolgáltatás egy ilyen típusú szolgáltatáshoz van létrehozva, az ebben a jegyzékfájlban deklarált összes kód-csomagot a rendszer a belépési pontok futtatásával aktiválja. A szolgáltatások típusai a jegyzékfájl szintjén vannak deklarálva, nem a kód csomag szintjén. További információ: [ServiceTypes elem](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType elem
Az állapot nélküli szolgáltatástípus leírása. További információ: [StatelessServiceType elem](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Egy meghatározott szolgáltatástípus használatát támogató programkódot ismertet. Ha egy szolgáltatás egy ilyen típusú szolgáltatáshoz van létrehozva, az ebben a jegyzékfájlban deklarált összes kód-csomagot a rendszer a belépési pontok futtatásával aktiválja. Az eredményül kapott folyamatokat a rendszer futási időben regisztrálja a támogatott szolgáltatások típusait. Ha több kódrészlet is van, akkor azok mind aktiválva lesznek, amikor a rendszer a deklarált szolgáltatások bármelyikének valamelyik típusát keresi. További információ: [CodePackage elem](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint elem
A rendszerjogosultságú belépési pont alapértelmezés szerint ugyanazzal a hitelesítő adatokkal fut, mint a Service Fabric (jellemzően a NETWORKSERVICE fiók) a többi belépési pont előtt. A BelépésiPont által megadott végrehajtható fájl általában a hosszan futó szolgáltatás gazdagépe. Egy különálló telepítési belépési pont jelenléte elkerüli, hogy a szolgáltatási gazdagépet hosszú ideig magas jogosultságokkal futtassa. További információ: [SetupEntryPoint elem](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost elem
 További információ: [ExeHost elem](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Program eleme
A végrehajtható fájl neve.  Például: "MySetup.bat" vagy "MyServiceHost.exe". További információ: [program elem](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Argumentumok elem
 További információ: [argumentumok elem](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder elem
A folyamat munkakönyvtára azon a fürtcsomóponton, amelyben az alkalmazás telepítve van. Három értéket is megadhat: Work (alapértelmezett), CodePackage vagy kód. A kód azt adja meg, hogy a munkakönyvtár azon könyvtárra van beállítva, amelyben az EXE a Code csomagban van definiálva. A CodePackage úgy állítja be a munkakönyvtárat, hogy a programkód gyökerét adja meg, függetlenül attól, hogy az EXE hol van definiálva a Code Package könyvtárban. A Work könyvtár a csomóponton létrehozott egyedi mappára állítja be a munkahelyi könyvtárat.  Ez a mappa megegyezik a teljes alkalmazás példányával. Alapértelmezés szerint az alkalmazásban lévő összes folyamat munkakönyvtára az alkalmazás munkahelyi mappájára van beállítva. Ebben az esetben a folyamatok írhatják az adatbevitelt. A kód vagy a kód alapja nem ajánlott, mert ezek a mappák megoszthatók a különböző alkalmazás-példányok között, és előfordulhat, hogy törölve lettek. További információ: [WorkingFolder elem](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>ConsoleRedirection elem

> [!WARNING]
> Ne használja a konzol átirányítását éles alkalmazásban, csak helyi fejlesztéshez és hibakereséshez használja. Átirányítja a konzol kimenetét az indítási parancsfájlból egy kimeneti fájlba az alkalmazás mappa "log" nevű mappájából, ahol az alkalmazás telepítve van és fut. További információ: [ConsoleRedirection elem](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>BelépésiPont elem
A BelépésiPont által megadott végrehajtható fájl általában a hosszan futó szolgáltatás gazdagépe. Egy különálló telepítési belépési pont jelenléte elkerüli, hogy a szolgáltatási gazdagépet hosszú ideig magas jogosultságokkal futtassa. A BelépésiPont által megadott végrehajtható fájl futtatása a SetupEntryPoint sikeres bezárása után történik. Az eredményül kapott folyamat figyelése és újraindítása (a SetupEntryPoint-vel kezdődően kezdődik), ha még leáll vagy összeomlik. További információ: [BelépésiPont elem](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost elem
 További információ: [ExeHost elem](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage elem
A Name attribútum által elnevezett mappát deklarál egy Settings.xml fájlt tartalmazó PackageRoot alatt. Ez a fájl a felhasználó által definiált kulcs-érték párokat tartalmazó részeket tartalmazza, amelyeket a folyamat futási időben tud visszaolvasni. Ha a frissítés során csak a ConfigPackage verziója módosult, akkor a futó folyamat nem indul újra. Ehelyett a visszahívás értesíti a konfigurációs beállítások megváltozásának folyamatát, hogy azok dinamikusan is újratölthetők legyenek. További információ: [ConfigPackage elem](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Erőforrások elem
Ismerteti a szolgáltatás által használt erőforrásokat, amelyek a lefordított kód módosítása nélkül deklarálható, és a szolgáltatás telepítésekor módosulnak. Az ehhez az erőforrásokhoz való hozzáférést az alkalmazás jegyzékfájljának rendszerbiztonsági tagjai és házirendjei szakaszban találja. További információ: [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok eleme
Meghatározza a szolgáltatáshoz tartozó végpontokat. További információ: [végpontok eleme](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont eleme
A felülbíráláshoz a szolgáltatás jegyzékfájljában deklarált végpont. További információ: [végpont elem](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>VotingData szolgáltatás jegyzékfájljának elemei
### <a name="servicemanifest-element"></a>ServiceManifest elem
A szolgáltatás típusának és verziójának deklaratív leírása. Felsorolja a különállóan frissíthető kód-, konfigurációs és adatcsomagokat, amelyek együtt alkotnak egy szervizcsomagot egy vagy több szolgáltatás típusának támogatásához. Az erőforrások, a diagnosztikai beállítások és a szolgáltatás metaadatainak, például a szolgáltatástípus, az állapot tulajdonságai és a terheléselosztás metrikái is meg vannak adva. További információ: [ServiceManifest elem](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes elem
Meghatározza, hogy a CodePackage milyen szolgáltatási típusokat támogat a jegyzékfájlban. Ha egy szolgáltatás egy ilyen típusú szolgáltatáshoz van létrehozva, az ebben a jegyzékfájlban deklarált összes kód-csomagot a rendszer a belépési pontok futtatásával aktiválja. A szolgáltatások típusai a jegyzékfájl szintjén vannak deklarálva, nem a kód csomag szintjén. További információ: [ServiceTypes elem](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType elem
Egy állapot-nyilvántartó szolgáltatás típusát írja le. További információ: [StatefulServiceType elem](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Egy meghatározott szolgáltatástípus használatát támogató programkódot ismertet. Ha egy szolgáltatás egy ilyen típusú szolgáltatáshoz van létrehozva, az ebben a jegyzékfájlban deklarált összes kód-csomagot a rendszer a belépési pontok futtatásával aktiválja. Az eredményül kapott folyamatokat a rendszer futási időben regisztrálja a támogatott szolgáltatások típusait. Ha több kódrészlet is van, akkor azok mind aktiválva lesznek, amikor a rendszer a deklarált szolgáltatások bármelyikének valamelyik típusát keresi. További információ: [CodePackage elem](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>BelépésiPont elem
A BelépésiPont által megadott végrehajtható fájl általában a hosszan futó szolgáltatás gazdagépe. Egy különálló telepítési belépési pont jelenléte elkerüli, hogy a szolgáltatási gazdagépet hosszú ideig magas jogosultságokkal futtassa. A BelépésiPont által megadott végrehajtható fájl futtatása a SetupEntryPoint sikeres bezárása után történik. Az eredményül kapott folyamat figyelése és újraindítása (a SetupEntryPoint-vel kezdődően kezdődik), ha még leáll vagy összeomlik. További információ: [BelépésiPont elem](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost elem
 További információ: [ExeHost elem](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Program eleme
A végrehajtható fájl neve.  Például: "MySetup.bat" vagy "MyServiceHost.exe". További információ: [program elem](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder elem
A folyamat munkakönyvtára azon a fürtcsomóponton, amelyben az alkalmazás telepítve van. Három értéket is megadhat: Work (alapértelmezett), CodePackage vagy kód. A kód azt adja meg, hogy a munkakönyvtár azon könyvtárra van beállítva, amelyben az EXE a Code csomagban van definiálva. A CodePackage úgy állítja be a munkakönyvtárat, hogy a programkód gyökerét adja meg, függetlenül attól, hogy az EXE hol van definiálva a Code Package könyvtárban. A Work könyvtár a csomóponton létrehozott egyedi mappára állítja be a munkahelyi könyvtárat.  Ez a mappa megegyezik a teljes alkalmazás példányával. Alapértelmezés szerint az alkalmazásban lévő összes folyamat munkakönyvtára az alkalmazás munkahelyi mappájára van beállítva. Ebben az esetben a folyamatok írhatják az adatbevitelt. A kód vagy a kód alapja nem ajánlott, mert ezek a mappák megoszthatók a különböző alkalmazás-példányok között, és előfordulhat, hogy törölve lettek. További információ: [WorkingFolder elem](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage elem
A Name attribútum által elnevezett mappát deklarál egy Settings.xml fájlt tartalmazó PackageRoot alatt. Ez a fájl a felhasználó által definiált kulcs-érték párokat tartalmazó részeket tartalmazza, amelyeket a folyamat futási időben tud visszaolvasni. Ha a frissítés során csak a ConfigPackage verziója módosult, akkor a futó folyamat nem indul újra. Ehelyett a visszahívás értesíti a konfigurációs beállítások megváltozásának folyamatát, hogy azok dinamikusan is újratölthetők legyenek. További információ: [ConfigPackage elem](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Datapackage szakaszából elem
A Name attribútum által elnevezett mappa deklarálása a PackageRoot alatt, amely a folyamat futási idején felhasználható statikus adatfájlokat tartalmazza. Service Fabric újrahasznosítja a gazdagépen és a támogatási csomagokban megadott összes EXEs és DLLHOSTs, ha a szolgáltatás jegyzékfájljában felsorolt adatcsomagok bármelyike frissül. További információ: [Datapackage szakaszából elem](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Erőforrások elem
Ismerteti a szolgáltatás által használt erőforrásokat, amelyek a lefordított kód módosítása nélkül deklarálható, és a szolgáltatás telepítésekor módosulnak. Az ehhez az erőforrásokhoz való hozzáférést az alkalmazás jegyzékfájljának rendszerbiztonsági tagjai és házirendjei szakaszban találja. További információ: [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok eleme
Meghatározza a szolgáltatáshoz tartozó végpontokat. További információ: [végpontok eleme](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont eleme
A felülbíráláshoz a szolgáltatás jegyzékfájljában deklarált végpont. További információ: [végpont elem](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

