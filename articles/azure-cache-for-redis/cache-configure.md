---
title: A Azure Cache for Redis
description: Ismerje meg az alapértelmezett Redis-konfigurációt a Azure Cache for Redis, és megtudhatja, hogyan konfigurálhatja a Azure Cache for Redis példányokat
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ffcaf7fc50a310cdd4773868c62bbbb801619e3c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833147"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>A Azure Cache for Redis
Ez a témakör a saját példányai számára elérhető Azure Cache for Redis ismerteti. Ez a témakör a redis-példányok alapértelmezett Redis-Azure Cache for Redis is beható.

> [!NOTE]
> A prémium szintű gyorsítótár funkcióinak konfigurálásával és használatával kapcsolatos [](cache-how-to-premium-clustering.md)további információkért lásd az adatmegőrzés konfigurálását, [](cache-how-to-premium-persistence.md)a fürtszolgáltatás konfigurálását és a Virtual Network [konfigurálását.](cache-how-to-premium-vnet.md)
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>A Azure Cache for Redis konfigurálása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache for Redis beállításokat az Erőforrás menü Azure Cache for Redis **panelen** lehet **megtekinteni és konfigurálni.**

![Azure Cache for Redis beállítások](./media/cache-configure/redis-cache-settings.png)

A következő beállításokat az Erőforrás menüben **tudja megtekinteni és konfigurálni.**

