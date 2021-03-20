---
title: Azure Service Fabric üzembe helyezés a PowerShell-lel
description: Ismerje meg, hogyan távolíthat el és helyezhet üzembe alkalmazásokat az Azure Service Fabricban, és hogyan hajthatja végre ezeket a műveleteket a PowerShellben.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: d428a51c0bc224ca8706403ae176d46f1db82a32
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785447"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Alkalmazások telepítése és eltávolítása a PowerShell használatával

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric parancssori felület](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient API-k](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Miután [becsomagolta az alkalmazás típusát][10], készen áll az üzembe helyezésre egy Azure Service Fabric-fürtön. Az üzembe helyezés a következő három lépésből áll:

1. Töltse fel az alkalmazáscsomag a rendszerkép-tárolóba.
2. Regisztrálja az alkalmazás típusát a rendszerkép-tároló relatív elérési útján.
3. Hozza létre az alkalmazás példányát.

Ha már nincs szükség az üzembe helyezett alkalmazásra, törölheti az alkalmazás példányát és az alkalmazás típusát. Az alkalmazások fürtből való teljes eltávolításához a következő lépések szükségesek:

1. Távolítsa el (vagy törölje) a futó alkalmazás példányát.
2. Szüntesse meg az alkalmazás típusának regisztrációját, ha már nincs rá szüksége.
3. Távolítsa el az alkalmazáscsomag a rendszerkép-tárolóból.

Ha a Visual studiót használja az alkalmazások helyi fejlesztési fürtön való üzembe helyezéséhez és hibakereséséhez, a rendszer az összes korábbi lépést automatikusan egy PowerShell-parancsfájl segítségével kezeli.  Ez a szkript az alkalmazás projekt *szkriptek* mappájában található. Ez a cikk a szkript működésének hátterét ismerteti, így a Visual studión kívül is végrehajthatja a műveleteket. 

Az alkalmazások üzembe helyezésének másik módja a külső kiépítés használata. Az alkalmazáscsomag [becsomagolható `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) és feltölthető egy külső tárolóba. Ebben az esetben nincs szükség a rendszerkép-tárolóba való feltöltésre. Az üzembe helyezéshez a következő lépések szükségesek:

1. Töltse fel a `sfpkg` -t egy külső tárolóba. A külső tároló bármely olyan tároló lehet, amely egy REST http-vagy https-végpontot tesz elérhetővé.
2. Regisztrálja az alkalmazás típusát a külső letöltési URI-val és az alkalmazás típusával kapcsolatos információk használatával.
2. Hozza létre az alkalmazás példányát.

A tisztításhoz távolítsa el az alkalmazás példányait, és törölje az alkalmazás típusának regisztrációját. Mivel a csomag nem lett átmásolva a rendszerkép-tárolóba, nincs ideiglenes hely a tisztításhoz. A külső tárolóból való kiépítés az 6,1-es verziótól kezdődően érhető el, Service Fabric.

>[!NOTE]
> A Visual Studio jelenleg nem támogatja a külső létesítést.

 

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A cikkben található PowerShell-parancsok futtatása előtt a Service Fabric-fürthöz való kapcsolódáshoz mindig a [ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) használatával kezdjen. A helyi fejlesztési fürthöz való kapcsolódáshoz futtassa a következőt:

```powershell
Connect-ServiceFabricCluster
```

A Azure Active Directory-, X509-vagy Windows-Active Directory használatával biztonságossá tett távoli fürthöz vagy fürthöz való csatlakozásra például a [biztonságos fürthöz való csatlakozást](service-fabric-connect-to-secure-cluster.md)ismertető témakörben talál további információt.

## <a name="upload-the-application-package"></a>Alkalmazáscsomag feltöltése

Az alkalmazáscsomag feltöltése a belső Service Fabric-összetevők által elérhető helyre helyezi.
Ha helyileg szeretné ellenőrizni az alkalmazáscsomag használatát, használja a [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) parancsmagot.

A [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) parancs feltölti az alkalmazáscsomag a fürt rendszerkép-tárolójába.

Tegyük fel, hogy létrehoz és becsomagol egy *MyApplication* nevű alkalmazást a Visual Studio 2015-ben. Alapértelmezés szerint az ApplicationManifest.xml "MyApplicationType" néven szerepel az alkalmazás típusa.  Az alkalmazás-jegyzékfájlt, a szolgáltatási jegyzékfájlokat és a Code/config/adatcsomagokat tartalmazó alkalmazáscsomag a *C:\Users \<username\> \Documents\Visual Studio 2015 \ Projects\MyApplication\MyApplication\pkg\Debug* címen található. 

A következő parancs felsorolja az alkalmazáscsomag tartalmát:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Ha az alkalmazáscsomag nagyméretű és/vagy sok fájllal rendelkezik, [tömörítheti](service-fabric-package-apps.md#compress-a-package)is. A tömörítés csökkenti a méretet és a fájlok számát.
Ez gyorsabb regisztrálást és az alkalmazás típusának regisztrációját eredményezi. A feltöltési idő jelenleg lassabb lehet, különösen akkor, ha a csomag tömörítésének idejét is tartalmazza. 

A csomagok tömörítéséhez használja ugyanazt a [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) parancsot. A tömörítés elkülöníthető a feltöltéstől, a `SkipCopy` jelző használatával vagy a feltöltési művelettel. Tömörített csomag tömörítésének alkalmazása nem-op.
Tömörített csomag kibontásához használja ugyanazt a [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) parancsot a `UncompressPackage` kapcsolóval.

A következő parancsmag a rendszerkép-tárolóba történő másolás nélkül tömöríti a csomagot. A csomag mostantól tartalmazza a és a csomagok tömörített fájljait `Code` `Config` . Az alkalmazás és a szolgáltatási jegyzékfájlok nem tömörítettek, mert számos belső művelethez szükségesek (például a csomagok megosztása, az alkalmazásnév neve és a verziók kinyerése bizonyos érvényességek esetén). A jegyzékfájlok kijavítása nem teszi hatékonyabbá a műveleteket.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

Nagyméretű alkalmazáscsomag esetén a tömörítés időt vesz igénybe. A legjobb eredmény érdekében használjon egy gyors SSD-meghajtót. A tömörítési idő és a tömörített csomag mérete is eltérő a csomag tartalma alapján.
Íme például néhány csomag tömörítési statisztikája, amely a kezdeti és a tömörített csomag méretét jeleníti meg a tömörítési idővel.

|Kezdeti méret (MB)|Fájlok száma|Tömörítési idő|Tömörített csomag mérete (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

A csomag tömörítése után egy vagy több Service Fabric-fürtre szükség szerint feltölthető. Az üzembe helyezési mechanizmus a tömörített és a tömörítetlen csomagok esetében azonos. A tömörített csomagokat a rendszer a fürt rendszerkép-tárolójában tárolja. A csomagok kibontása a csomóponton az alkalmazás futtatása előtt történik.


A következő példa feltölti a csomagot a rendszerkép-tárolóba egy "MyApplicationV1" nevű mappába:

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Ha nem ad meg a *-ApplicationPackagePathInImageStore* paramétert, az alkalmazáscsomag a rendszerkép-tároló "hibakeresés" mappájába lesz másolva.

>[!NOTE]
>A **copy-ServiceFabricApplicationPackage** automatikusan felismeri a megfelelő rendszerkép-tárolási kapcsolati karakterláncot, ha a PowerShell-munkamenet egy Service Fabric-fürthöz csatlakozik. Service Fabric az 5,6-nál régebbi verziók esetében a **-ImageStoreConnectionString** argumentumot explicit módon meg kell adni.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>A Service Fabric SDK PowerShell-modul részét képező **Get-ImageStoreConnectionStringFromClusterManifest** parancsmag a rendszerkép-tároló kapcsolati karakterláncának beolvasására szolgál.  Az SDK-modul importálásához futtassa a következőt:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>A rendszerkép-tároló és a rendszerkép-tároló közötti kapcsolatok karakterláncával kapcsolatos további információkért lásd: [a rendszerkép-tárolási kapcsolatok karakterláncának megismerése](service-fabric-image-store-connection-string.md) .
>
>
>

A csomagok feltöltéséhez szükséges idő több tényezőtől függően eltérő lehet. Néhány tényező a csomagban található fájlok száma, a csomag mérete és a fájlméret. A forrásoldali gép és a Service Fabric-fürt közötti hálózati sebesség a feltöltési időt is befolyásolja. A [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) alapértelmezett időtúllépése 30 perc.
A leírt tényezőktől függően előfordulhat, hogy meg kell emelnie az időtúllépést. Ha a másolási hívás során tömöríti a csomagot, a tömörítési időt is figyelembe kell vennie.



## <a name="register-the-application-package"></a>Alkalmazáscsomag regisztrálása

Az alkalmazás jegyzékfájljában deklarált alkalmazás típusa és verziója elérhetővé válik az alkalmazáscsomag regisztrálása során. A rendszer beolvassa az előző lépésben feltöltött csomagot, ellenőrzi a csomagot, feldolgozza a csomag tartalmát, és átmásolja a feldolgozott csomagot egy belső rendszer helyére.  

Futtassa a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) parancsmagot, és regisztrálja az alkalmazás típusát a fürtben, és tegye elérhetővé az üzembe helyezéshez:

### <a name="register-the-application-package-copied-to-image-store"></a>A rendszerkép-tárolóba másolt alkalmazáscsomag regisztrálása

Ha a csomagot előzőleg a rendszerkép-tárolóba másolták, a regisztrálási művelet a rendszerkép-tárolóban lévő relatív elérési utat határozza meg.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

A "MyApplicationV1" a rendszerkép-tároló azon mappája, ahol az alkalmazáscsomag található. Már regisztrálva van a fürtben a "MyApplicationType" és a "1.0.0" nevű alkalmazás típusa (mindkettő megtalálható az alkalmazás jegyzékfájljában).

### <a name="register-the-application-package-copied-to-an-external-store"></a>A külső tárolóba másolt alkalmazáscsomag regisztrálása

A Service Fabric 6,1-es verziótól kezdődően a kiépítés támogatja a csomag külső tárból való letöltését. A letöltési URI azt az [ `sfpkg` alkalmazáscsomag](service-fabric-package-apps.md#create-an-sfpkg) elérési útját adja meg, AHONNAN az ALKALMAZÁSCSOMAG http vagy HTTPS protokoll használatával tölthető le. A csomagot előzőleg fel kell tölteni erre a külső helyre. Az URI-nak engedélyezni kell az OLVASÁSI hozzáférést, hogy Service Fabric le tudja tölteni a fájlt. A `sfpkg` fájlnak ". sfpkg" kiterjesztéssel kell rendelkeznie. A kiépítési műveletnek tartalmaznia kell az alkalmazás típusára vonatkozó információkat, ahogy az az alkalmazás jegyzékfájljában található.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

A [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) parancs csak azt követően tér vissza, hogy a System sikeresen regisztrálta az alkalmazáscsomag. A regisztráció időtartama az alkalmazáscsomag méretétől és tartalmától függ. Ha szükséges, a **-TimeoutSec** paraméter hosszabb időtúllépést is igénybe vehet (az alapértelmezett időkorlát 60 másodperc).

Ha nagyméretű alkalmazáscsomag van, vagy ha időtúllépést tapasztal, használja az **-aszinkron** paramétert. A parancs visszaadja, ha a fürt elfogadja a Register parancsot. A regisztrálási művelet szükség szerint folytatódik.
A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) parancs felsorolja az alkalmazás típusának verziószámát és a regisztráció állapotát. Ezzel a paranccsal meghatározhatja, hogy mikor történik a regisztráció.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Alkalmazáscsomag eltávolítása a rendszerkép-tárolóból

Ha a csomagot a rendszerkép-tárolóba másolták, akkor az alkalmazás sikeres regisztrálása után távolítsa el az ideiglenes helyről. A rendszerkép-tárolóban lévő alkalmazáscsomag törlése a rendszererőforrásokat felszabadítja. A nem használt alkalmazáscsomag megőrzése a lemezes tárolást és az alkalmazások teljesítményével kapcsolatos problémákat eredményez.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Az alkalmazás létrehozása

A [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication) parancsmag használatával bármely olyan alkalmazás-típusból létrehozhat egy alkalmazást, amely sikeresen regisztrálva van. Az egyes alkalmazások nevének a *"Fabric:"* sémával kell kezdődnie, és minden egyes alkalmazás-példány esetében egyedinek kell lennie. A rendszer az alkalmazás jegyzékfájljában definiált alapértelmezett szolgáltatásokat is létrehozza.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Több alkalmazás-példány is létrehozható egy regisztrált alkalmazás típusának bármely adott verziójához. Minden alkalmazás-példány elkülönítve fut a saját munkahelyi könyvtárával és folyamatával.

Ha szeretné megtudni, hogy mely megnevezett alkalmazások és szolgáltatások futnak a fürtben, futtassa a [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) és a [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice) parancsmagot:

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Alkalmazás eltávolítása

Ha egy alkalmazás-példányra már nincs szükség, véglegesen eltávolíthatja azt a [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) parancsmag használatával. A [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) automatikusan eltávolítja az alkalmazáshoz tartozó összes szolgáltatást, és véglegesen eltávolítja az összes szolgáltatási állapotot. 

> [!WARNING]
> Ez a művelet nem vonható vissza, és az alkalmazás állapota nem állítható helyre.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Alkalmazás típusának törlése

Ha már nincs szükség az alkalmazás egy adott verziójára, szüntesse meg az alkalmazás típusának regisztrációját a regisztráció törlése [-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) parancsmag használatával. A nem használt alkalmazások regisztrációjának törlése a rendszerkép-tároló által használt tárolóhelyet szabadítja fel az alkalmazás típusú fájlok eltávolításával. Az alkalmazás típusának törlése nem távolítja el a rendszerkép-tároló ideiglenes helyére másolt alkalmazáscsomag, ha a másolást a rendszerkép-tárolóba használták. Az alkalmazás típusa törölhető mindaddig, amíg egyetlen alkalmazás sem lett létrehozva, és a függőben lévő alkalmazások frissítése nem hivatkozik rá.

A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) futtatásával tekintse meg a fürtben jelenleg regisztrált alkalmazások típusát:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

A [Regisztráció törlése-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) futtatása egy adott alkalmazás típusának megszüntetéséhez:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage kér egy ImageStoreConnectionString

A Service Fabric SDK-környezetnek már meg kell határoznia a megfelelő alapértelmezett beállításokat. Ha azonban szükség van rá, az összes parancs ImageStoreConnectionString meg kell egyeznie a Service Fabric-fürt által használt értékkel. A ImageStoreConnectionString megkeresheti a fürt jegyzékfájljában, a [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) és a Get-ImageStoreConnectionStringFromClusterManifest parancs használatával:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
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

Probléma: a [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) időtúllépést jelent egy nagyméretű alkalmazáscsomag esetében (GB-os sorrend).
Próbálja ki:
- Nagyobb időtúllépést ad meg a [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) parancshoz a `TimeoutSec` paraméterrel. Alapértelmezés szerint az időtúllépés 30 percet vesz igénybe.
- Keresse meg a számítógép és a fürt közötti hálózati kapcsolatot. Ha a kapcsolatok lassúak, érdemes lehet olyan gépet használni, amelynek jobb hálózati kapcsolatai vannak.
Ha az ügyfélszámítógép más régióban található, mint a fürt, érdemes lehet egy ügyfélszámítógépet használni a fürttel megegyező vagy azonos régióban.
- Ellenőrizze, hogy van-e külső szabályozás. Ha például a rendszerkép-tároló az Azure Storage használatára van konfigurálva, akkor a feltöltés szabályozható.

Probléma: a feltöltési csomag sikeresen befejeződött, de a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) időtúllépést jelent. Próbálja
- [Tömörítse a csomagot](service-fabric-package-apps.md#compress-a-package) a rendszerkép-tárolóba történő másolás előtt.
A tömörítés csökkenti a méretet és a fájlok számát, ami viszont csökkenti a forgalom mennyiségét és a Service Fabric által végrehajtandó munkát. A feltöltési művelet lassabb lehet (különösen akkor, ha belefoglalja a tömörítési időt), de az alkalmazás típusának regisztrálása és regisztrációja gyorsabb.
- Nagyobb időtúllépést ad meg a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) `TimeoutSec` paraméterrel.
- `Async`Kapcsoló megadása a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype)számára. A parancs visszaadja a parancsot, ha a fürt elfogadja a parancsot, és az alkalmazás típusának regisztrálása aszinkron módon folytatódik. Ezért ebben az esetben nincs szükség magasabb időtúllépés megadására. A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) parancs felsorolja az összes sikeresen regisztrált alkalmazáshiba-verziót és a regisztrációs állapotát. Ezzel a paranccsal meghatározhatja, hogy mikor történik a regisztráció.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Alkalmazáscsomag központi telepítése sok fájllal

Probléma: a [ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) időtúllépést jelent a sok fájllal rendelkező alkalmazáscsomag esetében (több ezer).
Próbálja ki:
- [Tömörítse a csomagot](service-fabric-package-apps.md#compress-a-package) a rendszerkép-tárolóba történő másolás előtt. A tömörítés csökkenti a fájlok számát.
- Nagyobb időtúllépést ad meg a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) `TimeoutSec` paraméterrel.
- `Async`Kapcsoló megadása a [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype)számára. A parancs visszaadja a parancsot, ha a fürt elfogadja a parancsot, és az alkalmazás típusának regisztrálása aszinkron módon folytatódik.
Ezért ebben az esetben nincs szükség magasabb időtúllépés megadására. A [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) parancs felsorolja az összes sikeresen regisztrált alkalmazáshiba-verziót és a regisztrációs állapotát. Ezzel a paranccsal meghatározhatja, hogy mikor történik a regisztráció.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Következő lépések

[Alkalmazás becsomagolása](service-fabric-package-apps.md)

[Service Fabric alkalmazás frissítése](service-fabric-application-upgrade.md)

[Service Fabric állapot bemutatása](service-fabric-health-introduction.md)

[Service Fabric szolgáltatás diagnosztizálása és megoldása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Alkalmazás modellezése Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
