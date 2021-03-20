---
title: Virtuális hálózati szabályok – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan használhatja a Virtual Network (vnet) szolgáltatás-végpontokat Azure Database for PostgreSQL-kiszolgálóhoz való kapcsolódásra.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: b875936e13edfe0eff12f253836b093796951308
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98876326"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql---single-server"></a>Virtual Network szolgáltatási végpontok és szabályok használata Azure Database for PostgreSQL – egyetlen kiszolgáló esetén

A *virtuális hálózati szabályok* egy tűzfal biztonsági funkciója, amely azt szabályozza, hogy a Azure Database for PostgreSQL-kiszolgáló fogadja-e a virtuális hálózatok egyes alhálózatai által továbbított kommunikációt. Ebből a cikkből megtudhatja, miért érdemes a virtuális hálózati szabály funkciót időnként a Azure Database for PostgreSQL-kiszolgálóval való kommunikáció biztonságos engedélyezésére.

Virtuális hálózati szabály létrehozásához először [virtuális hálózatnak][vm-virtual-network-overview] (VNet) és [virtuális hálózati szolgáltatási végpontnak][vm-virtual-network-service-endpoints-overview-649d] kell lennie ahhoz, hogy a szabály hivatkozzon. Az alábbi ábra azt szemlélteti, hogyan működik a Virtual Network szolgáltatás-végpont Azure Database for PostgreSQL:

:::image type="content" source="media/concepts-data-access-and-security-vnet/vnet-concept.png" alt-text="Példa egy VNet-szolgáltatási végpont működésére":::

> [!NOTE]
> Ez a funkció az Azure nyilvános felhő összes régiójában elérhető, ahol a Azure Database for PostgreSQL általános célú és a memóriára optimalizált kiszolgálók esetében van telepítve.
> VNet esetén, ha a forgalom egy közös VNet-átjárón keresztül áramlik a szolgáltatási végpontokkal, és a partnernek kell lennie, hozzon létre egy ACL/VNet szabályt, amely lehetővé teszi, hogy az Azure Virtual Machines az átjáró VNet hozzáférjenek a Azure Database for PostgreSQL-kiszolgálóhoz.

Azt is megteheti, hogy [privát hivatkozást](concepts-data-access-and-security-private-link.md) használ a kapcsolatokhoz. A privát hivatkozás egy magánhálózati IP-címet biztosít a Azure Database for PostgreSQL-kiszolgáló VNet.

<a name="anch-terminology-and-description-82f"></a>
## <a name="terminology-and-description"></a>Terminológia és leírás

**Virtuális hálózat:** Az Azure-előfizetéshez társított virtuális hálózatokat is használhat.

**Alhálózat:** Egy virtuális hálózat **alhálózatokat** tartalmaz. A VNet belül bármely Azure-beli virtuális gép (VM) hozzá van rendelve egy alhálózathoz. Egy alhálózat több virtuális gépet és/vagy más számítási csomópontot is tartalmazhat. A virtuális hálózatán kívüli számítási csomópontok nem férnek hozzá a virtuális hálózathoz, kivéve, ha úgy konfigurálja a biztonságot, hogy engedélyezze a hozzáférést.

**Virtual Network szolgáltatási végpont:** A [Virtual Network szolgáltatási végpont][vm-virtual-network-service-endpoints-overview-649d] olyan alhálózat, amelynek tulajdonságértékek egy vagy több formális Azure-szolgáltatástípus nevét tartalmazzák. Ebben a cikkben a **Microsoft. SQL** típus neve érdekli, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatási címke a Azure Database for PostgreSQL és a MySQL szolgáltatásokra is vonatkozik. Fontos megjegyezni, hogy amikor a **Microsoft. SQL** szolgáltatás címkéjét egy VNet szolgáltatási végpontra alkalmazza, akkor az Azure adatbázis-szolgáltatásaihoz a szolgáltatási végpont forgalmát konfigurálja: SQL Database, Azure szinapszis Analytics, Azure Database for PostgreSQL és Azure Database for MySQL kiszolgálók az alhálózaton. 

**Virtuális hálózati szabály:** A Azure Database for PostgreSQL-kiszolgáló virtuális hálózati szabálya egy alhálózat, amely a Azure Database for PostgreSQL-kiszolgáló hozzáférés-vezérlési listájában (ACL) szerepel. Ahhoz, hogy a Azure Database for PostgreSQL-kiszolgáló ACL-je legyen, az alhálózatnak tartalmaznia kell a **Microsoft. SQL** típus nevét.

Egy virtuális hálózati szabály közli a Azure Database for PostgreSQL-kiszolgálóval, hogy fogadja a kommunikációt az alhálózaton lévő összes csomópontról.

<a name="anch-details-about-vnet-rules-38q"></a>

## <a name="benefits-of-a-virtual-network-rule"></a>Virtuális hálózati szabály előnyei

