---
title: Geo-replikáció konfigurálása prémium szintű Azure cache-hez Redis-példányokhoz
description: Ismerje meg, hogyan replikálhatja az Azure cache-t a prémium szintű Redis az Azure-régiók között
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 130cb1d63da27010012c22dc2cdb40c3d8f03273
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178558"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Geo-replikáció konfigurálása prémium szintű Azure cache-hez Redis-példányokhoz

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy földrajzilag replikált Azure-gyorsítótárat a Azure Portal használatával.

A Geo-replikáció két prémium szintű Azure cache-t tartalmaz a Redis-példányokhoz, és létrehoz egy adatreplikálási kapcsolatot. Ezek a gyorsítótár-példányok általában különböző Azure-régiókban találhatók, de nem szükségesek hozzájuk. Az egyik példány elsődlegesként, a másik pedig másodlagosként működik. Az elsődleges kezeli az olvasási és írási kérelmeket, és propagálja a másodlagos módosításokat. Ez a folyamat addig folytatódik, amíg a két példány közötti kapcsolat el nem távolítva.

> [!NOTE]
> A Geo-replikáció vész-helyreállítási megoldásként lett kialakítva.
>
>

## <a name="geo-replication-prerequisites"></a>Földrajzi replikálás előfeltételei

Két gyorsítótár közötti földrajzi replikálás konfigurálásához a következő előfeltételek teljesülése szükséges:

- Mindkét gyorsítótár [prémium szintű](cache-overview.md#service-tiers) gyorsítótár.
- Mindkét gyorsítótár ugyanahhoz az Azure-előfizetéshez tartozik.
- A másodlagos csatolt gyorsítótár a gyorsítótár mérete vagy a gyorsítótár nagyobb mérete, mint az elsődleges csatolt gyorsítótár.
- Mindkét gyorsítótár létre van hozva, és futó állapotban van.

Bizonyos funkciók nem támogatottak a Geo-replikációval:

- Az adatmegőrzés nem támogatott a Geo-replikációval.
- A fürtözés akkor támogatott, ha mindkét gyorsítótárban engedélyezve van a fürtözés, és azonos számú szegmens van.
- Az azonos VNET lévő gyorsítótárak támogatottak.
- A különböző virtuális hálózatok található gyorsítótárak a kikötésekkel együtt támogatottak. Lásd: használhatom a [geo-replikációt a saját gyorsítótárával egy VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) további információért.

A földrajzi replikálás konfigurálása után a következő korlátozások vonatkoznak a csatolt gyorsítótár-párokra:

- A másodlagos csatolt gyorsítótár írásvédett; elolvashatja, de nem írhat adatokra. 
- A rendszer eltávolítja a hivatkozás hozzáadása előtt a másodlagos csatolt gyorsítótárban található összes olyan adattal. Ha a Geo-replikáció később el lett távolítva, a replikált adatforgalom a másodlagos csatolt gyorsítótárban marad.
- A gyorsítótárak összekapcsolása közben nem [méretezheti](cache-how-to-scale.md) a gyorsítótárat.
- A szegmensek [száma nem módosítható](cache-how-to-premium-clustering.md) , ha a gyorsítótárban engedélyezve van a fürtözés.
- Az adatmegőrzés nem engedélyezhető mindkét gyorsítótárban.
- A gyorsítótárból is [exportálhat](cache-how-to-import-export-data.md#export) .
- A másodlagos csatolt gyorsítótárba nem [importálható](cache-how-to-import-export-data.md#import) .
- A csatolt gyorsítótár vagy az azokat tartalmazó erőforráscsoport nem törölhető, amíg le nem kapcsolja a gyorsítótárak csatolását. További információ: [Miért volt sikertelen a művelet, amikor megpróbáltam törölni a csatolt gyorsítótárat?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Ha a gyorsítótárak különböző régiókban találhatók, a hálózati kimenő költségek a régiók között áthelyezett adatforgalomra vonatkoznak. További információ: [Mennyibe kerül az adatok replikálása az Azure-régiók között?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Az elsődleges és a másodlagos csatolt gyorsítótár között nem történik automatikus feladatátvétel. Az ügyfélalkalmazások feladatátvételének módjával kapcsolatos további információkért lásd: [Hogyan történik a feladatátvétel a másodlagos csatolt gyorsítótárba?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Földrajzi replikálási hivatkozás hozzáadása

1. Ha két gyorsítótárat szeretne összekapcsolni a Geo-replikációhoz, akkor kattintson a **geo-replikáció** elemre azon gyorsítótár erőforrás menüjében, amelyet elsődlegesen csatolt gyorsítótárként kíván használni. Ezután a **geo-replikáció** panelen kattintson a **gyorsítótár replikálási hivatkozás hozzáadása** elemre.

    ![Hivatkozás hozzáadása](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kattintson a kívánt másodlagos gyorsítótár nevére a **kompatibilis gyorsítótárak** listából. Ha a másodlagos gyorsítótár nem jelenik meg a listában, ellenőrizze, hogy teljesülnek-e a másodlagos gyorsítótár [földrajzi replikálásának előfeltételei](#geo-replication-prerequisites) . A gyorsítótárak régiónként történő szűréséhez kattintson a térképen a régióra, hogy csak a **kompatibilis gyorsítótárak** listájában megjelenjenek a gyorsítótárak.

    ![Geo-replikációval kompatibilis gyorsítótárak](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    A helyi menüből is elindíthatja a csatolási folyamatot, vagy megtekintheti a másodlagos gyorsítótár részleteit.

    ![Geo-replikálás helyi menüje](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kattintson a **hivatkozás** gombra a két gyorsítótár együttes összekapcsolásához és a replikálási folyamat megkezdéséhez.

    ![Csatolási gyorsítótárak](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. A replikációs folyamat előrehaladását a **geo-replikáció** panelen tekintheti meg.

    ![Csatolás állapota](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    A csatolási állapotot az **Áttekintés** panelen is megtekintheti az elsődleges és másodlagos gyorsítótárak esetében is.

    ![Képernyőfelvétel: az elsődleges és másodlagos gyorsítótárak csatolási állapotának megtekintése.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    A replikálási folyamat befejeződése után a **hivatkozás állapota** **sikeresre** változik.

    ![Gyorsítótár állapota](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Az elsődleges csatolt gyorsítótár továbbra is használható a csatolási folyamat során. A másodlagos csatolt gyorsítótár nem érhető el, amíg a csatolási folyamat be nem fejeződik.

## <a name="remove-a-geo-replication-link"></a>Geo-replikálási hivatkozás eltávolítása

1. A két gyorsítótár közötti kapcsolat eltávolításához és a földrajzi replikálás leállításához kattintson a **gyorsítótárak leválasztása** lehetőségre a **geo-replikáció** panelen.
    
    ![Gyorsítótárak leválasztása](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Ha a leválasztási folyamat befejeződik, a másodlagos gyorsítótár az olvasások és írások esetében is elérhető.

>[!NOTE]
>A Geo-replikációs hivatkozás eltávolításakor az elsődleges csatolt gyorsítótárból származó replikált adatok a másodlagos gyorsítótárban maradnak.
>
>

## <a name="geo-replication-faq"></a>Geo-replikálás – gyakori kérdések

- [Használhatom a Geo-replikációt standard vagy alapszintű gyorsítótárral?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Használható a gyorsítótár a csatolási vagy leválasztási folyamat során?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Összekapcsolhatok kettőnél több gyorsítótárat is?](#can-i-link-more-than-two-caches-together)
- [Kapcsolhatok két gyorsítótárat különböző Azure-előfizetésből?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Kapcsolhatok két gyorsítótárat különböző méretekkel?](#can-i-link-two-caches-with-different-sizes)
- [Használhatom a Geo-replikációt a fürtözés engedélyezésével?](#can-i-use-geo-replication-with-clustering-enabled)
- [Használhatom a Geo-replikációt a VNET a gyorsítótárral?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Mi a Redis földrajzi replikálásának replikációs ütemterve?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Mennyi ideig tart a Geo-replikálási replikáció?](#how-long-does-geo-replication-replication-take)
- [Garantált a replikálás helyreállítási pontja?](#is-the-replication-recovery-point-guaranteed)
- [Használhatom a PowerShellt vagy az Azure CLI-t a Geo-replikáció kezeléséhez?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Mennyibe kerül az adatmennyiség az Azure-régiók között?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Miért sikertelen a művelet, amikor megpróbáltam törölni a csatolt gyorsítótárat?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Milyen régiót használhatok a másodlagos csatolt gyorsítótárhoz?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hogyan működik a feladatátvétel a másodlagos csatolt gyorsítótárral?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Konfigurálható a tűzfal a Geo-replikációval?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Használhatom a Geo-replikációt standard vagy alapszintű gyorsítótárral?

Nem, a Geo-replikáció csak a prémium szintű gyorsítótárak esetében érhető el.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Használható a gyorsítótár a csatolási vagy leválasztási folyamat során?

- A csatoláskor az elsődleges csatolt gyorsítótár továbbra is elérhető marad, amíg a kapcsolódási folyamat befejeződik.
- A csatolás esetén a másodlagos csatolt gyorsítótár nem érhető el, amíg a csatolási folyamat be nem fejeződik.
- A leválasztáskor mindkét gyorsítótár elérhető marad, amíg a leválasztási folyamat befejeződik.

### <a name="can-i-link-more-than-two-caches-together"></a>Összekapcsolhatok kettőnél több gyorsítótárat is?

Nem, egyszerre csak két gyorsítótárat lehet összekapcsolni.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Kapcsolhatok két gyorsítótárat különböző Azure-előfizetésből?

Nem, mindkét gyorsítótárnak ugyanabban az Azure-előfizetésben kell lennie.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Kapcsolhatok két gyorsítótárat különböző méretekkel?

Igen, amíg a másodlagos csatolt gyorsítótár nagyobb, mint az elsődleges csatolt gyorsítótár.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Használhatom a Geo-replikációt a fürtözés engedélyezésével?

Igen, feltéve, hogy mindkét gyorsítótár azonos számú szegmenssel rendelkezik.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Használhatom a Geo-replikációt a VNET a gyorsítótárral?

Igen, a virtuális hálózatok-gyorsítótárak földrajzi replikálását a rendszer a kikötésekkel támogatja:

- Az azonos VNET lévő gyorsítótárak közötti földrajzi replikálás támogatott.
- A különböző virtuális hálózatok található gyorsítótárak közötti földrajzi replikálás is támogatott.
  - Ha a virtuális hálózatok ugyanabban a régióban vannak, csatlakoztathatja őket a VNET- [társ](../virtual-network/virtual-network-peering-overview.md) vagy egy [VPN Gateway VNET-VNET kapcsolat](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)használatával.
  - Ha a virtuális hálózatok különböző régiókban találhatóak, a VNET-társítást használó geo-replikáció támogatott, de az 1. VNET (1. régió) egy ügyfél-virtuális gép nem fér hozzá a gyorsítótárhoz a (z) VNET 2 (2. régió) DNS-nevével, az alapszintű belső terheléselosztó korlátozásai miatt. A VNET-társítási korlátozásokkal kapcsolatos további információkért lásd: [Virtual Network-peering-követelmények és megkötések](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Az ajánlott megoldás egy VPN Gateway VNET-VNET-kapcsolatok használata.
  
[Ezzel az Azure-sablonnal](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)gyorsan üzembe helyezhet két földrajzi replikált gyorsítótárat egy VPN Gateway VNET – VNET kapcsolattal összekapcsolt VNET.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Mi a Redis földrajzi replikálásának replikációs ütemterve?

A replikáció folyamatos és aszinkron, és nem egy adott időpontban történik. A rendszer azonnal és aszinkron módon replikálja az elsődlegesen végzett összes írást a másodlagosra.

### <a name="how-long-does-geo-replication-replication-take"></a>Mennyi ideig tart a Geo-replikálási replikáció?

A replikáció növekményes, aszinkron és folyamatos, és a megtett idő nem sokban különbözik a régiók közötti késéstől. Bizonyos körülmények között előfordulhat, hogy a másodlagos gyorsítótárnak az elsődlegesből származó adatok teljes szinkronizálására van szükség. Ebben az esetben a replikálási idő függ a következő tényezők számától: az elsődleges gyorsítótár terhelése, a rendelkezésre álló hálózati sávszélesség és a régiók közötti késés. A teljes 53 GB-os geo-replikált párok esetében a replikálási idő 5 – 10 percet is igénybe vehet.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Garantált a replikálás helyreállítási pontja?

Földrajzilag replikált módban lévő gyorsítótárak esetében a megőrzés le van tiltva. Ha egy földrajzilag replikált pár nincs összekapcsolva, például egy ügyfél által kezdeményezett feladatátvételsel, a másodlagos csatolt gyorsítótár az adott időpontig megőrzi a szinkronizált adataikat. Ilyen helyzetekben nincs garantált helyreállítási pont.

Helyreállítási pont beszerzéséhez [exportálja](cache-how-to-import-export-data.md#export) a gyorsítótárból. Később [importálhatja](cache-how-to-import-export-data.md#import) az elsődleges csatolt gyorsítótárba.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Használhatom a PowerShellt vagy az Azure CLI-t a Geo-replikáció kezeléséhez?

Igen, a Geo-replikáció a Azure Portal, a PowerShell vagy az Azure CLI használatával kezelhető. További információ: [PowerShell-dokumentumok](/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) vagy [Azure CLI-dokumentumok](/cli/azure/redis/server-link).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Mennyibe kerül az adatmennyiség az Azure-régiók között?

A Geo-replikáció használatakor az elsődleges csatolt gyorsítótárból származó adatok replikálódnak a másodlagos csatolt gyorsítótárba. Ha a két csatolt gyorsítótár ugyanahhoz a régióhoz tartozik, az adatátvitel díjmentes. Ha a két csatolt gyorsítótár különböző régiókban található, az adatátviteli díj a hálózat kimenő adatforgalmi díja a régión áthaladó adatforgalomért. További információ: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Miért sikertelen a művelet, amikor megpróbáltam törölni a csatolt gyorsítótárat?

A Geo-replikált gyorsítótárak és az erőforráscsoportok nem törölhetők, amíg hozzá nem távolítja a Geo-replikálási hivatkozást. Ha a csatolt gyorsítótárak egyikét vagy mindkettőt tartalmazó erőforráscsoportot is megpróbálja törölni, a rendszer törli az erőforráscsoport többi erőforrását, de az erőforráscsoport `deleting` állapota és az erőforráscsoport kapcsolódó gyorsítótárai változatlan állapotban maradnak `running` . Az erőforráscsoport és a csatolt gyorsítótárak teljes törléséhez szüntesse meg a gyorsítótárak leválasztását a [geo-replikációs hivatkozás eltávolítása](#remove-a-geo-replication-link)című témakörben leírtak szerint.

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Milyen régiót használhatok a másodlagos csatolt gyorsítótárhoz?

Általánosságban azt javasoljuk, hogy a gyorsítótára ugyanabban az Azure-régióban legyen, mint az ahhoz az alkalmazáshoz, amely hozzáfér. A különálló elsődleges és tartalék régióval rendelkező alkalmazások esetében ajánlott, hogy az elsődleges és másodlagos gyorsítótárak ugyanabban a régióban vannak. A párosított régiókkal kapcsolatos további információkért lásd: [ajánlott eljárások – Azure párosított régiók](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hogyan működik a feladatátvétel a másodlagos csatolt gyorsítótárral?

Az Azure-régiók közötti automatikus feladatátvétel nem támogatott a földrajzilag replikált gyorsítótárak esetében. A vész-helyreállítási forgatókönyvek esetén az ügyfeleknek a teljes alkalmazás veremét koordinált módon kell meghozniuk a biztonsági mentési régióban. Az egyes alkalmazás-összetevők kiválasztásával eldöntheti, hogy mikor kell a saját biztonsági másolatokra váltani, negatív hatással lehet a teljesítményre. A Redis egyik fő előnye, hogy ez egy nagyon alacsony késésű tároló. Ha az ügyfél fő alkalmazása a gyorsítótártól eltérő régióban található, akkor a hozzáadott időponthoz képest érezhető hatással lehet a teljesítményre. Emiatt az átmeneti rendelkezésre állási problémák miatt nem történik meg automatikusan a feladatátvétel.

Az ügyfél által kezdeményezett feladatátvétel elindításához először a gyorsítótárak leválasztása szükséges. Ezután módosítsa a Redis-ügyfelet a (korábban csatolt) másodlagos gyorsítótár kapcsolati végpontjának használatára. Ha a két gyorsítótár le van csatolva, a másodlagos gyorsítótár egy normál olvasási írási gyorsítótárba kerül, és közvetlenül a Redis-ügyfelektől fogadja a kéréseket.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Beállíthat egy tűzfalat a Geo-replikációval?

Igen, konfigurálhat egy [tűzfalat](./cache-configure.md#firewall) a Geo-replikálás használatával. Ahhoz, hogy a Geo-replikáció egy tűzfal mellett működjön, győződjön meg arról, hogy a másodlagos gyorsítótár IP-címe hozzá van adva az elsődleges gyorsítótár tűzfalszabályok számára.

## <a name="next-steps"></a>Következő lépések

További információ az Azure cache Redis szolgáltatásairól.

* [Azure cache a Redis szolgáltatási szintjeihez](cache-overview.md#service-tiers)
* [Magas rendelkezésre állás az Azure cache Redis](cache-high-availability.md)
