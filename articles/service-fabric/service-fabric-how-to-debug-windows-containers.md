---
title: Hibakeresés Windows-tárolókon a Service Fabric és a VS használatával
description: Ismerje meg, hogyan lehet Windows-tárolókat hibakeresést végezni az Azure Service Fabric a Visual Studio 2019 használatával.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 3e6e7785278b182cebb21115a70f35ade52303c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247251"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Útmutató: Windows-tárolók hibakeresése az Azure Service Fabric a Visual Studio 2019 használatával

A Visual Studio 2019-es verziójában a .NET-alkalmazások a tárolókban Service Fabric szolgáltatásként használhatók. Ez a cikk bemutatja, hogyan konfigurálhatja a környezetet, és hogyan végezhet hibakeresést egy .NET-alkalmazásban egy helyi Service Fabric-fürtön futó tárolóban.

## <a name="prerequisites"></a>Előfeltételek

* Windows 10 rendszeren a Windows 10 Windows- [tárolók futtatására való konfigurálásához](/virtualization/windowscontainers/quick-start/quick-start-windows-10) kövesse ezt a rövid útmutatót.
* Windows Server 2016 rendszeren a Windows 2016 Windows- [tárolók futtatására való konfigurálásához](/virtualization/windowscontainers/quick-start/quick-start-windows-server) kövesse ezt a rövid útmutatót.
* A helyi Service Fabric környezet beállítása a [fejlesztési környezet előkészítése Windows](./service-fabric-get-started.md) rendszeren

## <a name="configure-your-developer-environment-to-debug-containers"></a>A fejlesztői környezet konfigurálása a tárolók hibakereséséhez

1. A következő lépés végrehajtása előtt ellenőrizze, hogy fut-e az ablakhoz tartozó Docker szolgáltatás.

1. A tárolók közötti DNS-feloldás támogatásához be kell állítania a helyi fejlesztési fürtöt a számítógépnév használatával. Ezek a lépések akkor is szükségesek, ha a szolgáltatásokat a fordított proxyn keresztül szeretné kezelni.
   1. A PowerShell megnyitása rendszergazdaként
   2. Navigáljon az SDK-fürt telepítési mappájához, amely általában a következő: `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup` .
   3. A parancsfájl futtatása `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > A használatával egy `-CreateOneNodeCluster` egycsomópontos fürtöt állíthat be. Az alapértelmezett érték egy helyi, öt csomópontot tartalmazó fürtöt hoz létre.
      >

      Ha többet szeretne megtudni a Service Fabric DNS szolgáltatásáról, tekintse meg a [DNS-szolgáltatás az Azure Service Fabric](./service-fabric-dnsservice.md)-ban című témakört. Ha többet szeretne megtudni a Service Fabric fordított proxy használatáról a tárolóban futó szolgáltatásokból, tekintse meg a [reverse proxy speciális kezelését tárolókban futó szolgáltatásokhoz](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers)című témakört.

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Ismert korlátozások a tárolók hibakereséséhez Service Fabric

Az alábbiakban felsoroljuk a Service Fabric és a lehetséges megoldásokkal kapcsolatos hibakeresési tárolók ismert korlátozásait:

* A ClusterFQDNorIP localhost használata nem támogatja a tárolókban a DNS-feloldást.
    * Megoldás: állítsa be a helyi fürtöt a számítógépnév használatával (lásd fent)
* A Windows10 virtuális gépen való futtatása nem fogja tudni visszakapni a DNS-választ a tárolóba.
    * Megoldás: tiltsa le az UDP ellenőrzőösszeg kiszervezését az IPv4-hez a Virtual Machines hálózati adapteren
    * A Windows10 futtatása csökkenti a hálózati teljesítményt a gépen.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* A DNS-szolgáltatásnév használatával a szolgáltatások ugyanazon alkalmazásban való feloldása nem működik a Windows10, ha az alkalmazás a Docker-összeállítással lett telepítve.
    * Megoldás: a szolgáltatásnév. ApplicationName használata a szolgáltatási végpontok feloldásához
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Ha IP-címet használ a ClusterFQDNorIP, az elsődleges IP-cím módosítása a gazdagépen megszakítja a DNS-funkciókat.
    * Megoldás: hozza létre újra a fürtöt az új elsődleges IP-cím használatával a gazdagépen, vagy használja a gép nevét. Ez a törés a terv szerint történik.
* Ha a fürt teljes tartományneve úgy lett létrehozva, hogy a nem oldható fel a hálózaton, a DNS sikertelen lesz.
    * Megoldás: hozza létre újra a helyi fürtöt a gazdagép elsődleges IP-címének használatával. Ez a hiba a tervezés szerint történik.
* A tárolók hibakeresése során a Docker-naplók csak a Visual Studio kimeneti ablakában lesznek elérhetők, nem pedig Service Fabric API-kkal, beleértve a Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Docker-tárolókban futó .NET-alkalmazás hibakeresése Service Fabric

1. Futtassa a Visual studiót rendszergazdaként.

1. Nyisson meg egy meglévő .NET-alkalmazást, vagy hozzon létre egy újat.

1. Kattintson a jobb gombbal a projektre, és válassza a **Hozzáadás – > tároló Orchestrator-támogatás-> Service Fabric**

1. Az alkalmazás hibakeresésének megkezdéséhez nyomja le az **F5** billentyűt.

    A Visual Studio támogatja a .NET és a .NET Core konzol-és ASP.NET.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Service Fabric és a tárolók képességeiről, tekintse meg a [Service Fabric-tárolók áttekintése](service-fabric-containers-overview.md)című témakört.
