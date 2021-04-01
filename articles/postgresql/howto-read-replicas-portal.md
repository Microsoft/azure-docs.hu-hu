---
title: Olvasási replikák kezelése – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerje meg, hogyan kezelheti az olvasási replikákat Azure Database for PostgreSQL – egyetlen kiszolgáló a Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 9fdef187e9bdf77b29c548f767a4b4edfeb62f44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422178"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Olvasási replikák létrehozása és kezelése Azure Database for PostgreSQL – egyetlen kiszolgálón a Azure Portal

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat Azure Database for PostgreSQL a Azure Portalból. Az olvasási replikákkal kapcsolatos további tudnivalókért tekintse meg az [áttekintést](concepts-read-replicas.md).


## <a name="prerequisites"></a>Előfeltételek
Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md) elsődleges kiszolgálóként.

## <a name="azure-replication-support"></a>Azure-beli replikálás támogatása

Az [olvasási replikák](concepts-read-replicas.md) és a [logikai dekódolás](concepts-logical.md) a postgres írási előre megadott naplójától (Wal) függ. Ehhez a két szolgáltatáshoz különböző naplózási szintek szükségesek a postgres. A logikai dekódolás magasabb szintű naplózást igényel, mint az olvasási replikák.

A megfelelő naplózási szint konfigurálásához használja az Azure-replikáció támogatási paraméterét. Az Azure-beli replikáció támogatásának három beállítási lehetősége van:

* **Off** – a legkevesebb információt helyezi el a Wal-ben. Ez a beállítás nem érhető el a legtöbb Azure Database for PostgreSQL kiszolgálón.  
* **Replika** – részletesebb, mint a **kikapcsolás**. Az [olvasási replikák](concepts-read-replicas.md) működéséhez szükséges naplózás minimális szintje. Ez a beállítás az alapértelmezett a legtöbb kiszolgálón.
* **Logikai** – részletesebb, mint a **replika**. Ez a minimális szintű naplózás a logikai dekódolás működéséhez. Ebben a beállításban az olvasási replikák is működnek.


> [!NOTE]
> Ha olvasási replikákat helyez üzembe az állandó, nagy írási igényű elsődleges számítási feladatokhoz, a replikációs késés továbbra is növekedni fog, és előfordulhat, hogy soha nem fogja tudni felvenni az elsődlegest. Ez növelheti az elsődleges tárterület-használatot is, mivel a rendszer nem törli a WAL-fájlokat, amíg azok a replikán nem érkeznek.

## <a name="prepare-the-primary-server"></a>Az elsődleges kiszolgáló előkészítése

1. A Azure Portal válasszon ki egy meglévő Azure Database for PostgreSQL-kiszolgálót, amelyet főkiszolgálóként kíván használni.

2. A kiszolgáló menüjében válassza a **replikálás** lehetőséget. Ha az Azure-replikáció támogatásának értéke legalább **replika**, létrehozhat olvasási replikákat. 

3. Ha az Azure-beli replikálás támogatása nem a legalább **replika** értékre van beállítva, állítsa be. Kattintson a **Mentés** gombra.

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Azure Database for PostgreSQL – replikálás – replika beállítása és mentés":::

4. A módosítás alkalmazásához indítsa újra a kiszolgálót az **Igen** lehetőség kiválasztásával.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Azure Database for PostgreSQL – replikálás – újraindítás megerősítése":::

5. A művelet befejezését követően két Azure Portal értesítést fog kapni. A kiszolgálói paraméter frissítése egyetlen értesítéssel történik. A kiszolgáló azonnali újraindítására vonatkozóan egy másik értesítés is van, amely azonnal következik.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="Sikeres értesítések":::

6. Frissítse a Azure Portal lapot a replikálási eszköztár frissítéséhez. Most már létrehozhat olvasási replikákat ehhez a kiszolgálóhoz.
   

## <a name="create-a-read-replica"></a>Olvasási replika létrehozása
Olvasási replika létrehozásához kövesse az alábbi lépéseket:

1. Válasszon ki egy meglévő Azure Database for PostgreSQL kiszolgálót, amelyet elsődleges kiszolgálóként kíván használni. 

2. A kiszolgáló oldalsávjának **Beállítások** területén válassza a **replikálás** elemet.

3. Válassza a **replika hozzáadása** lehetőséget.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="Replika hozzáadása":::

4. Adja meg az olvasási replika nevét. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="A replika neve":::

5. Válassza ki a replika helyét. Az alapértelmezett hely ugyanaz, mint az elsődleges kiszolgáló.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="Hely kiválasztása":::

   > [!NOTE]
   > Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md). 

6. A replika létrehozásának megerősítéséhez kattintson **az OK gombra** .

Az olvasási replika létrehozása után a **replikálási** ablakból lehet megtekinteni:

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="Az új replika megtekintése a replikálási ablakban":::
 

