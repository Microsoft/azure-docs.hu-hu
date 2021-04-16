---
title: Georeplikáció konfigurálása prémium szintű Azure Cache for Redis példányokhoz
description: Megtudhatja, hogyan replikálhatja Azure Cache for Redis Premium-példányait az Azure-régiók között
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 0be2bb59b46dc827001d89f8e0f1be23f35a714d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536097"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Georeplikáció konfigurálása Prémium Azure Cache for Redis példányokhoz

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a georeplikált Azure Cache-t a Azure Portal.

A georeplikáció összeköt két prémium Azure Cache for Redis példányt, és létrehoz egy adatreplikációs kapcsolatot. Ezek a gyorsítótárpéldányok általában különböző Azure-régiókban találhatók, bár nem kötelező. Az egyik példány elsődlegesként, a másik másodlagosként működik. Az elsődleges kezeli az olvasási és írási kéréseket, és propagálja a módosításokat a másodlagos példányra. Ez a folyamat addig folytatódik, amíg el nem távolítja a két példány közötti kapcsolatot.

> [!NOTE]
> A georeplikáció vészhelyreállítási megoldásként lett kialakítva.
>
>

## <a name="geo-replication-prerequisites"></a>Georeplikáció előfeltételei

A két gyorsítótár közötti georeplikáció konfigurálásának előfeltételei a következők:

