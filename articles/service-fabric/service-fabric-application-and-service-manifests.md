---
title: Az Azure Service Fabric-alkalmazások és-szolgáltatások leírása
description: Leírja, hogyan használják a jegyzékfájlokat Service Fabric alkalmazások és szolgáltatások leírására.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: fcf4c7611f0a6f52c28b234717b9244ac58ad2d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86248220"
---
# <a name="service-fabric-application-and-service-manifests"></a>Service Fabric alkalmazás-és szolgáltatás-jegyzékfájlok
Ez a cikk azt ismerteti, hogyan történik a Service Fabric alkalmazások és szolgáltatások meghatározása és verziószámozása a ApplicationManifest.xml és ServiceManifest.xml fájlok használatával.  Részletesebb példákért tekintse meg az [alkalmazás-és szolgáltatás-jegyzékfájl példáit](service-fabric-manifest-examples.md).  A jegyzékfájlok XML-sémája dokumentálva van a [ServiceFabricServiceModel. XSD-séma dokumentációjában](service-fabric-service-model-schema.md).

> [!WARNING]
> A jegyzékfájl XML-fájl sémája kikényszeríti a gyermek elemek helyes sorrendjét.  Részleges megkerülő megoldásként nyissa meg a "C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" kifejezést a Visual Studióban a Service Fabric-jegyzékek bármelyikének létrehozásakor vagy módosításakor. Ez lehetővé teszi, hogy megtekintse a gyermek elemek sorrendjét, és az ész értelmet adja.

## <a name="describe-a-service-in-servicemanifestxml"></a>Szolgáltatás leírása ServiceManifest.xml
A szolgáltatás jegyzékfájlja deklaratívul határozza meg a szolgáltatás típusát és verzióját. Meghatározza a szolgáltatás metaadatait, például a szolgáltatástípus, az állapot tulajdonságai, a terheléselosztás metrikáit, a szolgáltatás bináris fájljait és a konfigurációs fájlokat.  Ezt egy másik módszer írja le, amely leírja, hogy milyen kód-, konfigurációs és adatcsomagokat állít össze egy vagy több szolgáltatás típusának támogatásához. A szolgáltatási jegyzékfájl több kód-, konfigurációs és adatcsomagot is tartalmazhat, amelyek egymástól függetlenül telepíthetők. Itt látható a [szavazási minta alkalmazás](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) ASP.net Core webes előtér-szolgáltatásának szolgáltatási jegyzékfájlja (és íme néhány [részletesebb példa](service-fabric-manifest-examples.md)):

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
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

A **verzió** attribútumai strukturálatlan karakterláncok, és a rendszer nem elemzi őket. A verziók attribútumai az egyes összetevők verziófrissítéséhez használhatók.

A **ServiceTypes** azt deklarálja, hogy a **CodePackages** milyen szolgáltatási típusokat támogat ebben a jegyzékfájlban. Ha egy szolgáltatás egy ilyen típusú szolgáltatáshoz van létrehozva, az ebben a jegyzékfájlban deklarált összes kód-csomagot a rendszer a belépési pontok futtatásával aktiválja. Az eredményül kapott folyamatokat a rendszer futási időben regisztrálja a támogatott szolgáltatások típusait. A szolgáltatások típusai a jegyzékfájl szintjén vannak deklarálva, nem a kód csomag szintjén. Így ha több kód is van, akkor azok mind aktiválva vannak, amikor a rendszer a deklarált szolgáltatások bármelyikét keresi.

A **BelépésiPont** által megadott végrehajtható fájl általában a hosszan futó szolgáltatás gazdagépe. A **SetupEntryPoint** egy rendszerjogosultságú belépési pont, amely ugyanazzal a hitelesítő adatokkal fut, mint a Service Fabric (jellemzően a *LocalSystem* fiók) a többi belépési pont előtt.  Egy különálló telepítési belépési pont jelenléte elkerüli, hogy a szolgáltatási gazdagépet hosszú ideig magas jogosultságokkal futtassa. A **BelépésiPont** által megadott végrehajtható fájl futtatása a **SetupEntryPoint** sikeres bezárása után történik. Ha a folyamat már leáll vagy összeomlik, az eredményül kapott folyamat figyelése és újraindítása (a **SetupEntryPoint**-vel kezdődően).  

A **SetupEntryPoint** használatára jellemző forgatókönyvek, amikor végrehajtható fájlt futtat a szolgáltatás elindítása előtt, vagy emelt szintű jogosultságokkal hajt végre műveletet. Például:

* A szolgáltatás végrehajtható fájlja által igényelt környezeti változók beállítása és inicializálása. Ez nem korlátozódik kizárólag az Service Fabric programozási modelleken keresztül írt végrehajtható fájlokra. npm.exe például szüksége van egy node.js alkalmazás üzembe helyezéséhez konfigurált környezeti változóra.
* Hozzáférés-vezérlés beállítása biztonsági tanúsítványok telepítésével.

A SetupEntryPoint konfigurálásával kapcsolatos további információkért lásd: [a szolgáltatás telepítési belépési pontja házirend konfigurálása](service-fabric-application-runas-security.md)

A **EnvironmentVariables** (az előző példában nem megadott) tartalmazza a programkódhoz beállított környezeti változók listáját. A környezeti változók felülbírálása a alkalmazásban a `ApplicationManifest.xml` különböző szolgáltatási példányok eltérő értékeinek megadásával lehetséges. 

A **datapackage szakaszából** (nem az előző példában) deklarálja a **Name** attribútum által elnevezett mappát, amely tetszőleges statikus adatmennyiséget tartalmaz, amelyet a folyamat futási időben felhasználhat.

A **ConfigPackage** deklarál egy *Settings.xml* nevű fájlt tartalmazó mappát a **Name attribútum nevével** . A Settings (beállítások) fájl a felhasználó által definiált kulcs-érték párokat tartalmazó részeket tartalmazza, amelyeket a folyamat futási időben olvas vissza. Ha a frissítés során csak a **ConfigPackage** **verziója** módosult, akkor a futó folyamat nem indul újra. Ehelyett a visszahívás értesíti a konfigurációs beállítások megváltozásának folyamatát, hogy azok dinamikusan is újratölthetők legyenek. Példa *Settings.xml* fájlra:

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

