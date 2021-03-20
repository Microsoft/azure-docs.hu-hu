---
title: Példák az Azure Service Fabric Container Application alkalmazásra – jegyzékfájlok
description: Megtudhatja, hogyan konfigurálhatja az alkalmazás-és szolgáltatás-jegyzékfájlok beállításait egy többtárolós Service Fabric alkalmazáshoz.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84701166"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Példák többtárolós alkalmazások és szolgáltatások jegyzékeire
A következő példák a többtárolós Service Fabric alkalmazás alkalmazás-és szolgáltatási jegyzékfájlját szemléltetik. A példák célja, hogy megmutassa, milyen beállítások érhetők el, és hogyan használhatja őket. Ezek az alkalmazások és szolgáltatások jegyzékfájli a [Windows Server 2016-tároló mintájának](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) jegyzékfájlján alapulnak.

A következő funkciók jelennek meg:

|Jegyzék|Funkciók|
|---|---|
|[Alkalmazásjegyzék](#application-manifest)| a [környezeti változók felülbírálása](service-fabric-get-started-containers.md#configure-and-set-environment-variables), a [tároló-portok közötti megfeleltetés konfigurálása](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), a [tároló beállításjegyzék-hitelesítésének](service-fabric-get-started-containers.md#configure-container-repository-authentication)konfigurálása, az [erőforrás-szabályozás](service-fabric-resource-governance.md), az [elkülönítési mód beállítása](service-fabric-get-started-containers.md#configure-isolation-mode), az [operációs rendszer Build-specifikus rendszerképének megadása](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService szolgáltatás jegyzékfájlja](#frontendservice-service-manifest)| [környezeti változók beállítása](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [végpont konfigurálása](service-fabric-get-started-containers.md#configure-communication), parancsok továbbítása a tárolóhoz, [tanúsítvány importálása tárolóba](service-fabric-securing-containers.md)| 
|[BackEndService szolgáltatás jegyzékfájlja](#backendservice-service-manifest)|[környezeti változók beállítása](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [végpont konfigurálása](service-fabric-get-started-containers.md#configure-communication), [kötet-illesztőprogram konfigurálása](service-fabric-containers-volume-logging-drivers.md)| 

Az adott XML-elemekkel kapcsolatos további információkért lásd: az [alkalmazás jegyzékfájljának elemei](#application-manifest-elements), a [FrontEndService](#frontendservice-service-manifest-elements)és a [BackEndService szolgáltatás jegyzékfájljának](#backendservice-service-manifest-elements) elemei.

## <a name="application-manifest"></a>Alkalmazásjegyzék

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService szolgáltatás jegyzékfájlja

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService szolgáltatás jegyzékfájlja

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
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

### <a name="policies-element"></a>Szabályzatok elem
A szabályzatok (a végponti kötés, a csomagok megosztása, a futtatási és a biztonsági hozzáférés) ismertetése az importált szolgáltatás jegyzékfájlján. További információ: [szabályzatok elem](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy elem
Meghatározza a teljes szolgáltatáscsomag szintjén alkalmazott erőforrás-irányítási házirendet. További információ: [ServicePackageResourceGovernancePolicy elem](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy elem
Megadja a kódok erőforrás-korlátait. További információ: [ResourceGovernancePolicy elem](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies elem
A tároló gazdagépek aktiválására vonatkozó házirendeket határozza meg. További információ: [ContainerHostPolicies elem](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>RepositoryCredentials elem
A tároló lemezkép-tárházának hitelesítő adatai, amelyekből képeket lehet lekérni. További információ: [RepositoryCredentials elem](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>PortBinding elem
Meghatározza, hogy melyik végpont-erőforrást kell kötni a kitett tároló portjához. További információ: [PortBinding elem](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Kötet elem
Meghatározza a tárolóhoz kötni kívánt kötetet. További információ: [Volume Element](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>DriverOption elem
Illesztőprogram-beállítások, amelyeket át kell adni az illesztőprogramnak. További információ: [DriverOption elem](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>ImageOverrides elem
Előfordulhat, hogy a Windows Server-tárolók nem kompatibilisek az operációs rendszer különböző verzióival.  Egy tárolóban több operációsrendszer-lemezképet is megadhat, és címkézheti azokat az operációs rendszer kiépítési verzióival. Szerezze be az operációs rendszer Build verzióját a "winver" parancs futtatásával egy Windows-parancssorban. Ha az alapul szolgáló operációs rendszer a 16299-es verzió (Windows Server 1709-es verzió), Service Fabric az operációs rendszer = "16299" címkével rendelkező tároló rendszerképét választja. A címkézetlen tároló képe az operációs rendszer összes verziójában működik, és felülbírálja a szolgáltatás jegyzékfájljában megadott képet. További információ: [ImageOverrides elem](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Képelem
Az operációsrendszer-Build verziószámának megfelelő tároló-rendszerkép. Ha nincs megadva az operációs rendszer attribútuma, a rendszer a tároló rendszerképét feltételezi, hogy az operációs rendszer összes verziójában működik, és felülbírálja a szolgáltatás jegyzékfájljában megadott képet. További információ: [képelem](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides elem
 További információ: [EnvironmentOverrides elem](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>EnvironmentVariable elem
Környezeti változó. További információ: [EnvironmentVariable elem](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>CertificateRef elem
Egy olyan X509-tanúsítvánnyal kapcsolatos információt ad meg, amely a tároló-környezet számára elérhető. A tanúsítványt az összes fürtcsomópont LocalMachine-tárolójába kell telepíteni.
Az alkalmazás indításakor a futtatókörnyezet beolvassa a tanúsítványt, és létrehozza a PFX-fájlt és-jelszót (Windows rendszeren) vagy egy PEM-fájlt (Linux rendszeren).
A PFX-fájl és a jelszó a tárolóban a Certificates_ServicePackageName_CodePackageName_CertName_PFX és Certificates_ServicePackageName_CodePackageName_CertName_Password környezeti változók használatával érhető el. A PEM-fájl a tárolóban Certificates_ServicePackageName_CodePackageName_CertName_PEM és Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey környezeti változók használatával érhető el. További információ: [CertificateRef elem](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>DefaultServices elem
Azokat a szolgáltatási példányokat deklarálja, amelyek automatikusan létrejönnek, amikor egy alkalmazás ezen az alkalmazási típuson jön létre. További információ: [DefaultServices elem](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Szolgáltatási elem
Az alkalmazás példányainak létrehozásakor automatikusan létrehozandó szolgáltatás deklarálása. További információ: [szolgáltatási elem](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>StatelessService elem
Állapot nélküli szolgáltatást definiál. További információ: [StatelessService elem](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService szolgáltatás jegyzékfájljának elemei
### <a name="servicemanifest-element"></a>ServiceManifest elem
A szolgáltatás típusának és verziójának deklaratív leírása. Felsorolja a különállóan frissíthető kód-, konfigurációs és adatcsomagokat, amelyek együtt alkotnak egy szervizcsomagot egy vagy több szolgáltatás típusának támogatásához. Az erőforrások, a diagnosztikai beállítások és a szolgáltatás metaadatainak, például a szolgáltatástípus, az állapot tulajdonságai és a terheléselosztás metrikái is meg vannak adva. További információ: [ServiceManifest elem](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes elem
Meghatározza, hogy a CodePackage milyen szolgáltatási típusokat támogat a jegyzékfájlban. Ha egy szolgáltatás egy ilyen típusú szolgáltatáshoz van létrehozva, az ebben a jegyzékfájlban deklarált összes kód-csomagot a rendszer a belépési pontok futtatásával aktiválja. A szolgáltatások típusai a jegyzékfájl szintjén vannak deklarálva, nem a kód csomag szintjén. További információ: [ServiceTypes elem](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType elem
Az állapot nélküli szolgáltatástípus leírása. További információ: [StatelessServiceType elem](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Egy meghatározott szolgáltatástípus használatát támogató programkódot ismertet. Ha egy szolgáltatás egy ilyen típusú szolgáltatáshoz van létrehozva, az ebben a jegyzékfájlban deklarált összes kód-csomagot a rendszer a belépési pontok futtatásával aktiválja. Az eredményül kapott folyamatokat a rendszer futási időben regisztrálja a támogatott szolgáltatások típusait. Ha több kódrészlet is van, akkor azok mind aktiválva lesznek, amikor a rendszer a deklarált szolgáltatások bármelyikének valamelyik típusát keresi. További információ: [CodePackage elem](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>BelépésiPont elem
A BelépésiPont által megadott végrehajtható fájl általában a hosszan futó szolgáltatás gazdagépe. Egy különálló telepítési belépési pont jelenléte elkerüli, hogy a szolgáltatási gazdagépet hosszú ideig magas jogosultságokkal futtassa. A BelépésiPont által megadott végrehajtható fájl futtatása a SetupEntryPoint sikeres bezárása után történik. Az eredményül kapott folyamat figyelése és újraindítása (a SetupEntryPoint-vel kezdődően kezdődik), ha még leáll vagy összeomlik. További információ: [BelépésiPont elem](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost elem
 További információ: [ContainerHost elem](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName elem
A tárház és a rendszerkép on [https://hub.docker.com](https://hub.docker.com) vagy Azure Container Registry. További információ: [imagename elem](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables elem
Adja át a környezeti változókat a tárolóba vagy az exe-be.  További információ: [EnvironmentVariables elem](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable elem
Környezeti változó. További információ: [EnvironmentVariable elem](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage elem
A Name attribútum által elnevezett mappát deklarál, amely Settings.xml fájlt tartalmaz. Ez a fájl a felhasználó által definiált kulcs-érték párokat tartalmazó részeket tartalmazza, amelyeket a folyamat futási időben tud visszaolvasni. Ha a frissítés során csak a ConfigPackage verziója módosult, akkor a futó folyamat nem indul újra. Ehelyett a visszahívás értesíti a konfigurációs beállítások megváltozásának folyamatát, hogy azok dinamikusan is újratölthetők legyenek. További információ: [ConfigPackage elem](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>Datapackage szakaszából elem
A Name attribútum által elnevezett mappát deklarál, amely statikus adatfájlokat tartalmaz. Service Fabric újrahasznosítja a gazdagépen és a támogatási csomagokban megadott összes EXEs és DLLHOSTs, ha a szolgáltatás jegyzékfájljában felsorolt adatcsomagok bármelyike frissül. További információ: [Datapackage szakaszából elem](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Erőforrások elem
Ismerteti a szolgáltatás által használt erőforrásokat, amelyek a lefordított kód módosítása nélkül deklarálható, és a szolgáltatás telepítésekor módosulnak. Az ehhez az erőforrásokhoz való hozzáférést az alkalmazás jegyzékfájljának rendszerbiztonsági tagjai és házirendjei szakaszban találja. További információ: [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok eleme
Meghatározza a szolgáltatáshoz tartozó végpontokat. További információ: [végpontok eleme](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont eleme
További információ: [végpont elem](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>BackEndService szolgáltatás jegyzékfájljának elemei
### <a name="servicemanifest-element"></a>ServiceManifest elem
A szolgáltatás típusának és verziójának deklaratív leírása. Felsorolja a különállóan frissíthető kód-, konfigurációs és adatcsomagokat, amelyek együtt alkotnak egy szervizcsomagot egy vagy több szolgáltatás típusának támogatásához. Az erőforrások, a diagnosztikai beállítások és a szolgáltatás metaadatainak, például a szolgáltatástípus, az állapot tulajdonságai és a terheléselosztás metrikái is meg vannak adva. További információ: [ServiceManifest elem](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes elem
Meghatározza, hogy a CodePackage milyen szolgáltatási típusokat támogat a jegyzékfájlban. Ha egy szolgáltatás egy ilyen típusú szolgáltatáshoz van létrehozva, az ebben a jegyzékfájlban deklarált összes kód-csomagot a rendszer a belépési pontok futtatásával aktiválja. A szolgáltatások típusai a jegyzékfájl szintjén vannak deklarálva, nem a kód csomag szintjén. További információ: [ServiceTypes elem](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType elem
Az állapot nélküli szolgáltatástípus leírása. További információ: [StatelessServiceType elem](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage elem
Egy meghatározott szolgáltatástípus használatát támogató programkódot ismertet. Ha egy szolgáltatás egy ilyen típusú szolgáltatáshoz van létrehozva, az ebben a jegyzékfájlban deklarált összes kód-csomagot a rendszer a belépési pontok futtatásával aktiválja. Az eredményül kapott folyamatokat a rendszer futási időben regisztrálja a támogatott szolgáltatások típusait. Ha több kódrészlet is van, akkor azok mind aktiválva lesznek, amikor a rendszer a deklarált szolgáltatások bármelyikének valamelyik típusát keresi. További információ: [CodePackage elem](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>BelépésiPont elem
A BelépésiPont által megadott végrehajtható fájl általában a hosszan futó szolgáltatás gazdagépe. Egy különálló telepítési belépési pont jelenléte elkerüli, hogy a szolgáltatási gazdagépet hosszú ideig magas jogosultságokkal futtassa. A BelépésiPont által megadott végrehajtható fájl futtatása a SetupEntryPoint sikeres bezárása után történik. Az eredményül kapott folyamat figyelése és újraindítása (a SetupEntryPoint-vel kezdődően kezdődik), ha még leáll vagy összeomlik. További információ: [BelépésiPont elem](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost elem
További információ: [ContainerHost elem](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName elem
A tárház és a rendszerkép on [https://hub.docker.com](https://hub.docker.com) vagy Azure Container Registry. További információ: [imagename elem](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Parancsok elem
Adja meg a parancsok vesszővel tagolt listáját a tárolóhoz. További információ: [commands Element](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables elem
Adja át a környezeti változókat a tárolóba vagy az exe-be.  További információ: [EnvironmentVariables elem](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable elem
Környezeti változó. További információ: [EnvironmentVariable elem](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage elem
A Name attribútum által elnevezett mappát deklarál, amely Settings.xml fájlt tartalmaz. Ez a fájl a felhasználó által definiált kulcs-érték párokat tartalmazó részeket tartalmazza, amelyeket a folyamat futási időben tud visszaolvasni. Ha a frissítés során csak a ConfigPackage verziója módosult, akkor a futó folyamat nem indul újra. Ehelyett a visszahívás értesíti a konfigurációs beállítások megváltozásának folyamatát, hogy azok dinamikusan is újratölthetők legyenek. További információ: [ConfigPackage elem](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Erőforrások elem
Ismerteti a szolgáltatás által használt erőforrásokat, amelyek a lefordított kód módosítása nélkül deklarálható, és a szolgáltatás telepítésekor módosulnak. Az ehhez az erőforrásokhoz való hozzáférést az alkalmazás jegyzékfájljának rendszerbiztonsági tagjai és házirendjei szakaszban találja. További információ: [Resources Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Végpontok eleme
Meghatározza a szolgáltatáshoz tartozó végpontokat. További információ: [végpontok eleme](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Végpont eleme
 További információ: [végpont elem](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

