---
title: Támogatott verziók postgres az Azure arc engedélyezve PostgreSQL nagy kapacitású
description: Támogatott verziók postgres az Azure arc engedélyezve PostgreSQL nagy kapacitású
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936088"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>A Postgres támogatott verziói az Azure Arc-kompatibilis, rugalmas skálázású PostgreSQL használata esetén

Ez a cikk ismerteti, hogy a postgres mely verziói érhetők el az Azure arc-kompatibilis PostgreSQL-nagy kapacitású.
A támogatott verziók listája az idő múlásával változik. Napjainkban a támogatott főbb verziók a következők:
- Postgres 12 (alapértelmezett)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Hogyan kell választani a verziók között?
Javasoljuk, hogy tekintse meg, milyen verziókat terveztek az alkalmazások számára, és hogy milyen funkciók vannak az egyes verziókhoz. További tudnivalókért tekintse meg a hivatalos postgres webhelyén található minden egyes verziót:
- [Postgres 12 (alapértelmezett)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Egy adott verzió létrehozása az Azure arc engedélyezve PostgreSQL nagy kapacitású-ben?
A létrehozáskor lehetősége van megadni, hogy melyik verziót hozza létre a _-Engine-Version_ paraméter átadásával. Ha nem jelöli meg a verziószámot, a rendszer alapértelmezés szerint létrehoz egy postgres-12-es verziójú kiszolgálói csoportot.

## <a name="how-do-be-notified-when-other-versions-are-available"></a>Hogyan lehet értesítést kapni, ha más verziók is elérhetők?
Térjen vissza, és olvassa el ezt a cikket. A rendszer a megfelelő módon frissíti. Az egyéni erőforrás-definíciók (CRD) típusát is megtekintheti a Kubernetes-fürt ív-adatvezérlőjében.
Futtassa az alábbi parancsot:
```console
kubectl get crds
```

A következőhöz hasonló kimenetet ad vissza:
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

Ebben a példában ez a kimenet két, a postgres-hez kapcsolódó tőkekövetelmény-típust jelez:
- a postgresql-12s.arcdata.microsoft.com a CRD a postgres 12 esetében
- a postgresql-11s.arcdata.microsoft.com a CRD a postgres 11 esetében

Ezek a CRDs, és nem kiszolgálócsoportok. A CRD jelenléte nem jelenti azt, hogy nem hozta létre az adott verziójú kiszolgálói csoportot.
A CRD arra utal, hogy milyen típusú erőforrásokat lehet létrehozni.

## <a name="next-steps"></a>Következő lépések:
- [További információ az Azure arc-kompatibilis PostgreSQL-nagy kapacitású létrehozásáról](create-postgresql-hyperscale-server-group.md)
- [További információ az arc-adatkezelőben létrehozott Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoportok listájának beszerzéséről](list-server-groups-postgres-hyperscale.md)