A Service Fabric szolgáltatási **végpont** egy Service Fabric-erőforrásra mutat példát. Egy Service Fabric erőforrás deklarálható/módosítható a lefordított kód módosítása nélkül. A szolgáltatás jegyzékfájljában megadott Service Fabric erőforrások elérését az alkalmazás jegyzékfájljának **SecurityGroup** keresztül lehet szabályozni. Ha egy végponti erőforrás van definiálva a szolgáltatás jegyzékfájljában, akkor Service Fabric a fenntartott alkalmazás-porttartomány portjait rendeli hozzá, ha nincs explicit módon megadva port. További információ a [végponti erőforrások megadásáról vagy felülbírálásáról](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> A statikus portok megtervezése nem fedi át a ClusterManifest megadott alkalmazási porttartomány tartományát. Ha statikus portot ad meg, az alkalmazás-porttartomány kívülre rendeli, ellenkező esetben a port ütközéseket eredményez. A Release 6.5 CU2 esetében az ütközés észlelése után **Figyelmeztetés** jelenik meg, de az üzemelő példány szinkronizálása a szállított 6,5-viselkedéssel folytatódik. Előfordulhat azonban, hogy az alkalmazás központi telepítését a következő jelentős kiadásokból meggátoljuk.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Alkalmazás leírása ApplicationManifest.xml
Az alkalmazás jegyzékfájlja deklaratívan leírja az alkalmazás típusát és verzióját. Meghatározza a szolgáltatás-összeállítási metaadatokat, például a stabil neveket, a particionálási sémát, a példányok darabszámát/replikálási tényezőjét, a biztonsági/elkülönítési házirendet, az elhelyezési korlátozásokat, a konfiguráció felülbírálásait és az összetevők A terheléselosztási tartományok, amelyekben az alkalmazás elhelyezve van, szintén le vannak írva.

Így az alkalmazás-jegyzékfájl az alkalmazás szintjén leírja az elemeket, és egy vagy több szolgáltatási jegyzékfájlra hivatkozik az alkalmazás típusának összeállításához. Itt látható a [szavazási minta alkalmazáshoz](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) tartozó alkalmazási jegyzékfájl (és íme néhány [részletesebb példa](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
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
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

A szolgáltatási jegyzékfájlokhoz hasonlóan a **verziók** attribútumai nem strukturálatlan karakterláncok, és a rendszer nem elemzi őket. A verzió attribútumait a rendszer az egyes összetevők verziófrissítéshez is használja.

A **Paraméterek** az alkalmazás jegyzékfájljában használt paramétereket határozzák meg. Ezeknek a paramétereknek az értékei az alkalmazás példányainak létrehozásakor és az alkalmazás vagy szolgáltatás konfigurációs beállításainak felülbírálásával állíthatók be.  Az alapértelmezett paraméterérték akkor használatos, ha az érték nem módosul az alkalmazás létrehozásakor. Ha szeretné megtudni, hogyan kezelheti az egyes környezetek különböző alkalmazás-és szolgáltatási paramétereit, tekintse meg az [alkalmazások paramétereinek kezelése több környezethez](service-fabric-manage-multiple-environment-app-configuration.md)című témakört.

A **ServiceManifestImport** az alkalmazás típusát alkotó szolgáltatás-jegyzékfájlokra mutató hivatkozásokat tartalmaz. Az alkalmazás jegyzékfájlja több Service manifest-importálást is tartalmazhat, amelyek egymástól függetlenül telepíthetők. Az importált szolgáltatás-jegyzékfájlok határozzák meg, hogy milyen típusú szolgáltatások érvényesek ezen az alkalmazás-típuson belül. A ServiceManifestImport belül felülbírálja Settings.xml és környezeti változók konfigurációs értékeit ServiceManifest.xml fájlokban. A végponti kötésre, a biztonságra és a hozzáférésre vonatkozó **szabályzatok** , valamint a csomagok megosztása az importált szolgáltatási jegyzékeken állítható be.  További információ: [biztonsági szabályzatok konfigurálása az alkalmazáshoz](service-fabric-application-runas-security.md).

A **DefaultServices** deklarálja azokat a szolgáltatási példányokat, amelyek automatikusan létrejönnek, amikor egy alkalmazás ezen az alkalmazási típuson jön létre. Az alapértelmezett szolgáltatások csak kényelmi jellegűek, és minden tekintetben ugyanúgy viselkednek, mint a normál szolgáltatások. Ezek az alkalmazás-példány minden más szolgáltatásával együtt frissülnek, és szintén eltávolíthatók. Az alkalmazás jegyzékfájlja több alapértelmezett szolgáltatást is tartalmazhat.

Az előző példában nem beállított **tanúsítványok** deklarálják a [https-végpontok beállításához](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) vagy [a titkok titkosításához használt tanúsítványokat az alkalmazás jegyzékfájljában](service-fabric-application-secret-management.md).

Az **elhelyezési megkötések** azok a utasítások, amelyek meghatározzák, hogy a szolgáltatások hol futnak. Ezek az utasítások egy vagy több csomópont-tulajdonsághoz kiválasztott egyes szolgáltatásokhoz vannak csatolva. További információ: [elhelyezési korlátozások és csomópont-tulajdonságok szintaxisa](./service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-property-syntax)

Az előző példában nem megadott **házirendek** ismertetik a naplózási gyűjteményt, az [alapértelmezett futtatási](service-fabric-application-runas-security.md) [állapotot](service-fabric-health-introduction.md#health-policies), valamint a [biztonsági hozzáférési](service-fabric-application-runas-security.md) szabályzatokat, amelyek az alkalmazás szintjén állíthatók be, beleértve azt is, hogy a szolgáltatás (ok) hozzáfér-e a Service Fabric futtatókörnyezethez.

> [!NOTE] 
> Alapértelmezés szerint Service Fabric alkalmazások hozzáférhetnek a Service Fabric futtatókörnyezethez, az alkalmazásspecifikus kérelmeket fogadó végpont formájában, valamint környezeti változók, amelyek a hálót és az alkalmazásspecifikus fájlokat tartalmazó gazdagép fájlelérési útjaira mutatnak. Érdemes lehet letiltani ezt a hozzáférést, ha az alkalmazás nem megbízható programkódot (például olyan kódot tartalmaz, amelynek a neve ismeretlen, vagy amelyről az alkalmazás tulajdonosa nem tudja végrehajtani a biztonságos végrehajtást). További információkért tekintse [meg a Service Fabric ajánlott biztonsági eljárásokat](service-fabric-best-practices-security.md#platform-isolation). 
>

A **rendszerbiztonsági tag (amelyek** nem szerepelnek a fenti példában) ismertetik a [szolgáltatások és a biztonságos szolgáltatási erőforrások futtatásához](service-fabric-application-runas-security.md)szükséges rendszerbiztonsági tag (felhasználók vagy csoportok) leírását.  A rendszerbiztonsági tag hivatkozásokat tartalmaz a **szabályzatok** szakaszban.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Következő lépések
- [Alkalmazás becsomagolása](service-fabric-package-apps.md) és üzembe helyezése készen áll.
- [Alkalmazások telepítése és eltávolítása](service-fabric-deploy-remove-applications.md).
- [Konfigurálja a paramétereket és a környezeti változókat a különböző alkalmazás-példányokhoz](service-fabric-manage-multiple-environment-app-configuration.md).
- [Konfigurálja az alkalmazás biztonsági szabályzatait](service-fabric-application-runas-security.md).
- [Https-végpontok beállítása](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service).
- [A titkok titkosítása az alkalmazás jegyzékfájljában](service-fabric-application-secret-management.md)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
