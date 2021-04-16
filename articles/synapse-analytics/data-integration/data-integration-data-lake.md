---
title: Be- és Azure Data Lake Storage Gen2
description: Ismerje meg, hogyan lehet adatokat Azure Data Lake Storage Gen2 Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: e3024fe1a8fe524a1deddef23a67d86a600b9394
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567604"
---
# <a name="ingest-data-into-azure-data-lake-storage-gen2"></a>Adatok be- és Azure Data Lake Storage Gen2 

Ebből a cikkből megtudhatja, hogyan adatokat olvashat be egyik helyről a másikra egy 2. generációs Azure Data Lake Gen (Azure Data Lake Gen 2) tárfiókban a Azure Synapse Analytics.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés:** Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt hozzon létre egy ingyenes [Azure-fiókot.](https://azure.microsoft.com/free/)
* **Azure Storage-fiók:** Az Azure Data Lake Gen 2-t használja *forrásadattárként.* Ha még nincs tárfiókja, a létrehozás lépéseit az [Azure Storage-fiók](../../storage/common/storage-account-create.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) létrehozása lehetőségnél láthatja.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben Azure Synapse Analytics szolgáltatásban definiálhatja a kapcsolati adatokat más szolgáltatásokkal. Ebben a szakaszban hozzáadja a virtuális Azure Synapse Analytics Azure Data Lake Gen2-t csatolt szolgáltatásként.

1. Nyissa meg Azure Synapse Analytics felhasználói felületét, és nyissa meg a **Kezelés** lapot.
1. A **Külső kapcsolatok alatt** válassza a Csatolt szolgáltatások **lehetőséget.**
1. Csatolt szolgáltatás hozzáadásához válassza az Új **lehetőséget.**
1. Válassza a Azure Data Lake Storage Gen2 csempét a listából, majd válassza a **Folytatás lehetőséget.**
1. Adja meg a hitelesítő adatait. A fiókkulcs, a szolgáltatásnév és a felügyelt identitás jelenleg támogatott hitelesítési típusok. Válassza a Kapcsolat tesztelése lehetőséget a hitelesítő adatok helyességének ellenőrzéséhez. 
1. Miután végzett, válassza a **Létrehozás** lehetőséget.

## <a name="create-pipeline"></a>Folyamat létrehozása

A folyamat egy tevékenységkészlet végrehajtásának logikai folyamatát tartalmazza. Ebben a szakaszban egy másolási tevékenységet tartalmazó folyamatot fog létrehozni, amely az Azure Data Lake Gen 2 adatait egy dedikált SQL-készletbe használja.

1. Ugrás az **Orchestrate (Vezénylés)** lapra. Válassza a pipelines fejléc melletti plusz ikont, majd a **Pipeline (Folyamat) lehetőséget.**
1. A **tevékenységek ablaktáblán az** Áthelyezés és átalakítás alatt húzza az Adatok **másolása gombra** a folyamatvászonra.
1. Válassza a lehetőséget a másolási tevékenységben, és ugrás a **Forrás lapra.** Új **forrásadatkészlet** létrehozásához válassza az Új lehetőséget.
1. Válassza Azure Data Lake Storage Gen2 lehetőséget adattárként, majd válassza a Folytatás lehetőséget.
1. Válassza a TagoltSzöveg lehetőséget formátumként, majd válassza a Folytatás lehetőséget.
1. A Tulajdonságok beállítása panelen válassza ki a létrehozott ADLS-hez kapcsolódó szolgáltatást. Adja meg a forrásadatok elérési útját, és adja meg, hogy az első sor tartalmaz-e fejlécet. Importálhatja a sémát a fájltárolóból vagy egy mintafájlból. Ha elkészült, kattintson az OK gombra.
1. Ugrás a **Fogadó lapra.** Új **fogadó** adatkészlet létrehozásához válassza az Új lehetőséget.
1. Válassza Azure Data Lake Storage gen2 lehetőséget adattárként, majd válassza a Folytatás lehetőséget.
1. Válassza a Tagolt Szöveg lehetőséget formátumként, majd válassza a Folytatás lehetőséget.
1. A Tulajdonságok beállítása panelen válassza ki a létrehozott ADLS-hez kapcsolódó szolgáltatást. Adja meg annak a mappának az elérési útját, ahová adatokat szeretne írni. Ha elkészült, kattintson az OK gombra.

## <a name="debug-and-publish-pipeline"></a>Folyamat hibakeresése és közzététele

Miután befejezte a folyamat konfigurálását, végrehajthat egy hibakeresési futtatásokat az összetevők közzététele előtt, így ellenőrizheti, hogy minden rendben van-e.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg. 
1. Ha a folyamat sikeresen lefutott, a felső eszköztáron válassza az Összes **közzététele lehetőséget.** Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és Synapse Analytics szolgáltatásban.
1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetekhez kattintson a jobb felső sarokban található harang gombra. 


## <a name="trigger-and-monitor-the-pipeline"></a>A folyamat aktiválása és monitorozása

Ebben a lépésben manuálisan aktiválja az előző lépésben közzétett folyamatot. 

1. Az **eszköztáron** válassza az Eseményindító hozzáadása, majd az Aktiválás **most lehetőséget.** A **Folyamatfuttatás** lapon válassza a **Befejezés** elemet.  
1. A bal **oldali oldalsávon** kattintson a Figyelás lapra. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A Műveletek oszlopban  található hivatkozásokkal megtekintheti a tevékenység részleteit, és újrafuttathatja a folyamatot.
1. A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Ebben a példában csak egy tevékenység van, így csak egy bejegyzés látható a listában. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). Válassza **a felül található Folyamatfuttasok** lehetőséget, hogy visszatérjön a Folyamatfuttaik nézetre. A nézet frissítéséhez válassza a **Frissítés** parancsot.
1. Ellenőrizze, hogy az adatok megfelelően íródottak-e a dedikált SQL-készletbe.


## <a name="next-steps"></a>Következő lépések

Az adatintegrációval kapcsolatos további Azure Synapse Analytics az Adatok beszúrása dedikált [SQL-készletbe](data-integration-sql-pool.md) című cikkben talál.