---
title: Azure Service Fabric-alkalmazás becsomagolása
description: Ismerje meg az Azure Service Fabric-alkalmazások csomagolását és a fürtre történő telepítés előkészítését ismertető témakört.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 168e6d6dc7ab5bfeccc4e1dabc7bd50efcbe8f34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789702"
---
# <a name="package-an-application"></a>Alkalmazás becsomagolása

Ez a cikk azt ismerteti, hogyan lehet becsomagolni egy Service Fabric alkalmazást, és készen áll az üzembe helyezésre.

## <a name="package-layout"></a>Csomag elrendezése

Az alkalmazás jegyzékfájlját, egy vagy több szolgáltatási jegyzékfájlt és az egyéb szükséges csomagokat egy Service Fabric-fürtbe történő központi telepítéshez egy adott elrendezésben kell megszervezni. A cikkben szereplő példákat a következő címtár-struktúrában kell megszervezni:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

A mappák neve megegyezik a megfelelő elemek **nevének** attribútumaival. Ha például a szolgáltatás jegyzékfájlja két, **MyCodeA** és **MyCodeB** nevű kódot tartalmaz, akkor két azonos nevű mappa tartalmazni fogja a szükséges bináris fájlokat az egyes kódokhoz.

## <a name="use-setupentrypoint"></a>SetupEntryPoint használata

A **SetupEntryPoint** használatára jellemző forgatókönyvek, ha a szolgáltatás elindítása előtt futtatni kell egy végrehajtható fájlt, vagy emelt szintű jogosultságokkal kell végrehajtania egy műveletet. Például:

* A szolgáltatás végrehajtható fájlja által igényelt környezeti változók beállítása és inicializálása. Nem korlátozódik kizárólag a Service Fabric programozási modelleken keresztül írt végrehajtható fájlokra. npm.exe például szüksége van egy node.js alkalmazás üzembe helyezéséhez konfigurált környezeti változóra.
* Hozzáférés-vezérlés beállítása biztonsági tanúsítványok telepítésével.

