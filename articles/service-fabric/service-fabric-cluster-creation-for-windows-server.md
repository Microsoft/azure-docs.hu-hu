---
title: Önálló Azure Service Fabric-fürt létrehozása
description: Hozzon létre egy Azure Service Fabric-fürtöt bármely olyan gépen (fizikai vagy virtuális gépen), amely Windows Server rendszert futtat, akár helyszíni, akár bármilyen felhőben.
ms.topic: conceptual
ms.date: 2/21/2019
ms.openlocfilehash: 41af655be07ccae2b66e75f5bfe87629cdb54924
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785684"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Windows Serveren futó különálló fürt létrehozása
Az Azure Service Fabric használatával Service Fabric-fürtöket hozhat létre a Windows Servert futtató virtuális gépeken vagy számítógépeken. Ez azt jelenti, hogy Service Fabric alkalmazásokat bármely olyan környezetben telepítheti és futtathatja, amely összekapcsolt Windows Server-számítógépeket, illetve helyszíni vagy bármilyen felhőalapú szolgáltatót tartalmaz. A Service Fabric egy önálló Windows Server-csomagot tartalmazó Service Fabric-fürtök létrehozására szolgáló telepítőcsomagot biztosít. Az Azure-beli hagyományos Service Fabric-fürtök felügyelt szolgáltatásként érhetők el, míg a különálló Service Fabric-fürtök önkiszolgáló szolgáltatások. További információ a különbségekről: az [Azure és az önálló Service Fabric-fürtök összehasonlítása](./service-fabric-deploy-anywhere.md).

Ez a cikk végigvezeti a Service Fabric önálló fürt létrehozásának lépésein.

