---
title: Betöltés Azure Data Lake Storage Gen2
description: Ismerkedjen meg az Azure szinapszis Analytics Azure Data Lake Storage Gen2ba való betöltésével
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbc4f11b450a645002daedc800d4fed74ed37a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219572"
---
# <a name="ingest-data-into-azure-data-lake-storage-gen2"></a>Adatbevitel a Azure Data Lake Storage Gen2ba 

Ebből a cikkből megtudhatja, hogyan végezheti el az adatok egyik helyről a másikra való betöltését egy Azure Data Lake Gen 2 (Azure Data Lake Gen 2) Storage-fiókban az Azure szinapszis Analytics használatával.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) .
* **Azure Storage-fiók**: a 2. generációs Azure Data Lake használja *forrás* adattárként. Ha nem rendelkezik Storage-fiókkal, tekintse meg az [Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) lépéseit.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Az Azure szinapszis Analyticsben a társított szolgáltatás a kapcsolati adatok más szolgáltatásokhoz való definiálására szolgál. Ebben a szakaszban az Azure szinapszis Analytics és a 2. generációs Azure Data Lake fogja hozzáadni társított szolgáltatásként.

1. Nyissa meg az Azure szinapszis Analytics UX eszközt, és lépjen a **kezelés** lapra.
1. A **külső kapcsolatok** területen válassza a **társított szolgáltatások** elemet.
1. Társított szolgáltatás hozzáadásához válassza az **új** lehetőséget.
1. Válassza ki a listából a Azure Data Lake Storage Gen2 csempét, és válassza a **Folytatás** lehetőséget.
1. Adja meg a hitelesítő adatait. A fiók kulcsa, az egyszerű szolgáltatásnév és a felügyelt identitás jelenleg támogatott hitelesítési típus. Válassza a kapcsolódás tesztelése lehetőséget a hitelesítő adatok helyességének ellenőrzéséhez. 
1. Miután végzett, válassza a **Létrehozás** lehetőséget.

## <a name="create-pipeline"></a>Folyamat létrehozása

A folyamatok egy adott tevékenységek végrehajtásának logikai folyamatát tartalmazzák. Ebben a szakaszban egy másolási tevékenységet tartalmazó folyamatot hoz létre, amely a 2. generációs Azure Data Lake adatait egy dedikált SQL-készletbe tölti be.

1. Lépjen a **hangszerelés** lapra. Válassza a folyamatok fejléc melletti plusz ikont, és válassza a **folyamat** lehetőséget.
1. Az **áthelyezés és átalakítás** területen a tevékenységek panelen húzza az **Adatmásolás** elemet a folyamat vászonra.
1. Válassza ki a másolási tevékenységet, és lépjen a **forrás** lapra. Az **új** elemre kattintva hozzon létre egy új forrás-adatkészletet.
1. Válassza a Azure Data Lake Storage Gen2 lehetőséget az adattárban, majd válassza a Folytatás lehetőséget.
1. Válassza a DelimitedText formátumot, és válassza a Folytatás lehetőséget.
1. A készlet tulajdonságai ablaktáblán válassza ki a létrehozott ADLS társított szolgáltatást. Adja meg a forrásadatok fájljának elérési útját, és adja meg, hogy az első sorban van-e fejléc. Importálhatja a sémát a file Store-ból vagy egy mintából. Ha elkészült, kattintson az OK gombra.
1. Nyissa meg a **fogadó lapot.** Új fogadó adatkészlet létrehozásához válassza az **új** lehetőséget.
1. Válassza ki Azure Data Lake Storage Gen2, és válassza a Folytatás lehetőséget.
1. Válassza a DelimitedText formátumot, és válassza a Folytatás lehetőséget.
1. A készlet tulajdonságai ablaktáblán válassza ki a létrehozott ADLS társított szolgáltatást. Itt adhatja meg annak a mappának az elérési útját, ahová az adatírást szeretné írni. Ha elkészült, kattintson az OK gombra.

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

Az Azure szinapszis Analytics adatintegrálásával kapcsolatos további információkért tekintse meg az adatok betöltése [egy DEDIKÁLT SQL-készletbe](data-integration-sql-pool.md) című cikket.