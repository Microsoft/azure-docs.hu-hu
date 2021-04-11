---
title: 'Gyors útmutató: alapszintű nagy kapacitású létrehozása (Citus) – Azure Database for PostgreSQL'
description: Ismerkedjen meg a Azure Database for PostgreSQL nagy kapacitású (Citus) alapszintű csomaggal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 04/07/2021
ms.openlocfilehash: 07ace217e5299662a1c3145a225abc25f4f1f337
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024081"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Hozzon létre egy nagy kapacitású (Citus) alapszintű kiszolgálói csoportot a Azure Portal

A Azure Database for PostgreSQL-nagy kapacitású (Citus) egy felügyelt szolgáltatás, amely a felhőben található, magasan elérhető PostgreSQL-adatbázisok futtatásához, kezeléséhez és méretezéséhez használható. Az [alapszintű csomag](concepts-hyperscale-tiers.md) egy kényelmes üzembe helyezési lehetőség a kezdeti fejlesztéshez és teszteléshez.

Ez a rövid útmutató bemutatja, hogyan hozhat létre nagy kapacitású (Citus) alapszintű kiszolgálói csoportot a Azure Portal használatával. Kiépíti a kiszolgálói csoportot, és ellenőrizheti, hogy tud-e csatlakozni a lekérdezésekhez.

> [!IMPORTANT]
> A nagy kapacitású (Citus) alapszintű csomag jelenleg előzetes verzióban érhető el.  Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> Megtekintheti a [nagy kapacitású (Citus) előzetes](hyperscale-preview-features.md)verziójának új funkcióinak teljes listáját.

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan építhet ki egy nagy kapacitású-(Citus-) kiszolgáló csoportot. Csatlakoztatta azt a psql-hoz, létrehozott egy sémát és egy elosztott adatkészletet.

- A [skálázható több-bérlős alkalmazások létrehozásához](./tutorial-design-database-hyperscale-multi-tenant.md) kövesse az oktatóanyagot
- A kiszolgálócsoport legjobb [kezdeti méretének](howto-hyperscale-scale-initial.md) meghatározása
