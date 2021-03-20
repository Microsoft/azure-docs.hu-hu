---
title: Parancsfájl futtatása Azure Service Fabric szolgáltatás indításakor
description: Megtudhatja, hogyan konfigurálhat házirendet egy Service Fabric szolgáltatás telepítési pontjához, és hogyan futtathat parancsfájlt a szolgáltatás indítási ideje alatt.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012527"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Szolgáltatásindítási szkript futtatása helyi felhasználóként vagy rendszerfiókként
A Service Fabric szolgáltatás végrehajtható fájljának elindítása előtt szükség lehet néhány konfiguráció vagy beállítás futtatására.  Például a környezeti változók konfigurálása. Megadhat egy parancsfájlt, amelyet a szolgáltatás végrehajtható fájljának a szolgáltatáshoz tartozó szolgáltatási jegyzékfájlban való elindítása előtt szeretne futtatni. A szolgáltatás telepítési belépési pontjához tartozó futtató házirend konfigurálásával megváltoztathatja, hogy a telepítő végrehajtható fájlja melyik fiókon fusson.  Egy különálló telepítési belépési pont lehetővé teszi, hogy rövid idő alatt magas jogosultsági szintű konfigurációt futtasson, így a szolgáltatás-gazdagép végrehajtható fájljának nem kell magas szintű jogosultságokkal rendelkeznie a hosszabb ideig.

A telepítési belépési pont (a SetupEntryPoint [) egy](service-fabric-application-and-service-manifests.md)emelt szintű belépési pont, amely alapértelmezés szerint ugyanazokkal a hitelesítő adatokkal fut, mint a Service Fabric (jellemzően a *NetworkService* fiók) a többi belépési pont előtt. A **BelépésiPont** által megadott végrehajtható fájl általában a hosszan futó szolgáltatás gazdagépe. A **BelépésiPont** végrehajtható fájl futtatása a **SetupEntryPoint** végrehajtható fájl sikeres bezárása után történik. Az eredményül kapott folyamat figyelése és újraindítása megkezdődött, és újra megkezdődik a **SetupEntryPoint** , ha még leáll vagy összeomlik. 

## <a name="configure-the-service-setup-entry-point"></a>Szolgáltatásbeállítás belépési pontjának konfigurálása
Az alábbiakban egy olyan állapot nélküli szolgáltatás egyszerű szolgáltatási jegyzékfájlja látható, amely a szolgáltatás **SetupEntryPoint** *MySetup.bat* telepítési parancsfájlt határoz meg.  A **argumentumok** argumentumokat adnak át a parancsfájlnak a futtatásakor.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Egy szolgáltatás telepítési belépési pontja házirend konfigurálása
Alapértelmezés szerint a szolgáltatás telepítőjének belépési pontjának végrehajtható fájlja ugyanazokat a hitelesítő adatokat futtatja, mint a Service Fabric (általában a *NetworkService* fiók).  Az alkalmazás jegyzékfájljában módosíthatja a biztonsági engedélyeket, hogy a helyi rendszerfiók vagy egy rendszergazdai fiók használatával futtassa az indítási parancsfájlt.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>A házirend konfigurálása helyi rendszerfiók használatával
A következő Application manifest-példa azt mutatja be, hogyan lehet konfigurálni a szolgáltatás telepítési belépési pontját a felhasználói rendszergazdai fiók (SetupAdminUser) alatt történő futtatásra.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Először hozzon létre egy **rendszerbiztonsági tag** nevű szakaszt egy felhasználónévvel, például SetupAdminUser. A SetupAdminUser felhasználói fiók a rendszergazdák Rendszercsoport tagja.

Ezután a **ServiceManifestImport** szakaszban állítson be egy házirendet, amely alkalmazza ezt a rendszerbiztonsági tag **SetupEntryPoint**. Ez a szabályzat azt közli Service Fabric, hogy a **MySetup.bat** fájl futtatásakor SetupAdminUser kell futnia (rendszergazdai jogosultságokkal). Mivel *nem* alkalmazott szabályzatot a fő belépési pontra, a **MyServiceHost.exe** a System **NetworkService** fiókban fut. Ez az alapértelmezett fiók, amelyet az összes szolgáltatási belépési pont futtat.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>A házirend konfigurálása helyi rendszerfiókok használatával
Gyakran érdemes az indítási parancsfájlt helyi rendszerfiókkal futtatni, nem pedig rendszergazdai fiókot használni. Ha a futtató házirendet a rendszergazdák csoport tagjaként futtatja, általában nem működik megfelelően, mert a számítógépeken alapértelmezés szerint engedélyezve van a felhasználói Access Control (UAC). Ilyen esetekben az ajánlott, hogy a SetupEntryPoint a LocalSystem-ként futtassa, nem pedig helyi felhasználóként a rendszergazdák csoporthoz. A következő példa azt mutatja be, hogy a SetupEntryPoint a LocalSystem fiókkal való futtatásra van beállítva:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Linux-fürtök esetén a szolgáltatás vagy a telepítési belépési pont **gyökérként** való futtatásához megadhatja  a AccountType **LocalSystemként**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Parancsfájl futtatása a telepítési belépési pontról
Most adjon hozzá egy indítási parancsfájlt a projekthez, hogy rendszergazdai jogosultságok alatt fusson. 