> [!IMPORTANT]
> Tekintse át az [olvasási replika áttekintése című témakör szempontjait](concepts-read-replicas.md#considerations).
>
> Ahhoz, hogy egy elsődleges kiszolgáló beállítása új értékre frissüljön, frissítse a replika beállításait egy egyenlő vagy nagyobb értékre. Ez a művelet segíti a replikát a főkiszolgálón végrehajtott bármilyen módosítással.

## <a name="stop-replication"></a>Replikáció leállítása
Leállíthatja az elsődleges kiszolgáló és az olvasási replika közötti replikációt.

> [!IMPORTANT]
> Miután leállította a replikálást egy elsődleges kiszolgálóra és egy olvasási replikára, nem vonható vissza. Az olvasási replika önálló kiszolgáló lesz, amely támogatja az olvasást és az írást is. Az önálló kiszolgáló nem hozható létre újra replikába.

Az elsődleges kiszolgáló és a Azure Portalból származó olvasási replika közötti replikáció leállításához kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki az elsődleges Azure Database for PostgreSQL-kiszolgálót.

2. A kiszolgáló menü **Beállítások** területén válassza a **replikálás** elemet.

3. Válassza ki azt a másodpéldány-kiszolgálót, amelynél le szeretné állítani a replikálást.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="A replika kijelölése":::
 
4. Válassza a **replikálás leállítása** lehetőséget.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="Válassza a replikáció leállítása lehetőséget":::
 
5. A replikáció leállításához kattintson **az OK gombra** .

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="A replikáció leállításának megerősítése":::
 

## <a name="delete-a-primary-server"></a>Elsődleges kiszolgáló törlése
Elsődleges kiszolgáló törléséhez használja az önálló Azure Database for PostgreSQL-kiszolgáló törléséhez szükséges lépéseket. 

> [!IMPORTANT]
> Elsődleges kiszolgáló törlésekor a rendszer leállítja az összes olvasási replikára való replikálást. Az olvasási replikák olyan önálló kiszolgálók lesznek, amelyek már támogatják az olvasást és az írást is.

Ha törölni szeretne egy kiszolgálót a Azure Portalból, kövesse az alábbi lépéseket:

1. A Azure Portal válassza ki az elsődleges Azure Database for PostgreSQL-kiszolgálót.

2. Nyissa meg a kiszolgáló **Áttekintés** lapját. Válassza a **Törlés** elemet.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="A kiszolgáló áttekintése lapon válassza az elsődleges kiszolgáló törlését":::
 
3. Adja meg a törlendő elsődleges kiszolgáló nevét. Válassza a **Törlés** lehetőséget az elsődleges kiszolgáló törlésének megerősítéséhez.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="Az elsődleges kiszolgáló törlésének megerősítése":::
 

## <a name="delete-a-replica"></a>Replika törlése
Az olvasási replikát az elsődleges kiszolgáló törléséhez hasonló módon törölheti.

- A Azure Portalban nyissa meg az olvasási replika **Áttekintés** lapját. Válassza a **Törlés** elemet.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="A replika áttekintése lapon válassza a (z) lehetőséget a replika törléséhez.":::
 
Az olvasási replikát a **replikálási** ablakból is törölheti a következő lépések végrehajtásával:

1. A Azure Portal válassza ki az elsődleges Azure Database for PostgreSQL-kiszolgálót.

2. A kiszolgáló menü **Beállítások** területén válassza a **replikálás** elemet.

3. Válassza ki a törlendő olvasási replikát.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="Válassza ki a törölni kívánt replikát":::
 
4. Válassza a **replika törlése** lehetőséget.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="Replika törlésének kiválasztása":::
 
5. Adja meg a törlendő replika nevét. A replika törlésének megerősítéséhez válassza a **Törlés** lehetőséget.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="A te replika törlésének megerősítése":::
 

## <a name="monitor-a-replica"></a>Replika figyelése
Az olvasási replikák figyelésére két metrika áll rendelkezésre.

### <a name="max-lag-across-replicas-metric"></a>Maximális késés a replikák metrikája között
A **replikák maximális késése** az elsődleges kiszolgáló és a legkésleltetésű replika közötti késést mutatja bájtban. 

1.  A Azure Portal válassza ki az elsődleges Azure Database for PostgreSQL kiszolgálót.

2.  Válassza a **Metrikák** lehetőséget. A **metrikák** ablakban válassza a **maximális késés a replikák között** lehetőséget.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="A replikák közötti maximális késés figyelése":::
 
3.  Az **összesítéshez** válassza a **Max** lehetőséget.


### <a name="replica-lag-metric"></a>Replika késésének mérőszáma
A **replika késésének** mérőszáma az utolsó visszajátszott tranzakció óta eltelt időt jeleníti meg a replikán. Ha nincs tranzakció a főkiszolgálón, a metrika ezt az időbeli késést tükrözi.

1. A Azure Portal válassza ki a Azure Database for PostgreSQL olvasási replikát.

2. Válassza a **Metrikák** lehetőséget. A **metrikák** ablakban válassza a **replika késése** elemet.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="A replika késésének figyelése":::
 
3. Az **összesítéshez** válassza a **Max** lehetőséget. 
 
## <a name="next-steps"></a>Következő lépések
* További információ [az olvasási replikákkal kapcsolatban Azure Database for PostgreSQL](concepts-read-replicas.md).
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI-ben és a REST APIban](howto-read-replicas-cli.md).