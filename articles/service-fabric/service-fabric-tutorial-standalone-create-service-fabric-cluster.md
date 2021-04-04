---
title: Service Fabric önálló ügyfél telepítése
description: Ebből az oktatóanyagból megtudhatja, hogyan telepítheti az önálló Service Fabric-ügyfelet a fürtön.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae0b343be986f4d8d5176c1f39eef6b23ca81278
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91840642"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Oktatóanyag: Service Fabric-fürt telepítése és létrehozása

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban létre fog hozni egy AWS-ben vagy az Azure-ban üzemeltetett önálló fürtöt, és telepítenie kell egy alkalmazást.

Ez az oktatóanyag egy sorozat második része. Ez az oktatóanyag végigvezeti az önálló Service Fabric-fürt létrehozásának lépésein.

Ebből a cikkből megtudhatja, hogyan:

> [!div class="checklist"]
> * Az önálló Service Fabric-csomag letöltése és telepítése
> * A Service Fabric-fürt létrehozása
> * Csatlakozás a Service Fabric-fürthöz

## <a name="download-the-service-fabric-for-windows-server-package"></a>A Windows Serverhez készült Service Fabric-csomag letöltése

A Service Fabric egy telepítőcsomagot biztosít az önálló Service Fabric-fürtök telepítéséhez.  [Töltse le a telepítőcsomagot](https://go.microsoft.com/fwlink/?LinkId=730690) a helyi számítógépre.  Miután sikeresen letöltötte a példányt a virtuális gépre irányuló RDP-kapcsolaton keresztül, és beilleszti az asztalra.

Válassza ki a zip-fájlt, és nyissa meg a helyi menüt, és válassza az **összes**  >  **kibontása** lehetőséget.  A fájlok kibontásakor létrejön egy mappa az asztalon, amelynek neve megegyezik a ZIP-fájl nevével.

További információ a [telepítőcsomag tartalmáról](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>A konfigurációs fájl beállítása

Egy három csomópontos Windows-fürtöt hoz létre, ezért módosítania kell a `ClusterConfig.Unsecure.MultiMachine.json` fájlt.

Ezután frissítse a fájlokban előforduló három IP-címet a 8., 15. és 22. sorban az egyes példányok IP-címeire.

A csomópontok a frissítésük után a következőképpen jelennek meg:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

Ezután frissítenie kell néhány tulajdonságot.  A 34. sorban módosítania kell a diagnosztikai tároló kapcsolati sztringjét. A módosítás után a következőképpen kell kinéznie: `"connectionstring": "C:\\ProgramData\\SF\\DiagnosticsStore"`

Végül a konfiguráció `nodeTypes` szakaszában adjon hozzá egy új szakaszt a Windows által használt rövid élettartamú portok leképezéséhez.  A konfigurációs fájlnak a következőképpen kell kinéznie:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>A környezet érvényesítése

Az önálló csomagban lévő *TestConfiguration.ps1* szkript az ajánlott eljárásokat elemző eszközként használható annak ellenőrzésére, hogy egy fürt az adott környezetben üzembe helyezhető-e. Az [Üzembe helyezés előkészítése](service-fabric-cluster-standalone-deployment-preparation.md) az előfeltételeket és a környezeti követelményeket sorolja fel. A szkript futtatásával ellenőrizze, hogy létre tudja-e hozni a fejlesztési fürtöt:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

Az alábbi példához hasonló kimenetnek kell megjelennie. Az alsó „Sikerült” mezőt `True` értékkel adja vissza a rendszer, az épségtesztek sikerültek, és a fürt üzembe helyezhetőnek tűnik a bemeneti konfiguráció alapján.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>A fürt létrehozása

Miután sikeresen ellenőrizte a fürt konfigurációját, futtassa a *CreateServiceFabricCluster.ps1* parancsfájlt, hogy a Service Fabric-fürtöt a konfigurációs fájlban lévő virtuális gépekre telepítse.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Ha minden működik, az alábbihoz hasonló kimenetet kap:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Az üzembe helyezés nyomait arra a gépre/virtuális gépre írja a rendszer, amelyen a CreateServiceFabricCluster.ps1 PowerShell-szkriptet futtatta. Ezek a DeploymentTraces nevű almappában találhatók, azon könyvtár alapján, amelyből a szkriptet futtatta. Ha ellenőrizni kívánja, hogy a Service Fabric megfelelően lett-e üzembe helyezve a gépen, keresse meg a telepített fájlokat a FabricDataRoot könyvtárban, ahogy az a fürtkonfigurációs fájl FabricSettings szakaszában szerepel (alapértelmezés szerint ez a c:\ProgramData\SF). A FabricHost.exe és a Fabric.exe folyamat futása is látható a Feladatkezelőben.
>
>

### <a name="open-service-fabric-explorer"></a>Service Fabric Explorer megnyitása

Most már csatlakozhat a fürthöz Service Fabric Explorer vagy közvetlenül az egyik számítógépről a http: \/ /localhost: 19080/Explorer/index.html vagy távolról a http: \/ /< *IPAddressofaMachine*>:19080/Explorer/index.html használatával.

## <a name="add-and-remove-nodes"></a>Csomópontok hozzáadása és eltávolítása

Az üzleti igényei változásával hozzáadhat vagy eltávolíthat csomópontokat az önálló Service Fabric-fürtről. A lépések részletes leírása: [Csomópontok hozzáadása vagy eltávolítása egy önálló Service Fabric-fürtről](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan tölthet fel párhuzamosan nagy mennyiségű véletlenszerű információt egy Storage-fiókba, például:

> [!div class="checklist"]
> * Kapcsolati sztring konfigurálása
> * Az alkalmazás létrehozása
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

A sorozat harmadik részében egy alkalmazást fog telepíteni a létrehozott fürtre.

> [!div class="nextstepaction"]
> [Az alkalmazás telepítése a Service Fabric-fürtre](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
