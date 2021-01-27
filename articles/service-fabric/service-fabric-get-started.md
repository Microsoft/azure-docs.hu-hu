---
title: Windows fejlesztési környezet beállítása
description: Telepítse a futtatókörnyezetet, az SDK-t és az eszközöket, majd hozzon létre egy helyi fejlesztési fürtöt. A beállítás befejezése után készen áll az alkalmazások létrehozására Windows rendszeren.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: sfrev, devx-track-azurepowershell
ms.openlocfilehash: 07482fe41cdf7700fddd516c1a71eca5beebb4c6
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920173"
---
# <a name="prepare-your-development-environment-on-windows"></a>A fejlesztőkörnyezet előkészítése Windowson

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)
>
>

Az [Azure Service Fabric-alkalmazásoknak][1] a Windows rendszerű fejlesztői gépen való létrehozásához és futtatásához telepítse a Service Fabric-futtatókörnyezetet, az SDK-t és az eszközöket. Továbbá [engedélyeznie kell az SDK-ban található Windows PowerShell-szkriptek](#enable-powershell-script-execution) végrehajtását.

## <a name="prerequisites"></a>Előfeltételek

### <a name="supported-operating-system-versions"></a>Támogatott operációsrendszer-verziók

A fejlesztéshez a következő operációsrendszer-verziók támogatottak:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7-támogatás:
> - Alapértelmezés szerint a Windows 7 csak a Windows PowerShell 2.0-t tartalmazza. A Service Fabric PowerShell-parancsmagokhoz a PowerShell 3.0 vagy újabb verziója szükséges. A [Windows PowerShell 5,1][powershell5-download] a Microsoft letöltőközpontból tölthető le.
> - A Service Fabric fordított proxyja nem érhető el Windows 7 rendszeren.

## <a name="install-the-sdk-and-tools"></a>Az SDK és az eszközök telepítése

A webplatform-telepítő (WebPI) az SDK és az eszközök telepítésének ajánlott módja. Ha futásidejű hibákat kap a WebPI használatával, az adott Service Fabric kiadás kibocsátási megjegyzései között közvetlen hivatkozásokat is találhat a telepítőknek. A kibocsátási megjegyzések a [Service Fabric csapat blogjának](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)különböző kiadási hirdetményekben találhatók.

> [!NOTE]
> A helyi Service Fabric fejlesztési fürtök frissítése nem támogatott.

### <a name="to-use-visual-studio-2017-or-2019"></a>A Visual Studio 2017 vagy a 2019 használata

A Service Fabric-eszközök a Visual Studio 2017-es és 2019-es verziójának Azure-fejlesztési számítási feladatának részét képezik. A Visual Studio telepítésének részeként engedélyezze ezt a munkafolyamatot.
Emellett telepítenie kell a Microsoft Azure Service Fabric SDK-t és futtatókörnyezetet is a webplatform-telepítővel.

* [A Microsoft Azure Service Fabric SDK telepítése][core-sdk]

### <a name="sdk-installation-only"></a>Csak az SDK telepítése

Ha csak az SDK-ra van szükség, telepítse a következő csomagot:

* [A Microsoft Azure Service Fabric SDK telepítése][core-sdk]

Az aktuális verziók a következők:

* Service Fabric SDK és eszközök 4.2.452
* Service Fabric futtatókörnyezet 7.2.452

A támogatott verziók listáját lásd: [Service Fabric verziók](service-fabric-versions.md)

> [!NOTE]
> Az alkalmazás-vagy fürt frissítései nem támogatják az egyetlen gépi fürtöket (beépített); törölje a beépített-fürtöt, és hozza létre újra, ha a fürt frissítését kell végrehajtania, vagy ha problémába ütközik az alkalmazások frissítésével. 

## <a name="enable-powershell-script-execution"></a>A PowerShell-parancsfájl végrehajtásának engedélyezése

A Service Fabric Windows PowerShell-parancsfájlokat használ a helyi fejlesztési fürtök létrehozásához és az alkalmazások Visual Studióból történő üzembe helyezéséhez. Alapértelmezés szerint a Windows blokkolja ezen szkriptek futását. Az engedélyezésükhöz módosítania kell a PowerShell végrehajtási házirendjét. Nyissa meg a PowerShellt rendszergazdaként, és írja be a következő parancsot:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>A Docker telepítése (nem kötelező)

A [Service Fabric egy tároló Orchestrator](service-fabric-containers-overview.md) , amely a gépek egy fürtön való üzembe helyezését végzi. A Windows-tároló alkalmazások helyi fejlesztési fürtön való futtatásához először telepítenie kell a Docker for Windowst. Szerezze [be a Windows rendszerhez készült Docker CE-t (stable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Miután telepítette és elindította a Dockert, kattintson a jobb gombbal a tálca ikonjára, és válassza a **Switch to Windows containers** (Váltás Windows-tárolókra) lehetőséget. Ez a lépés szükséges ahhoz, hogy Windows-alapú Docker-rendszerképeket tudjon futtatni.

## <a name="next-steps"></a>Következő lépések

Most, hogy végzett a fejlesztőkörnyezet beállításával, belefoghat az alkalmazások létrehozásába és futtatásába.

* [Ismerje meg, hogyan hozhat létre, helyezhet üzembe és kezelhet alkalmazásokat](service-fabric-tutorial-create-dotnet-app.md)
* [További tudnivalók a programozási modellekről: Reliable Services és Reliable Actors](service-fabric-choose-framework.md)
* [A Service Fabric mintakódjainak megtekintése a GitHubon](/samples/browse/?products=azure)
* [A fürt megjelenítése a Service Fabric Explorerrel](service-fabric-visualizing-your-cluster.md)
* [Linux fejlesztőkörnyezet előkészítése Windowson](service-fabric-local-linux-cluster-windows.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "A Service Fabric kampányoldala"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-hivatkozás"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-hivatkozás"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI-hivatkozás"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
