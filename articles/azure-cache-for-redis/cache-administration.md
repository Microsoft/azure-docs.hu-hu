---
title: Az Azure cache felügyelete a Redis-hez
description: Megtudhatja, hogyan hajthat végre olyan felügyeleti feladatokat, mint például a Redis készült Azure cache újraindítása és frissítéseinek ütemezett frissítése
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 0a79b0b5b5f21d1c75fec6b062f1ca91cfe9dd1f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219199"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Az Azure cache felügyelete a Redis-hez
Ez a témakör azt ismerteti, hogyan hajtható végre olyan adminisztrációs feladatok, mint például a Redis-példányok Azure cache-re való [újraindítása](#reboot) és a [frissítések ütemezése](#schedule-updates) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Újraindítás
Az **Újraindítás** panel lehetővé teszi a gyorsítótár egy vagy több csomópontjának újraindítását. Ez az újraindítási funkció lehetővé teszi az alkalmazás rugalmasságának tesztelését, ha hiba történt a gyorsítótár-csomópontok meghibásodása esetén.

![Képernyőkép, amely kiemeli az újraindítás menüpontot.](./media/cache-administration/redis-cache-administration-reboot.png)

Válassza ki az újraindítani kívánt csomópontokat, majd kattintson az **Újraindítás** gombra.

![Képernyőkép, amely megjeleníti, hogy mely csomópontokat lehet újraindítani.](./media/cache-administration/redis-cache-reboot.png)

Ha olyan prémium szintű gyorsítótárral rendelkezik, amelyen engedélyezve van a fürtözés, kiválaszthatja, hogy a gyorsítótár mely szegmensei induljon újra.

![Újraindítás](./media/cache-administration/redis-cache-reboot-cluster.png)

A gyorsítótár egy vagy több csomópontjának újraindításához válassza ki a kívánt csomópontokat, majd kattintson az **Újraindítás** gombra. Ha olyan prémium szintű gyorsítótárral rendelkezik, amelyen engedélyezve van a fürtözés, válassza ki a kívánt szegmenseket az újraindításhoz, majd kattintson az **Újraindítás** gombra. Néhány perc elteltével a kijelölt csomópontok újraindulnak, és néhány perc múlva újra online állapotba kerülnek.

Az ügyfélalkalmazások hatása attól függően változik, hogy melyik csomópontokat kívánja újraindítani.

* **Főkiszolgáló** – az elsődleges csomópont újraindításakor a Redis Azure-gyorsítótára feladatátvételt hajt végre a replika csomóponton, és támogatja az elsődlegesnek. A feladatátvétel során előfordulhat, hogy egy rövid időszak, amelyben a kapcsolatok sikertelenek lesznek a gyorsítótárban.
* **Replika** – a replika csomópontjának újraindításakor általában nincs hatással a gyorsítótár-ügyfelekre.
* **Mind az elsődleges,** mind a replika – ha a gyorsítótár-csomópontok újraindulnak, az összes adat elvész a gyorsítótárban, és a gyorsítótárhoz való kapcsolódás meghiúsul, amíg az elsődleges csomópont ismét online állapotba nem kerül. Ha beállította az [adatmegőrzést](cache-how-to-premium-persistence.md), a legutóbbi biztonsági mentés visszakerül, ha a gyorsítótár online állapotba kerül, de a legutóbbi biztonsági mentést követően történt gyorsítótár-írások elvesznek.
* **Prémium szintű gyorsítótár csomópontja** , amelyen engedélyezve van a fürtözés. Ha egy prémium szintű gyorsítótár egy vagy több csomópontját a fürtözés engedélyezése után újraindítja, a kiválasztott csomópontok viselkedése megegyezik a nem fürtözött gyorsítótár megfelelő csomópontjának vagy csomópontjainak újraindításával.

## <a name="reboot-faq"></a>Újraindítással kapcsolatos gyakori kérdések
* [Melyik csomópontot kell újraindítani az alkalmazás teszteléséhez?](#which-node-should-i-reboot-to-test-my-application)
* [Újra lehet indítani a gyorsítótárat az ügyfélkapcsolatok törléséhez?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Elveszítem az adatok a gyorsítótárból, ha újraindítást végezek?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Újraindíthatom a gyorsítótárat a PowerShell, a CLI vagy más felügyeleti eszközök használatával?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Melyik csomópontot kell újraindítani az alkalmazás teszteléséhez?
Az alkalmazás rugalmasságának teszteléséhez a gyorsítótár elsődleges csomópontjának meghibásodása esetén indítsa újra a **fő** csomópontot. Ha tesztelni szeretné az alkalmazás rugalmasságát a replika csomópont meghibásodása miatt, indítsa újra a **replika** csomópontot. Ha tesztelni szeretné az alkalmazás rugalmasságát a gyorsítótár teljes meghibásodása ellen, indítsa újra **mindkét** csomópontot.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Újra lehet indítani a gyorsítótárat az ügyfélkapcsolatok törléséhez?
Igen, ha újraindítja a gyorsítótárat, az összes ügyfél-kapcsolat törlődik. Az újraindítás hasznos lehet abban az esetben, ha az összes ügyfélkapcsolatot a rendszer logikai hiba vagy az ügyfélalkalmazás hibája miatt használja fel. Az egyes díjszabási szintek eltérő [ügyfélkapcsolati korlátokkal](cache-configure.md#default-redis-server-configuration) rendelkeznek a különböző méretekhez, és a határértékek elérésekor a rendszer nem fogad el több ügyfélkapcsolatot. A gyorsítótár újraindítása lehetővé teszi az összes ügyfélkapcsolat törlését.

> [!IMPORTANT]
> Ha újraindítja a gyorsítótárat az ügyfélkapcsolatok törléséhez, a StackExchange. Redis automatikusan újracsatlakozik, ha a Redis csomópont ismét online állapotba kerül. Ha az alapul szolgáló probléma nem oldható fel, az ügyfélkapcsolatok továbbra is használhatók.
> 
> 



### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Elveszítem az adatok a gyorsítótárból, ha újraindítást végezek?
Ha a **fő** -és a **replika** -csomópontokat is újraindítja, akkor a gyorsítótárban lévő összes adat (vagy abban az esetben, ha egy prémium szintű gyorsítótár használata engedélyezve van a fürtözéssel) elveszik, de ez nem garantált. Ha beállította az [adatmegőrzést](cache-how-to-premium-persistence.md), a rendszer visszaállítja a legutóbbi biztonsági mentést, ha a gyorsítótár online állapotba kerül, de a biztonsági mentés után történt gyorsítótár-írások elvesznek.

Ha csak az egyik csomópontot újraindítja, a rendszer általában nem veszíti el az adatvesztést, de az is lehetséges. Ha például az elsődleges csomópont újraindul, és a gyorsítótár írása folyamatban van, a gyorsítótárból származó adatok elvesznek. Az adatvesztés egy másik forgatókönyve az egyik csomópont újraindítása, a másik csomópont pedig egy hiba miatt leáll. Az adatvesztés lehetséges okaival kapcsolatos további információkért lásd: [Mi történt az Redis-beli adatokkal?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Újraindíthatom a gyorsítótárat a PowerShell, a CLI vagy más felügyeleti eszközök használatával?
Igen, a PowerShell-utasításokért lásd: az [Azure cache újraindítása a Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Frissítések ütemezése
A **frissítések ütemezett frissítése** panelen megadhatja a gyorsítótár-példány karbantartási időszakát. A karbantartási időszak lehetővé teszi a hét azon napjának és időpontjának szabályozását, amely alatt a gyorsítótárat üzemeltető virtuális gépek (ek) frissíthetők. Az Azure cache for Redis az Ön által megadott időszakon belül a Redis-kiszolgáló szoftverének megkezdéséhez és befejezéséhez szükséges legjobb megoldás.

> [!NOTE] 
> A karbantartási időszak a Redis kiszolgálói frissítéseire és frissítéseire vonatkozik a gyorsítótárat üzemeltető virtuális gépek operációs rendszerének esetében. A karbantartási időszak nem vonatkozik a gazdagép operációs rendszerének frissítéseire a gyorsítótárban lévő virtuális gépeket vagy más Azure hálózati összetevőket üzemeltető gazdagépeken. Ritka esetekben, amikor a gyorsítótárak régebbi modelleken futnak (ha a gyorsítótár DNS-neve a "cloudapp.net", a "chinacloudapp.cn", a "usgovcloudapi.net" vagy a "cloudapi.de" utótagot oldja fel, akkor a karbantartási időszak nem lesz érvényes a vendég operációs rendszer frissítéseire.
>


![Frissítések ütemezése](./media/cache-administration/redis-schedule-updates.png)

A karbantartási időszak megadásához tekintse meg a kívánt napokat, és minden nap esetében határozza meg a karbantartási időszak kezdő óráját, majd kattintson **az OK** gombra. Vegye figyelembe, hogy a karbantartási időszak időpontja UTC. 

A frissítések alapértelmezett és minimális karbantartási időszaka öt óra. Ez az érték nem konfigurálható a Azure Portalból, de a `MaintenanceWindow` [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) parancsmag paraméterének használatával konfigurálhatja a PowerShellben. További információ: kezelhetem az ütemezett frissítéseket a PowerShell, a CLI vagy más felügyeleti eszközök használatával?

## <a name="schedule-updates-faq"></a>Frissítések ütemezett frissítései – gyakori kérdések
* [Mikor történik a frissítések használata, ha nem használom a frissítések ütemezett szolgáltatását?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Milyen típusú frissítések történnek az ütemezett karbantartási időszakban?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kezelhetem az ütemezett frissítéseket a PowerShell, a CLI vagy más felügyeleti eszközök használatával?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Mikor történik a frissítések használata, ha nem használom a frissítések ütemezett szolgáltatását?
Ha nem ad meg karbantartási időszakot, a frissítés bármikor elvégezhető.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Milyen típusú frissítések történnek az ütemezett karbantartási időszakban?
Az ütemezett karbantartási időszakban csak a Redis-kiszolgáló frissítései lesznek elérhetők. A karbantartási időszak nem vonatkozik az Azure frissítéseire vagy a virtuális gép operációs rendszerének frissítéseire.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kezelhetem az ütemezett frissítéseket a PowerShell, a CLI vagy más felügyeleti eszközök használatával?
Igen, a következő PowerShell-parancsmagokkal kezelheti az ütemezett frissítéseket:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [Új – AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [Új – AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Következő lépések
További információ az Azure cache Redis szolgáltatásairól.

* [Azure cache a Redis szolgáltatási szintjeihez](cache-overview.md#service-tiers)