- Mindkét gyorsítótár prémium [szintű gyorsítótár.](cache-overview.md#service-tiers)
- Mindkét gyorsítótár ugyanabban az Azure-előfizetésben található.
- A másodlagos csatolt gyorsítótár mérete vagy azonos, vagy nagyobb, mint az elsődleges csatolt gyorsítótár.
- Mindkét gyorsítótár fut, és fut.

Egyes funkciók nem támogatottak a georeplikációval:

- A georeplikáció nem támogatja az adatmegőrzést.
- A fürtözés akkor támogatott, ha mindkét gyorsítótáron engedélyezve van a fürtözés, és azonos számú szegmens van.
- Az azonos virtuális hálózatban lévő gyorsítótárak támogatottak.
- A különböző VNET-ek gyorsítótárai a kikötésekkel támogatottak. További [információ: Használhatok georeplikációt a](#can-i-use-geo-replication-with-my-caches-in-a-vnet) gyorsítótáraimat virtuális hálózatban? című fejezetben.

A georeplikáció konfigurálása után a csatolt gyorsítótárpárra a következő korlátozások vonatkoznak:

- A másodlagos csatolt gyorsítótár csak olvasható; Olvashat róla, de nem írhat rá adatokat. Ha úgy dönt, hogy beolvassa a Geo-Secondary-példányt, fontos megjegyezni, hogy amikor teljes adatszinkronizálás történik az Geo-Primary és az Geo-Secondary között (akkor történik, amikor Geo-Primary vagy az Geo-Secondary is frissül, és bizonyos újraindítási forgatókönyvek esetében is), az Geo-Secondary-példány a redis összes műveletének befejezéséig el fogja dobni a Geo-Secondary-példányokat Geo-Secondary Geo-Primary (amelyek szerint teljes adatszinkronizálás van folyamatban). Az alkalmazásból Geo-Seocndary alkalmazásokat úgy kell összeépítenünk, hogy visszatérnek a Geo-Primary, amikor a Geo-Seocndary ilyen hibákat dobnak. 
- A rendszer eltávolít minden olyan adatot, amely a másodlagos csatolt gyorsítótárban volt a hivatkozás hozzáadása előtt. Ha azonban a georeplikációt később eltávolítják, a replikált adatok a másodlagos csatolt gyorsítótárban maradnak.
- Egyik gyorsítótár sem [skálázható,](cache-how-to-scale.md) amíg a gyorsítótárak össze vannak kapcsolva.
- Ha a [gyorsítótárban engedélyezve](cache-how-to-premium-clustering.md) van a fürtözés, nem módosíthatja a szegmensek számát.
- Egyik gyorsítótárban sem engedélyezhető az adatmegőrzés.
- Bármelyik [gyorsítótárból](cache-how-to-import-export-data.md#export) exportálhat.
- A másodlagos [csatolt](cache-how-to-import-export-data.md#import) gyorsítótárba nem lehet importálni.
- Sem a csatolt gyorsítótárat, sem az azokat tartalmazó erőforráscsoportot nem törölheti, amíg le nem kapcsolta a gyorsítótárat. További információ: [Miért nem sikerült a művelet,](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache) amikor megpróbáltam törölni a csatolt gyorsítótárat?
- Ha a gyorsítótárak különböző régiókban vannak, a hálózati forgalom költségei a régiók között áthelyezve lévő adatokra vonatkoznak. További információ: Mennyibe kerül az adataim replikálása [az Azure-régiók között?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Az automatikus feladatátvétel nem történik meg az elsődleges és a másodlagos csatolt gyorsítótár között. További információ és információ az ügyfélalkalmazások feladatátvételről: Hogyan működik a másodlagos csatolt gyorsítótárba való [feladatátvétel?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Georeplikációs hivatkozás hozzáadása

1. Ha két gyorsítótárat kíván összekapcsolni a  georeplikációhoz, kattintson a Georeplikáció elemre annak a gyorsítótárnak az Erőforrás menüjében, amelyből az elsődleges hivatkozott gyorsítótárat szeretné használni. Ezután kattintson a **Georeplikáció** panel **gyorsítótár-replikációs hivatkozásának hozzáadása parancsára.**

    ![Hivatkozás hozzáadása](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kattintson a kívánt másodlagos gyorsítótár nevére a **Compatible caches (Kompatibilis gyorsítótárak) listában.** Ha a másodlagos gyorsítótár nem jelenik meg a listában, ellenőrizze, hogy [teljesülnek-e](#geo-replication-prerequisites) a másodlagos gyorsítótár georeplikációs előfeltételei. A gyorsítótárak régiónkénti szűréséhez kattintson a térképen a régióra, hogy csak ezek a gyorsítótárak jelennek meg a **Kompatibilis gyorsítótárak listában.**

    ![Georeplikációval kompatibilis gyorsítótárak](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Elindíthatja a csatolási folyamatot, vagy megtekintheti a másodlagos gyorsítótár részleteit a helyi menü használatával.

    ![Georeplikáció helyi menü](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kattintson **a Hivatkozás** gombra a két gyorsítótár csatolásához és a replikációs folyamat megkezdéséhez.

    ![Csatolási gyorsítótárak](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. A replikációs folyamat előrehaladását a Georeplikáció **panelen tudja** megtekinteni.

    ![Csatolás állapota](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    A hivatkozás állapotát az elsődleges  és a másodlagos gyorsítótár Áttekintés paneljének hivatkozási állapotát is megtekintheti.

    ![Képernyőkép az elsődleges és másodlagos gyorsítótárak csatolási állapotának megtekintéséről.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Ha a replikációs folyamat befejeződött, **a Hivatkozás állapota** Sikeres **állapotúra változik.**

    ![Gyorsítótár állapota](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Az elsődleges csatolt gyorsítótár továbbra is használható marad a csatolási folyamat során. A másodlagos csatolt gyorsítótár nem érhető el, amíg a csatolási folyamat be nem fejeződik.

## <a name="remove-a-geo-replication-link"></a>Georeplikációs hivatkozás eltávolítása

1. A két gyorsítótár közötti kapcsolat eltávolításához és  a georeplikáció leállításához kattintson a Gyorsítótárak leválasztása elemre a **Georeplikáció panelen.**
    
    ![Gyorsítótárak leválasztása](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Ha a leválasztási folyamat befejeződött, a másodlagos gyorsítótár olvasási és írási művelethez is elérhető.

>[!NOTE]
>A georeplikációs hivatkozás eltávolításakor az elsődleges csatolt gyorsítótárból származó replikált adatok a másodlagos gyorsítótárban maradnak.
>
>

## <a name="geo-replication-faq"></a>Georeplikáció – gyakori kérdések

- [Használhatok georeplikációt standard vagy alapszintű gyorsítótárral?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Használható a gyorsítótáram a csatolási vagy leválasztási folyamat során?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Összekapcsolhat kettőnél több gyorsítótárat?](#can-i-link-more-than-two-caches-together)
- [Összekapcsolok két gyorsítótárat különböző Azure-előfizetésből?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Összekapcsolok két különböző méretű gyorsítótárat?](#can-i-link-two-caches-with-different-sizes)
- [Használhatok georeplikációt engedélyezett fürtözéssel?](#can-i-use-geo-replication-with-clustering-enabled)
- [Használhatok georeplikációt a virtuális hálózatban lévő gyorsítótáraimat használva?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Mi a Redis georeplikáció replikációs ütemezése?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Mennyi ideig tart a georeplikáció replikálása?](#how-long-does-geo-replication-replication-take)
- [Garantált a replikációs helyreállítási pont?](#is-the-replication-recovery-point-guaranteed)
- [Használhatom a PowerShellt vagy az Azure CLI-t a georeplikáció kezelésére?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Mennyibe kerül az adataim azure-régiók közötti replikálása?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Miért volt sikertelen a művelet, amikor megpróbáltam törölni a csatolt gyorsítótárat?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Melyik régiót használjam a másodlagos csatolt gyorsítótárhoz?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hogyan működik a másodlagos csatolt gyorsítótárba való adat-visszahelyezett kapcsolat?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Konfigurálható a tűzfal georeplikációval?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Használhatok georeplikációt standard vagy alapszintű gyorsítótárral?

Nem, a georeplikáció csak a prémium szintű gyorsítótárak esetében érhető el.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Használható a gyorsítótáram a csatolási vagy leválasztási folyamat során?

- Összekapcsoláskor az elsődleges csatolt gyorsítótár elérhető marad, amíg a csatolási folyamat befejeződik.
- Összekapcsoláskor a másodlagos csatolt gyorsítótár nem érhető el, amíg a csatolási folyamat be nem fejeződik.
- A leválasztáskor mindkét gyorsítótár elérhető marad, amíg a leválasztási folyamat befejeződik.

### <a name="can-i-link-more-than-two-caches-together"></a>Összekapcsolhat kettőnél több gyorsítótárat?

Nem, csak két gyorsítótárat kapcsolhat össze.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Összekapcsolok két gyorsítótárat különböző Azure-előfizetésből?

Nem, mindkét gyorsítótárnak ugyanabban az Azure-előfizetésben kell lennie.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Összekapcsolok két különböző méretű gyorsítótárat?

Igen, ha a másodlagos csatolt gyorsítótár nagyobb, mint az elsődleges csatolt gyorsítótár.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Használhatok georeplikációt engedélyezett fürtözéssel?

Igen, ha mindkét gyorsítótárban azonos számú szegmens van.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Használhatok georeplikációt a virtuális hálózat gyorsítótáraival?

Igen, a virtuális gépek gyorsítótárai georeplikációja a következő kikötésekkel támogatott:

- Az azonos virtuális hálózatban lévő gyorsítótárak közötti georeplikáció támogatott.
- A különböző virtuális gépek gyorsítótárai közötti georeplikáció is támogatott.
  - Ha a virtuális hálózatok ugyanabban a régióban vannak, [](../virtual-network/virtual-network-peering-overview.md) csatlakoztathatja őket virtuális hálózatok közötti társviszony-létesítés vagy virtuális hálózatok [közötti VPN Gateway kapcsolattal.](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
  - Ha a virtuális hálózatok különböző régiókban vannak, a virtuális hálózatok közötti társviszony-létesítés használatával történő georeplikáció támogatott, de az 1. virtuális hálózatban (1. régió) lévő ügyfél virtuális gép nem fog tudni hozzáférni a 2. virtuális hálózat gyorsítótárához (2. régió) annak DNS-nevén keresztül, mert az alapszintű belső terheléselosztási eszközökre vonatkozó korlátozás van behatárolva. A virtuális hálózatok közötti társviszony korlátozásokkal kapcsolatos további információkért [lásd: Virtual Network – Társviszony – Követelmények és megkötések.](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Az ajánlott megoldás egy virtuális hálózatok VPN Gateway virtuális hálózatok között kapcsolat használata.
  
Ezzel [az Azure-sablonnal](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)gyorsan üzembe helyezhet két georeplikált gyorsítótárat egy virtuális hálózatok és virtuális hálózatok VPN Gateway csatlakoztatott virtuális hálózatban.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Mi a Redis georeplikáció replikációs ütemezése?

A replikáció folyamatos és aszinkron, és nem egy adott ütemezés szerint történik. Az elsődleges példányon végzett összes írás azonnal és aszinkron módon replikálva van a másodlagoson.

### <a name="how-long-does-geo-replication-replication-take"></a>Mennyi ideig tart a georeplikáció replikációja?

A replikáció növekményes, aszinkron és folyamatos, és a szükséges idő nem sokban különbözik a régiók közötti késéstől. Bizonyos körülmények között szükség lehet a másodlagos gyorsítótárra az elsődleges adatbázis adatainak teljes szinkronizálásához. Ebben az esetben a replikáció ideje a következő tényezők számán függ: az elsődleges gyorsítótár terhelése, a rendelkezésre álló hálózati sávszélesség és a régiók közötti késés. Azt találtuk, hogy egy teljes 53 GB-os georeplikált pár replikációs ideje 5 és 10 perc között lehet.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Garantált a replikációs helyreállítási pont?

Georeplikált módban lévő gyorsítótárak esetén az adatmegőrzés le van tiltva. Ha egy georeplikált pár nincs összekapcsolva, például egy ügyfél által kezdeményezett feladatátvétel, a másodlagos csatolt gyorsítótár megőrzi a szinkronizált adatokat az adott időpontig. Ilyen helyzetekben nem garantálunk helyreállítási pontot.

Helyreállítási pont beszerzéséhez exportálja [bármelyik](cache-how-to-import-export-data.md#export) gyorsítótárból az Exportálás gombra. Később [importálhat az elsődleges](cache-how-to-import-export-data.md#import) csatolt gyorsítótárba.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Használhatom a PowerShellt vagy az Azure CLI-t a georeplikáció kezelésére?

Igen, a georeplikáció az Azure Portal, a PowerShell vagy az Azure CLI használatával is kezelhető. További információ: [PowerShell-dokumentumok](/powershell/module/az.rediscache/#redis_cache) vagy [Azure CLI-dokumentumok.](/cli/azure/redis/server-link)

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Mennyibe kerül az adataim azure-régiók közötti replikálása?

Georeplikáció használata esetén a rendszer az elsődleges csatolt gyorsítótár adatait replikálja a másodlagos csatolt gyorsítótárba. Ha a két csatolt gyorsítótár ugyanabban a régióban található, az adatátvitel díjmentes. Ha a két összekapcsolt gyorsítótár eltérő régióban van, az adatátviteli díj az adatok régiónkénti áthelyezésének hálózati ki- és beátviteli költsége. További információ: [Bandwidth Pricing Details (Sávszélesség díjszabásának részletei).](https://azure.microsoft.com/pricing/details/bandwidth/)

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Miért volt sikertelen a művelet, amikor megpróbáltam törölni a csatolt gyorsítótárat?

A georeplikált gyorsítótárak és azok erőforráscsoportja nem törölhető csatolás közben, amíg el nem távolítja a georeplikációs hivatkozást. Ha megkísérli törölni az egyik vagy mindkét csatolt gyorsítótárat tartalmazó erőforráscsoportot, az erőforráscsoport többi erőforrása is törlődik, de az erőforráscsoport állapota és az erőforráscsoportban lévő összes csatolt gyorsítótár állapota `deleting` `running` megmarad. Az erőforráscsoport és a benne lévő csatolt gyorsítótárak teljes törléséhez távolítsa el a gyorsítótárat a Georeplikációs hivatkozás [eltávolítása című leírásban leírtak szerint.](#remove-a-geo-replication-link)

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Melyik régiót használjam a másodlagos csatolt gyorsítótárhoz?

Általánosságban elmondható, hogy a gyorsítótárnak ugyanabban az Azure-régióban kell léteznie, mint az ahhoz hozzáférő alkalmazásnak. A különálló elsődleges és tartalék régiókkal lévő alkalmazások esetében ajánlott az elsődleges és másodlagos gyorsítótárak ugyanabban a régióban létezni. További információ a párosított régiókról: [Ajánlott eljárások – Azure párosított régiók.](../best-practices-availability-paired-regions.md)

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hogyan működik a másodlagos csatolt gyorsítótárba való adat-visszahelyezett kapcsolat?

A georeplikált gyorsítótárak esetében az Azure-régiók közötti automatikus feladatátvétel nem támogatott. Vészhelyreállítási forgatókönyv esetén az ügyfeleknek a teljes alkalmazáskészletet koordinált módon kell kihozni a biztonsági mentési régiójukból. Ha az egyes alkalmazás-összetevők döntik el, hogy mikor váltson önálló biztonsági mentésre, az negatív hatással lehet a teljesítményre. A Redis egyik fő előnye, hogy ez egy nagyon kis késésű tároló. Ha az ügyfél fő alkalmazása más régióban van, mint a gyorsítótára, a hozzáadott körbeúti idő észrevehető hatással lenne a teljesítményre. Ezért az átmeneti rendelkezésre állási problémák miatt nem kerüljük el az automatikus átvesztéseket.

Az ügyfél által kezdeményezett feladatátvételhez először a gyorsítótárak leválasztása után indítsa el a feladatátvételt. Ezután módosítsa a Redis-ügyfelet úgy, hogy a (korábban csatolt) másodlagos gyorsítótár kapcsolati végpontját használja. Ha a két gyorsítótár le van csatolva, a másodlagos gyorsítótár újra normál olvasási/írási gyorsítótár lesz, és közvetlenül a Redis-ügyfelektől fogad kéréseket.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Konfigurálható tűzfal georeplikációval?

Igen, konfigurálhat [tűzfalat](./cache-configure.md#firewall) georeplikációval. Ahhoz, hogy a georeplikáció egy tűzfallal együtt működjön, győződjön meg arról, hogy a másodlagos gyorsítótár IP-címe hozzá van adva az elsődleges gyorsítótár tűzfalszabályainak.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Cache for Redis funkciókról.

* [Azure Cache for Redis szolgáltatási szintek](cache-overview.md#service-tiers)
* [Magas rendelkezésre állás a Azure Cache for Redis](cache-high-availability.md)