Amíg el nem végzi a műveletet, az alhálózat (k) ben lévő virtuális gépek nem tudnak kommunikálni a Azure Database for PostgreSQL-kiszolgálóval. A kommunikációt létrehozó egyik művelet egy virtuális hálózati szabály létrehozása. A VNet szabály megközelítésének indoklása egy összehasonlítási és kontrasztos vitát igényel, amely magában foglalja a tűzfal által kínált versengő biztonsági beállításokat.

### <a name="allow-access-to-azure-services"></a>Hozzáférés engedélyezése Azure-szolgáltatások számára

A kapcsolat biztonsági paneljén **be-és KIkapcsoló** gomb található, amely **lehetővé teszi az Azure-szolgáltatásokhoz való hozzáférést**. A **on** beállítás lehetővé teszi az összes Azure IP-cím és az összes Azure-alhálózat kommunikációját. Előfordulhat, hogy ezek az Azure-beli IP-címek vagy alhálózatok nem tulajdonosai. Ez **a** beállítás valószínűleg nyitottabb, mint amennyire szeretné, hogy a Azure Database for PostgreSQL adatbázisa legyen. A virtuális hálózati szabály funkció sokkal finomabb, részletesebb szabályozást biztosít.

### <a name="ip-rules"></a>IP-szabályok

A Azure Database for PostgreSQL tűzfal lehetővé teszi olyan IP-címtartományok megadását, amelyekről a Azure Database for PostgreSQL-adatbázis fogadja a kommunikációt. Ez a megközelítés az Azure-magánhálózaton kívüli stabil IP-címekre is kiváló. Az Azure-magánhálózat számos csomópontja azonban *dinamikus* IP-címekkel van konfigurálva. Előfordulhat, hogy a dinamikus IP-címek változhatnak, például a virtuális gép újraindításakor. Az éles környezetben nem lehet dinamikus IP-címet megadni egy tűzfalszabály számára.

Az IP-címet a virtuális gép *statikus* IP-címének beszerzésével lehet megmenteni. Részletekért lásd: [a virtuális gép magánhálózati IP-címeinek konfigurálása a Azure Portal használatával][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

A statikus IP-cím azonban nehezen kezelhető, és költséges, ha nagy léptékben történik. A virtuális hálózati szabályok könnyebben hozhatók létre és kezelhetők.


<a name="anch-details-about-vnet-rules-38q"></a>

## <a name="details-about-virtual-network-rules"></a>A virtuális hálózati szabályok részletei

Ez a szakasz a virtuális hálózati szabályokkal kapcsolatos néhány részletet ismerteti.

### <a name="only-one-geographic-region"></a>Csak egy földrajzi régió

Minden Virtual Network szolgáltatási végpont csak egy Azure-régióra vonatkozik. A végpont nem teszi lehetővé más régiók számára, hogy fogadják a kommunikációt az alhálózatból.

Bármely virtuális hálózati szabály arra a régióra korlátozódik, amelyre a mögöttes végpont vonatkozik.

### <a name="server-level-not-database-level"></a>Kiszolgáló szintű, nem adatbázis-szintű

Minden virtuális hálózati szabály a teljes Azure Database for PostgreSQL-kiszolgálóra vonatkozik, nem csupán egy adott adatbázisra a kiszolgálón. Más szóval a virtuális hálózati szabály a kiszolgáló szintjén, nem pedig az adatbázis szintjén érvényes.

#### <a name="security-administration-roles"></a>Biztonsági felügyeleti szerepkörök

A biztonsági szerepkörök elkülönítése Virtual Network szolgáltatási végpontok felügyelete alatt áll. A következő szerepkörök mindegyike esetében beavatkozásra van szükség:

- **Hálózati rendszergazda:** &nbsp; Kapcsolja be a végpontot.
- **Adatbázis-rendszergazda:** &nbsp; Frissítse a hozzáférés-vezérlési listát (ACL), hogy hozzáadja a megadott alhálózatot a Azure Database for PostgreSQL-kiszolgálóhoz.

*Azure RBAC alternatíva:*

A hálózati rendszergazda és az adatbázis-rendszergazda szerepkörének több funkciója van, mint amennyi a virtuális hálózati szabályok kezeléséhez szükséges. A képességeinek csak egy részhalmazára van szükség.

Lehetősége van arra, hogy az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)][rbac-what-is-813s] használatával egyetlen egyéni szerepkört hozzon létre, amely csak a képességek megfelelő részhalmazával rendelkezik. Az egyéni szerepkör felhasználható a hálózati rendszergazda vagy az adatbázis-rendszergazda bevonása helyett. Ha egyéni szerepkörhöz ad hozzá felhasználót, és a másik két fő rendszergazdai szerepkörhöz hozzáadja a felhasználót, a biztonsági expozíció felülete alacsonyabb lesz.

> [!NOTE]
> Bizonyos esetekben a Azure Database for PostgreSQL és a VNet különböző előfizetésekben találhatók. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
> - Mindkét előfizetésnek ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia.
> - A felhasználó rendelkezik a szükséges engedélyekkel a műveletek elindításához, például a szolgáltatási végpontok engedélyezéséhez és egy VNet-alhálózat hozzáadásához az adott kiszolgálóhoz.
> - Győződjön meg arról, hogy az előfizetés mind a **Microsoft. SQL** , mind a **Microsoft. DBforPostgreSQL** erőforrás-szolgáltató regisztrálva van. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

