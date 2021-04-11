---
title: Olvasási replikák kezelése – Azure Portal-Azure Database for PostgreSQL-nagy kapacitású (Citus)
description: Ismerje meg, hogyan kezelheti az olvasási replikákat Azure Database for PostgreSQL-nagy kapacitású (Citus) a Azure Portalból.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 4d3a88692da6d1fc78e96a6261d42d3ca97b0dd0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023983"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Olvasási replikák létrehozása és kezelése a Azure Database for PostgreSQL-nagy kapacitású (Citus) szolgáltatásból a Azure Portal

> [!IMPORTANT]
> A nagy kapacitású-ben (Citus) jelenleg előzetes verzióban érhető el a replikák. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
>
> Megtekintheti a [nagy kapacitású (Citus) előzetes](hyperscale-preview-features.md)verziójának új funkcióinak teljes listáját.

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a nagy kapacitású (Citus) a Azure Portalból. Az olvasási replikákkal kapcsolatos további tudnivalókért tekintse meg az [áttekintést](concepts-hyperscale-read-replicas.md).


## <a name="prerequisites"></a>Előfeltételek

Egy [nagy kapacitású-(Citus-) kiszolgálócsoport](quickstart-create-hyperscale-portal.md) , amely elsődleges.

## <a name="create-a-read-replica"></a>Olvasási replika létrehozása

Olvasási replika létrehozásához kövesse az alábbi lépéseket:

1. Válasszon ki egy meglévő Azure Database for PostgreSQL, amelyet elsődlegesként kíván használni. 

2. A kiszolgálócsoport oldalsávja alatt válassza a **replikáció** lehetőséget a kiszolgáló csoport **kezelése** területen.

3. Válassza a **replika hozzáadása** lehetőséget.

4. Adja meg az olvasási replika nevét. 

5. A replika létrehozásának megerősítéséhez kattintson **az OK gombra** .

Az olvasási replika létrehozása után a **replikáció** ablakból lehet megtekinteni.

> [!IMPORTANT]
>
> Tekintse át az [olvasási replika áttekintése című témakör szempontjait](concepts-hyperscale-read-replicas.md#considerations).
>
> Mielőtt egy elsődleges kiszolgálócsoport-beállítást új értékre frissít, frissítse a replika beállításait egy egyenlő vagy nagyobb értékre. Ez a művelet segíti a replikát a főkiszolgálón végrehajtott bármilyen módosítással.

## <a name="delete-a-primary-server-group"></a>Elsődleges kiszolgálócsoport törlése

Elsődleges kiszolgálócsoport törléséhez használja az önálló nagy kapacitású (Citus) kiszolgáló csoportjának törléséhez szükséges lépéseket. 

> [!IMPORTANT]
>
> Ha töröl egy elsődleges kiszolgálócsoport-csoportot, a replikálás minden olvasási replikára leállt. Az olvasási replikák önálló kiszolgálócsoportok lesznek, amelyek mostantól támogatják az olvasást és az írást is.

Ha törölni szeretne egy számítógépcsoportot a Azure Portalból, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki az elsődleges Azure Database for PostgreSQL-kiszolgáló csoportját.

2. Nyissa meg a kiszolgálói csoport **Áttekintés** lapját. Válassza a **Törlés** elemet.
 
3. Adja meg a törlendő elsődleges kiszolgálócsoport nevét. Válassza a **Törlés** lehetőséget az elsődleges kiszolgálócsoport törlésének megerősítéséhez.
 

## <a name="delete-a-replica"></a>Replika törlése

Az olvasási replikát ugyanúgy törölheti, mint az elsődleges kiszolgáló csoportjának törlését.

- A Azure Portalban nyissa meg az olvasási replika **Áttekintés** lapját. Válassza a **Törlés** elemet.
 
Az olvasási replikát a **replikálási** ablakból is törölheti a következő lépések végrehajtásával:

1. A Azure Portal válassza ki az elsődleges nagy kapacitású (Citus).

2. A kiszolgáló csoport menü **kiszolgáló csoport kezelése** területén válassza a **replikálás** elemet.

3. Válassza ki a törlendő olvasási replikát.
 
4. Válassza a **replika törlése** lehetőséget.
 
5. Adja meg a törlendő replika nevét. A replika törlésének megerősítéséhez válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* További információ az [olvasási replikákkal kapcsolatban: Azure Database for PostgreSQL-nagy kapacitású (Citus)](concepts-hyperscale-read-replicas.md).
