---
title: Azure Service Fabric üzembe helyezés a FabricClient
description: A FabricClient API-kkal alkalmazásokat telepíthet és távolíthat el Service Fabricban.
ms.topic: conceptual
ms.date: 01/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 343a37c983b1d64a4b1986913d9d6fd648a113fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785549"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Alkalmazások telepítése és eltávolítása a FabricClient használatával
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric parancssori felület](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Miután [becsomagolta az alkalmazás típusát][10], készen áll az üzembe helyezésre egy Azure Service Fabric-fürtön. Az üzembe helyezés a következő három lépésből áll:

1. Alkalmazáscsomag feltöltése a rendszerkép-tárolóba
2. Az alkalmazás típusának regisztrálása
3. Az alkalmazáscsomag eltávolítása a rendszerkép-tárolóból
4. Az alkalmazás példányának létrehozása

Miután telepített egy alkalmazást, és futtatott egy példányt a fürtben, törölheti az alkalmazás példányát és az alkalmazás típusát. Az alábbi lépéseket követve teljesen eltávolíthat egy alkalmazást a fürtből:

1. A futó alkalmazás példányának eltávolítása (vagy törlése)
2. Az alkalmazás típusának regisztrációjának törlése, ha már nincs rá szükség

Ha a Visual studiót használja az alkalmazások helyi fejlesztési fürtön való üzembe helyezéséhez és hibakereséséhez, a rendszer az összes korábbi lépést automatikusan egy PowerShell-parancsfájl segítségével kezeli.  Ez a szkript az alkalmazás projekt *szkriptek* mappájában található. Ez a cikk a szkript működésének hátterét ismerteti, így ugyanazokat a műveleteket végezheti el a Visual studión kívül. 
 
## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz
Kapcsolódjon a fürthöz egy [FabricClient](/dotnet/api/system.fabric.fabricclient) -példány létrehozásával, mielőtt futtatja a jelen cikkben szereplő programkódot. A helyi fejlesztési fürthöz való csatlakozásra, illetve a Azure Active Directory, X509 tanúsítványok vagy a Windows Active Directory használatával biztonságossá tett távoli fürthöz vagy fürthöz való kapcsolódásra vonatkozó Példákért lásd: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). A helyi fejlesztési fürthöz való kapcsolódáshoz futtassa a következő példát:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Alkalmazáscsomag feltöltése
Tegyük fel, hogy létrehoz és becsomagol egy *MyApplication* nevű alkalmazást a Visual Studióban. Alapértelmezés szerint az ApplicationManifest.xml "MyApplicationType" néven szerepel az alkalmazás típusa.  Az alkalmazás-jegyzékfájlt, a szolgáltatási jegyzékfájlokat és a Code/config/adatcsomagokat tartalmazó alkalmazáscsomag a *C:\Users \& lt; username &gt; \Documents\Visual Studio 2019 \ Projects\MyApplication\MyApplication\pkg\Debug* helyen található.

Az alkalmazáscsomag feltöltése a belső Service Fabric-összetevők által elérhető helyre helyezi azt. Service Fabric ellenőrzi az alkalmazáscsomag regisztrálását az alkalmazáscsomag regisztrálása során. Ha azonban helyileg szeretné ellenőrizni az alkalmazáscsomag használatát (azaz a feltöltés előtt), használja a [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) parancsmagot.

