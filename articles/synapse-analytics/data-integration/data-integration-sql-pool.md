---
title: Az adat betöltése egy dedikált SQL-készletbe
description: Ismerje meg, hogyan foglalhat be egy dedikált SQL-készletet az Azure szinapszis Analytics szolgáltatásban
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: a02abff712fc26f653307108fcc3bb284444d0c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676652"
---
# <a name="ingest-data-into-a-dedicated-sql-pool"></a>Az adat betöltése egy dedikált SQL-készletbe

Ebből a cikkből megtudhatja, hogyan végezheti el az adatok betöltését egy Azure Data Lake Gen 2 Storage-fiókból egy dedikált SQL-készletbe az Azure szinapszis Analyticsben.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) .
* **Azure Storage-fiók**: a 2. generációs Azure Data Lake Storage használja *forrás* adattárként. Ha nem rendelkezik Storage-fiókkal, tekintse meg az [Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md) lépéseit.
* **Azure szinapszis Analytics**: egy dedikált SQL-készletet használ fogadó *adattárként* . Ha nem rendelkezik Azure szinapszis Analytics-példánnyal, tekintse meg [a DEDIKÁLT SQL-készlet létrehozása](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című témakört a létrehozás lépéseihez.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Az Azure szinapszis Analyticsben a társított szolgáltatás a kapcsolati adatok más szolgáltatásokhoz való definiálására szolgál. Ebben a szakaszban egy Azure szinapszis Analytics és Azure Data Lake Storage Gen2 társított szolgáltatást fog hozzáadni.

1. Nyissa meg az Azure szinapszis Analytics UX eszközt, és lépjen a **kezelés** lapra.
1. A **külső kapcsolatok** területen válassza a **társított szolgáltatások** elemet.
1. Társított szolgáltatás hozzáadásához válassza az **új** lehetőséget.
1. Válassza ki a listából a Azure Data Lake Storage Gen2 csempét, és válassza a **Folytatás** lehetőséget.
1. Adja meg a hitelesítő adatait. A fiók kulcsa, az egyszerű szolgáltatásnév és a felügyelt identitás jelenleg támogatott hitelesítési típus. Válassza a kapcsolódás tesztelése lehetőséget a hitelesítő adatok helyességének ellenőrzéséhez. Miután végzett, válassza a **Létrehozás** lehetőséget.
1. Ismételje meg a 3-5 lépést, de Azure Data Lake Storage Gen2 helyett válassza ki az Azure szinapszis Analytics csempét, és adja meg a megfelelő kapcsolatok hitelesítő adatait. Az Azure szinapszis Analytics, az SQL-hitelesítés, a felügyelt identitás és az egyszerű szolgáltatásnév jelenleg támogatott.

## <a name="create-pipeline"></a>Folyamat létrehozása

A folyamatok egy adott tevékenységek végrehajtásának logikai folyamatát tartalmazzák. Ebben a szakaszban egy másolási tevékenységet tartalmazó folyamatot hoz létre, amely a ADLS Gen2ból származó adatok egy dedikált SQL-készletbe való betöltését teszi elérhetővé.

1. Nyissa meg az **integrálás** lapot. Válassza a folyamatok fejléc melletti plusz ikont, és válassza a **folyamat** lehetőséget.
1. Az **áthelyezés és átalakítás** területen a tevékenységek panelen húzza az **Adatmásolás** elemet a folyamat vászonra.
1. Válassza ki a másolási tevékenységet, és lépjen a **forrás** lapra. Az **új** elemre kattintva hozzon létre egy új forrás-adatkészletet.
1. Válassza ki Azure Data Lake Storage Gen2, és válassza a Folytatás lehetőséget.
1. Válassza a DelimitedText formátumot, és válassza a Folytatás lehetőséget.
1. A készlet tulajdonságai ablaktáblán válassza ki a létrehozott ADLS társított szolgáltatást. Adja meg a forrásadatok fájljának elérési útját, és adja meg, hogy az első sorban van-e fejléc. Importálhatja a sémát a file Store-ból vagy egy mintából. Ha elkészült, kattintson az OK gombra.
1. Nyissa meg a **fogadó lapot.** Új fogadó adatkészlet létrehozásához válassza az **új** lehetőséget.
1. Válassza az Azure szinapszis Analytics lehetőséget adattárként, és válassza a Folytatás lehetőséget.
1. A készlet tulajdonságai ablaktáblán válassza ki az Ön által létrehozott Azure szinapszis Analytics társított szolgáltatást. Ha egy meglévő táblába ír, válassza ki a legördülő menüből. Ha nem, akkor a **Szerkesztés** és a bevitel megadásával adja meg az új tábla nevét. Ha elkészült, kattintson az OK gombra
1. Ha táblát hoz létre, engedélyezze az **Automatikus létrehozás táblát** a tábla beállítás mezőjében.

## <a name="debug-and-publish-pipeline"></a>Folyamat hibakeresése és közzététele

Ha befejezte a folyamat konfigurálását, hibakeresési futtatást hajthat végre, mielőtt közzéteszi az összetevőket, hogy minden helyes legyen.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg. 
1. Ha a folyamat sikeresen futtatható, a felső eszköztáron válassza az **összes közzététele** lehetőséget. Ez a művelet közzéteszi a szinapszis Analytics szolgáltatásban létrehozott entitásokat (adatkészleteket és folyamatokat).
1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megtekintéséhez kattintson a jobb felső sarokban található harang gombra. 


## <a name="trigger-and-monitor-the-pipeline"></a>A folyamat elindítása és figyelése

Ebben a lépésben az előző lépésben közzétett folyamatot manuálisan indítja el. 

1. Válassza az **aktiválás hozzáadása** lehetőséget az eszköztáron, majd válassza az **aktiválás most** lehetőséget. A **Folyamatfuttatás** lapon válassza a **Befejezés** elemet.  
1. Lépjen a **figyelés** lapra a bal oldali oldalsávban. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A **műveletek** oszlopban található hivatkozások használatával megtekintheti a tevékenységek részleteit, és újra futtathatja a folyamatot.
1. A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Ebben a példában csak egy tevékenység van, így csak egy bejegyzés jelenik meg a listában. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). Válassza a felső **folyamat futtatása** lehetőséget a folyamat futási nézetének visszalépéséhez. A nézet frissítéséhez válassza a **Frissítés** parancsot.
1. Ellenőrizze, hogy az adatai helyesen vannak-e írva a dedikált SQL-készletben.


## <a name="next-steps"></a>Következő lépések

Az Azure szinapszis Analytics adatintegrálásával kapcsolatos további információkért lásd: adatok beolvasása [Azure Data Lake Storage Gen2 ](data-integration-data-lake.md) cikkbe.