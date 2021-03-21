---
title: Meglévő .NET-alkalmazás tárolóba helyezése a Service Fabric Mesh használatához
description: Adja hozzá Service Fabric Mesh-tárolók előkészítésének támogatását a teljes .NET-keretrendszert használó ASP.NET és konzolos projektekhez.
author: georgewallace
ms.author: gwallace
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: b9c5053a2a49c942cc89bd50c65e13f3a2f8d9d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625878"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Meglévő .NET-alkalmazás tárolóba helyezése a Service Fabric Mesh használatához

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez a cikk bemutatja, hogyan adhat hozzá Service Fabric Mesh Container-előkészítési támogatást egy meglévő .NET-alkalmazáshoz.

A Visual Studio 2017-es verziójában tárolókra bontás-támogatást adhat hozzá a teljes .NET-keretrendszert használó ASP.NET és konzolos projektekhez.

> [!NOTE]
> A .NET **Core** -projektek jelenleg nem támogatottak.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Győződjön meg arról, hogy [beállította a fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Ide tartozik a Service Fabric futtatókörnyezet, az SDK, a Docker, a Visual Studio 2017 telepítése és egy helyi fürt létrehozása.

## <a name="open-an-existing-net-app"></a>Meglévő .NET-alkalmazás megnyitása

Nyissa meg azt az alkalmazást, amelyhez hozzá szeretné adni a Container-előkészítési támogatást.

Ha példát szeretne kipróbálni, használhatja az [üzlet](https://github.com/MikkelHegn/ContainersSFLab) kód mintát. A cikk további része azt feltételezi, hogy ezt a projektet használjuk, bár ezeket a lépéseket saját projektre is alkalmazhatja.

Az **üzlet** -projekt másolatának beolvasása:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

A letöltés után a Visual Studio 2017-es verziójában nyissa meg a **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.SLN**.

## <a name="add-container-support"></a>Tároló-támogatás hozzáadása
 
Adja hozzá a Container-előkészítési támogatást egy meglévő ASP.NET-vagy konzol-projekthez az Service Fabric Mesh Tools használatával a következőképpen:

A Visual Studio Solution Explorerben kattintson a jobb gombbal a projekt nevére (a példában a **eShopLegacyWebForms**), majd válassza a tároló **hozzáadása**  >  **Orchestrator-támogatás** lehetőséget.
Megjelenik a **Container Orchestrator-támogatás hozzáadása** párbeszédpanel.

![A Visual Studio tároló-Orchestrator hozzáadása párbeszédpanel](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Válassza ki **Service Fabric rácsvonalat** a legördülő listából, majd kattintson **az OK** gombra.


>[!NOTE]
> 2020. november 2., a [letöltési sebességre vonatkozó korlátozások](https://docs.docker.com/docker-hub/download-rate-limit/) a Docker ingyenes csomag fiókjaiból származó névtelen és hitelesített kérelmekre vonatkoznak, és az IP-cím kényszeríti. További részletek: [hitelesítés a Docker hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub)használatával.
>
> A korlátozott arány elkerülése érdekében győződjön meg arról, hogy a `FROM microsoft/aspnet:4.7.2-windowsservercore-1803 AS base` Docker alapértelmezett értéke lecserélve a következőre: `FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-1803 AS base`

Az eszköz ezután ellenőrzi, hogy telepítve van-e a Docker, hozzáadja a Docker a projekthez, és lekéri a projekthez tartozó Docker-rendszerképet.  
A megoldáshoz egy Service Fabric Mesh-alkalmazási projekt van hozzáadva. A háló közzétételi profiljait és konfigurációs fájljait tartalmazza. A projekt neve megegyezik a projekt nevével, az "alkalmazás" a végéhez fűzve, például **eShopLegacyWebFormsApplication**. 

Az új háló projektben két mappát fog látni:
- Olyan YAML-fájlokat tartalmazó **alkalmazás-erőforrások** , amelyek további rácsvonal-erőforrásokat, például a hálózatot írják le.
- A Service. YAML fájlt tartalmazó **szolgáltatás-erőforrások** , amelyek azt ismertetik, hogyan kell futtatni az alkalmazást az üzembe helyezéskor.

Miután hozzáadta a Container-előkészítési támogatást az alkalmazáshoz, az **F5** billentyű lenyomásával hibakeresést végezhet a .net-alkalmazáson a helyi Service Fabric Mesh-fürtön. Itt látható az Service Fabric Mesh-fürtön futó ASP.NET alkalmazás: 

![Elektronikus alkalmazás](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Most már közzéteheti az alkalmazást az Azure Service Fabric Mesh szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

Tekintse meg, hogyan tehet közzé egy alkalmazást a Service Fabric Meshban: [oktatóanyag – Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)