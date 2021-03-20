---
title: VNet-végpontok kezelése – Azure Portal-Azure Database for MySQL
description: Azure Database for MySQL VNet-szolgáltatási végpontok és szabályok létrehozása és kezelése a Azure Portal használatával
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5273681f23f6eea54c35e5cacea487dab18793e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240783"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Azure Database for MySQL VNet-szolgáltatási végpontok és VNet-szabályok létrehozása és kezelése a Azure Portal használatával
A virtuális hálózat (VNet) szolgáltatásvégpontjai és szabályai kiterjesztik a virtuális hálózat magánhálózati címtartományát az Azure Database for MySQL-kiszolgálóra. Az Azure Database for MySQL VNet szolgáltatásbeli végpontok, például a korlátozások áttekintését lásd: [Azure Database for MySQL Server VNet Service-végpontok](concepts-data-access-and-security-vnet.md). A VNet szolgáltatási végpontok a Azure Database for MySQL összes támogatott régiójában elérhetők.

> [!NOTE]
> A VNet szolgáltatás-végpontok támogatása csak a általános célú és a memóriára optimalizált kiszolgálók esetében támogatott.
> VNet esetén, ha a forgalom egy közös VNet-átjárón keresztül áramlik a szolgáltatási végpontokkal, és a partnernek kell lennie, hozzon létre egy ACL/VNet szabályt, amely lehetővé teszi, hogy az Azure Virtual Machines az átjáró VNet hozzáférjenek a Azure Database for MySQL-kiszolgálóhoz.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>VNet-szabály létrehozása és szolgáltatási végpontok engedélyezése a Azure Portal

1. A MySQL-kiszolgáló lapon, a beállítások fejléc alatt kattintson a **kapcsolatbiztonsági** elemre a Azure Database for MySQL kapcsolódási biztonsági paneljének megnyitásához. 

2. Győződjön meg arról, hogy az Azure-szolgáltatások hozzáférésének engedélyezése beállítás **ki van kapcsolva**.

> [!Important]
> Ha a vezérlőt a be értékre állítja, az Azure MySQL adatbázis-kiszolgáló bármely alhálózatról fogadja a kommunikációt. Ha a vezérlőt a be értékre állítja, előfordulhat, hogy a biztonsági szempontból túlságosan nagy a hozzáférés. A Microsoft Azure Virtual Network szolgáltatási végpont funkciója, amely a Azure Database for MySQL virtuális hálózati szabály funkciójával együttműködve csökkenti a biztonsági felületét.

3. Ezután kattintson a **+ meglévő virtuális hálózat hozzáadása** lehetőségre. Ha nem rendelkezik meglévő VNet, kattintson az **+ új virtuális hálózat létrehozása** lehetőségre egy létrehozásához. Lásd [: gyors útmutató: virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md)

   :::image type="content" source="./media/howto-manage-vnet-using-portal/1-connection-security.png" alt-text="Azure Portal – kattintson a kapcsolatbiztonsági lehetőségre":::

4. Adja meg a VNet szabály nevét, válassza ki az előfizetést, a virtuális hálózatot és az alhálózatot, majd kattintson az **Engedélyezés** gombra. Ez automatikusan engedélyezi a VNet szolgáltatási végpontok használatát az alhálózaton a **Microsoft. SQL** szolgáltatás címkével.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/2-configure-vnet.png" alt-text="Azure Portal – VNet konfigurálása":::

   A fióknak rendelkeznie kell a megfelelő engedélyekkel egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

   A szolgáltatási végpontok egymástól függetlenül konfigurálhatók virtuális hálózatokon a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználóktól.
    
   Az Azure-szolgáltatási erőforrások VNet való biztonságossá tételéhez a felhasználónak engedéllyel kell rendelkeznie a "Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/" jogosultsággal a hozzáadott alhálózatokhoz. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.
    
   További információk a [beépített szerepkörökről](../role-based-access-control/built-in-roles.md) és a bizonyos engedélyek [egyéni szerepkörökhöz](../role-based-access-control/custom-roles.md) való hozzárendeléséről.
    
   A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a VNet és az Azure-szolgáltatási erőforrások különböző előfizetésekben találhatók, akkor az erőforrásoknak ugyanahhoz a Active Directory (AD) bérlőhöz kell tartozniuk. Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. SQL** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

   > [!IMPORTANT]
   > Javasoljuk, hogy a szolgáltatási végpontok konfigurálása előtt olvassa el ezt a cikket a szolgáltatás-végponti konfigurációkkal és szempontokkal kapcsolatban. **Virtual Network szolgáltatási végpont:** A [Virtual Network szolgáltatási végpont](../virtual-network/virtual-network-service-endpoints-overview.md) olyan alhálózat, amelynek tulajdonságértékek egy vagy több formális Azure-szolgáltatástípus nevét tartalmazzák. A VNet Services-végpontok a **Microsoft. SQL** nevű szolgáltatástípus-nevet használják, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatási címke a Azure SQL Databasere, a Azure Database for PostgreSQL és a MySQL szolgáltatásokra is vonatkozik. Fontos megjegyezni, hogy amikor a **Microsoft. SQL** szolgáltatás címkéjét egy VNet szolgáltatási végpontra alkalmazza, a szolgáltatás végponti forgalmát konfigurálja az összes Azure Database-szolgáltatáshoz, beleértve az alhálózaton lévő Azure SQL Database, Azure Database for PostgreSQL és Azure Database for MySQL kiszolgálókat is. 
   > 

5. Ha engedélyezve van, kattintson **az OK** gombra, és látni fogja, hogy a VNet szolgáltatás-végpontok engedélyezve vannak a VNet szabállyal együtt.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png" alt-text="VNet szolgáltatás-végpontok engedélyezve és VNet szabály létrehozva":::

## <a name="next-steps"></a>Következő lépések
- Ehhez hasonlóan parancsfájlt is [engedélyezhet a VNet szolgáltatás végpontjai számára, és létrehozhat egy VNet-szabályt az Azure CLI-vel való Azure Database for MySQLhoz](howto-manage-vnet-using-cli.md).
- Ha segítségre van a Azure Database for MySQL kiszolgálóhoz való csatlakozáshoz, tekintse meg a [Azure Database for MySQLhoz tartozó kapcsolódási kódtárakat](./concepts-connection-libraries.md) .

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md