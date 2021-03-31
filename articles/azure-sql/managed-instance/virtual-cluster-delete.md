---
title: Alhálózat törlése egy felügyelt SQL-példány törlése után
description: Ismerje meg, hogyan törölhet Azure-beli virtuális hálózatokat egy felügyelt Azure SQL-példány törlése után.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496ff6c7ec39706a99bb40447b6443ca71f19e5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99093680"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Alhálózat törlése egy felügyelt Azure SQL-példány törlése után
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk azt ismerteti, hogyan lehet manuálisan törölni az alhálózatot az utolsó Azure SQL felügyelt példány törlése után.

Az SQL felügyelt példányai [virtuális fürtökre](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)vannak telepítve. Minden virtuális fürt társítva van egy alhálózattal. A virtuális fürt a legutóbbi példány törlése után 12 órával megőrzi a tervet, így az SQL felügyelt példányok gyorsabban hozhatók létre ugyanabban az alhálózatban. Üres virtuális fürt megőrzése díjmentes. Ebben az időszakban a virtuális fürthöz társított alhálózatot nem lehet törölni.

Ha nem szeretne 12 órát várni, és hamarabb szeretné törölni a virtuális fürtöt és alhálózatát, manuálisan is megteheti. Törölje manuálisan a virtuális fürtöt a Azure Portal vagy a virtuális fürtök API használatával.

> [!IMPORTANT]
> - A virtuális fürt nem tartalmazhat SQL felügyelt példányt a törlés sikerességéhez. 
> - A virtuális fürt törlése egy hosszú ideig tartó művelet, amely körülbelül 1,5 óráig tart fenn (lásd az [SQL felügyelt példányok felügyeleti műveleteinek](./sql-managed-instance-paas-overview.md#management-operations) naprakész virtuális fürt törlési idejét). A folyamat befejezése előtt a virtuális fürt továbbra is látható lesz a portálon.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Virtuális fürt törlése a Azure Portal

Ha a Azure Portal használatával szeretne törölni egy virtuális fürtöt, keresse meg a virtuális fürt erőforrásait.

![Képernyőkép a Azure Portalről, a keresőmezőbe kiemelve](./media/virtual-cluster-delete/virtual-clusters-search.png)

Miután megtalálta a törölni kívánt virtuális fürtöt, válassza ki ezt az erőforrást, és válassza a **Törlés** lehetőséget. A rendszer felszólítja, hogy erősítse meg a virtuális fürt törlését.

![Képernyőfelvétel a Azure Portal Virtual Clusters irányítópultról, a törlés lehetőség kiemelve](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure Portal értesítések megerősítik, hogy a virtuális fürt törlésére vonatkozó kérelem sikeresen elküldve. Maga a törlési művelet körülbelül 1,5 óráig tart, ami alatt a virtuális fürt továbbra is látható lesz a portálon. A folyamat befejezése után a virtuális fürt többé nem lesz látható, és a hozzá társított alhálózat újra fel lesz szabadítva.

> [!TIP]
> Ha a virtuális fürtben nem találhatók SQL-felügyelt példányok, és nem tudja törölni a virtuális fürtöt, győződjön meg arról, hogy nem rendelkezik folyamatban lévő példány-telepítéssel. Ez magában foglalja a még folyamatban lévő elindított és megszakított központi telepítéseket is. Ennek az az oka, hogy ezek a műveletek továbbra is a virtuális fürtöt használják, és zárolják azt a törlésből. Azon erőforráscsoport **központi telepítések** lapjának áttekintése, amelyen a példány telepítve lett, a rendszer minden folyamatban lévő központi telepítést jelez. Ebben az esetben várjon, amíg a telepítés befejeződik, törölje az SQL felügyelt példányát, majd törölje a virtuális fürtöt.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Virtuális fürt törlése az API használatával

Ha a virtuális fürtöt az API-n keresztül szeretné törölni, használja a [virtuális fürtök delete metódusában](/rest/api/sql/virtualclusters/delete)megadott URI-paramétereket.

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- Ismerje meg a [kapcsolati architektúrát az SQL felügyelt példányában](connectivity-architecture-overview.md).
- Megtudhatja, hogyan [módosíthat egy meglévő virtuális hálózatot az SQL felügyelt példányain](vnet-existing-add-subnet.md).
- A virtuális hálózatok létrehozását, az Azure SQL felügyelt példányának létrehozását és az adatbázis biztonsági másolatból való visszaállítását bemutató oktatóanyagért tekintse meg [Az Azure SQL felügyelt példány létrehozása (portál)](instance-create-quickstart.md)című témakört.
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](custom-dns-configure.md)című témakört.