A **SetupEntryPoint** konfigurálásával kapcsolatos további információkért lásd: [a szolgáltatás telepítési belépési pontja házirend konfigurálása](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurálás

### <a name="build-a-package-by-using-visual-studio"></a>Csomag létrehozása a Visual Studio használatával

Ha a Visual Studióval hozza létre az alkalmazást, a *Package* paranccsal automatikusan létrehozhat egy csomagot, amely megfelel a fent ismertetett elrendezésnek.

Csomag létrehozásához kattintson a jobb gombbal az alkalmazás projektre *megoldáskezelő* , és válassza a **csomag** parancsot:

![Alkalmazás becsomagolása a Visual Studióval][vs-package-command]

A csomagolás befejezésekor a **kimeneti** ablakban található a csomag helye. A csomagolási lépés automatikusan megtörténik az alkalmazás Visual Studióban történő telepítésekor vagy hibakeresése során.

### <a name="build-a-package-by-command-line"></a>Csomag létrehozása parancssorból

Az alkalmazás a használatával programozott módon is becsomagolható `msbuild.exe` . A Visual Studio a motorháztető alatt fut, így a kimenet ugyanaz.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>A csomag tesztelése

A [ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) parancs használatával helyileg ellenőrizheti a csomag szerkezetét a PowerShell használatával.
Ez a parancs ellenőrzi a jegyzékfájl-elemzési problémákat, és ellenőrzi az összes hivatkozást. Ez a parancs csak a csomagok könyvtárainak és fájljainak szerkezeti helyességét ellenőrzi.
A kód vagy az adatcsomag tartalmának ellenőrzése nem ellenőrzi, hogy az összes szükséges fájl megtalálható-e.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Ez a hiba azt mutatja, hogy a szolgáltatás jegyzékfájljában hivatkozott *MySetup.bat* fájl hiányzik a **SetupEntryPoint** . A hiányzó fájl hozzáadása után az alkalmazás ellenőrzésének menete:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Ha az alkalmazáshoz [alkalmazás-paraméterek](service-fabric-manage-multiple-environment-app-configuration.md) vannak meghatározva, a megfelelő ellenőrzés érdekében átadhatja azokat a [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) .

Ha ismeri azt a fürtöt, amelyen az alkalmazás telepítve lesz, javasoljuk, hogy adja át a `ImageStoreConnectionString` paramétert. Ebben az esetben a csomag a fürtben már futó alkalmazás korábbi verzióival is érvényesítve lesz. Az ellenőrzés például képes észlelni, hogy egy azonos verziójú, de különböző tartalommal rendelkező csomag már telepítve van-e.  

Ha az alkalmazás megfelelően van csomagolva, és átadja az ellenőrzést, érdemes lehet a csomag tömörítését a gyorsabb üzembe helyezési műveletekhez.

## <a name="compress-a-package"></a>Csomag tömörítése

Ha egy csomag nagyméretű vagy sok fájllal rendelkezik, a gyorsabb üzembe helyezéshez tömörítheti azt. A tömörítés csökkenti a fájlok számát és a csomag méretét.
Tömörített alkalmazáscsomag esetén [az alkalmazáscsomag feltöltése](service-fabric-deploy-remove-applications.md#upload-the-application-package) hosszabb időt vehet igénybe a tömörítetlen csomag feltöltésekor, különösen akkor, ha a tömörítés a másolás részeként történik. A tömörítés, [az alkalmazás típusának](service-fabric-deploy-remove-applications.md#unregister-an-application-type) [regisztrálása](service-fabric-deploy-remove-applications.md#register-the-application-package) és regisztrációja gyorsabb.

Az üzembe helyezési mechanizmus a tömörített és a tömörítetlen csomagok esetében azonos. Ha a csomag tömörítve van, azt a rendszer a fürt rendszerkép-tárolójában tárolja, és az alkalmazás futtatása előtt tömöríti a csomóponton.
A tömörítés lecseréli az érvényes Service Fabric csomagot a tömörített verzióra. A mappának engedélyeznie kell az írási engedélyeket. Egy már tömörített csomag tömörítésének futtatása nem változik.

A csomagokat tömörítheti úgy, hogy a PowerShell-parancsot a [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) `CompressPackage` kapcsolóval futtatja. A kapcsoló használatával kibonthatja a csomagot ugyanazzal a paranccsal `UncompressPackage` .

A következő parancs tömöríti a csomagot anélkül, hogy átmásolja azt a rendszerkép-tárolóba. A tömörített csomagokat szükség szerint egy vagy több Service Fabric-fürtre is másolhatja, a jelző nélkül a [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) használatával `SkipCopy` .
A csomag mostantól tartalmazza a, a és a csomagok tömörített fájljait `code` `config` `data` . Az alkalmazás jegyzékfájlja és a szolgáltatási jegyzékfájlok nem tömörítettek, mert számos belső művelethez szükségesek. Például a csomagok megosztása, az alkalmazás típusa és a verzió kibontása bizonyos érvényességi igényekhez mindennek hozzá kell férnie a jegyzékekhez. A jegyzékfájlok kijavítása nem teszi hatékonyabbá a műveleteket.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Azt is megteheti, hogy egy lépésben tömöríti és átmásolja a csomagot a [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) .
Ha a csomag nagyméretű, elég magas időtúllépést biztosít a csomagok tömörítéséhez és a fürtre való feltöltéshez szükséges idő megadásához.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Belsőleg a Service Fabric kiszámítja az alkalmazás-csomagok ellenőrzőösszegét az ellenőrzéshez. Tömörítés használatakor az ellenőrzőösszegeket az egyes csomagok tömörített verzióin számítjuk ki. Ha egy új zip-fájlt hoz létre ugyanabból az alkalmazáscsomag, különböző ellenőrzőösszegeket hoz létre. Az érvényesítési hibák elkerülése érdekében használja a [diff kiépítés](service-fabric-application-upgrade-advanced.md)lehetőséget. Ha ezt a beállítást választja, ne adja meg a változatlan csomagokat az új verzióban. Ehelyett közvetlenül az új szolgáltatás jegyzékfájlján keresztül hivatkozhat rájuk.

Ha a diff kiépítés nem választható, és meg kell adnia a csomagokat, új verziókat kell létrehoznia a, a és a csomagok számára, `code` `config` `data` hogy elkerülje az ellenőrzőösszeg-eltéréseket. A változatlan csomagok új verzióinak létrehozása akkor szükséges, ha tömörített csomagot használ, függetlenül attól, hogy az előző verzió tömörítést használ-e.

A csomag megfelelően van csomagolva, érvényesítve és tömörítve (ha szükséges), ezért készen áll az [üzembe helyezésre](service-fabric-deploy-remove-applications.md) egy vagy több Service Fabric-fürtön.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Csomagok tömörítése a Visual Studióval való üzembe helyezéskor

A Visual Studio segítségével tömörítheti a csomagokat az üzembe helyezés során, ha hozzáadja az `CopyPackageParameters` elemet a közzétételi profilhoz, és az attribútumot a értékre állítja `CompressPackage` `true` .

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Sfpkg létrehozása

Az 6,1-es verziótól kezdődően Service Fabric lehetővé teszi a kiépítés külső tárolóból.
Ezzel a beállítással nem kell átmásolni az alkalmazáscsomag a rendszerkép-tárolóba. Helyette létrehozhat egy `sfpkg` külső tárolóba, és feltöltheti azt, majd megadhatja a letöltési URI-t, hogy Service Fabric a kiépítés során. Ugyanaz a csomag több fürthöz is kiépíthető. A külső áruházból való kiépítés elmenti a csomagnak az egyes fürtökre történő másolásához szükséges időt.

A `sfpkg` fájl egy olyan zip, amely tartalmazza a kezdeti alkalmazáscsomag, és a ". sfpkg" kiterjesztésű.
A zip-ben az alkalmazáscsomag tömöríthető vagy tömörítetlen is lehet. A zip-ben lévő alkalmazáscsomag tömörítése a kód, a konfiguráció és az adatcsomag szintjén történik, ahogy azt [korábban említettük](service-fabric-package-apps.md#compress-a-package).

A létrehozásához `sfpkg` kezdjen el egy olyan mappával, amely tartalmazza az eredeti alkalmazáscsomag tömörített vagy nem. Ezután a ". sfpkg" kiterjesztésű mappa zip-fájljának kitöltéséhez használjon bármilyen segédprogramot. Használja például a [ZipFile. CreateFromDirectory](/dotnet/api/system.io.compression.zipfile.createfromdirectory#System_IO_Compression_ZipFile_CreateFromDirectory_System_String_System_String_System_IO_Compression_CompressionLevel_System_Boolean_).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

A `sfpkg` t a sávon kívüli külső tárolóba kell feltölteni, Service Fabricon kívülről. A külső tároló bármely olyan tároló lehet, amely egy REST http-vagy https-végpontot tesz elérhetővé. A kiépítés során a Service Fabric végrehajt egy GET műveletet az alkalmazáscsomag letöltéséhez `sfpkg` , így a tárolónak engedélyeznie kell a csomag olvasási hozzáférését.

A csomag kiépítéséhez használja a külső kiépítés elemet, amelyhez a letöltési URI és az alkalmazás típusa információ szükséges.

>[!NOTE]
> A rendszerkép-tároló relatív elérési útja alapján történő kiépítés jelenleg nem támogatja a `sfpkg` fájlokat. Ezért a `sfpkg` nem másolható a rendszerkép-tárolóba.

## <a name="next-steps"></a>Következő lépések

Az [alkalmazások telepítése és eltávolítása című][10] témakör ismerteti, hogyan kezelheti az alkalmazás példányait a PowerShell használatával

[Az alkalmazás paramétereinek kezelése több környezet esetében][11] azt ismerteti, hogyan lehet paramétereket és környezeti változókat beállítani a különböző alkalmazás-példányokhoz.

Az [alkalmazásra vonatkozó biztonsági szabályzatok konfigurálásával][12] megtudhatja, hogyan futtathat szolgáltatásokat a hozzáférés korlátozására szolgáló biztonsági házirendekben.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
