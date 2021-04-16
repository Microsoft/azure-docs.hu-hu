---
title: Adatok beszúrása dedikált SQL-készletbe
description: Ismerje meg, hogyan lehet adatokat egy dedikált SQL-készletbe Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: pipeline
ms.date: 11/03/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 79f0b1a6942f141f0a2d2d38adc5875d82e86d50
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567621"
---
# <a name="ingest-data-into-a-dedicated-sql-pool"></a>Adatok beszúrása dedikált SQL-készletbe

Ebből a cikkből megtudhatja, hogyan adatokat olvashat be egy Azure Data Lake Gen 2-tárfiókból egy dedikált SQL-készletbe a Azure Synapse Analytics.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés:** Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt hozzon létre egy ingyenes [Azure-fiókot.](https://azure.microsoft.com/free/)
* **Azure Storage-fiók:** A Azure Data Lake Storage Gen2-t használja *forrásadattárként.* Ha még nincs tárfiókja, a létrehozás lépéseit az [Azure Storage-fiók](../../storage/common/storage-account-create.md) létrehozása lehetőségnél láthatja.
* **Azure Synapse Analytics:** Dedikált SQL-készletet használ *fogadóadattárként.* Ha még nem rendelkezik egy Azure Synapse Analytics, a létrehozás lépéseit [lásd:](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Dedikált SQL-készlet létrehozása.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben Azure Synapse Analytics szolgáltatásban definiálhatja a kapcsolati adatokat más szolgáltatásokkal. Ebben a szakaszban hozzáad egy Azure Synapse Analytics és Azure Data Lake Storage Gen2 szolgáltatást.

1. Nyissa meg Azure Synapse Analytics felhasználói felületét, és nyissa meg a **Kezelés** lapot.
1. A **Külső kapcsolatok alatt** válassza a Csatolt szolgáltatások **lehetőséget.**
1. Csatolt szolgáltatás hozzáadásához válassza az Új **lehetőséget.**
1. Válassza a Azure Data Lake Storage Gen2 csempét a listából, majd válassza a **Folytatás lehetőséget.**
1. Adja meg a hitelesítő adatait. A fiókkulcs, a szolgáltatásnév és a felügyelt identitás jelenleg támogatott hitelesítési típusok. Válassza a Kapcsolat tesztelése lehetőséget a hitelesítő adatok helyességének ellenőrzéséhez. Miután végzett, válassza a **Létrehozás** lehetőséget.
1. Ismételje meg a 3–5. lépést, Azure Data Lake Storage Gen2 a Azure Synapse Analytics csempét, és adja meg a megfelelő kapcsolati hitelesítő adatokat. A Azure Synapse Analytics SQL-hitelesítés, a felügyelt identitás és a szolgáltatásnév jelenleg támogatott.

## <a name="create-pipeline"></a>Folyamat létrehozása

A folyamat egy tevékenységkészlet végrehajtásának logikai folyamatát tartalmazza. Ebben a szakaszban egy másolási tevékenységet tartalmazó folyamatot fog létrehozni, amely adatokat ADLS Gen2 egy dedikált SQL-készletbe.

1. Ugrás az **Integrálás lapra.** Válassza a pipelines fejléc melletti plusz ikont, majd a **Pipeline (Folyamat) lehetőséget.**
1. A **tevékenységek ablaktáblán az** Áthelyezés és átalakítás alatt húzza az Adatok **másolása gombra** a folyamatvászonra.
1. Válassza a lehetőséget a másolási tevékenységben, és ugrás a **Forrás lapra.** Új **forrásadatkészlet** létrehozásához válassza az Új lehetőséget.
1. Válassza Azure Data Lake Storage gen2 lehetőséget adattárként, majd válassza a Folytatás lehetőséget.
1. Válassza a Tagolt Szöveg lehetőséget formátumként, majd válassza a Folytatás lehetőséget.
1. A Tulajdonságok beállítása panelen válassza ki a létrehozott ADLS-hez kapcsolódó szolgáltatást. Adja meg a forrásadatok elérési útját, és adja meg, hogy az első sor tartalmaz-e fejlécet. Importálhatja a sémát a fájltárolóból vagy egy mintafájlból. Ha elkészült, kattintson az OK gombra.
1. Ugrás a **Fogadó lapra.** Új **fogadó** adatkészlet létrehozásához válassza az Új lehetőséget.
1. Válassza Azure Synapse Analytics lehetőséget adattárként, majd válassza a Folytatás lehetőséget.
1. A Tulajdonságok beállítása panelen válassza ki Azure Synapse Analytics létrehozott csatolt szolgáltatást. Ha egy meglévő táblába ír, válassza ki azt a legördülő menüből. Ellenkező esetben jelölje be **a Szerkesztés jelölőnégyzetet,** és írja be az új tábla nevét. Ha elkészült, kattintson az OK gombra
1. Ha táblát hoz létre, engedélyezze a **Tábla automatikus létrehozása beállítást** a tábla lehetőségmezőben.

## <a name="debug-and-publish-pipeline"></a>Folyamat hibakeresése és közzététele

Miután befejezte a folyamat konfigurálását, végrehajthat egy hibakeresési futtatásokat az összetevők közzététele előtt, így ellenőrizheti, hogy minden rendben van-e.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg. 
1. Ha a folyamat sikeresen lefutott, a felső eszköztáron válassza az Összes **közzététele lehetőséget.** Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és Synapse Analytics szolgáltatásban.
1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetekhez kattintson a jobb felső sarokban található harang gombra. 


## <a name="trigger-and-monitor-the-pipeline"></a>A folyamat aktiválása és monitorozása

Ebben a lépésben manuálisan aktiválja az előző lépésben közzétett folyamatot. 

1. Az **eszköztáron** válassza az Eseményindító hozzáadása, majd az Aktiválás **most lehetőséget.** A **Folyamatfuttatás** lapon válassza a **Befejezés** elemet.  
1. A bal **oldali oldalsávon** található Figyelás lapra kell ugrás. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A Műveletek oszlop  hivatkozásai segítségével megtekintheti a tevékenységek részleteit, és újrafuttathatja a folyamatot.
1. A folyamat futásához társított tevékenységfuttatások megtekintéséhez kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. Ebben a példában csak egy tevékenység van, így csak egy bejegyzés szerepel a listában. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). Válassza **a folyamatfuttatásokat** a felső sarokban a Folyamatfuttaik nézetre való visszaúthoz. A nézet frissítéséhez válassza a **Frissítés** parancsot.
1. Ellenőrizze, hogy az adatok megfelelően megírva van-e a dedikált SQL-készletben.


## <a name="next-steps"></a>Következő lépések

Az adatok adatintegrációval kapcsolatos további Azure Synapse Analytics az adatok a következőbe történő [Azure Data Lake Storage Gen2 ](data-integration-data-lake.md) talál.