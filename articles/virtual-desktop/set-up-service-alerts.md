---
title: Szolgáltatási riasztások beállítása a Windows rendszerű virtuális asztali környezethez – Azure
description: Azure Service Health beállítása a szolgáltatás értesítéseinek fogadására a Windows rendszerű virtuális asztali számítógépeken.
author: Heidilohr
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: ca5f225265b42ffc67a41a5252b2e789f7bcf500
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445635"
---
# <a name="tutorial-set-up-service-alerts"></a>Oktatóanyag: szolgáltatási riasztások beállítása

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md).

A Azure Service Health segítségével figyelheti a Windows rendszerű virtuális asztali szolgáltatással kapcsolatos problémákat és az állapot-tanácsadókat. A Azure Service Health különböző típusú riasztásokat (például e-maileket vagy SMS-ket) tud értesíteni, segít megérteni a probléma hatását, és a probléma megoldásakor naprakészen tarthatja a frissítést. A Azure Service Health az állásidő enyhítését, valamint a tervezett karbantartás és az erőforrások rendelkezésre állását befolyásoló változások előkészítését is segíti.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Szolgáltatási riasztások létrehozása és konfigurálása.

Ha többet szeretne megtudni a Azure Service Healthről, tekintse meg az [Azure Health dokumentációját](../service-health/index.yml).

## <a name="create-service-alerts"></a>Szolgáltatás-riasztások létrehozása

Ebből a szakaszból megtudhatja, hogyan konfigurálhatja a Azure Service Health és hogyan állíthatja be az értesítéseket, amelyeket a Azure Portal érhet el. Különböző típusú riasztásokat állíthat be, és ütemezhet úgy, hogy időben értesítse Önt.

### <a name="recommended-service-alerts"></a>Ajánlott szolgáltatási riasztások

Javasoljuk, hogy hozzon létre szolgáltatási riasztásokat a következő állapot-események típusaihoz:

- **Szolgáltatási probléma:** Értesítéseket kaphat olyan főbb problémákról, amelyek hatással vannak a felhasználók és a szolgáltatás közötti kapcsolatra, illetve a Windows rendszerű virtuális asztali bérlők felügyeletére.
- **Egészségügyi tanácsadó:** A beavatkozást igénylő értesítések fogadása. Az alábbiakban néhány példát láthat az ilyen típusú értesítésekre:
    - Virtual Machines (VM) nem biztonságos módon konfigurálva a 3389-as Open-portként
    - A funkciók elavulása

### <a name="configure-service-alerts"></a>Szolgáltatási riasztások konfigurálása

A szolgáltatási riasztások konfigurálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **Service Health lehetőséget.**
3. A riasztások és értesítések beállításához kövesse a [műveletnapló riasztások létrehozása a szolgáltatás értesítéseiben](../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json) című témakör utasításait.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan állíthatja be és használhatja a Azure Service Health a szolgáltatással kapcsolatos problémák és a Windows rendszerű virtuális asztalok állapot-tanácsadói figyelésére. Ha szeretné megtudni, hogyan jelentkezhet be a Windows rendszerű virtuális asztalra, folytassa a Kapcsolódás a Windows rendszerű virtuális asztali gépekhez című témakört.

> [!div class="nextstepaction"]
> [Kapcsolódás a Távoli asztal-ügyfélhez Windows 7 és Windows 10 rendszeren](./connect-windows-7-10.md)