* [Áttekintés](#overview)
* [Tevékenységnapló](#activity-log)
* [Hozzáférés-vezérlés (IAM)](#access-control-iam)
* [Címkék](#tags)
* [Problémák diagnosztizálása és megoldása](#diagnose-and-solve-problems)
* [Beállítások](#settings)
    * [Elérési kulcs](#access-keys)
    * [Speciális beállítások](#advanced-settings)
    * [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
    * [Méretezés](#scale)
    * [Fürtméret](#cluster-size)
    * [Adatmegőrzés](#redis-data-persistence)
    * [Frissítések ütemezése](#schedule-updates)
    * [Georeplikáció](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Tűzfal](#firewall)
    * [Tulajdonságok](#properties)
    * [Zárolások](#locks)
    * [Automation-szkript](#automation-script)
* Felügyelet
    * [Adatok importálása](#importexport)
    * [Adatok exportálása](#importexport)
    * [Újraindítás](#reboot)
* [Figyelés](#monitoring)
    * [Redis-metrikák](#redis-metrics)
    * [Riasztási szabályok](#alert-rules)
    * [Diagnosztika](#diagnostics)
* Támogatási & hibaelhárítási beállítások
    * [Resource health](#resource-health)
    * [Új támogatási kérelem](#new-support-request)


## <a name="overview"></a>Áttekintés

**Az** Áttekintés alapszintű információkat biztosít a gyorsítótárról, például a nevet, a portokat, a tarifacsomagot és a kiválasztott gyorsítótár-metrikákat.

### <a name="activity-log"></a>Tevékenységnapló

Kattintson **a Tevékenységnapló elemre** a gyorsítótáron végrehajtott műveletek megtekintéséhez. A nézet kibontásához szűrést is használhat, hogy más erőforrásokat is tartalmazni fog. További információ az auditnaplókról: [Naplózási műveletek a Resource Manager.](../azure-resource-manager/management/view-activity-logs.md) Az események figyelésével kapcsolatos Azure Cache for Redis lásd: [Műveletek és riasztások.](cache-how-to-monitor.md#operations-and-alerts)

### <a name="access-control-iam"></a>Hozzáférés-vezérlés (IAM)

A **Hozzáférés-vezérlés (IAM)** szakasz az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) támogatását biztosítja a Azure Portal. Ezzel a konfigurációval a szervezetek egyszerűen és pontosan teljesítik hozzáférés-kezelési követelményeiket. További információ: [Azure szerepköralapú hozzáférés-vezérlés a Azure Portal.](../role-based-access-control/role-assignments-portal.md)

### <a name="tags"></a>Címkék

A **Címkék** szakasz segít az erőforrások rendszerezésében. További információkért lásd: [Az Azure-erőforrások rendszerezése címkék használatával](../azure-resource-manager/management/tag-resources.md) című cikket.


### <a name="diagnose-and-solve-problems"></a>Problémák diagnosztizálása és megoldása

Kattintson **a Problémák diagnosztizálása és megoldása** elemre, hogy meg tudja adni a gyakori problémákat és a megoldásukhoz használt stratégiákat.



## <a name="settings"></a>Beállítások
A **Beállítások** szakasz lehetővé teszi a gyorsítótár következő beállításainak elérését és konfigurálát.

* [Elérési kulcs](#access-keys)
* [Speciális beállítások](#advanced-settings)
* [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
* [Méretezés](#scale)
* [Fürtméret](#cluster-size)
* [Adatmegőrzés](#redis-data-persistence)
* [Frissítések ütemezése](#schedule-updates)
* [Georeplikáció](#geo-replication)
* [Virtual Network](#virtual-network)
* [Tűzfal](#firewall)
* [Tulajdonságok](#properties)
* [Zárolások](#locks)
* [Automation-szkript](#automation-script)



### <a name="access-keys"></a>Elérési kulcs
Kattintson **a Hozzáférési kulcsok elemre** a gyorsítótár hozzáférési kulcsának megtekintéséhez vagy újrageneráláshoz. Ezeket a kulcsokat a gyorsítótárhoz csatlakozó ügyfelek használják.

![Azure Cache for Redis hozzáférési kulcsok](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Speciális beállítások
Az alábbi beállítások a Speciális beállítások **panelen konfigurálhatóak.**

* [Portok elérése](#access-ports)
* [Memória-házirendek](#memory-policies)
* [Kulcstérértesítések (speciális beállítások)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Portok elérése
Alapértelmezés szerint a nem TLS-/SSL-hozzáférés le van tiltva az új gyorsítótárakhoz. A nem TLS-port engedélyezéséhez kattintson a Nem lehetőségre a Hozzáférés engedélyezése csak **SSL-en** keresztül beállításnál a Speciális **beállítások** panelen, majd kattintson a **Mentés gombra.** 

> [!NOTE]
> Az Azure Cache for Redis TLS-hozzáférése jelenleg támogatja a TLS 1.0-t, 1.1-et és 1.2-t, de az 1.0-s és 1.1-es verziók hamarosan ki lesznek vezetve.  További részletekért olvassa el [a TLS 1.0 és 1.1](cache-remove-tls-10-11.md) eltávolítása oldalt.

![Azure Cache for Redis hozzáférési portok](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Memória-házirendek
A  Speciális beállítások panel **maxmemory-reserved**, és **maxfragmentationmemory-reserved** beállításai konfigurálják a gyorsítótár memória-házirendjét. 

![Azure Cache for Redis Maxmemory házirend](./media/cache-configure/redis-cache-maxmemory-policy.png)

**A Maxmemory házirend** konfigurálja a gyorsítótár kiürítés szabályzatát, és lehetővé teszi a következő kiürítés-szabályzatok közül való választást:

* `volatile-lru` – Ez az alapértelmezett kiépítési szabályzat.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

A szabályzatokkal kapcsolatos `maxmemory` további információkért lásd: [Evivalens szabályzatok.](https://redis.io/topics/lru-cache#eviction-policies)

A **maxmemory-reserved** beállítás a fürtben lévő példányonként MB-ban konfigurálja a nem gyorsítótárazott műveletek, például feladatátvétel során történő replikáció számára lefoglalt memória mennyiségét. Ennek az értéknek a beállításával konzisztensebb Redis-kiszolgálói élményt nyújt, ha a terhelés változó. Ezt az értéket magasabbra kell állítani a nagy írási terhelésű számítási feladatokhoz. Ha a memória ilyen műveletekhez van fenntartva, nem érhető el a gyorsítótárazott adatok tárolásához.

A **maxfragmentationmemory-reserved** beállítás konfigurálja a fürtben példányonként MB-ban megadott memóriamennyiséget, amely a memória töredezettségéhez van fenntartva. Ennek az értéknek a beállításával konzisztensebb Redis-kiszolgálói élményt biztosít, amikor a gyorsítótár megtelik vagy majdnem megtelik, és a töredezettség aránya magas. Ha a memória ilyen műveletekhez van fenntartva, nem érhető el a gyorsítótárazott adatok tárolásához.

Az új memóriafoglalási érték **(maxmemory-reserved** vagy **maxfragmentationmemory-reserved)** kiválasztásakor figyelembe kell venni, hogy ez a változás milyen hatással lehet a már nagy mennyiségű adatot tároló gyorsítótárra. Ha például 53 GB-os gyorsítótárral és 49 GB adat áll rendelkezésre, akkor a foglalás értékét 8 GB-ra módosítja, ez a módosítás a rendszer számára rendelkezésre álló maximális memóriát 45 GB-ra csökkeni. Ha az aktuális vagy az ön értékei nagyobbak, mint az új 45 GB-os korlát, akkor a rendszernek ki kell laktatnia az adatokat, amíg mindkettő `used_memory` `used_memory_rss` `used_memory` 45 GB alá nem `used_memory_rss` csökken. A kilakoltatás növelheti a kiszolgáló terhelését és a memória töredezettségét. További információ a gyorsítótár-metrikákról( `used_memory` és ) : Elérhető `used_memory_rss` [metrikák és jelentési időközök.](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)

> [!IMPORTANT]
> A **maxmemory-reserved** és **a maxfragmentationmemory-reserved** beállítások csak Standard és Prémium gyorsítótárakhoz érhetők el.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Kulcstérértesítések (speciális beállítások)
A Redis-kulcstérértesítések a Speciális **beállítások** panelen konfigurálhatóak. A kulcstér-értesítések lehetővé teszik az ügyfelek számára, hogy bizonyos események esetén értesítéseket fogadjanak.

![Azure Cache for Redis speciális beállítások](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> A kulcstérértesítések és **a notify-keyspace-events** beállítás csak standard és prémium szintű gyorsítótárakhoz érhetők el.
>
>

További információ: [Redis Keyspace Notifications](https://redis.io/topics/notifications).. Mintakódért tekintse meg a [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) fájlt a [Hello world mintában.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache for Redis Advisor
A **Azure Cache for Redis Advisor panel a** gyorsítótárra vonatkozó javaslatokat jelenít meg. Normál működés közben nem jelennek meg javaslatok.

![Képernyőkép a javaslatok megjelenítésének helyről.](./media/cache-configure/redis-cache-no-recommendations.png)

Ha a gyorsítótár műveletei során bármilyen feltétel (például magas memóriahasználat, hálózati sávszélesség vagy kiszolgálóterhelés) jelentkezik, a rendszer riasztást jelenít meg a **Azure Cache for Redis** panelen.

![A riasztások megjelenítési helyének képernyőképe a Azure Cache for Redis szakaszban.](./media/cache-configure/redis-cache-recommendations-alert.png)

További információt a Javaslatok **panelen** talál.

![Javaslatok](./media/cache-configure/redis-cache-recommendations.png)

Ezeket a metrikákat [](cache-how-to-monitor.md#monitoring-charts) a panel [](cache-how-to-monitor.md#usage-charts) Figyelési diagramok és Használati **diagramok Azure Cache for Redis** figyelheti.

Minden tarifacsomag eltérő korlátokkal rendelkezik az ügyfélkapcsolatok, a memória és a sávszélesség esetében. Ha a gyorsítótár hosszabb idő alatt eléri ezeknek a metrikáknak a maximális kapacitását, a rendszer javaslatot hoz létre. A Javaslatok eszköz által áttekintett metrikákról és korlátokról az alábbi táblázatban található további információ: 

| Azure Cache for Redis metrika | További információ |
| --- | --- |
| Hálózatisávszélesség-felhasználás |[Gyorsítótár teljesítménye – rendelkezésre álló sávszélesség](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Csatlakoztatott ügyfelek |[A Redis-kiszolgáló alapértelmezett konfigurációja – maxclients](#maxclients) |
| Kiszolgálóterhelés |[Használati diagramok – Redis Server Load](cache-how-to-monitor.md#usage-charts) |
| Memóriahasználat |[Gyorsítótár teljesítménye – méret](cache-planning-faq.md#azure-cache-for-redis-performance) |

A gyorsítótár frissítését az Upgrade **now** (Frissítés most) gombra kattintva módosíthatja a tarifacsomagot, és [skálázhat](#scale) egy gyorsítótárat. További információ a tarifacsomag kiválasztásáról: [A megfelelő szint kiválasztása](cache-overview.md#choosing-the-right-tier)


### <a name="scale"></a>Méretezés
Kattintson **a Skálázás** elemre a gyorsítótár tarifacsomag megtekintéséhez vagy szerkesztéshez. További információ a méretezésről: [Skálázás](cache-how-to-scale.md)Azure Cache for Redis.

![Azure Cache for Redis tarifacsomag](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis-fürt mérete
Kattintson **a Fürtméret elemre** egy olyan futó prémium szintű gyorsítótár fürtméretének beállítására, amely számára engedélyezve van a fürtözés.

![Fürtméret](./media/cache-configure/redis-cache-redis-cluster-size.png)

A fürt méretének beállításhoz használja a csúszkát, vagy írjon be egy 1 és 10 közötti számot a **Szegmensek** száma szövegmezőbe, majd kattintson az **OK** gombra a mentéshez.

> [!IMPORTANT]
> A Redis-fürtszolgáltatás csak prémium szintű gyorsítótárakhoz érhető el. További információ: [Fürtözés konfigurálása](cache-how-to-premium-clustering.md)prémium szintű Azure Cache for Redis.
>
>


### <a name="redis-data-persistence"></a>Redis-adatmegőrzés
Kattintson **az Adatmegőrzés elemre** a prémium szintű gyorsítótár adatmegőrzésének engedélyezéséhez, letiltásához vagy konfigurálásához. Azure Cache for Redis RDB-adatmegőrzést vagy AOF-megőrzést használó Redis-adatmegőrzést kínál.

További információ: [Adatmegőrzés konfigurálása](cache-how-to-premium-persistence.md)prémium szintű Azure Cache for Redis.


> [!IMPORTANT]
> A Redis-adatmegőrzés csak prémium szintű gyorsítótárakhoz érhető el.
>
>

### <a name="schedule-updates"></a>Frissítések ütemezése
A **Frissítések ütemezése** panelen kijelölheti a Redis-kiszolgáló frissítésére vonatkozó karbantartási időszakokat a gyorsítótárhoz.

> [!IMPORTANT]
> A karbantartási időszak csak a Redis-kiszolgáló frissítésére vonatkozik, a gyorsítótárat üzemeltető virtuális gépek operációs rendszerének Azure-frissítésére és frissítésére nem.
>
>

![Frissítések ütemezése](./media/cache-configure/redis-schedule-updates.png)

Karbantartási időszak megadásához ellenőrizze a kívánt napokat, adja meg a karbantartási időszak kezdő óráját minden naphoz, majd kattintson az **OK gombra.** A karbantartási időszak utc időzónában van megadva.

További információ és útmutatás: Azure Cache for Redis [frissítései ütemezése](cache-administration.md#schedule-updates)

### <a name="geo-replication"></a>Georeplikáció

A **Georeplikáció** panelen két prémium szintű réteget kapcsolunk össze Azure Cache for Redis példányokkal. Az egyik gyorsítótár lesz az elsődleges, a másik pedig a másodlagos csatolt gyorsítótár. A másodlagos csatolt gyorsítótár csak olvashatóvá válik, és az elsődleges gyorsítótárba írt adatok replikálódnak a másodlagos csatolt gyorsítótárba. Ezzel a funkcióval egy gyorsítótár replikálható az Azure-régiók között.

> [!IMPORTANT]
> **A georeplikáció** csak prémium szintű gyorsítótárakhoz érhető el. További információkért és utasításokért lásd: [Georeplikáció](cache-how-to-geo-replication.md)konfigurálása a Azure Cache for Redis.
>
>

### <a name="virtual-network"></a>Virtual Network
A **Virtual Network** szakaszban konfigurálhatja a gyorsítótár virtuális hálózati beállításait. A prémium szintű gyorsítótár VNET-támogatással való létrehozásával és a beállításainak frissítésével kapcsolatos információkért lásd: How [to configure Virtual Network Support for a Premium Azure Cache for Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> A virtuális hálózati beállítások csak olyan prémium szintű gyorsítótárakhoz érhetők el, amelyek a gyorsítótár létrehozása során VNET-támogatással voltak konfigurálva.
>
>

### <a name="firewall"></a>Firewall

A tűzfalszabályok konfigurálása az összes Azure Cache for Redis elérhető.

Kattintson **a Tűzfal elemre** a gyorsítótár tűzfalszabályainak megtekintéséhez és konfiguráláshoz.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

A tűzfalszabályokat kezdő és záró IP-címtartománysal is megadhatja. Tűzfalszabályok konfigurálásakor csak a megadott IP-címtartományból származó ügyfélkapcsolatok csatlakozhatnak a gyorsítótárhoz. Tűzfalszabály mentésekor a szabály csak kis késéssel lesz eredményes. Ez a késés általában kevesebb, mint egy perc.

> [!IMPORTANT]
> A hálózati Azure Cache for Redis kapcsolatai mindig engedélyezettek, még akkor is, ha tűzfalszabályok vannak konfigurálva.
>
>

### <a name="properties"></a>Tulajdonságok
Kattintson **a Tulajdonságok** elemre a gyorsítótár információinak megtekintéséhez, beleértve a gyorsítótár végpontját és portját.

![Azure Cache for Redis tulajdonságai](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Zárolások
A **Zárolások** szakaszban zárolhat egy előfizetést, erőforráscsoportot vagy erőforrást, nehogy a szervezet más felhasználói véletlenül törölzék vagy módosítsák a kritikus fontosságú erőforrásokat. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automation-szkript

Kattintson **az Automation-szkript** elemre az üzembe helyezett erőforrások sablonjának a jövőbeli üzembe helyezéshez történő felépítéséhez és exportálásához. A sablonok használatával kapcsolatos további információkért lásd: [Erőforrások üzembe helyezése Azure Resource Manager sablonokkal.](../azure-resource-manager/templates/deploy-powershell.md)

## <a name="administration-settings"></a>Adminisztrációs beállítások
Az Adminisztráció szakaszban **található** beállítások lehetővé teszik a gyorsítótár következő felügyeleti feladatainak elvégzését.

![Felügyelet](./media/cache-configure/redis-cache-administration.png)

* [Adatok importálása](#importexport)
* [Adatok exportálása](#importexport)
* [Újraindítás](#reboot)


### <a name="importexport"></a>Import/Export
Az Import/Export egy Azure Cache for Redis adatkezelési művelet, amely lehetővé teszi az adatok importálását és exportálását a gyorsítótárba egy prémium gyorsítótárból származó Azure Cache for Redis Database- (RDB-) pillanatkép importálásával és exportálásával egy Lapblobba egy Azure Storage-fiókban. Az Import/Export lehetővé teszi a különböző Azure Cache for Redis példányok közötti áttelepítést, vagy használat előtt feltöltheti a gyorsítótárat adatokkal.

Az importálás segítségével Bármilyen felhőben vagy környezetben futó Redis-kiszolgálóról át lehet vinni a Redis-kompatibilis RDB-fájlokat, beleértve a Linuxon, Windowson futó Redist, vagy bármely felhőszolgáltatót, például a Amazon Web Services-t és másokat. Az adatok importálásával egyszerűen hozhat létre gyorsítótárat előre feltöltett adatokkal. Az importálási folyamat során a Azure Cache for Redis az RDB-fájlokat az Azure-tárolóból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

Az exportálással redis-kompatibilis RDB-fájlokba exportálhatja a Azure Cache for Redis tárolt adatokat. Ezzel a funkcióval adatokat mozgathat egy Azure Cache for Redis-példányról egy másikra vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során a rendszer létrehoz egy ideiglenes fájlt a Azure Cache for Redis-kiszolgálópéldányt tároló virtuális gépen, és feltölti a fájlt a kijelölt tárfiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződik be, az ideiglenes fájl törlődik.

> [!IMPORTANT]
> Az Import/Export csak prémium szintű gyorsítótárakhoz érhető el. További információkért és utasításokért lásd: Adatok importálása [és exportálása a Azure Cache for Redis.](cache-how-to-import-export-data.md)
>
>

### <a name="reboot"></a>Újraindítás
Az **Újraindítás** panelen újraindíthatja a gyorsítótár csomópontjait. Ez az újraindítási képesség lehetővé teszi, hogy tesztelje az alkalmazás rugalmasságát egy gyorsítótár-csomópont meghibásodása esetén.

![Újraindítás](./media/cache-configure/redis-cache-reboot.png)

Ha prémium szintű gyorsítótára van, amelyen engedélyezve van a fürtözés, kiválaszthatja, hogy a gyorsítótár mely szegmensei újraindulnak.

![Képernyőkép arról, hogy hol válassza ki, hogy a gyorsítótár mely szegmensei indítsanak újra.](./media/cache-configure/redis-cache-reboot-cluster.png)

A gyorsítótár egy vagy több csomópontja újraindításhoz válassza ki a kívánt csomópontokat, majd kattintson az **Újraindítás elemre.** Ha prémium szintű gyorsítótára van, és engedélyezve van a fürtözés, válassza ki az újraindítani kívánt szegmens(öke)t, majd kattintson az **Újraindítás elemre.** Néhány perc múlva a kiválasztott csomópont(ak) újraindul, és néhány perccel később újra elérhető lesz.

> [!IMPORTANT]
> Az újraindítás mostantól minden tarifacsomaghoz elérhető. További információkért és utasításokért lásd: [Azure Cache for Redis felügyelete – Újraindítás.](cache-administration.md#reboot)
>
>


## <a name="monitoring"></a>Figyelés

A **Figyelés** szakaszban konfigurálhatja a diagnosztikai és monitorozási adatokat a Azure Cache for Redis.
További információ a monitorozásról és Azure Cache for Redis diagnosztikákról: [A monitorozás Azure Cache for Redis.](cache-how-to-monitor.md)

![Diagnosztika](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis-metrikák](#redis-metrics)
* [Riasztási szabályok](#alert-rules)
* [Diagnosztika](#diagnostics)

### <a name="redis-metrics"></a>Redis-metrikák
Kattintson **a Redis-metrikák elemre** a gyorsítótár [metrikák](cache-how-to-monitor.md#view-cache-metrics) megtekintéséhez.

### <a name="alert-rules"></a>Riasztási szabályok

Kattintson **a Riasztási szabályok** elemre a riasztások metrikák alapján Azure Cache for Redis konfigurálás érdekében. További információ: [Riasztások.](cache-how-to-monitor.md#alerts)

### <a name="diagnostics"></a>Diagnosztika

Alapértelmezés szerint a gyorsítótár-metrikák Azure Monitor [30](../azure-monitor/essentials/data-platform-metrics.md) napig vannak tárolva, majd törlődnek. A gyorsítótár-metrikák 30 napnál  hosszabb ideig [](cache-how-to-monitor.md#export-cache-metrics) való megőrzéséhez kattintson a Diagnosztika elemre a gyorsítótár-diagnosztika tárolásához használt tárfiók konfigurálásához.

>[!NOTE]
>A gyorsítótár-metrikák tárolóba való archiválásán kívül egy eseményközpontba is streamelheti őket, vagy elküldheti őket az [Azure Monitor naplókba.](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)
>
>

## <a name="support--troubleshooting-settings"></a>Támogatási & hibaelhárítási beállítások
A Támogatás **és** hibaelhárítás szakaszban található beállítások lehetőséget biztosítanak a gyorsítótárral kapcsolatos problémák megoldására.

![Támogatás + hibaelhárítás](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Resource health](#resource-health)
* [Új támogatási kérelem](#new-support-request)

### <a name="resource-health"></a>Erőforrás állapota
**A Resource Health** figyeli az erőforrást, és közli, hogy a várt módon fut-e. További információ az Azure Resource Health szolgáltatásról: [Az Azure Resource Health áttekintése.](../service-health/resource-health-overview.md)

> [!NOTE]
> Az erőforrás-állapot jelenleg nem tud jelentést Azure Cache for Redis virtuális hálózaton üzemeltetett virtuális gépek állapotáról. További információkért lásd: [Működik az összes gyorsítótár-funkció, ha virtuális](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network) hálózatban tetszetnek a gyorsítótárak?
>
>

### <a name="new-support-request"></a>Új támogatási kérelem
Kattintson **az Új támogatási kérelem** elemre a gyorsítótárra vonatkozó támogatási kérelem megnyitásához.





## <a name="default-redis-server-configuration"></a>A Redis-kiszolgáló alapértelmezett konfigurációja
Az Azure Cache for Redis-példányok az alábbi alapértelmezett Redis-konfigurációs értékekkel vannak konfigurálva:

> [!NOTE]
> Az ebben a szakaszban található beállítások nem módosíthatók a `StackExchange.Redis.IServer.ConfigSet` metódussal. Ha ezt a metódust a szakasz egyik parancsával hívjuk meg, a következő példához hasonló kivételt ad vissza:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Minden konfigurálható érték, például a **max-memory-policy,** Azure Portal vagy parancssori felügyeleti eszközökkel, például az Azure CLI-val vagy a PowerShell-rel konfigurálható.
>
>

| Beállítás | Alapértelmezett érték | Description |
| --- | --- | --- |
| `databases` |16 |Az adatbázisok alapértelmezett száma 16, de a tarifacsomag alapján más számot is konfigurálhat. <sup>1</sup> Az alapértelmezett adatbázis a DB 0, kapcsolatonként egy másik adatbázist is kiválaszthat a és a közötti szám `connection.GetDatabase(dbid)` `dbid` `0` `databases - 1` használatával. |
| `maxclients` |A<sup>2. tarifacsomagtól függ</sup> |Ez az érték a csatlakoztatott ügyfelek egyidejűleg engedélyezett maximális száma. A korlát elérése után a Redis bezárja az összes új kapcsolatot, és egy "elérte az ügyfelek maximális számát" hibaüzenetet adja vissza. |
| `maxmemory-policy` |`volatile-lru` |A Maxmemory szabályzat azt a beállítást adja meg, hogy a Redis hogyan válassza ki az eltávolítani kívánt adatokat (a gyorsítótár létrehozásakor kiválasztott gyorsítótár-ajánlat `maxmemory` mérete) elérésekor. A Azure Cache for Redis alapértelmezett beállítás a , amely LRU-algoritmus használatával eltávolítja a lejárati értékekkel `volatile-lru` rendelkező kulcsokat. Ez a beállítás a következő Azure Portal. További információ: [Memória-házirendek.](#memory-policies) |
| `maxmemory-samples` |3 |A memória megtakarítása érdekében az LRU és a minimális TTL-algoritmusok pontos algoritmusok helyett hozzávetőleges algoritmusok. Alapértelmezés szerint a Redis három kulcsot ellenőriz, és kiválasztja azt, amelyet ritkábban használtak. |
| `lua-time-limit` |5000 |Lua-szkript maximális végrehajtási ideje ezredmásodpercben. Ha eléri a maximális végrehajtási időt, a Redis naplózza, hogy egy szkript a maximálisan engedélyezett idő után is végrehajtás alatt áll, és hibával válaszol a lekérdezésekre. |
| `lua-event-limit` |500 |A szkriptek esemény-üzenetsorának maximális mérete. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Az ügyfél kimeneti pufferének korlátaival kényszerítheti az olyan ügyfelek leválasztott kapcsolatát, amelyek valamilyen okból nem olvasnak elég gyorsan adatokat a kiszolgálóról (gyakori ok, hogy a Pub/Sub-ügyfelek nem tudnak olyan gyorsan üzeneteket használni, amilyen gyorsan a közzétevő elő tudja őket készíteni). További információ: [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup> A korlátja minden Azure Cache for Redis tarifacsomag esetében eltérő, és a gyorsítótár `databases` létrehozásakor lehet beállítani. Ha nincs `databases` megadva beállítás a gyorsítótár létrehozása során, az alapértelmezett érték 16.

* Alapszintű és Standard gyorsítótárak
  * C0 (250 MB) gyorsítótár – legfeljebb 16 adatbázis
  * C1 (1 GB) gyorsítótár – legfeljebb 16 adatbázis
  * C2 (2,5 GB) gyorsítótár – legfeljebb 16 adatbázis
  * C3 (6 GB) gyorsítótár – legfeljebb 16 adatbázis
  * C4 (13 GB) gyorsítótár – legfeljebb 32 adatbázis
  * C5 (26 GB) gyorsítótár – legfeljebb 48 adatbázis
  * C6 (53 GB) gyorsítótár – legfeljebb 64 adatbázis
* Prémium szintű gyorsítótárak
  * P1 (6 GB – 60 GB) – legfeljebb 16 adatbázis
  * P2 (13 GB – 130 GB) – legfeljebb 32 adatbázis
  * P3 (26 GB – 260 GB) – legfeljebb 48 adatbázis
  * P4 (53 GB – 530 GB) – legfeljebb 64 adatbázis
  * Minden prémium szintű gyorsítótár, amelyeken engedélyezve van a Redis-fürt – A Redis-fürt csak a 0. adatbázis használatát támogatja, így a Redis-fürtöt engedélyező prémium szintű gyorsítótárak korlátja gyakorlatilag 1, a Select parancs `databases` pedig nem engedélyezett. [](https://redis.io/commands/select) További információ: Módosítanom kell az ügyfélalkalmazásomat a [fürtözéshez?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Az adatbázisokkal kapcsolatos további információkért lásd: Mik azok a [Redis-adatbázisok?](cache-development-faq.md#what-are-redis-databases)

> [!NOTE]
> A beállítás csak a gyorsítótár létrehozása során konfigurálható, és csak a PowerShell, a parancssori felület vagy más felügyeleti `databases` ügyfelek használatával. Példa a gyorsítótár PowerShell használatával való létrehozása során való konfigurálásra: `databases` [New-AzRedisCache.](cache-how-to-manage-redis-cache-powershell.md#databases)
>
>

<a name="maxclients"></a>
<sup>A 2</sup> `maxclients` eltérő az egyes Azure Cache for Redis tarifacsomagok esetében.

* Alapszintű és Standard gyorsítótárak
  * C0 (250 MB) gyorsítótár – legfeljebb 256 kapcsolat
  * C1 (1 GB) gyorsítótár – legfeljebb 1000 kapcsolat
  * C2 (2,5 GB) gyorsítótár – legfeljebb 2000 kapcsolat
  * C3 (6 GB) gyorsítótár – legfeljebb 5000 kapcsolat
  * C4 (13 GB) gyorsítótár – legfeljebb 10 000 kapcsolat
  * C5 (26 GB) gyorsítótár – legfeljebb 15 000 kapcsolat
  * C6 (53 GB) gyorsítótár – legfeljebb 20 000 kapcsolat
* Prémium szintű gyorsítótárak
  * P1 (6 GB – 60 GB) – legfeljebb 7500 kapcsolat
  * P2 (13 GB – 130 GB) – legfeljebb 15 000 kapcsolat
  * P3 (26 GB – 260 GB) – legfeljebb 30 000 kapcsolat
  * P4 (53 GB – 530 GB) – legfeljebb 40 000 kapcsolat

> [!NOTE]
> Bár a gyorsítótár minden mérete *egy* adott számú kapcsolatot tesz lehetővé, a Redishez való csatlakozások többletterheléssel is jár. Ilyen többletterhelés lehet például a CPU- és memóriahasználat a TLS/SSL-titkosítás eredményeként. Egy adott gyorsítótár méretének maximális csatlakozási korlátja egy kis terhelésű gyorsítótárat feltételez. Ha a kapcsolat  többletterhelése és az ügyfélműveletekkel kapcsolatos terhelés meghaladja a rendszer kapacitását, a gyorsítótár kapacitási problémákat tapasztalhat, még akkor is, ha nem lépte túl az aktuális gyorsítótárméret kapcsolati korlátját.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>A Redis-parancsok nem támogatottak a Azure Cache for Redis
> [!IMPORTANT]
> Mivel a Azure Cache for Redis példányok konfigurációját és felügyeletét a Microsoft kezeli, az alábbi parancsok le vannak tiltva. Ha megpróbálja meghívni őket, a következő hez hasonló hibaüzenet jelenik meg: `"(error) ERR unknown command"` .
>
> * BGREWRITEAOF
> * BGSAVE
> * Config
> * HIBAKERESÉS
> * Áttelepítése
> * MENTÉS
> * Shutdown
> * SLAVEOF
> * FÜRT – A fürtírási parancsok le vannak tiltva, de a csak olvasható fürtparancsok engedélyezettek.
>
>

A Redis-parancsokkal kapcsolatos további információkért lásd: [https://redis.io/commands](https://redis.io/commands) .

## <a name="redis-console"></a>Redis-konzol
A **Redis-konzollal** biztonságosan kiadhat parancsokat a Azure Cache for Redis-példánynak, amely minden gyorsítótárszinthez elérhető Azure Portal-ban.

> [!IMPORTANT]
> - A Redis-konzol nem működik a [virtuális hálózatával.](cache-how-to-premium-vnet.md) Ha a gyorsítótár egy virtuális hálózat része, csak a virtuális hálózat ügyfelei férhetnek hozzá a gyorsítótárhoz. Mivel a Redis-konzol a VNET-en kívüli helyi böngészőben fut, nem tud csatlakozni a gyorsítótárhoz.
> - Nem minden Redis-parancs támogatott a Azure Cache for Redis. Azon Redis-parancsok listájáért, amelyek le vannak tiltva a Azure Cache for Redis, tekintse meg a korábbi Redis-parancsokat, amelyek nem [támogatottak Azure Cache for Redis](#redis-commands-not-supported-in-azure-cache-for-redis) szakaszban. A Redis-parancsokkal kapcsolatos további információkért lásd: [https://redis.io/commands](https://redis.io/commands) .
>
>

A Redis-konzol eléréséhez kattintson a **Konzol** elemre a **Azure Cache for Redis** panelen.

![A Konzol gombot kikapcsoló képernyőkép.](./media/cache-configure/redis-console-menu.png)

Ha parancsokat kíván kiadni a gyorsítótárpéldányon, írja be a kívánt parancsot a konzolba.

![A Thas képernyőképe a Redis-konzolt mutatja a bemeneti paranccsal és az eredményekkel.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>A Redis-konzol használata prémium fürtözött gyorsítótárral

Ha a Redis-konzolt prémium fürtözött gyorsítótárral használja, a gyorsítótár egyetlen szegmenséhez is ki tud parancsokat ki kiadásra. Ha parancsot szeretne kidedni egy adott szegmensnek, először csatlakozzon a kívánt szegmenshez a szegmensválasztóra kattintva.

![Redis-konzol](./media/cache-configure/redis-console-premium-cluster.png)

Ha a csatlakoztatott szegmenstől eltérő szegmensben tárolt kulcshoz próbál hozzáférni, az alábbihoz hasonló hibaüzenet jelenik meg:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

Az előző példában a kiválasztott szegmens az 1. szegmens, de a 0. szegmensben található, ahogyan azt a hibaüzenet `myKey` `(shard 0)` része jelzi. Ebben a példában a eléréséhez válassza a 0. szegmenst a szegmensválasztóval, majd adja ki a `myKey` kívánt parancsot.


## <a name="move-your-cache-to-a-new-subscription"></a>A gyorsítótár áthelyezése új előfizetésbe
A gyorsítótárat az Áthelyezés gombra kattintva áthelyezheti egy új **előfizetésbe.**

![Áthelyezés Azure Cache for Redis](./media/cache-configure/redis-cache-move.png)

Az erőforrások egyik erőforráscsoportból egy másikba való áthelyezésével és az egyik előfizetésből a másikba való áthelyezésével kapcsolatos információkért lásd: Erőforrások áthelyezése új [erőforráscsoportba vagy előfizetésbe.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="next-steps"></a>Következő lépések
* A Redis-parancsok használatával kapcsolatos további információkért lásd: [Redis-parancsok futtatása](cache-development-faq.md#how-can-i-run-redis-commands)
