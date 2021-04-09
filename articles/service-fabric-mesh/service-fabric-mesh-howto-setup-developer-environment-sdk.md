---
title: Windows fejlesztői környezet beállítása Service Fabric Mesh számára
description: Kialakíthatja úgy a Windows fejlesztési környezetet, hogy Service Fabric Mesh-alkalmazást hozhasson létre, és üzembe helyezhesse azt az Azure Service Fabric Mesh-ben.
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: fc234f6954cf263423cc517bb3dda2ba2efa3358
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625735"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>A Windows fejlesztési környezet kialakítása Service Fabric Mesh-alkalmazások létrehozásához

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Az Azure Service Fabric Mesh-alkalmazások Windows-fejlesztői gépen való létrehozásához és futtatásához a következőkre lesz szüksége:

* Docker
* Visual Studio 2017 vagy újabb
* Service Fabric Mesh futtatókörnyezet
* Service Fabric Mesh SDK és eszközök.

Valamint a Windows következő verzióinak egyike:

* Windows 10 (Enterprise, Professional vagy Education) verziók 1709 (Fall Creators Update) vagy 1803 (Windows 10 április 2018 Update)
* Windows Server 1709-es verzió
* Windows Server 1803-es verzió

A következő utasítások segítséget nyújtanak a futtatott Windows-verzió alapján végzett minden telepítéshez.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Service Fabric Mesh-alkalmazások telepítéséhez a Visual Studio 2017-es vagy újabb verziójára van szükség. [Telepítse a 15.6.0-s][download-visual-studio] vagy újabb verziót, és engedélyezze a következő számítási feladatokat:

* ASP.NET és webfejlesztés
* Azure-fejlesztés

## <a name="install-docker"></a>A Docker telepítése

Ha a Docker már telepítve van, győződjön meg arról, hogy a legújabb verzióval rendelkezik. A Docker kérheti, hogy ha egy új verzió ki van-e, de manuálisan is ellenőrizheti, hogy a legújabb verzióval rendelkezik-e.

#### <a name="install-docker-on-windows-10"></a>A Docker telepítése Windows 10 rendszeren

Töltse le, majd telepítse a [Docker Community Edition for Windows][download-docker] legújabb verzióját a Service Fabric Mesh által használt tárolóalapú Service Fabric-alkalmazások támogatásához.

Amikor a telepítés során a rendszer kéri, válassza a **Windows-tárolók használatát Linux-tárolók helyett**.

Ha a Hyper-V nincs engedélyezve a gépen, akkor a Docker telepítője felajánlja az engedélyezését. Ehhez kattintson az **OK** gombra, ha a rendszer arra kéri.

#### <a name="install-docker-on-windows-server-2016"></a>A Docker telepítése Windows Server 2016 rendszerre

Ha nincs engedélyezve a Hyper-V szerepkör, nyissa meg a PowerShellt rendszergazdaként, futtassa a következő parancsot a Hyper-V engedélyezéséhez, majd indítsa újra a számítógépet. További információt a [Docker Enterprise Edition for Windows Server][download-docker-server] kiadást ismertető szakaszban talál.

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Indítsa újra a gépet.

Nyissa meg a PowerShellt rendszergazdaként, majd futtassa a következő parancsokat a Docker telepítéséhez:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK és eszközök

Telepítse a Service Fabric Mesh-futtatókörnyezetet, az SDK-t és az eszközöket a következő sorrendben.

1. A [Service Fabric Mesh SDK][download-sdkmesh] telepítéséhez használjon webplatform-telepítőt. Ez a Microsoft Azure Service Fabric SDK-t és a futtatókörnyezetet is telepíti.
2. Telepítse a [Visual Studio Service Fabric Mesh Tools (előzetes verzió) bővítményt][download-tools] a Visual Studio piacteréről.

## <a name="build-a-cluster"></a>Fürt létrehozása

> [!IMPORTANT]
> Egy fürt létrehozása előtt a Dockernek futnia **kell**.
> A Docker futásának ellenőrzéséhez nyisson meg egy terminálablakot, majd a `docker ps` parancs futtatásával ellenőrizze, hogy történik-e hiba. Ha a válasz nem jelez hibát, akkor a Docker fut, és készen áll a fürt létrehozására.

> [!Note]
> Ha a Windows Fall Creators Update (1709-es verzió) gépen fejleszt, csak a Windows Version 1709 Docker-rendszerképeket használhatja.
> Ha a Windows 10 április 2018 Update (1803-es verzió) gépen dolgozik, a Windows-verzió 1709-es vagy 1803-es Docker-rendszerképeket is használhat.

Ha a Visual studiót használja, akkor kihagyhatja ezt a szakaszt, mert a Visual Studio létrehoz egy helyi fürtöt, ha még nem rendelkezik ilyennel.

Ha egyszerre csak egyetlen Service Fabric alkalmazást hoz létre és futtat, hozzon létre egy egycsomópontos helyi fejlesztési fürtöt a legjobb hibakeresési teljesítményhez. Ha egyszerre több alkalmazást futtat, hozzon létre egy öt csomópontos helyi fejlesztési fürtöt. Ha Service Fabric Mesh-projektet telepít vagy hibakeresést végez, a fürtnek futnia kell.

Miután telepítette a futtatókörnyezetet, az SDK-kat, a Visual Studio-eszközöket, a Docker-t és a Docker szolgáltatást, hozzon létre egy fejlesztési fürtöt.

1. Zárja be a PowerShell-ablakot.
2. Nyisson meg egy új, emelt szintű PowerShell-ablakot rendszergazdaként. Ez a lépés a nemrégiben telepített Service Fabric-modulok betöltéséhez szükséges.
3. Futtassa az alábbi PowerShell-parancsot egy fejlesztési fürt létrehozásához:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. A helyi fürtkezelő eszköz elindításához futtassa a következő PowerShell-parancsot:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Miután a Service cluster Manager eszköz fut (megjelenik a rendszertálcán), kattintson rá a jobb gombbal, majd kattintson a **helyi fürt indítása** lehetőségre.

![1. ábra – a helyi fürt elindítása](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Készen áll a Service Fabric Mesh-alkalmazások létrehozására!

## <a name="next-steps"></a>Következő lépések

Olvassa át az [Azure Service Fabric-alkalmazás létrehozásáról](service-fabric-mesh-tutorial-create-dotnetcore.md) szóló szakaszt.

Választ kaphat a [gyakori kérdésekre és az ismert problémákra](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
