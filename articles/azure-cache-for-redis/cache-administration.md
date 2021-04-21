---
title: A Azure Cache for Redis
description: Megtudhatja, hogyan hajthat végre olyan felügyeleti feladatokat, mint például a frissítések újraindítása és ütemezése a Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7f8eb44ab301b211eaeb2dc3679c97714f91f0da
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833039"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>A Azure Cache for Redis
Ez a témakör azt ismerteti, [](#reboot) hogyan hajthat végre olyan adminisztrációs feladatokat, mint például a frissítések újraindítása és ütemezése a Azure Cache for Redis példányokhoz. [](#schedule-updates)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Újraindítás
Az **Újraindítás** panelen újraindíthatja a gyorsítótár egy vagy több csomópontját. Ez az újraindítási képesség lehetővé teszi, hogy tesztelje az alkalmazás rugalmasságát egy gyorsítótár-csomópont meghibásodása esetén.

![Képernyőkép az Újraindítás menüelem kiemelésről.](./media/cache-administration/redis-cache-administration-reboot.png)

Válassza ki az újraindítani kívánt csomópontokat, majd kattintson az **Újraindítás elemre.**

![Képernyőkép arról, hogy mely csomópontokat lehet újraindítani.](./media/cache-administration/redis-cache-reboot.png)

Ha prémium szintű gyorsítótára van, amelyen engedélyezve van a fürtözés, kiválaszthatja, hogy a gyorsítótár mely szegmensei újraindulnak.

![Újraindítás](./media/cache-administration/redis-cache-reboot-cluster.png)

A gyorsítótár egy vagy több csomópontja újraindításhoz válassza ki a kívánt csomópontokat, majd kattintson az **Újraindítás elemre.** Ha prémium szintű gyorsítótára van engedélyezett fürtözéssel, válassza ki a kívánt újraindítani kívánt szegmenseket, majd kattintson az **Újraindítás elemre.** Néhány perc múlva a kiválasztott csomópontok újraindulnak, és néhány perccel később újra online állapotba állnak.

Az ügyfélalkalmazásokra gyakorolt hatás attól függően változik, hogy melyik csomópontot indítja újra.

* **Fő** csomópont – Az elsődleges csomópont újraindításakor a Azure Cache for Redis a replikacsomópontnak, és előlépteti elsődleges csomópontra. A feladatátvétel során előfordulhat, hogy rövid ideig nem sikerül kapcsolatot létesítenünk a gyorsítótárral.
* **Replika** – A replikacsomópont újraindításakor általában nincs hatással a gyorsítótárazási ügyfelekre.
* **Elsődleges és replika** is – Ha mindkét gyorsítótár-csomópont újraindul, minden adat elveszik a gyorsítótárban, és a gyorsítótárhoz való kapcsolódás meghiúsul, amíg az elsődleges csomópont újra online állapotba nem kerül. Ha konfigurálta [](cache-how-to-premium-persistence.md)az adatmegőrzést, a rendszer visszaállítja a legutóbbi biztonsági mentést, amikor a gyorsítótár újra elérhető lesz, de a legutóbbi biztonsági mentés után végzett gyorsítótár-írások elvesznek.
*  Prémium szintű gyorsítótár csomópontjai engedélyezett fürtözéssel – Ha egy prémium szintű gyorsítótár egy vagy több csomópontját újraindítja engedélyezett fürtözéssel, a kiválasztott csomópontok viselkedése ugyanaz lesz, mint amikor újraindítja egy nem fürtözött gyorsítótár megfelelő csomópontját vagy csomópontját.

## <a name="reboot-faq"></a>Újraindítással kapcsolatos gyakori kérdések
* [Melyik csomópontot indítsam újra az alkalmazás tesztelése során?](#which-node-should-i-reboot-to-test-my-application)
* [Újraindítom a gyorsítótárat az ügyfélkapcsolatok kiürítése előtt?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Elveszítek adatokat a gyorsítótárból, ha újraindítom?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Újraindítom a gyorsítótárat a PowerShell, a parancssori felület vagy más felügyeleti eszközök használatával?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Melyik csomópontot indítsam újra az alkalmazás tesztelése során?
Az alkalmazás rugalmasságának a gyorsítótár elsődleges csomópontja meghibásodásával szembeni teszteléséhez indítsa újra a **főcsomópontot.** Az alkalmazás replikacsomópont meghibásodásával szembeni rugalmasságának teszteléséhez indítsa újra a **replikacsomópontot.** Az alkalmazás a gyorsítótár teljes meghibásodásával szembeni rugalmasságának teszteléséhez indítsa újra a **mindkét csomópontot.**

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Újraindítom a gyorsítótárat az ügyfélkapcsolatok kiürítése előtt?
Igen, ha újraindítja a gyorsítótárat, minden ügyfélkapcsolat törlődik. Az újraindítás hasznos lehet abban az esetben, ha az összes ügyfélkapcsolat logikai hiba vagy az ügyfélalkalmazás hibája miatt van használva. Minden tarifacsomag [](cache-configure.md#default-redis-server-configuration) eltérő ügyfélkapcsolati korlátokkal rendelkezik a különböző méretekhez, és a korlátok elérése után a szolgáltatás nem fogad el több ügyfélkapcsolatot. A gyorsítótár újraindításával az összes ügyfélkapcsolat törlődik.

> [!IMPORTANT]
> Ha újraindítja a gyorsítótárat az ügyfélkapcsolatok kiürítése miatt, a StackExchange.Redis automatikusan újracsatlakozik, amint a Redis-csomópont újra elérhetővé válik. Ha a mögöttes probléma nem oldódik meg, előfordulhat, hogy az ügyfélkapcsolatok továbbra is használhatók.
> 
> 



### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Elveszítek adatokat a gyorsítótárból, ha újraindítom?
Ha a fő-  és a replikacsomópontot is újraindítja, a gyorsítótárban (vagy abban a szegmensben, ha prémium gyorsítótárat használ, és engedélyezve van a fürtözés), elveszhet az összes adat, de ez sem garantált.  Ha konfigurálta [](cache-how-to-premium-persistence.md)az adatmegőrzést, a rendszer visszaállítja a legutóbbi biztonsági mentést, amikor a gyorsítótár újra elérhetővé lesz, de a biztonsági mentés után végzett gyorsítótár-írások elvesznek.

Ha csak az egyik csomópontot indítja újra, az adatok általában nem elvesznek, de továbbra is előfordulhatnak. Ha például az elsődleges csomópont újraindul, és gyorsítótár-írás van folyamatban, a gyorsítótár-írásból származó adatok elvesznek. Egy másik adatvesztési forgatókönyv az, ha újraindítja az egyik csomópontot, és a másik csomópont egyszerre leáll egy hiba miatt. További információ az adatvesztés lehetséges okairól: Mi történt a [Redis adataimban?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Újraindítom a gyorsítótárat a PowerShell, a parancssori felület vagy más felügyeleti eszközök használatával?
Igen, a PowerShell-utasításokért [lásd: Új](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis)alkalmazás Azure Cache for Redis.

## <a name="schedule-updates"></a>Frissítések ütemezése
A **Frissítések ütemezése** panelen kijelölheti a gyorsítótárpéldány karbantartási időszakát. A karbantartási időszakokkal szabályozhatja a hétnek azt a napját vagy idejét, amikor a gyorsítótárat üzemeltető virtuális gépek frissíthetők. Azure Cache for Redis mindent megtesz, hogy elindítsa és befejezze a Redis-kiszolgálószoftver frissítését a megadott időszakon belül.

> [!NOTE] 
> A karbantartási időszak a Redis-kiszolgáló frissítésére és a gyorsítótárat üzemeltető virtuális gépek operációs rendszerének frissítésére vonatkozik. A karbantartási időszak nem vonatkozik a gazdagép operációs rendszerének frissítésére a gyorsítótár virtuális gépeket vagy más Azure-hálózatkezelés gazdagépeket. Ritka esetekben, ahol a gyorsítótárak régebbi modelleken vannak üzemeltetve (meg lehet tudni, hogy a gyorsítótár egy régebbi modellen található-e, ha a gyorsítótár DNS-neve "cloudapp.net", "chinacloudapp.cn", "usgovcloudapi.net" vagy "cloudapi.de" utótaggal rendelkezik), a karbantartási időszak sem vonatkozik a vendég operációs rendszer frissítésére sem.
>


![Frissítések ütemezése](./media/cache-administration/redis-schedule-updates.png)

Karbantartási időszak megadásához ellenőrizze a kívánt napokat, adja meg a karbantartási időszak kezdő óráját minden naphoz, majd kattintson az **OK gombra.** Vegye figyelembe, hogy a karbantartási időszak utc időzónában van megadva. 

A frissítések alapértelmezett és minimális karbantartási időszaka öt óra. Ez az érték nem konfigurálható a Azure Portal, de a PowerShellben a `MaintenanceWindow` [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) parancsmag paraméterével konfigurálhatja. További információkért lásd: Ütemezett frissítéseket kezelhet a PowerShell, a parancssori felület vagy más felügyeleti eszközök használatával?

## <a name="schedule-updates-faq"></a>Frissítések ütemezése – gyakori kérdések
* [Mikor történik frissítés, ha nem használom a frissítések ütemezése funkciót?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Milyen típusú frissítéseket kell telepíteni az ütemezett karbantartási időszakban?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kezelhetek ütemezett frissítéseket a PowerShell, a parancssori felület vagy más felügyeleti eszközök használatával?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Mikor történik frissítés, ha nem használom a frissítések ütemezése funkciót?
Ha nem ad meg karbantartási időszakokat, bármikor lehet frissítéseket tenni.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Milyen típusú frissítéseket kell telepíteni az ütemezett karbantartási időszakban?
Az ütemezett karbantartási időszakban csak a Redis-kiszolgáló frissítései vannak meg. A karbantartási időszak nem vonatkozik a virtuális gép operációs rendszerének Azure-frissítésére vagy frissítésére.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kezeltem az ütemezett frissítéseket a PowerShell, a parancssori felület vagy más felügyeleti eszközök használatával?
Igen, az ütemezett frissítéseket a következő PowerShell-parancsmagokkal kezelheti:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>Következő lépések
További információ a Azure Cache for Redis funkciókról.

* [Azure Cache for Redis szolgáltatási szintek](cache-overview.md#service-tiers)

