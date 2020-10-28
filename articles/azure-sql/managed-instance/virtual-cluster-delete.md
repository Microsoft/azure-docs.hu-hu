---
title: Alhálózat törlése a felügyelt SQL-példány felügyelt példányának törlése után
description: Megtudhatja, hogyan törölhet egy Azure-beli virtuális hálózatot az Azure SQL felügyelt példány felügyelt példányának törlése után.
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
ms.openlocfilehash: 4ed8f6dc90debddd17282f8f96962ffd78055030
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791664"
---
# <a name="delete-a-subnet-after-deleting-a-managed-instance-of-sql-managed-instance"></a>Alhálózat törlése a felügyelt SQL-példány felügyelt példányának törlése után
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk útmutatást nyújt arról, hogyan lehet manuálisan törölni az alhálózatot az Azure SQL felügyelt példány utolsó felügyelt példányának törlése után.

A felügyelt példányok [virtuális fürtökbe](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)vannak telepítve. Minden virtuális fürt társítva van egy alhálózattal. A virtuális fürt az utolsó példány törlése után 12 órával megőrzi a tervet, így gyorsabban hozhat létre felügyelt példányokat ugyanabban az alhálózatban. Üres virtuális fürt megőrzése díjmentes. Ebben az időszakban a virtuális fürthöz társított alhálózatot nem lehet törölni.

Ha nem szeretne 12 órát várni, és hamarabb szeretné törölni a virtuális fürtöt és alhálózatát, manuálisan is megteheti. Törölje manuálisan a virtuális fürtöt a Azure Portal vagy a virtuális fürtök API használatával.

> [!IMPORTANT]
> - A virtuális fürtnek nem tartalmazhat felügyelt példányt, hogy a törlés sikeres legyen. 
> - A virtuális fürt törlése egy hosszú ideig tartó művelet, amely körülbelül 1,5 óráig tart fenn (lásd: [felügyelt példányok felügyeleti műveletei](./sql-managed-instance-paas-overview.md#management-operations) a naprakész virtuális fürtök törlési idejére). A folyamat befejezése előtt a virtuális fürt továbbra is látható lesz a portálon.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Virtuális fürt törlése a Azure Portal

Ha a Azure Portal használatával szeretne törölni egy virtuális fürtöt, keresse meg a virtuális fürt erőforrásait.

![Képernyőkép a Azure Portalről, a keresőmezőbe kiemelve](./media/virtual-cluster-delete/virtual-clusters-search.png)

Miután megtalálta a törölni kívánt virtuális fürtöt, válassza ki ezt az erőforrást, és válassza a **Törlés** lehetőséget. A rendszer felszólítja, hogy erősítse meg a virtuális fürt törlését.

![Képernyőfelvétel a Azure Portal Virtual Clusters irányítópultról, a törlés lehetőség kiemelve](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure Portal értesítések megerősítik, hogy a virtuális fürt törlésére vonatkozó kérelem sikeresen elküldve. Maga a törlési művelet körülbelül 1,5 óráig tart, ami alatt a virtuális fürt továbbra is látható lesz a portálon. A folyamat befejezése után a virtuális fürt többé nem lesz látható, és a hozzá társított alhálózat újra fel lesz szabadítva.

> [!TIP]
> Ha nem találhatók felügyelt példányok a virtuális fürtben, és nem tudja törölni a virtuális fürtöt, győződjön meg arról, hogy nem rendelkezik folyamatban lévő példány-telepítéssel. Ez magában foglalja a még folyamatban lévő elindított és megszakított központi telepítéseket is. Ennek az az oka, hogy ezek a műveletek továbbra is a virtuális fürtöt használják, és zárolják azt a törlésből. Azon erőforráscsoport **központi telepítések** lapjának áttekintése, amelyen a példány telepítve lett, a rendszer minden folyamatban lévő központi telepítést jelez. Ebben az esetben várjon, amíg a telepítés befejeződik, törölje a felügyelt példányt, majd törölje a virtuális fürtöt.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Virtuális fürt törlése az API használatával

Ha a virtuális fürtöt az API-n keresztül szeretné törölni, használja a [virtuális fürtök delete metódusában](/rest/api/sql/virtualclusters/delete)megadott URI-paramétereket.

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- Ismerje meg a [kapcsolati architektúrát az SQL felügyelt példányában](connectivity-architecture-overview.md).
- Megtudhatja, hogyan [módosíthat egy meglévő virtuális hálózatot az SQL felügyelt példányain](vnet-existing-add-subnet.md).
- A virtuális hálózatok létrehozásával, felügyelt példány létrehozásával és adatbázis biztonsági másolatból való visszaállításával kapcsolatos oktatóanyagért lásd: [felügyelt példány létrehozása](instance-create-quickstart.md).
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](custom-dns-configure.md)című témakört.