A [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API feltölti az alkalmazáscsomag a fürt rendszerkép-tárolójába. 

Ha az alkalmazáscsomag nagyméretű és/vagy sok fájllal rendelkezik, [tömörítheti azt](service-fabric-package-apps.md#compress-a-package) , és átmásolhatja a rendszerkép-tárolóba a PowerShell használatával. A tömörítés csökkenti a méretet és a fájlok számát.

A rendszerkép-tároló és a rendszerkép-tároló közötti kapcsolatok karakterláncával kapcsolatos további információkért lásd: [a rendszerkép-tárolási kapcsolatok karakterláncának megismerése](service-fabric-image-store-connection-string.md) .

## <a name="register-the-application-package"></a>Alkalmazáscsomag regisztrálása
Az alkalmazás jegyzékfájljában deklarált alkalmazás típusa és verziója elérhetővé válik az alkalmazáscsomag regisztrálása során. A rendszer beolvassa az előző lépésben feltöltött csomagot, ellenőrzi a csomagot, feldolgozza a csomag tartalmát, és átmásolja a feldolgozott csomagot egy belső rendszer helyére.  

A [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API regisztrálja az alkalmazás típusát a fürtben, és elérhetővé teszi az üzembe helyezéshez.

A [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API információt nyújt az összes sikeresen regisztrált alkalmazás típusáról. Ezzel az API-val meghatározhatja, hogy mikor történik a regisztráció.

## <a name="remove-an-application-package-from-the-image-store"></a>Alkalmazáscsomag eltávolítása a rendszerkép-tárolóból
Javasoljuk, hogy az alkalmazás sikeres regisztrálása után távolítsa el az alkalmazáscsomag-csomagot.  A rendszerkép-tárolóban lévő alkalmazáscsomag törlése a rendszererőforrásokat felszabadítja.  A nem használt alkalmazáscsomag megőrzése a lemezes tárolást és az alkalmazások teljesítményével kapcsolatos problémákat eredményez. Törölje az alkalmazáscsomag a rendszerkép-áruházból a [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API használatával.

## <a name="create-an-application-instance"></a>Alkalmazás-példány létrehozása
A [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API használatával bármely olyan alkalmazásból létrehozhat egy alkalmazást, amely sikeresen regisztrálva van. Az egyes alkalmazások nevének a *"Fabric:"* sémával kell kezdődnie, és egyedinek kell lennie minden egyes alkalmazás-példánynál (egy fürtön belül). A rendszer az alkalmazás jegyzékfájljában definiált alapértelmezett szolgáltatásokat is létrehozza.

Több alkalmazás-példány is létrehozható egy regisztrált alkalmazás típusának bármely adott verziójához. Minden egyes alkalmazás-példány elkülönítve fut a saját munkakönyvtárával és folyamatainak készletével.

Ha szeretné megtudni, hogy mely megnevezett alkalmazások és szolgáltatások futnak a fürtben, futtassa a [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) és a [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) API-t.

## <a name="create-a-service-instance"></a>Szolgáltatás példányának létrehozása
A [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API használatával létrehozhat egy szolgáltatást a szolgáltatás típusától.  Ha a szolgáltatás az alkalmazás jegyzékfájljában alapértelmezett szolgáltatásként van deklarálva, akkor a szolgáltatás példánya az alkalmazás példányainak létrehozásakor jön létre.  Az [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API meghívása egy már példányban létrehozott szolgáltatáshoz FabricException típusú kivételt ad vissza. A kivétel a FabricErrorCode. ServiceAlreadyExists értékkel rendelkező hibakódot fogja tartalmazni.

## <a name="remove-a-service-instance"></a>Szolgáltatás példányának eltávolítása
Ha már nincs szükség szolgáltatási példányra, a [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API meghívásával eltávolíthatja azt a futó alkalmazás-példányból.  

> [!WARNING]
> Ez a művelet nem vonható vissza, és a szolgáltatási állapot nem állítható helyre.

## <a name="remove-an-application-instance"></a>Alkalmazás példányának eltávolítása
Ha egy alkalmazás-példányra már nincs szükség, véglegesen eltávolíthatja azt név alapján a [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API használatával. A [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automatikusan eltávolítja az alkalmazáshoz tartozó összes szolgáltatást is, véglegesen eltávolítja az összes szolgáltatási állapotot.

> [!WARNING]
> Ez a művelet nem vonható vissza, és az alkalmazás állapota nem állítható helyre.

## <a name="unregister-an-application-type"></a>Alkalmazás típusának törlése
Ha már nincs szükség az alkalmazás egy adott verziójára, akkor a regisztráció megszüntetésével az [ServiceFabricApplicationType API-](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) val törölni kell az adott verziót. A nem használt alkalmazások regisztrációjának törlése felszabadítja a rendszerkép-tároló által használt tárolóhelyet. Az alkalmazás típusának egy verziója törölhető, feltéve, hogy egyetlen alkalmazás sem lett létrehozva az alkalmazás típusának adott verziójára. Emellett az alkalmazás típusa nem lehet függőben lévő alkalmazás-frissítés, amely az alkalmazás típusának adott verziójára hivatkozik.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage kér egy ImageStoreConnectionString
A Service Fabric SDK-környezetnek már meg kell határoznia a megfelelő alapértelmezett beállításokat. Ha azonban szükség van rá, az összes parancs ImageStoreConnectionString meg kell egyeznie a Service Fabric-fürt által használt értékkel. A ImageStoreConnectionString megkeresheti a fürt jegyzékfájljában, a [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) és a Get-ImageStoreConnectionStringFromClusterManifest parancs használatával:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

A Service Fabric SDK PowerShell-modul részét képező **Get-ImageStoreConnectionStringFromClusterManifest** parancsmag a rendszerkép-tároló kapcsolati karakterláncának beolvasására szolgál.  Az SDK-modul importálásához futtassa a következőt:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```


A ImageStoreConnectionString a fürt jegyzékfájljában található:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

A rendszerkép-tároló és a rendszerkép-tároló közötti kapcsolatok karakterláncával kapcsolatos további információkért lásd: [a rendszerkép-tárolási kapcsolatok karakterláncának megismerése](service-fabric-image-store-connection-string.md) .

### <a name="deploy-large-application-package"></a>Nagyméretű alkalmazáscsomag üzembe helyezése
Probléma: a [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API időtúllépést jelent egy nagyméretű alkalmazáscsomag esetében (GB-os sorrend).
Próbálja ki:
- A paraméterrel nagyobb időkorlát adható meg a [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) metódushoz `timeout` . Alapértelmezés szerint az időtúllépés 30 percet vesz igénybe.
- Keresse meg a számítógép és a fürt közötti hálózati kapcsolatot. Ha a kapcsolatok lassúak, érdemes lehet olyan gépet használni, amelynek jobb hálózati kapcsolatai vannak.
Ha az ügyfélszámítógép más régióban található, mint a fürt, érdemes lehet egy ügyfélszámítógépet használni a fürttel megegyező vagy azonos régióban.
- Ellenőrizze, hogy van-e külső szabályozás. Ha például a rendszerkép-tároló az Azure Storage használatára van konfigurálva, akkor a feltöltés szabályozható.

Probléma: a feltöltési csomag sikeresen befejeződött, de [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API-időtúllépés történt. Próbálja
- [Tömörítse a csomagot](service-fabric-package-apps.md#compress-a-package) a rendszerkép-tárolóba történő másolás előtt.
A tömörítés csökkenti a méretet és a fájlok számát, ami viszont csökkenti a forgalom mennyiségét és a Service Fabric által végrehajtandó munkát. A feltöltési művelet lassabb lehet (különösen akkor, ha belefoglalja a tömörítési időt), de az alkalmazás típusának regisztrálása és törlése gyorsabb.
- A paraméterrel nagyobb időkorlát adható meg a [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API-hoz `timeout` .

### <a name="deploy-application-package-with-many-files"></a>Alkalmazáscsomag központi telepítése sok fájllal
Probléma: a [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) időtúllépést jelent a sok fájlt tartalmazó alkalmazáscsomag esetében (több ezer).
Próbálja ki:
- [Tömörítse a csomagot](service-fabric-package-apps.md#compress-a-package) a rendszerkép-tárolóba történő másolás előtt. A tömörítés csökkenti a fájlok számát.
- Nagyobb időtúllépést ad meg [](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) a ProvisionApplicationAsync `timeout` paraméterrel.

## <a name="code-example"></a>Mintakód
Az alábbi példa egy alkalmazáscsomag átmásolása a rendszerkép-tárolóba, és az alkalmazás típusának kiosztása. Ezután a példa létrehoz egy alkalmazás-példányt, és létrehoz egy szolgáltatási példányt. Végül a példa eltávolítja az alkalmazás példányát, leállítja az alkalmazás típusát, és törli az alkalmazáscsomagt a rendszerkép-tárolóból.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Következő lépések
[Service Fabric alkalmazás frissítése](service-fabric-application-upgrade.md)

[Service Fabric állapot bemutatása](service-fabric-health-introduction.md)

[Service Fabric szolgáltatás diagnosztizálása és megoldása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Alkalmazás modellezése Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
