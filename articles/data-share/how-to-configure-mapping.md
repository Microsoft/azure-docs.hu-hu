---
title: Adatkészlet-hozzárendelés konfigurálása az Azure-adatmegosztásban
description: Megtudhatja, hogyan konfigurálhat adatkészlet-hozzárendelést egy fogadott megosztáshoz az Azure-adatmegosztás használatával.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88257830"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Adatkészlet-hozzárendelés konfigurálása egy fogadott megosztáshoz az Azure-adatmegosztásban

Ez a cikk azt ismerteti, hogyan lehet konfigurálni egy adatkészlet-hozzárendelést egy fogadott megosztáshoz az Azure-adatmegosztás használatával. Érdemes lehet konfigurálni egy adatkészlet-hozzárendelést, hogy megadja azt a célhelyet, amelyben adatokat szeretne kapni, vagy ha módosítania kell a cél adattárat.

## <a name="navigate-to-a-received-data-share"></a>Nyisson meg egy fogadott adatmegosztást

Az Azure adatmegosztási szolgáltatásban navigáljon a kapott megosztáshoz, és válassza az **adatkészletek** lapot. 

![Adatkészlet-hozzárendelés](./media/dataset-mapping.png "Adatkészlet-hozzárendelés") 

Jelölje be azon adatkészlet melletti jelölőnégyzetet, amelyhez hozzá szeretné rendelni a célhelyet. Válassza a **+ leképezés a célhelyre** lehetőséget egy új célhely kiválasztásához. Először válassza a **leképezésének megszüntetése** lehetőséget, ha az adatkészlet már le van képezve, és módosítani szeretné a célként megadott adattárat.

![Leképezés célhelyre](./media/dataset-map-target.png "Leképezés célhelyre") 

## <a name="select-a-target-store"></a>Válasszon tárolót

Válassza ki azt a célként megadott adattár-típust, amelybe az adatterületet szeretné. A pillanatkép-alapú megosztáshoz a korábban leképezett tárolási fiókokban már létező adatok nem lesznek automatikusan áthelyezve az új tárolóba. A helyi megosztáshoz válasszon egy adattárat a megadott helyen. A hely az az Azure-adatközpont, ahol az adatszolgáltató forrás-adattára található.

![Cél Storage-fiók](./media/dataset-map-target-sql.png "Cél tárterülete") 

## <a name="select-a-file-format-sql-sources-only"></a>Fájlformátum kiválasztása (csak SQL-források)

Ha a forrásadatok SQL-alapú forrásból származnak, és fájlként szeretné őket fogadni, kiválaszthatja, hogy melyik formátumot fogadja. 

![Formátum kiválasztása](./media/sql-file-formats.png "SQL-fájlformátumok")

## <a name="next-steps"></a>Következő lépések

Az adatmegosztás megkezdésének megismeréséhez folytassa az [adatgyűjtés megosztása](share-your-data.md) című oktatóanyagot.



