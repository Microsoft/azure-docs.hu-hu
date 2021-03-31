---
title: Kapcsolódás az Azure Kubernetes szolgáltatáshoz – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Tudnivalók az Azure Kubernetes szolgáltatás (ak) az Azure Database for PostgreSQL-Single Serverrel való csatlakoztatásáról
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.date: 07/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9b7da2fcc1310f03f894e048089658f25be3a149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91708849"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Az Azure Kubernetes szolgáltatás és Azure Database for PostgreSQL – egyetlen kiszolgáló csatlakoztatása

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes-fürtöt biztosít, amelyet az Azure-ban használhat. Az alábbiakban néhány olyan lehetőség közül választhat, amely az AK-t használja, és Azure Database for PostgreSQL együtt egy alkalmazás létrehozásához.


## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés
Gyorsított hálózatkezeléssel rendelkező mögöttes virtuális gépek használata az AK-fürtben. Ha a gyorsított hálózatkezelés engedélyezve van egy virtuális gépen, alacsonyabb a késés, a csökkent jitter és a CPU-kihasználtság csökkent a virtuális gépen. További információ a gyorsított hálózatkezelés működéséről, a támogatott operációsrendszer-verziókról és a [Linux](../virtual-network/create-vm-accelerated-networking-cli.md)rendszerhez támogatott VM-példányokról.

Az AK november 2018-én támogatja a gyorsított hálózatkezelést a támogatott VM-példányokon. A gyorsított hálózatkezelés alapértelmezés szerint engedélyezve van a virtuális gépeket használó új AK-fürtökön.

Megerősítheti, hogy az AK-fürt felgyorsult hálózattal rendelkezik-e:
1. Nyissa meg a Azure Portal, és válassza ki az AK-fürtöt.
2. Válassza a Properties (Tulajdonságok) lapot.
3. Másolja az **infrastruktúra-erőforráscsoport** nevét.
4. Az infrastruktúra-erőforráscsoport megkereséséhez és megnyitásához használja a portál keresési sávját.
5. Válasszon ki egy virtuális gépet az adott erőforráscsoporthoz.
6. Nyissa meg a virtuális gép **hálózatkezelés** lapját.
7. Győződjön meg arról, hogy a **gyorsított hálózatkezelés** engedélyezve van-e.

Vagy az Azure CLI-n keresztül az alábbi két parancs használatával:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
A kimenet a létrehozott erőforráscsoport, amely a hálózati adaptert tartalmazó AK-t hozza létre. Adja meg a "nodeResourceGroup" nevet, és használja a következő parancsban. A **EnableAcceleratedNetworking** értéke TRUE (igaz) vagy FALSE (hamis) lesz:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```


## <a name="connection-pooling"></a>Kapcsolatok készletezése
A kapcsolat Pooler az adatbázishoz való új kapcsolatok létrehozásához és bezárásához kapcsolódó költségeket és időt is lekicsinyíti. A készlet olyan kapcsolatok gyűjteménye, amelyek újra felhasználhatók. 

A PostgreSQL-sel több összekapcsolási készlet is használható. Ezek egyike a [PgBouncer](https://pgbouncer.github.io/). A Microsoft Container Registry egy kis méretű, tárolóban lévő PgBouncer biztosítunk, amely az oldalkocsiban használható az AK-ról Azure Database for PostgreSQL-ra való csatlakozásra. Látogasson el a [Docker hub oldalára](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) , ahol megtudhatja, hogyan érheti el és használhatja ezt a rendszerképet. 


## <a name="next-steps"></a>Következő lépések
-  [Azure Kubernetes Service-fürt létrehozása](../aks/kubernetes-walkthrough.md)
