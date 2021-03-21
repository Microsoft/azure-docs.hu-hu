---
title: Service Fabric szolgáltatási végpontok meghatározása
description: A végponti erőforrások leírása a szolgáltatás jegyzékfájljában, beleértve a HTTPS-végpontok beállítását is
ms.topic: conceptual
ms.date: 09/16/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 0ed5a4aa8993f52d42b97288cd143e6114ff36ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97033306"
---
# <a name="specify-resources-in-a-service-manifest"></a>Erőforrások meghatározása a szolgáltatás jegyzékfájljában
## <a name="overview"></a>Áttekintés
Service Fabric alkalmazások és szolgáltatások meghatározása és verziószámozása a manifest Files használatával történik. A ServiceManifest.xml és a ApplicationManifest.xml áttekintését lásd: [Service Fabric alkalmazás-és szolgáltatás-jegyzékfájlok](service-fabric-application-and-service-manifests.md).

A szolgáltatás jegyzékfájlja lehetővé teszi, hogy a szolgáltatás a lefordított kód módosítása nélkül deklarálja vagy módosítsa a szolgáltatás által használt erőforrásokat. Service Fabric támogatja a végponti erőforrások konfigurációját a szolgáltatáshoz. A szolgáltatás jegyzékfájljában megadott erőforrásokhoz való hozzáférést az alkalmazás jegyzékfájljának SecurityGroup keresztül lehet szabályozni. Az erőforrások deklarációja lehetővé teszi, hogy ezeket az erőforrásokat a központi telepítési időpontra módosítsák, ami azt jelenti, hogy a szolgáltatásnak nincs szüksége új konfigurációs mechanizmus bevezetésére. A ServiceManifest.xml fájl sémájának definíciója telepítve van a Service Fabric SDK-val és-eszközökkel a *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*, és dokumentálva van a [ServiceFabricServiceModel. XSD séma dokumentációjában](service-fabric-service-model-schema.md).

## <a name="endpoints"></a>Végpontok
Ha egy végponti erőforrás van definiálva a szolgáltatás jegyzékfájljában, akkor Service Fabric a fenntartott alkalmazás-porttartomány portjait rendeli hozzá, ha nincs explicit módon megadva port. Tekintse meg például az ezen bekezdés után megadott manifest-kódrészletben megadott végpont *ServiceEndpoint1* . Emellett a szolgáltatások egy adott portot is igényelhetnek egy adott erőforrásban. A különböző fürtcsomópontokon futó szolgáltatási replikák különböző portszámokhoz rendelhetők, míg az ugyanazon a csomóponton futó szolgáltatás replikái megoszthatják a portot. A szolgáltatási replikák ezt követően igény szerint használhatják ezeket a portokat a replikáláshoz és az ügyfelek kéréseinek figyeléséhez.

Egy https-végpontot megadó szolgáltatás aktiválása esetén Service Fabric beállítja a port hozzáférés-vezérlési bejegyzését, a megadott kiszolgálótanúsítvány kötését a porthoz, és megadja azt az identitást is, amelyet a szolgáltatás a tanúsítvány titkos kulcsaként futtat. Az aktiválási folyamat minden alkalommal meghívja a Service Fabric indításakor, vagy ha az alkalmazás tanúsítvány-deklarációját frissítéssel módosítják. A rendszer a végponti tanúsítványt is figyeli a módosítások és a megújítások esetében, és szükség esetén időről időre újra alkalmazza az engedélyeket.

A szolgáltatás megszűnése után Service Fabric törli a végpont hozzáférés-vezérlési bejegyzését, és eltávolítja a tanúsítvány kötését. A tanúsítvány titkos kulcsára alkalmazott engedélyek azonban nem lesznek törölve.

> [!WARNING] 
> A statikus portok megtervezése nem fedi át a ClusterManifest megadott alkalmazási porttartomány tartományát. Ha statikus portot ad meg, az alkalmazás-porttartomány kívülre rendeli, ellenkező esetben a port ütközéseket eredményez. A Release 6.5 CU2 esetében az ütközés észlelése után **Figyelmeztetés** jelenik meg, de az üzemelő példány szinkronizálása a szállított 6,5-viselkedéssel folytatódik. Előfordulhat azonban, hogy az alkalmazás központi telepítését a következő jelentős kiadásokból meggátoljuk.
>
> Az 7,0-es kiadásban az alkalmazás-porttartomány használatának észlelése után az HostingConfig:: ApplicationPortExhaustThresholdPercentage (alapértelmezett 80%) **állapotra vonatkozó figyelmeztetést** adunk ki.
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Ha egyetlen szervizcsomagban több kód is található, akkor azt is a **végpontok** szakaszban kell hivatkozni.  Ha például a **ServiceEndpoint2a** és a **ServiceEndpoint2b** azonos szervizcsomagból származó végpontok eltérő kódú csomagokra hivatkoznak, akkor az egyes végpontoknak megfelelő programkódot a következőképpen pontosítjuk:

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

