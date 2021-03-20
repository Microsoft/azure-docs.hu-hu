---
title: A Cloud Service (klasszikus) üzembe helyezési problémáinak elhárítása | Microsoft Docs
description: A felhőalapú szolgáltatások Azure-ba történő telepítésekor néhány gyakori probléma merülhet fel. Ez a cikk néhány megoldáshoz nyújt megoldást.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7b3d7a9a674aab3976da9399f71ff4d8df08eb62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741077"
---
# <a name="troubleshoot-azure-cloud-services-classic-deployment-problems"></a>Az Azure Cloud Services (klasszikus) üzembe helyezési problémáinak elhárítása

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ha felhőalapú szolgáltatásalkalmazás-csomagot telepít az Azure-ba, a Azure Portal **Tulajdonságok** paneljén szerezheti be az üzembe helyezésre vonatkozó információkat. Az ezen a panelen található részletek segítségével elháríthatja a felhőalapú szolgáltatással kapcsolatos problémákat, és az új támogatási kérelem megnyitásakor megadhatja ezeket az információkat az Azure-támogatáshoz.

A **Tulajdonságok** panel a következőképpen jelenik meg:

* A Azure Portal kattintson a felhőalapú szolgáltatás központi telepítésére, kattintson a **minden beállítás** elemre, majd a **Tulajdonságok** elemre.

> [!NOTE]
> A **Tulajdonságok** ablaktábla tartalmát a panel jobb felső sarkában látható ikonra kattintva másolhatja a vágólapra.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Probléma: nem tudok hozzáférni a webhelyhez, de az üzembe helyezés elindult, és az összes szerepkör-példány elkészült
A portálon látható URL-hivatkozás nem tartalmazza a portot. A webhelyek alapértelmezett portja a 80. Ha az alkalmazás úgy van konfigurálva, hogy egy másik porton fusson, a webhelyhez való hozzáféréskor hozzá kell adnia a megfelelő portszámot az URL-címhez.

1. A Azure Portal kattintson a Cloud Service üzembe helyezésére.
2. A Azure Portal **Tulajdonságok** ablaktáblájában tekintse meg a szerepkör-példányok portját (a **bemeneti végpontok** alatt).
3. Ha a port nem 80, adja hozzá a megfelelő portszámot az URL-címhez az alkalmazáshoz való hozzáféréskor. Nem alapértelmezett port megadásához írja be az URL-címet, majd egy kettőspontot (:), majd a portszámot, és ne legyen szóköz.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Probléma: a saját szerepkörű példányok nem csinálok semmit
A szolgáltatás-gyógyulás automatikusan megtörténik, amikor az Azure észleli a probléma csomópontjait, ezért a szerepkör-példányokat új csomópontokra helyezi. Ebben az esetben előfordulhat, hogy a rendszer automatikusan újrahasznosítja a szerepkör példányait. Annak megállapítása, hogy a szolgáltatás gyógyulása történt-e:

1. A Azure Portal kattintson a Cloud Service üzembe helyezésére.
2. A Azure Portal **Tulajdonságok** ablaktáblájában tekintse át az információkat, és állapítsa meg, hogy a szolgáltatás-gyógyulás a szerepkörök újrahasznosításának ideje alatt történt-e.

A szerepkörök a gazdagép-operációs rendszer és a vendég-operációsrendszer-frissítések során körülbelül havonta egyszer újra újraindulnak.  
További információkért lásd a blogbejegyzések az [operációs rendszer frissítései miatti újraindítását](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades) ismertető témakört.

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Probléma: nem tudok VIP-swap-cserét végezni, és hibaüzenetet kapok
A virtuális IP-címek cseréje nem engedélyezett, ha a központi telepítés frissítése folyamatban van. A központi telepítés frissítései automatikusan megjelenhetnek a következő esetekben:

* Új vendég operációs rendszer érhető el, és az automatikus frissítésekhez van konfigurálva.
* A szolgáltatás gyógyulása zajlik.

Annak megállapítása, hogy az automatikus frissítés megakadályozza-e a VIP-swap-cserét:

1. A Azure Portal kattintson a Cloud Service üzembe helyezésére.
2. A Azure Portal **Tulajdonságok** ablaktábláján tekintse meg az **állapot** értéket. Ha **elkészült**, tekintse meg az **utolsó művelet** lehetőséget, és ellenőrizze, hogy az egyik nemrég történt-e, ami megakadályozhatja a VIP-csere megváltoztatását.
3. Ismételje meg az 1. és a 2. lépést az éles üzembe helyezéshez.
4. Ha folyamatban van egy automatikus frissítés, várja meg, amíg befejeződik a VIP-swap-művelet végrehajtása előtt.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Probléma: A szerepkör-példány a megkezdett, az inicializálás, a foglalt és a leállított művelet között hurkos
Ez az állapot problémára mutathat vissza az alkalmazás kódjában, csomagjában, vagy konfigurációs fájljában. Ebben az esetben a néhány percenként változó állapotot kell látnia, és a Azure Portal az **újrahasznosítást**, a **foglalt** vagy az **inicializálást** is elmondhatja. Ez azt jelzi, hogy hiba történt azzal az alkalmazással, amely a szerepkör-példány futtatását tartja.

A probléma elhárításával kapcsolatos további információkért tekintse meg az [Azure Pásti számítási diagnosztikai adatokkal](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data) és a [szerepkörök újrahasznosítását okozó gyakori problémákkal](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)foglalkozó blogbejegyzést.

## <a name="problem-my-application-stopped-working"></a>Probléma: az alkalmazás működése leállt
1. A Azure Portal kattintson a szerepkör-példányra.
2. A Azure Portal **Tulajdonságok** ablaktábláján vegye figyelembe a következő feltételeket a probléma megoldásához:
   * Ha a szerepkör-példány nemrég leállt (megtekintheti a **megszakítások számát**), a központi telepítés frissíthető. Várjon, és ellenőrizze, hogy a szerepkör-példány folytatja-e a működést saját maga.
   * Ha a szerepkör-példány **foglalt**, ellenőrizze az alkalmazás kódját, hogy a [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) -eseményt kezeli-e a rendszer. Előfordulhat, hogy az eseményt kezelő kódot kell felvennie vagy kijavítania.
   * Tekintse át a diagnosztikai adatok és hibaelhárítási forgatókönyveket a blogbejegyzésben, az [Azure Pásti számítási diagnosztikai adatok](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

> [!WARNING]
> Ha újrahasznosítja a felhőalapú szolgáltatást, alaphelyzetbe állítja a központi telepítés tulajdonságait, és ténylegesen törli az eredeti problémával kapcsolatos információkat.
>
>

## <a name="next-steps"></a>Következő lépések
További [hibaelhárítási cikkek](./cloud-services-allocation-failures.md) a Cloud Services szolgáltatáshoz.

Ha szeretné megtudni, hogyan lehet elhárítani a Cloud Service szerepkörrel kapcsolatos problémákat az Azure Pásti számítógép-diagnosztikai adataival, tekintse [meg a Kevin Williamson blog-sorozatot](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).