A Visual Studióban kattintson a jobb gombbal a szolgáltatási projektre, és adjon hozzá egy *MySetup.bat* nevű új fájlt.

Ezután győződjön meg arról, hogy a *MySetup.bat* fájl szerepel a szervizcsomagban. Alapértelmezés szerint nem. Válassza ki a fájlt, kattintson a jobb gombbal a helyi menü beolvasásához, majd válassza a **Tulajdonságok parancsot**. A Tulajdonságok párbeszédpanelen győződjön meg arról, hogy a **Másolás a kimeneti könyvtárba** beállítás a másolás, **Ha újabb**. Tekintse meg a következő képernyőképet.

![Visual Studio-CopyToOutput a SetupEntryPoint batch-fájlhoz][image1]

Most szerkessze a *MySetup.bat* fájlt, és adja hozzá a következő parancsokat a rendszerkörnyezeti változó beállításához és egy szövegfájl kimenetéhez:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Ezután hozza létre és telepítse a megoldást egy helyi fejlesztési fürtre. A szolgáltatás elindítása után, ahogy az [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)látható, láthatja, hogy a MySetup.bat fájl két módon volt sikeres. Nyisson meg egy PowerShell-parancssort, és írja be a következőt:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Ezt követően jegyezze fel annak a csomópontnak a nevét, amelyen a szolgáltatás központi telepítése megkezdődött, és [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)indult el. Például: 2. csomópont. Ezután keresse meg az alkalmazás példányának munkahelyi mappáját, és keresse meg a **TestVariable** értékét megjelenítő out.txt fájlt. Ha például a szolgáltatás a 2. csomópontra lett telepítve, akkor az alábbi elérési útra léphet a **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>PowerShell-parancsok futtatása telepítési belépési pontról
Ha a PowerShellt a **SetupEntryPoint** pontról szeretné futtatni, **PowerShell.exe** futtathat egy olyan batch-fájlban, amely egy PowerShell-fájlra mutat. Először adjon hozzá egy PowerShell-fájlt a szolgáltatási projekthez – például **MySetup.ps1**. Ne felejtse el megadni a *másolást, ha újabb* tulajdonságot szeretne, hogy a fájl is szerepeljen a szervizcsomagban. Az alábbi példa egy MySetup.ps1 nevű PowerShell-fájlt indít, amely egy **TestVariable** nevű rendszerkörnyezeti változót állít be.

MySetup.bat egy PowerShell-fájl indításához:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

A PowerShell-fájlban adja hozzá a következőt a rendszerkörnyezeti változó beállításához:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Alapértelmezés szerint a batch-fájl futtatásakor a rendszer a **Work** for Files nevű alkalmazás mappát tekinti meg. Ebben az esetben, ha MySetup.bat fut, azt szeretnénk, hogy a MySetup.ps1 fájlt ugyanabban a mappában találja, amely az alkalmazáscsomag- **csomag** mappája. A mappa módosításához állítsa be a munkamappát:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Indítási parancsfájl hibakeresése helyileg a konzol átirányítása használatával
Alkalmanként hibakeresési célokra is hasznos lehet, ha látni szeretné a konzol kimenetét a telepítési parancsfájl futtatásával. A konzol átirányítási házirendjét a szolgáltatás jegyzékfájljának telepítési belépési pontján állíthatja be, amely egy fájlba írja a kimenetet. A fájl kimenete az alkalmazás központi telepítésére és futtatására szolgáló fürtcsomópont **log** nevű alkalmazás mappájába íródik. 

> [!WARNING]
> Soha ne használja a konzol átirányítási házirendjét az éles környezetben üzembe helyezett alkalmazásokban, mivel ez hatással lehet az alkalmazás feladatátvételére. Ezt *csak* helyi fejlesztési és hibakeresési célokra használhatja.  
> 
> 

A következő Service manifest-példa azt mutatja be, hogyan kell beállítani a konzol átirányítását egy FileRetentionCount értékkel:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Ha most módosítja a MySetup.ps1 fájlt egy **echo** -parancs írásához, a rendszer hibakeresési célból a kimeneti fájlba írja a következőt:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> A szkript hibakeresése után azonnal távolítsa el a konzol átirányítási házirendjét.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Következő lépések
* [Tudnivalók az alkalmazások és szolgáltatások biztonságáról](service-fabric-application-and-service-security.md)
* [Az alkalmazás modelljének megismerése](service-fabric-application-model.md)
* [Erőforrások meghatározása a szolgáltatás jegyzékfájljában](service-fabric-service-manifest-resources.md)
* [Alkalmazás üzembe helyezése](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
