---
title: Az Azure Spring Cloud VNET önálló diagnosztizálása
description: Ismerje meg, hogyan diagnosztizálhatja és megoldhatja a VNET-ben futó Azure Spring Cloud-beli problémákat.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125063"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>Az Azure Spring Cloud futtatásának önálló diagnosztizálása a VNET-ben
Az Azure Spring Cloud Diagnostics a konfiguráció nélkül támogatja a virtuális hálózatokon futó interaktív hibaelhárítási alkalmazásokat. Az Azure Spring Cloud Diagnostics észleli a problémákat, és útmutatást nyújt a hibák elhárításához és megoldásához.

## <a name="navigate-to-the-diagnostics-page"></a>Navigáljon a diagnosztika lapra
A következő eljárás elindítja a hálózati alkalmazások diagnosztizálását.
1. Jelentkezzen be az Azure Portalra.
1. Nyissa meg az Azure Spring Cloud áttekintés oldalát.
1. A bal oldali navigációs ablaktábla menüjében válassza a **diagnosztizálás és megoldás problémák** elemet.
1. Válassza ki a harmadik kategóriát, majd a **hálózatkezelés** lehetőséget.

   ![Saját diagnosztika címe](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>Diagnosztikai jelentés megtekintése
Miután rákattintott a **hálózatkezelés** kategóriára, megtekintheti az Azure Spring Cloud: **DNS-feloldás** és a **szükséges kimenő forgalom** szolgáltatással kapcsolatos, a VNet-hez kapcsolódó két problémát.

   ![Saját diagnosztikai beállítások](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

Válassza ki a célként megadott problémát a diagnosztikai jelentés megtekintéséhez. Ekkor megjelenik a diagnosztika összefoglalása, például: 

* *Az erőforrás el lett távolítva.*
* Az *erőforrás nincs telepítve a saját virtuális hálózatában*.

Egyes eredmények kapcsolódó dokumentációt tartalmaznak. A különböző alhálózatok külön jelenítik meg az eredményeket.

## <a name="dns-resolution"></a>DNS-feloldás 
Ha a **DNS-feloldás** lehetőséget választja, az eredmények jelzik, hogy vannak-e DNS-problémák az alkalmazásokkal.  Az kifogástalan állapotú alkalmazások a következőképpen vannak felsorolva:

* *A DNS-problémák a "subnet01" alhálózatban felmerülő problémák nélkül oldhatók* fel.
* *A DNS-problémák a "subnet02" alhálózatban felmerülő problémák nélkül oldhatók* fel.

A következő diagnosztikai jelentés például azt jelzi, hogy az alkalmazás állapota ismeretlen. A jelentéskészítési idő kerete nem tartalmazza az állapot jelentésének időpontját.  Tegyük fel, hogy a környezet befejezési időpontja *2021-03-03T04:20:00Z*. A **DNS-feloldási táblázat megjelenítésének** legújabb időbélyeg a *2021-03-03T03:39:00Z*, az előző nap. Előfordulhat, hogy az állapot-ellenőrzési napló nem lett elküldve a blokkolt hálózat miatt. 

Az ismeretlen állapot eredménye a kapcsolódó dokumentációt tartalmazza.  A bal oldali szögletes zárójelre kattintva megtekintheti a legördülő képernyőt.

   ![Ismeretlen DNS](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

Ha hibásan konfigurálta a saját DNS Zone Records-készletet, a következők kritikus eredményt kapnak: `Failed to resolve the Private DNS in subnet xxx` . 

A legördülő **DNS-feloldási táblázat megjelenítésében** megtalálja a részletes üzenet adatait, amelyből megtekintheti a konfigurációt.

## <a name="required-outbound-traffic"></a>Szükséges kimenő forgalom 

Ha a **szükséges kimenő forgalom** lehetőséget választja, az eredmények jelzik, hogy vannak-e kimenő forgalmi problémák az alkalmazásokkal.  Az kifogástalan állapotú alkalmazások a következőképpen vannak felsorolva:

* * A szükséges kimenő forgalom a "subnet01" alhálózatban felmerülő problémák nélkül oldható meg.
* * A szükséges kimenő forgalom a "subnet02" alhálózatban felmerülő problémák nélkül oldható meg.

Ha az alhálózatot NSG vagy tűzfalszabályok blokkolja, és ha nem tiltotta le a naplót, a következő hibák jelennek meg. Megtekintheti, hogy megtekintette-e az [ügyfél feladatait](spring-cloud-vnet-customer-responsibilities.md).
    
   ![Sikertelen végpont](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

Ha a 30 percen belül nem áll rendelkezésre adatérték `Required Outbound Traffic Table Renderings` , az eredmény lesz `health status unknown` . Lehet, hogy a hálózat le van tiltva, vagy a naplózási szolgáltatás nem áll le.

   ![A diagnosztikai végpont ismeretlen](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>Lásd még
* [Azure Spring Cloud öndiagnosztika](spring-cloud-howto-self-diagnose-solve.md)