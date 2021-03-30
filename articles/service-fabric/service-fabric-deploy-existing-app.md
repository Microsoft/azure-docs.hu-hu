---
title: Meglévő végrehajtható fájl üzembe helyezése az Azure Service Fabric
description: Megtudhatja, hogyan csomagolhat egy meglévő alkalmazást vendég végrehajtható fájlként, hogy Service Fabric-fürtön is üzembe helyezhető.
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 72fde75e16341164106bb952d0bb66b83be744e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86259261"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Meglévő végrehajtható fájl becsomagolása és üzembe helyezése Service Fabric

Egy meglévő végrehajtható fájl [vendégként](service-fabric-guest-executables-introduction.md)való csomagolásakor választhatja a Visual Studio-projekt sablonjának használatát, vagy manuálisan is [létrehozhatja az alkalmazáscsomag létrehozását](#manually). A Visual studiót használva az alkalmazáscsomag-struktúrát és a MANIFEST-fájlokat az új Project-sablon hozza létre.

> [!TIP]
> Egy meglévő Windows-végrehajtható fájl egy szolgáltatásba való becsomagolásának legegyszerűbb módja a Visual Studio és a Linux használata a Yeoman használatához
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Meglévő végrehajtható fájl becsomagolása és üzembe helyezése a Visual Studióval

A Visual Studio egy Service Fabric szolgáltatási sablont biztosít, amely segítséget nyújt a vendég végrehajtható fájlok Service Fabric fürtön való üzembe helyezésében.

1. Válassza a **fájl**  >  **új projekt** elemet, és hozzon létre egy Service Fabric alkalmazást.
2. Válassza a **vendég végrehajtható fájl** lehetőséget a szolgáltatás sablonként.
3. Kattintson a **Tallózás** gombra a végrehajtható fájl kiválasztásához, majd adja meg a többi paramétert a szolgáltatás létrehozásához.
   * A *kód csomagjainak viselkedése*. Beállítható úgy, hogy a mappa összes tartalmát a Visual Studio-projektbe másolja, ami akkor hasznos, ha a végrehajtható fájl nem változik. Ha a végrehajtható fájl módosítására vár, és szeretné, hogy az új buildek dinamikusan legyenek kiválasztva, akkor a mappára is hivatkozhat. A Visual Studióban az alkalmazás projekt létrehozásakor használhat csatolt mappákat. Ez a forrás helyéről a projekten belülre mutat, így a vendégek a forrás célhelyén frissíthetik a vendég végrehajtható fájlt. Ezek a frissítések a Build-alkalmazáscsomag részévé válnak.
   * A *program* meghatározza azt a végrehajtható fájlt, amelyet futtatni kell a szolgáltatás elindításához.
   * Az *argumentumok* a végrehajtható fájlnak átadandó argumentumokat határozzák meg. Az argumentumokkal rendelkező paraméterek listája lehet.
   * A *WorkingFolder* meghatározza az elindítani kívánt folyamat munkakönyvtárát. Három értéket is megadhat:
     * `CodeBase` Megadja, hogy a munkakönyvtár az alkalmazáscsomag kódjának könyvtárába lesz állítva ( `Code` az előző adatstruktúrában látható könyvtár).
     * `CodePackage` azt adja meg, hogy a munkakönyvtár az alkalmazáscsomag gyökerére lesz beállítva ( `GuestService1Pkg` az előző fájl struktúrában látható).
     * `Work` Megadja, hogy a fájlok a Work nevű alkönyvtárba kerülnek.
4. Nevezze el a szolgáltatást, és kattintson az **OK** gombra.
5. Ha a szolgáltatásnak szüksége van egy végpontra a kommunikációhoz, most hozzáadhatja a protokollt, a portot és a típust a ServiceManifest.xml fájlhoz. Példa: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Mostantól a csomagot és a közzétételi műveletet a helyi fürtön is használhatja a Visual Studióban található megoldás hibakeresésével. Ha elkészült, közzéteheti az alkalmazást egy távoli fürtön, vagy bejelölheti a megoldást a forrás vezérlőelemre.
7. Olvassa el a [futó alkalmazás](#check-your-running-application) megtekintését, és tekintse meg, hogyan tekintheti meg Service Fabric Explorer futó vendég végrehajtható szolgáltatását.

Példa a bemutatóra: [az első vendég végrehajtható alkalmazás létrehozása a Visual Studióval](quickstart-guest-app.md).

### <a name="packaging-multiple-executables-with-visual-studio"></a>Több végrehajtható fájl csomagolása a Visual Studióval

A Visual Studióval olyan alkalmazáscsomag hozható létre, amely több vendég végrehajtható fájlját is tartalmazza. Miután hozzáadta az első vendég végrehajtható fájlt, kattintson a jobb gombbal az alkalmazás projektre, és válassza az **Add->új Service Fabric szolgáltatást** , hogy hozzáadja a második vendég végrehajtható projektet a megoldáshoz.

> [!NOTE]
> Ha úgy dönt, hogy a Visual Studio-projektben összekapcsolja a forrást, a Visual Studio-megoldás létrehozásával gondoskodni fog arról, hogy az alkalmazáscsomag naprakész legyen a forrás változásaival.

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Meglévő végrehajtható fájl becsomagolása és üzembe helyezése Linux rendszeren a Yeoman használatával

Egy vendég végrehajtható fájl Linuxon való létrehozásának és üzembe helyezésének eljárása megegyezik egy csharp vagy Java-alkalmazás üzembe helyezésével.

1. Írja be a terminálba a következőt: `yo azuresfguest`.
2. Adjon nevet az alkalmazásnak.
3. Nevezze el a szolgáltatást, és adja meg a szükséges adatokat, beleértve a végrehajtható fájl elérési útját és a meghívott paramétereket.

A Yeoman létrehoz egy alkalmazáscsomag a megfelelő alkalmazás-és jegyzékfájl-fájlokkal együtt a telepítési és eltávolítási parancsfájlok mellett.

### <a name="packaging-multiple-executables-using-yeoman-on-linux"></a>Több végrehajtható fájl csomagolása a Yeoman használatával Linuxon

Ha egy másik szolgáltatást szeretne hozzáadni a `yo` használatával már létrehozott alkalmazáshoz, hajtsa végre az alábbi lépéseket:

1. Lépjen a meglevő alkalmazás gyökérkönyvtárába.  Például `cd ~/YeomanSamples/MyApplication`, ha a `MyApplication` a Yeoman által létrehozott alkalmazás.
2. Futtassa `yo azuresfguest:AddService` a parancsot, és adja meg a szükséges adatokat.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Meglévő végrehajtható fájl manuális becsomagolása és üzembe helyezése

A vendég végrehajtható fájlok manuális csomagolásának folyamata a következő általános lépéseken alapul:

1. Hozza létre a csomag könyvtárának struktúráját.
2. Adja hozzá az alkalmazás kódját és konfigurációs fájljait.
3. Szerkessze a szolgáltatás jegyzékfájlját.
4. Szerkessze az alkalmazás jegyzékfájlját.

### <a name="create-the-package-directory-structure"></a>A csomag címtár-struktúrájának létrehozása

Első lépésként hozza létre a címtár-struktúrát az [Azure Service Fabric-alkalmazás csomagolása](./service-fabric-package-apps.md)című témakörben leírtak szerint.

### <a name="add-the-applications-code-and-configuration-files"></a>Az alkalmazás kódjának és konfigurációs fájljainak hozzáadása

Miután létrehozta a címtár-struktúrát, felveheti az alkalmazás kódját és konfigurációs fájljait a kód és a konfiguráció könyvtáraiba. További címtárakat vagy alkönyvtárakat is létrehozhat a kód vagy a konfigurációs könyvtárak alatt.

Service Fabric az `xcopy` alkalmazás gyökérkönyvtárának tartalmát használja, így nincs előre definiált struktúra, amely nem két legfelső szintű könyvtárat, kódot és beállítást hoz létre. (Ha szeretné, különböző neveket is választhat. További részletek a következő szakaszban olvashatók.)

> [!NOTE]
> Győződjön meg arról, hogy az alkalmazás által igényelt összes fájl és függőség szerepel. Service Fabric az alkalmazáscsomag tartalmát a fürt minden olyan csomópontján átmásolja, ahol az alkalmazás szolgáltatásai lesznek telepítve. A csomagnak tartalmaznia kell az alkalmazás futtatásához szükséges összes kódot. Ne Tételezzük fel, hogy a függőségek már telepítve vannak.
>
>

### <a name="edit-the-service-manifest-file"></a>A szolgáltatás jegyzékfájljának szerkesztése

A következő lépés a szolgáltatás jegyzékfájljának szerkesztése, amely a következő információkat tartalmazza:

* A szolgáltatás típusának neve. Ez egy azonosító, amelyet a Service Fabric a szolgáltatás azonosítására használ.
* Az alkalmazás indításához használt parancs (ExeHost).
* Minden olyan parancsfájl, amelyet futtatni kell az alkalmazás beállításához (SetupEntrypoint).

Az alábbi példa egy fájlt mutat be `ServiceManifest.xml` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

A következő részek a fájl különböző részeire mutatnak, amelyeket frissítenie kell.

#### <a name="update-servicetypes"></a>ServiceTypes frissítése

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Kiválaszthatja a kívánt nevet `ServiceTypeName` . Az érték a `ApplicationManifest.xml` fájlban a szolgáltatás azonosítására szolgál.
* Adja meg a következőt: `UseImplicitHost="true"`. Ez az attribútum azt jelzi, Service Fabric, hogy a szolgáltatás egy önálló alkalmazáson alapul, ezért az összes Service Fabric kell tennie, hogy folyamatként indítsa el, és figyelje az állapotát.

#### <a name="update-codepackage"></a>CodePackage frissítése
A CodePackage elem a szolgáltatás kódjának helyét (és verzióját) adja meg.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

A `Name` elem használatával adható meg a könyvtár neve a szolgáltatás kódját tartalmazó alkalmazáscsomagban. `CodePackage` szintén rendelkezik az `version` attribútummal. Ez használható a kód verziójának megadására, valamint a szolgáltatás kódjának frissítésére is a Service Fabric alkalmazás-életciklus-kezelési infrastruktúrájának használatával.

#### <a name="optional-update-setupentrypoint"></a>Nem kötelező: a SetupEntrypoint frissítése

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
A SetupEntryPoint elem a szolgáltatás kódjának elindítása előtt végrehajtandó végrehajtható vagy batch fájl megadására szolgál. Ez egy opcionális lépés, így nem kell szerepelnie, ha nincs szükség inicializálásra. A SetupEntryPoint a szolgáltatás újraindításakor minden alkalommal végrehajtja.

Csak egy SetupEntryPoint létezik, ezért a telepítési parancsfájlokat egyetlen batch-fájlban kell csoportosítani, ha az alkalmazás telepítője több parancsfájlt igényel. A SetupEntryPoint bármilyen típusú fájlt futtathat: végrehajtható fájlokat, batch-fájlokat és PowerShell-parancsmagokat. További részleteket a [SetupEntryPoint konfigurálása](service-fabric-application-runas-security.md)című témakörben talál.

Az előző példában a SetupEntryPoint egy nevű batch-fájlt futtat, `LaunchConfig.cmd` amely a `scripts` kód könyvtárának alkönyvtárában található (feltéve, hogy a WorkingFolder elem a következőre van állítva).

#### <a name="update-entrypoint"></a>BelépésiPont frissítése

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

A `EntryPoint` szolgáltatás jegyzékfájljának eleme a szolgáltatás indításának megadására szolgál.

Az `ExeHost` elem meghatározza a szolgáltatás elindításához használandó végrehajtható fájlt (és argumentumokat). Az attribútum hozzáadásával `IsExternalExecutable="true"` `ExeHost` azt jelezheti, hogy a program külső végrehajtható fájl a kód csomagján kívül. Például: `<ExeHost IsExternalExecutable="true">`.

* `Program` annak a végrehajtható fájlnak a nevét adja meg, amelynek el kell indítania a szolgáltatást.
* `Arguments` meghatározza a végrehajtható fájlnak átadandó argumentumokat. Az argumentumokkal rendelkező paraméterek listája lehet.
* `WorkingFolder` meghatározza az elindítani kívánt folyamat munkakönyvtárát. Három értéket is megadhat:
  * `CodeBase` azt adja meg, hogy a munkakönyvtár az alkalmazáscsomag ( `Code` az előző fájl struktúrában található könyvtár) kódjának könyvtárába lesz állítva.
  * `CodePackage` Megadja, hogy a munkakönyvtár az alkalmazáscsomag gyökerére lesz beállítva ( `GuestService1Pkg` az előző fájl struktúrájában).
    * `Work` Megadja, hogy a fájlok a Work nevű alkönyvtárba kerülnek.

A WorkingFolder hasznos a megfelelő munkakönyvtár beállítása, hogy a relatív elérési utakat az alkalmazás-vagy inicializálási parancsfájlok is felhasználhatják.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Végpontok frissítése és a elnevezési szolgáltatás való regisztráció kommunikációhoz

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```

Az előző példában az `Endpoint` elem meghatározza azokat a végpontokat, amelyeket az alkalmazás figyelni tud. Ebben a példában a Node.js alkalmazás a 3000-es porton a http-t figyeli.

Emellett megkérheti Service Fabric, hogy tegye közzé ezt a végpontot a elnevezési szolgáltatás, hogy más szolgáltatások is felderítsék a szolgáltatás végpontjának címeit. Ez lehetővé teszi, hogy képes legyen kommunikálni a vendég végrehajtható fájlok között.
A közzétett végpont címe űrlap `UriScheme://IPAddressOrFQDN:Port/PathSuffix` . `UriScheme` és `PathSuffix` opcionális attribútumok. `IPAddressOrFQDN` annak a csomópontnak az IP-címe vagy teljes tartományneve, amelybe a végrehajtható fájl kerül, és amelyet a rendszer kiszámít.

A következő példában a szolgáltatás üzembe helyezését követően a Service Fabric Explorer egy, `http://10.1.4.92:3000/myapp/` a szolgáltatási példányhoz közzétett végpont jelenik meg. Ha ez egy helyi gép, akkor a következőt látja: `http://localhost:3000/myapp/` .

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```

Ezeket a címeket [fordított proxyval](service-fabric-reverseproxy.md) használhatja a szolgáltatások közötti kommunikációhoz.

### <a name="edit-the-application-manifest-file"></a>Az alkalmazás jegyzékfájljának szerkesztése

A fájl konfigurálása után módosítania kell `Servicemanifest.xml` a `ApplicationManifest.xml` fájlt, hogy a megfelelő szolgáltatástípus és név legyen használatban.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

A `ServiceManifestImport` elemben megadhat egy vagy több olyan szolgáltatást, amelyet fel szeretne venni az alkalmazásba. A szolgáltatásokra a (z) hivatkozik `ServiceManifestName` , amely megadja annak a könyvtárnak a nevét, ahol a `ServiceManifest.xml` fájl található.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Naplózás beállítása

A vendég végrehajtható fájlok esetében hasznos lehet megtekinteni a konzol naplófájljait, hogy az alkalmazás és a konfigurációs parancsfájlok milyen hibákat jelenítenek meg.
A konzol átirányítását az elem használatával lehet konfigurálni a `ServiceManifest.xml` fájlban `ConsoleRedirection` .

> [!WARNING]
> Soha ne használja a konzol átirányítási házirendjét az éles környezetben üzembe helyezett alkalmazásokban, mivel ez hatással lehet az alkalmazás feladatátvételére. Ezt *csak* helyi fejlesztési és hibakeresési célokra használhatja.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` a konzol kimenetének (stdout és stderr) átirányítására is használható egy munkakönyvtárra. Ez lehetővé teszi annak ellenőrzését, hogy nincsenek-e hibák az alkalmazás telepítése vagy végrehajtása során a Service Fabric-fürtben.

`FileRetentionCount` meghatározza, hogy a rendszer hány fájlt ment a munkakönyvtárba. Az 5 érték például azt jelenti, hogy az előző öt végrehajtás naplófájljai a munkakönyvtárban tárolódnak.

`FileMaxSizeInKb` Megadja a naplófájlok maximális méretét.

A naplófájlok a szolgáltatás egyik munkakönyvtárában lesznek mentve. Annak megállapításához, hogy a fájlok hol találhatók, Service Fabric Explorer segítségével határozza meg, hogy melyik csomóponton fut a szolgáltatás, és hogy melyik munkakönyvtárat használja a rendszer. Ezt a folyamatot a cikk későbbi részében tárgyaljuk.

## <a name="deployment"></a>Üzembe helyezés

Az utolsó lépés az [alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md). A következő PowerShell-szkript bemutatja, hogyan helyezheti üzembe az alkalmazást a helyi fejlesztési fürtön, és hogyan indíthat el új Service Fabric szolgáltatást.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Tömörítse a csomagot](service-fabric-package-apps.md#compress-a-package) a rendszerkép-tárolóba történő másolás előtt, ha a csomag nagyméretű vagy sok fájllal rendelkezik. További tudnivalók [itt](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

A Service Fabric szolgáltatás különböző konfigurációkon telepíthető. Például egyetlen vagy több példányban is üzembe helyezhető, vagy olyan módon telepíthető, hogy a szolgáltatás egy példánya a Service Fabric fürt minden egyes csomópontján megtalálható legyen.

A `InstanceCount` parancsmag paraméterének segítségével `New-ServiceFabricService` megadható, hogy a szolgáltatás hány példánya legyen elindítható a Service Fabric-fürtben. Az `InstanceCount` értéket az üzembe helyezett alkalmazás típusától függően állíthatja be. A két leggyakoribb forgatókönyv a következők:

* `InstanceCount = "1"`. Ebben az esetben a szolgáltatásnak csak egy példánya van telepítve a fürtben. Service Fabric ütemező határozza meg, hogy a szolgáltatás melyik csomóponton lesz telepítve.
* `InstanceCount ="-1"`. Ebben az esetben a szolgáltatás egy példánya van telepítve a Service Fabric fürt minden csomópontján. Ennek eredményeképpen a szolgáltatásnak egy (és csak egy) példánya van a fürt minden csomópontja számára.

Ez egy hasznos konfiguráció az előtér-alkalmazásokhoz (például egy REST-végponthoz), mert az ügyfélalkalmazások "csatlakozni" kell a fürt bármelyik csomópontjára a végpont használatához. Ez a konfiguráció akkor is használható, ha például az Service Fabric-fürt összes csomópontja csatlakoztatva van egy terheléselosztó számára. Az ügyfél-forgalom ezután terjeszthető a fürt összes csomópontján futó szolgáltatáson keresztül.

## <a name="check-your-running-application"></a>A futó alkalmazás keresése
A Service Fabric Explorerban azonosítsa azt a csomópontot, amelyen a szolgáltatás fut. Ebben a példában a Csomópont1 fut:

![Csomópont, ahol a szolgáltatás fut](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Ha megnyitja a csomópontot, és megkeresi az alkalmazást, megtekintheti az alapvető csomópont-információkat, beleértve annak helyét a lemezen.

![Hely a lemezen](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Ha a Server Explorer használatával keres a címtárban, a munkakönyvtár és a szolgáltatás naplójának mappáját a következő képernyőképen látható módon keresheti meg:

![A napló helye](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan csomagolhat vendég végrehajtható fájlt, és hogyan helyezheti üzembe a Service Fabric. A kapcsolódó információkkal és feladatokkal kapcsolatban tekintse meg a következő cikkeket.

* [Minta egy vendég végrehajtható fájl csomagolásához és üzembe helyezéséhez](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), beleértve a csomagoló eszköz előzetes kiadására mutató hivatkozást is.
* [Minta két vendég végrehajtható fájlról (C# és NodeJS) az elnevezési szolgáltatáson keresztül a REST használatával](https://github.com/Azure-Samples/service-fabric-containers)
* [Az első Service Fabric-alkalmazás létrehozása a Visual Studio használatával](service-fabric-tutorial-create-dotnet-app.md)