## <a name="limitations"></a>Korlátozások

Azure Database for PostgreSQL esetében a virtuális hálózati szabályok funkció a következő korlátozásokkal rendelkezik:

- A webalkalmazások egy VNet/alhálózat magánhálózati IP-címére képezhetők le. Még ha a szolgáltatási végpontok be vannak kapcsolva a megadott VNet/alhálózatból, akkor a webalkalmazás és a kiszolgáló közötti kapcsolatok Azure nyilvános IP-címmel rendelkeznek, nem VNet/alhálózat-forrásként. Ha egy webalkalmazás kapcsolatát szeretné engedélyezni egy olyan kiszolgálóra, amely VNet rendelkezik, engedélyeznie kell az Azure-szolgáltatások számára a kiszolgáló elérését a kiszolgálón.

- A Azure Database for PostgreSQL tűzfalában minden egyes virtuális hálózati szabály egy alhálózatra hivatkozik. Az összes hivatkozott alhálózatnak ugyanabban a földrajzi régióban kell lennie, amely a Azure Database for PostgreSQL üzemelteti.

- Minden Azure Database for PostgreSQL-kiszolgáló legfeljebb 128 ACL-bejegyzést tartalmazhat bármely adott virtuális hálózathoz.

- A virtuális hálózati szabályok csak Azure Resource Manager virtuális hálózatokra érvényesek; és nem a [klasszikus üzembe helyezési modell][arm-deployment-model-568f] hálózatait.

- A virtuális hálózati szolgáltatási végpontok a **Microsoft. SQL** szolgáltatással való Azure Database for PostgreSQL való bekapcsolásával az összes Azure Database-szolgáltatás végpontját is engedélyezi: Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL Database és az Azure szinapszis Analytics.

- A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.

- Ha a **Microsoft. SQL** engedélyezve van egy alhálózatban, az azt jelzi, hogy csak a VNet-szabályokat szeretné használni a kapcsolódáshoz. Az alhálózaton lévő erőforrások [nem VNet tűzfalszabályok](concepts-firewall-rules.md) nem fognak működni.

- A tűzfalon az IP-címtartományok a következő hálózati elemekre vonatkoznak, a virtuális hálózati szabályok azonban nem:
    - [Helyek közötti (S2S) virtuális magánhálózat (VPN)][vpn-gateway-indexmd-608y]
    - Helyszíni [ExpressRoute][expressroute-indexmd-744v] -on keresztül

## <a name="expressroute"></a>ExpressRoute

Ha a hálózat [ExpressRoute][expressroute-indexmd-744v]használatával csatlakozik az Azure-hálózathoz, minden áramkör két nyilvános IP-címmel van konfigurálva a Microsoft Edge-ben. A két IP-cím használatával kapcsolódhat a Microsoft-szolgáltatásokhoz, például az Azure Storage-hoz az Azure nyilvános társ-létrehozással.

Ha engedélyezni szeretné az áramkörről a Azure Database for PostgreSQL felé irányuló kommunikációt, létre kell hoznia IP-hálózati szabályokat az áramkörök nyilvános IP-címeihez. A ExpressRoute-áramkör nyilvános IP-címeinek megkereséséhez a Azure Portal segítségével nyisson meg egy támogatási jegyet a ExpressRoute.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNET-tűzfalszabály hozzáadása a kiszolgálóhoz a VNET szolgáltatás végpontjai bekapcsolása nélkül

Pusztán egy VNet-tűzfalszabály beállítása nem nyújt segítséget a kiszolgálónak a VNet való biztonságossá tételében. **A VNet** szolgáltatás-végpontokat is be kell kapcsolni a biztonság érvénybe léptetéséhez. Ha bekapcsolja **a** szolgáltatási végpontokat, a VNet-alhálózat az állásidőt, amíg be nem fejeződik a **kikapcsolás** és **a** közötti átmenet. Ez különösen igaz a nagyméretű virtuális hálózatok kontextusában. A **IgnoreMissingServiceEndpoint** jelzővel csökkentheti vagy törölheti az állásidőt az áttérés során.

A **IgnoreMissingServiceEndpoint** jelzőt az Azure CLI vagy a portál használatával állíthatja be.

## <a name="related-articles"></a>Kapcsolódó cikkek
- [Azure-beli virtuális hálózatok][vm-virtual-network-overview]
- [Azure Virtual Network szolgáltatásbeli végpontok][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Következő lépések
A VNet-szabályok létrehozásával kapcsolatos cikkekért lásd:
- [Azure Database for PostgreSQL VNet-szabályok létrehozása és kezelése a Azure Portal használatával](howto-manage-vnet-using-portal.md)
- [Azure Database for PostgreSQL VNet-szabályok létrehozása és kezelése az Azure CLI használatával](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../role-based-access-control/overview.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
