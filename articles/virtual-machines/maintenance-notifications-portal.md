---
title: A portál használata karbantartási értesítésekhez
description: Megtekintheti az Azure-ban futó virtuális gépek karbantartási értesítéseit, és megkezdheti az önkiszolgáló karbantartást a portál használatával.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: a13d79a28ac07d736b1eaf0d0e6b7f7b1ba4a4ec
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557709"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Tervezett karbantartási értesítések feldolgozása a portál használatával

**Ez a cikk a Linux és Windows rendszerű virtuális gépekre is vonatkozik.**

A [tervezett karbantartási](maintenance-notifications.md) hullám ütemezése után megtekintheti az érintett virtuális gépek listáját. 

Használhatja a Azure Portalt, és megkeresheti a karbantartásra ütemezett virtuális gépeket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali navigációs sávon kattintson a **Virtual Machines** elemre.

3. Az Virtual Machines ablaktáblán válassza az **Oszlopok szerkesztése** gombot az elérhető oszlopok listájának megnyitásához.

4. Válassza ki és adja hozzá a következő oszlopokat:

   **Karbantartási állapot**: a virtuális gép karbantartási állapotát jeleníti meg. A lehetséges értékek a következők:
      
    | Érték | Leírás |
    |-------|-------------|
    | Indítás most | A virtuális gép önkiszolgáló karbantartási ablakban van, amely lehetővé teszi a karbantartás elindítását. A virtuális gép karbantartásának megkezdéséről alább tájékozódhat. | 
    | Ütemezett | A virtuális gép karbantartásra van ütemezve, és nincs lehetőség a karbantartás elindítására. A karbantartási időszak megismeréséhez kattintson a karbantartás – ütemezett ablakra ebben a nézetben, vagy a virtuális gépre kattintva. | 
    | Már frissítve | A virtuális gép már frissítve van, és jelenleg nincs szükség további műveletre. | 
    | Újrapróbálkozás később | Sikeresen kezdeményezte a karbantartást. Az önkiszolgáló karbantartási lehetőséget később is használhatja. | 
    | Újrapróbálkozás most | Újra próbálkozhat egy korábban sikertelen, önkezdeményezett karbantartással. | 
    | - | A virtuális gép nem része egy tervezett karbantartási hullámnak. |

   **Karbantartás – önkiszolgáló ablak**: az időintervallumot jeleníti meg, amikor a virtuális gépeken végezheti el a karbantartást.
   
   **Karbantartás – ütemezett ablak**: azt az időintervallumot jeleníti meg, amikor az Azure karbantartja a virtuális gépet a karbantartás befejezése érdekében. 



## <a name="notification-and-alerts-in-the-portal"></a>Értesítés és riasztások a portálon

Az Azure a tervezett karbantartásra vonatkozó ütemtervet küld az előfizetés tulajdonosának és a közös tulajdonosok csoportjának e-mail-címének elküldésével. Ehhez a kommunikációhoz további címzetteket és csatornákat is hozzáadhat az Azure-műveletnapló riasztások létrehozásával. További információkért lásd: [műveletnapló riasztások létrehozása a szolgáltatási értesítéseken](../service-health/alerts-activity-log-service-notifications-portal.md).

Győződjön meg arról, hogy az **eseménytípus** **tervezett karbantartásként**, valamint  **Virtual Machine Scale sets** és/vagy **Virtual Machinesként** van megadva.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>A virtuális gép karbantartásának elindítása a portálról

A virtuális gép részleteinek megtekintése közben több karbantartással kapcsolatos részletet is megtekintheti.  
A virtuális gép részletes nézetének tetején megjelenik egy új értesítési menüszalag, ha a virtuális gép egy tervezett karbantartási hullámban szerepel. Emellett új lehetőség adódik a karbantartás megkezdéséhez, amikor lehetséges. 


A karbantartási értesítésre kattintva megtekintheti a karbantartási oldalt, ahol további részleteket talál a tervezett karbantartásról. Innentől kezdve **megkezdheti a karbantartást** a virtuális gépen.

A karbantartás megkezdése után a rendszer karbantartja a virtuális gépet, és a karbantartási állapot néhány percen belül frissülni fog.

Ha kihagyta az önkiszolgáló ablakot, akkor továbbra is megtekintheti az ablakot, amikor a virtuális gépet az Azure fogja karbantartani. 


## <a name="next-steps"></a>Következő lépések

A tervezett karbantartást az [Azure CLI](maintenance-notifications-cli.md) vagy a [PowerShell](maintenance-notifications-powershell.md)használatával is kezelheti.
