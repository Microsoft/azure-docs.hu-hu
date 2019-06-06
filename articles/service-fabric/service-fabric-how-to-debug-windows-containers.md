---
title: Windows-tárolókat Service Fabric és a VS hibakeresési |} A Microsoft Docs
description: Megtudhatja, hogyan lehet hibákat keresni a Windows-tárolókhoz az Azure Service Fabric használatával a Visual Studio 2019.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo, mikhegn
ms.openlocfilehash: 15f288d5400b49ec05c9ffb936fd2097cc61bae8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428144"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Útmutató: Windows-tárolókhoz az Azure Service Fabric használatával a Visual Studio 2019 hibakeresése

A Visual Studio 2019 mint a Service Fabric-szolgáltatások is hibakeresési tárolókban lévő .NET-alkalmazások. Ez a cikk bemutatja, hogyan konfigurálja a környezetet, és ezután hibakeresése egy helyi Service Fabric-fürtön futó tárolóban lévő .NET-alkalmazás.

## <a name="prerequisites"></a>Előfeltételek

* A Windows 10-es, kövesse az ebben a rövid útmutatóban a [konfigurálása a Windows 10-es Windows-tárolók futtatásához](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* Windows Server 2016 rendszerben, kövesse az ebben a rövid útmutatóban a [Windows 2016 konfigurálása a Windows-tárolók futtatásához](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* A helyi Service Fabric-környezet beállítása a következő [a Windows fejlesztési környezet előkészítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Tárolók hibakeresése a fejlesztői környezet beállítása

1. Győződjön meg arról, hogy a Docker ablak szolgáltatás fut-e a következő lépés végrehajtása előtt.

1. DNS-feloldás közötti tárolók támogatása érdekében kell a helyi fejlesztési fürtöt, a számítógépnevet használja. Ezeket a lépéseket is szükségesek, ha azt szeretné, a fordított proxyn keresztül cím szolgáltatásokhoz.
   1. Rendszergazdaként nyissa meg PowerShell
   2. Keresse meg a fürt SDK-telepítési mappa, általában `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. A parancsfájl futtatása `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Használhatja a `-CreateOneNodeCluster` , állítson be egy egycsomópontos fürtöt. Alapértelmezés szerint létrehoz egy helyi ötcsomópontos fürt.
      >

      A Service fabric DNS szolgáltatással kapcsolatos további tudnivalókért lásd: [DNS-szolgáltatás az Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). A Service Fabric használatával kapcsolatos további fordított proxy egy tárolóban futó szolgáltatások kapcsolatban lásd: [fordított proxy különleges kezelést tárolókban futó szolgáltatások](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Tárolók Service Fabricben történő hibakeresése során ismert korlátozások

Alább az ismert korlátozások hibakeresési tárolók a Service Fabric és a lehetséges megoldások listája:

* Localhost ClusterFQDNorIP használatával nem támogatja a DNS-feloldás tárolókban.
    * Megoldás: A gép nevét (lásd fent) használatával helyi fürt beállítása
* Egy virtuális gépen futó Windows 10-es nem kap vissza a tárolót a DNS-válaszban.
    * Megoldás: Tiltsa le a virtuális gépek hálózati Adapteren IPv4 UDP ellenőrzőösszeg-kiszervezés
    * Windows 10-es futtató csökkentheti a hálózati teljesítmény a gépen.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Ugyanazt az alkalmazást a DNS szolgáltatásban feloldása szolgáltatásnév nem működik, a Windows 10-es, ha az alkalmazás telepítve lett a Docker Compose használatával
    * Megoldás: A Szolgáltatásvégpontok megoldásához servicename.applicationname használja
    * https://github.com/Azure/service-fabric-issues/issues/1062
* ClusterFQDNorIP IP-címet használja, ha a gazdagép elsődleges IP módosítása megszakítja a DNS-funkciók.
    * Megoldás: Hozza létre újra a fürtöt az új elsődleges IP használata a gazdagépen, vagy használja a gép nevét. Ez törés a rendszer kialakításából fakad.
* Ha a teljes tartománynév, a fürt létrehozásakor nem oldható fel a hálózaton, a DNS sikertelen lesz.
    * Megoldás: Hozza létre újra a helyi fürtöt, az elsődleges IP-címe a host használatával. Ez a hiba a rendszer kialakításából fakad.
* Amikor hibakeresést egy tároló, docker-naplók csak akkor jelenik meg a Visual Studio kimeneti ablakában, nem Service Fabric API-kon keresztül, beleértve a Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>A Service Fabric docker-tárolókban futó .NET-alkalmazás hibakeresése

1. Futtassa a Visual Studiót rendszergazdaként.

1. Nyissa meg a meglévő .NET-alkalmazás, vagy hozzon létre egy újat.

1. Kattintson a jobb gombbal a projektre, és válassza ki **Hozzáadás -> tároló az Orchestrator-támogatás a Service Fabric ->**

1. Nyomja meg **F5** az alkalmazás hibakeresésének elkezdéséhez.

    A Visual Studio támogatja a .NET és .NET Core konzol és az ASP.NET-projekt típusa.

## <a name="next-steps"></a>További lépések
A Service Fabric és a tárolók képességeivel kapcsolatos további tudnivalókért tekintse meg a Service Fabric-tárolók overview](service-fabric-containers-overview.md).