Tekintse át az [állapot-nyilvántartó Reliable Services konfigurálását](service-fabric-reliable-services-configuration.md) , ha többet szeretne megtudni a végpontokról a konfigurációs csomag beállítási fájljáról (settings.xml).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Példa: HTTP-végpont megadása a szolgáltatáshoz
A következő szolgáltatási jegyzékfájl egy TCP-végponti erőforrást és két HTTP-végponti erőforrást határoz meg az &lt; erőforrások &gt; elemben.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Példa: HTTPS-végpont megadása a szolgáltatáshoz
A HTTPS protokoll kiszolgáló-hitelesítést biztosít, és az ügyfél-kiszolgáló kommunikáció titkosítására is használható. Ha engedélyezni szeretné a HTTPS-t a Service Fabric szolgáltatáson, adja meg a protokollt a szolgáltatás jegyzékfájljának *erőforrások-> végpontok-> végpont* szakaszában, ahogy azt a végpont *ServiceEndpoint3* korábban is mutatja.

> [!NOTE]
> A szolgáltatás protokollja nem módosítható az alkalmazások frissítése közben. Ha a frissítés során módosul, akkor ez a változás megszakítása.
> 

> [!WARNING] 
> HTTPS használata esetén ne használja ugyanazt a portot és tanúsítványt az azonos csomópontra telepített különböző szolgáltatási példányokhoz (az alkalmazástól függetlenül). Ha két különböző szolgáltatást frissít a különböző alkalmazás-példányok ugyanazon portjával, a frissítés sikertelen lesz. További információ: [több alkalmazás frissítése HTTPS-végpontokkal ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
>

Íme egy példa egy HTTPS-végponthoz szükséges konfigurációt bemutató ApplicationManifest. A kiszolgáló/végpont tanúsítványát ujjlenyomat vagy tulajdonos köznapi neve deklarálhatja, és meg kell adni egy értéket. A EndpointRef a ServiceManifest EndpointResource mutató hivatkozás, amelynek protokollját a "https" protokollra kell beállítani. Egyszerre több EndpointCertificate is hozzáadhat.  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_ ]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

Linux-fürtök esetén a **saját** áruház alapértelmezett értéke a **/var/lib/sfcerts** mappa.

Egy HTTPS-végpontot használó teljes alkalmazásra példát a következő témakörben talál: [https-végpont hozzáadása ASP.net Core webes API előtér-szolgáltatáshoz a vércse használatával](./service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest).

## <a name="port-acling-for-http-endpoints"></a>HTTP-végpontok hozzáférés portja
A Service Fabric automatikusan megadja a HTTP (S)-végpontok alapértelmezett beállítását. **Nem** végez automatikus hozzáférés, ha egy végponthoz nincs társítva [SecurityAccessPolicy](service-fabric-assign-policy-to-endpoint.md) , és a Service Fabric úgy van konfigurálva, hogy rendszergazdai jogosultságokkal rendelkező fiókkal fusson.

## <a name="overriding-endpoints-in-servicemanifestxml"></a>Végpontok felülbírálása a ServiceManifest.xmlban

A ApplicationManifest adjon hozzá egy ResourceOverrides szakaszt, amely egy testvér a ConfigOverrides szakasz lesz. Ebben a szakaszban megadhatja a végpontok szakaszának felülbírálását a szolgáltatás jegyzékfájljában megadott erőforrások szakaszban. A felülbírálási végpontok a Runtime 5.7.217/SDK 2.7.217 és újabb verziókban támogatottak.

Ahhoz, hogy a végpontot felülbírálja a ServiceManifest a ApplicationParameters használatával, módosítsa a ApplicationManifest a következőképpen:

A ServiceManifestImport szakaszban adjon hozzá egy új "ResourceOverrides" szakaszt.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

Adja meg az alábbi paramétereket:

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

Az alkalmazás telepítése során ezeket az értékeket ApplicationParameters-ként adhatja át.  Például:

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

Megjegyzés: Ha egy adott ApplicationParameter megadott érték üres, a megfelelő Végpontneve tartozó ServiceManifest megadott alapértelmezett értékre megyünk vissza.

Például:

Ha a megadott ServiceManifest

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

Tegyük fel, hogy az alkalmazás paramétereinek Port1 és Protocol1 értéke null vagy üres. A portot a ServiceFabric határozzák meg, a protokoll pedig TCP lesz.

Tegyük fel, hogy helytelen értéket ad meg. Tegyük fel, hogy a porthoz a "foo" karakterlánc-értéket adta meg egy int helyett.  A New-ServiceFabricApplication parancs hibával meghiúsul: `The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int'.`

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt ismerteti, hogyan lehet végpontokat meghatározni a Service Fabric szolgáltatási jegyzékfájljában. Részletesebb példákat a következő témakörben talál:

> [!div class="nextstepaction"]
> [Példák alkalmazások és szolgáltatások jegyzékeire](service-fabric-manifest-examples.md)

Egy meglévő alkalmazás Service Fabric-fürtön történő üzembe helyezéséhez és telepítéséhez tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Meglévő végrehajtható fájl becsomagolása és üzembe helyezése Service Fabric](service-fabric-deploy-existing-app.md)