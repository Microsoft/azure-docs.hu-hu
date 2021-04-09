---
title: Mi az Azure arc engedélyezve PostgreSQL nagy kapacitású?
description: Mi az Azure arc engedélyezve PostgreSQL nagy kapacitású?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 8edf540c3f67a3a8bee075569f0a2588cae18a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390011"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Mi az Azure arc engedélyezve PostgreSQL nagy kapacitású?

Az Azure arc engedélyezve PostgreSQL nagy kapacitású az Azure arc-kompatibilis adatszolgáltatások részeként elérhető adatbázis-szolgáltatások egyike. Az Azure arc lehetővé teszi az Azure-beli adatszolgáltatások helyszíni, peremhálózati és nyilvános felhőben történő futtatását a Kubernetes és az Ön által választott infrastruktúra használatával. Az Azure arc-kompatibilis adatszolgáltatások kiosztása a következő tagolást nyújtja:
- Mindig aktuális
- Rugalmas méretezés
- Önkiszolgáló kiépítés
- Egységes felügyelet
- Leválasztott forgatókönyvek támogatása

További részletek:
- [Mik azok az Azure arc-kompatibilis adatszolgáltatások](overview.md)
- [Csatlakozási módok és követelmények](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>Megoldások összehasonlítása

Ez a szakasz azt ismerteti, hogyan különbözik az Azure arc-kompatibilis PostgreSQL nagy kapacitású Azure Database for PostgreSQL nagy kapacitású (Citus)?

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL – rugalmas skálázás (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database PostgreSQL-nagy kapacitású (Citus)":::

Ez az Azure-beli szolgáltatásként elérhető postgres-adatbázismotor nagy kapacitású formája. Ez a Citus-bővítmény, amely lehetővé teszi a nagy kapacitású élményt. Ebben az formátumban a szolgáltatás a Microsoft adatközpontokban fut, és a Microsoft működteti.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure arc engedélyezve PostgreSQL nagy kapacitású

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure arc engedélyezve PostgreSQL nagy kapacitású":::

Ez az Azure arc-kompatibilis adatszolgáltatások által elérhető postgres-adatbázismotor nagy kapacitású formája. Emellett a Citus bővítmény is, amely lehetővé teszi a nagy kapacitású élményét. Ezen az űrlapon ügyfeleink a rendszereket üzemeltető infrastruktúrát biztosítják, és működtetik azokat.

## <a name="next-steps"></a>Következő lépések
- **Próbálja ki.** Gyorsan elsajátíthatja az [Azure arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) az Azure Kubernetes Service (ak), az AWS rugalmas Kubernetes szolgáltatás (EKS), a Google Cloud Kubernetes Engine (GKE) vagy egy Azure-beli virtuális gépen. 

- **Hozza létre a sajátját.** Kövesse az alábbi lépéseket a saját Kubernetes-fürtön való létrehozásához: 
   1. [Az ügyféleszközök telepítése](install-client-tools.md)
   2. [Az Azure arc-adatkezelő létrehozása](create-data-controller.md)
   3. [Azure Database for PostgreSQL nagy kapacitású-kiszolgálócsoport létrehozása az Azure arc-ban](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [További információ az Azure arc-kompatibilis adatszolgáltatásokról](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Az Azure arc áttekintése](https://aka.ms/azurearc)
