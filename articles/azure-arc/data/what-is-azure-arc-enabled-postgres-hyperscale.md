---
title: Mi az Azure arc engedélyezve PostgreSQL nagy kapacitású?
description: Mi az Azure arc engedélyezve PostgreSQL nagy kapacitású?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7e8746d9b29b0b4af30ba799628328acc3fd8bc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936017"
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

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure SQL Database PostgreSQL-nagy kapacitású (Citus)":::

Ez az Azure arc-kompatibilis adatszolgáltatások által elérhető postgres-adatbázismotor nagy kapacitású formája. Emellett a Citus bővítmény is, amely lehetővé teszi a nagy kapacitású élményét. Ezen az űrlapon ügyfeleink a rendszereket üzemeltető infrastruktúrát biztosítják, és működtetik azokat.

## <a name="next-steps"></a>Következő lépések
- **Létrehozás**
   > **Csak szeretné kipróbálni a dolgokat?**  
   > Gyorsan elsajátíthatja az [Azure arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) az Azure Kubernetes Service (ak), az AWS rugalmas Kubernetes szolgáltatás (EKS), a Google Cloud Kubernetes Engine (GKE) vagy egy Azure-beli virtuális gépen.

   - **Létrehozása**
      - [Az ügyféleszközök telepítése](install-client-tools.md)
      - [Hozza létre az Azure arc-adatkezelőt](create-data-controller.md) (először telepítenie kell az ügyféleszközök telepítését)
      - [Hozzon létre egy Azure Database for PostgreSQL nagy kapacitású-kiszolgáló csoportot az Azure-ív](create-postgresql-hyperscale-server-group.md) (először egy Azure arc-adatkezelőt kell létrehoznia).
- [**További információ az Azure arc-kompatibilis adatszolgáltatásokról**](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [**Az Azure arc áttekintése**](https://aka.ms/azurearc)
