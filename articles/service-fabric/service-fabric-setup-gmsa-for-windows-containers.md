---
title: Az Azure Service Fabric-tároló szolgáltatások csoportosan felügyelt szolgáltatásfiók beállítása |} A Microsoft Docs
description: Ismerje meg most, hogy az Azure Service Fabric egy tárolóban használt csoportosan felügyelt szolgáltatásfiók beállítása.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: subramar
ms.openlocfilehash: b4aa08c10cef91b3bf2eef4c87967a74524f6500
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621794"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>A Service Fabricen futó Windows-tárolók csoportosan felügyelt szolgáltatásfiók beállítása

Csoportosan felügyelt szolgáltatásfiókok (felügyelt szolgáltatásfiókok csoportot), a hitelesítő adatok specifikáció fájl beállítása (`credspec`) a fürt minden csomópontján el van helyezve. A fájl átmásolható egy Virtuálisgép-bővítménnyel minden csomóponton.  A `credspec` fájlnak tartalmaznia kell a gMSA-fiók adatait. További információ a `credspec` fájlt [hozzon létre egy hitelesítőadat-specifikációja](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). A hitelesítő adatok meghatározása és a `Hostname` az alkalmazásjegyzékben megadott címkével. A `Hostname` címkének egyeznie kell a tároló fut a gMSA-fiók neve.  A `Hostname` címke lehetővé teszi, hogy a tároló hitelesítse magát a tartományban, a Kerberos-hitelesítést használó más szolgáltatásokhoz.  Adjon meg egy minta a `Hostname` és a `credspec` jegyzékfájlt, az alkalmazás az alábbi kódrészletben látható:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
A következő lépésben olvassa el a következő cikkeket:

* [Egy Windows-tároló üzembe helyezése a Service Fabric Windows Server 2016-on](service-fabric-get-started-containers.md)
* [Egy Docker-tároló üzembe helyezése a Service fabric Linux rendszeren](service-fabric-get-started-containers-linux.md)