> [!NOTE]
> Ez a különálló Windows Server-csomag díjmentesen elérhető, és üzemi környezetben is használható. Ez a csomag olyan új Service Fabric szolgáltatásokat tartalmazhat, amelyek az előzetes verzióban találhatók. Görgessen le az "[előzetes verziójú szolgáltatások ebben a csomagban" című részhez](#previewfeatures_anchor). az előzetes verziójú funkciók listájának szakasza. [A végfelhasználói licencszerződés másolatát most letöltheti](https://go.microsoft.com/fwlink/?LinkID=733084) .
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>A Windows Server-csomag Service Fabric támogatásának beolvasása
* Kérdezze meg a Közösséget a Windows Serverhez készült Service Fabric önálló csomagjáról a [Microsoft Q&az Azure Service Fabric kérdéseit tartalmazó oldalt](/answers/topics/azure-service-fabric.html).
* Nyisson meg egy jegyet a [Service Fabric szakmai támogatásához](https://support.microsoft.com/oas/default.aspx?prid=16146).  További információ [a Microsoft szakmai](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)támogatásáról.
* A csomag támogatását a [Microsoft Premier szintű támogatás](https://support.microsoft.com/en-us/premier)részeként is elérheti.
* További részletekért tekintse meg az [Azure Service Fabric támogatási lehetőségeit](./service-fabric-support.md).
* A naplók támogatási célból történő gyűjtéséhez futtassa a [Service Fabric önálló napló gyűjtőjét](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>A Windows Serverhez készült Service Fabric-csomag letöltése
A fürt létrehozásához használja a Service Fabric a Windows Server-csomaghoz (Windows Server 2012 R2 és újabb), amely itt található: <br>
[Link letöltése – Service Fabric önálló csomag – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

A csomag tartalmával kapcsolatban [itt](service-fabric-cluster-standalone-package-contents.md)talál részleteket.

A Service Fabric futtatókörnyezet-csomagot a rendszer automatikusan letölti a fürt létrehozásakor. Ha egy olyan gépről telepít, amely nem kapcsolódik az internethez, töltse le a sávon kívüli futtatókörnyezetet innen: <br>
[Hivatkozás letöltése – Service Fabric futtatókörnyezet – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Önálló fürtözött konfigurációs minták keresése a következő helyen: <br>
[Önálló fürtözött konfigurációs minták](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>A fürt létrehozása
Több fürtkonfigurációs mintafájl is települ a telepítőcsomaggal. A *ClusterConfig.Unsecure.DevCluster.json* a legegyszerűbb fürtkonfiguráció: egy nem védett, egyetlen számítógépen futó, három csomóponttal rendelkező fürt.  A többi konfigurációs fájl X.509 tanúsítványokkal vagy a Windows biztonsági szolgáltatásaival védett egy- vagy többgépes fürtöket ír le.  Ehhez az oktatóanyaghoz az alapértelmezett konfigurációs beállítások egyikét sem kell módosítania, csak át kell tekintenie a konfigurációs fájlt, és meg kell ismerkednie a beállításokkal.  A **csomópontok** szakasz ismerteti a fürt három csomópontját: név, IP-cím, [csomóponttípus, tartalék tartomány és frissítési tartomány](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  A **tulajdonságok** szakasz definiálja a fürt [biztonságát, megbízhatósági szintjét, diagnosztikai gyűjteményét és a benne lévő csomópontok típusait](service-fabric-cluster-manifest.md#cluster-properties).

A cikkben létrehozott fürt nem biztonságos.  Bárki csatlakozhat hozzá névtelenül és végrehajthat kezelési műveleteket, ezért az üzemben lévő fürtöket mindig X.509 tanúsítványok vagy a Windows rendszerbiztonság használatával kell védeni.  A biztonság konfigurálására csak a fürt létrehozásakor van lehetőség, a fürt létrehozása után már nem lehet engedélyezni. A konfigurációs fájl frissítése engedélyezze a [tanúsítványok biztonsági](service-fabric-windows-cluster-x509-security.md) vagy a [Windows-biztonságot](service-fabric-windows-cluster-windows-security.md). A Service Fabric-fürtök védelmével kapcsolatos további tudnivalókért lásd: [Fürt biztonságossá tétele](service-fabric-cluster-security.md).

### <a name="step-1-create-the-cluster"></a>1. lépés: a fürt létrehozása

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>"A" forgatókönyv: nem biztonságos helyi fejlesztési fürt létrehozása
A Service Fabric egy gépi fejlesztési fürtön is üzembe helyezhetők a [mintákban](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)található fájl *ClusterConfig.Unsecure.DevCluster.js* használatával.

Csomagolja ki a különálló csomagot a gépre, másolja a minta konfigurációs fájlt a helyi gépre, majd futtassa a *CreateServiceFabricCluster.ps1* parancsfájlt egy rendszergazdai PowerShell-munkameneten keresztül az önálló csomag mappájából.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

A hibaelhárítással kapcsolatos részletekért tekintse meg a környezet beállítása szakaszt [, és készítse elő a fürt üzembe helyezését](service-fabric-cluster-standalone-deployment-preparation.md) .

Ha végzett a fejlesztési forgatókönyvek futtatásával, távolítsa el a Service Fabric fürtöt a gépről a "[fürt eltávolítása](#removecluster_anchor)" szakasz lépéseit követve. 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>B forgatókönyv: több számítógépből álló fürt létrehozása
Miután elvégezte a [fürt üzembe helyezésének](service-fabric-cluster-standalone-deployment-preparation.md)megtervezése és előkészítése során megjelenő tervezési és előkészítési lépéseket, készen áll arra, hogy létrehozza az üzemi fürtöt a fürt konfigurációs fájljának használatával.

A fürt üzembe helyezését és konfigurálását végző fürtrendszergazdának rendszergazdai jogosultságokkal kell rendelkeznie a számítógépen. A Service Fabric tartományvezérlőn nem telepíthető.

1. Az önálló csomagban lévő *TestConfiguration.ps1* szkript az ajánlott eljárásokat elemző eszközként használható annak ellenőrzésére, hogy egy fürt az adott környezetben üzembe helyezhető-e. Az [Üzembe helyezés előkészítése](service-fabric-cluster-standalone-deployment-preparation.md) az előfeltételeket és a környezeti követelményeket sorolja fel. A szkript futtatásával ellenőrizze, hogy létre tudja-e hozni a fejlesztési fürtöt:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Az alábbihoz hasonló kimenetnek kell megjelennie. Ha a "Passed" alsó mező értéke "true" (igaz), a rendszer az alapértékeket ellenőrzi, és a fürt úgy tűnik, hogy a bemeneti konfiguráció alapján telepíthető.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. A fürt létrehozása: futtassa a *CreateServiceFabricCluster.ps1* szkriptet a Service Fabric-fürt a konfigurációban lévő minden egyes gépen való üzembe helyezéséhez. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Az üzembe helyezés nyomait arra a gépre/virtuális gépre írja a rendszer, amelyen a CreateServiceFabricCluster.ps1 PowerShell-szkriptet futtatta. Ezek a DeploymentTraces nevű almappában találhatók, azon könyvtár alapján, amelyből a szkriptet futtatta. Ha ellenőrizni kívánja, hogy a Service Fabric megfelelően lett-e üzembe helyezve a gépen, keresse meg a telepített fájlokat a FabricDataRoot könyvtárban, ahogy az a fürtkonfigurációs fájl FabricSettings szakaszában szerepel (alapértelmezés szerint ez a c:\ProgramData\SF). A FabricHost.exe és a Fabric.exe folyamat futása is látható a Feladatkezelőben.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>C. forgatókönyv: kapcsolat nélküli (internetről leválasztott) fürt létrehozása
A rendszer automatikusan letölti a Service Fabric futtatókörnyezet-csomagot a fürt létrehozásakor. Amikor fürtöt telepít az internethez nem csatlakozó gépekre, le kell töltenie a Service Fabric Runtime csomagot, és meg kell adnia a fürt létrehozásához szükséges elérési utat.
A futásidejű csomag külön tölthető le, az internethez csatlakozó másik gépről, a [letöltési hivatkozás – Service Fabric futtatókörnyezet – Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Másolja a futtatókörnyezetet arra a pontra, ahová az offline fürtöt telepíti, és hozza létre a fürtöt `CreateServiceFabricCluster.ps1` a `-FabricRuntimePackagePath` paraméterrel együtt, az alábbi példában látható módon: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

A és a *.\MicrosoftAzureServiceFabric.cab* *.\ClusterConfig.js* a fürtkonfiguráció és a Runtime. cab fájl elérési útja.

### <a name="step-2-connect-to-the-cluster"></a>2. lépés: Kapcsolódás a fürthöz
Kapcsolódjon a fürthöz, és ellenőrizze, hogy a fürt fut-e, és elérhető-e. A ServiceFabric PowerShell-modul a futtatókörnyezettel együtt települ.  A fürthöz a fürtcsomópontokon vagy a Service Fabric futtatókörnyezettel rendelkező távoli számítógépről is csatlakozhat.  A [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag kiépít egy kapcsolatot a fürttel.

Nem biztonságos fürthöz való kapcsolódáshoz futtassa a következő PowerShell-parancsot:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Például:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.234:19000
```

A fürtökhöz való csatlakozást bemutató egyéb példákért lásd: [Csatlakozás biztonságos fürthöz](service-fabric-connect-to-secure-cluster.md). Miután csatlakozott a fürthöz, a [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) parancsmaggal listázza a fürtben lévő csomópontokat és az egyes csomópontok állapotinformációit. A **HealthState** tulajdonságnak *OK* értékűnek kell lennie minden csomópont esetében.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>3. lépés: a fürt megjelenítése a Service Fabric Explorer használatával
A [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) hatékony eszköz a fürtök megjelenítéséhez és az alkalmazások kezeléséhez.  Service Fabric Explorer egy olyan szolgáltatás, amely a fürtben fut, és a böngészővel fér hozzá a alkalmazáshoz `http://localhost:19080/Explorer` .

A fürt irányítópultja áttekintést nyújt a fürtről, beleértve az alkalmazások és a csomópontok állapotának összefoglalását. A csomópontnézet a fürt fizikai elrendezését mutatja. Az egyes csomópontoknál megtekintheti, hogy melyik alkalmazások kódja üzemel az adott csomóponton.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Csomópontok hozzáadása és eltávolítása
Az üzleti igényei változásával hozzáadhat vagy eltávolíthat csomópontokat az önálló Service Fabric-fürtről. A lépések részletes leírása: [Csomópontok hozzáadása vagy eltávolítása egy önálló Service Fabric-fürtről](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Fürt eltávolítása
Egy fürt eltávolításához futtassa a *RemoveServiceFabricCluster.ps1* PowerShell-szkriptet a csomag mappájából, és adja meg a JSON-konfigurációs fájl elérési útját. Megadhatja a törlés naplójának mentési helyét is.

Ez a parancsfájl bármely olyan gépen futtatható, amely rendszergazdai hozzáféréssel rendelkezik az összes olyan géphez, amely csomópontként szerepel a fürt konfigurációs fájljában. A parancsfájl futtatásához használt gépnek nem kell a fürt részét képeznie.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Telemetria gyűjtött adatok és az onnan való leiratkozás
Alapértelmezés szerint a termék a termék telemetria gyűjti a Service Fabric használatot. Az ajánlott eljárásokat elemző eszköz, amely a telepítés részeként fut, ellenőrzi a kapcsolódást [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) . Ha nem érhető el, a telepítés sikertelen lesz, hacsak nem törli a telemetria.

1. A telemetria folyamat naponta egyszer megpróbálja feltölteni az alábbi adatokkal [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) . Ez a legjobb feltöltés, és nincs hatással a fürt működésére. A telemetria csak a Feladatátvevőfürt-kezelőt futtató csomópontról lesz elküldve. Más csomópontok nem küldenek telemetria.
2. A telemetria a következőkből áll:

* Szolgáltatások száma
* ServiceTypes száma
* Alkalmazások száma
* ApplicationUpgrades száma
* Szabályozott száma
* InBuildFailoverUnits száma
* UnhealthyFailoverUnits száma
* Replikák száma
* InBuildReplicas száma
* StandByReplicas száma
* OfflineReplicas száma
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Csomópontok száma
* IsContextComplete: TRUE/FALSE
* ClusterId: minden fürthöz véletlenszerűen generált GUID
* ServiceFabricVersion
* Annak a virtuális gépnek vagy gépnek az IP-címe, amelyről a telemetria feltöltve van

A telemetria letiltásához adja hozzá a következő *tulajdonságokat* a fürt konfigurációjában: *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>A csomagban található előzetes verziójú szolgáltatások
Nincsenek.


> [!NOTE]
> A [Windows Serverhez készült önálló fürt új GA-verziójától (5.3.204. x verzió)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)kezdve a fürtöt a későbbi kiadásokra, manuálisan vagy automatikusan is frissítheti. További részletekért tekintse meg az [önálló Service Fabric-fürt verziójának frissítése](service-fabric-cluster-upgrade-windows-server.md) című dokumentumot.
> 
> 

## <a name="next-steps"></a>Következő lépések
* [Alkalmazások telepítése és eltávolítása a PowerShell használatával](service-fabric-deploy-remove-applications.md)
* [Önálló Windows-fürt konfigurációs beállításai](service-fabric-cluster-manifest.md)
* [Csomópontok hozzáadása vagy eltávolítása önálló Service Fabric-fürthöz](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Önálló Service Fabric fürt verziójának frissítése](service-fabric-cluster-upgrade-windows-server.md)
* [Önálló Service Fabric-fürt létrehozása Windows rendszerű Azure-beli virtuális gépekkel](./service-fabric-cluster-creation-via-arm.md)
* [Önálló fürt biztonságossá tétele Windows rendszeren Windows-Biztonság használatával](service-fabric-windows-cluster-windows-security.md)
* [Önálló fürt biztonságossá tétele a Windowsban X509-tanúsítványok használatával](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
