---
title: 'Rövid útmutató: adatok betöltése dedikált SQL-készletbe a másolási tevékenység használatával'
description: A folyamat másolási tevékenységének használatával Azure Synapse Analytics adatokat a dedikált SQL-készletbe.
services: synapse-analytics
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: 8c0ebebd98a34a82b464707b82a650a1825676be
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566241"
---
# <a name="quickstart-load-data-into-dedicated-sql-pool-using-the-copy-activity"></a>Rövid útmutató: Adatok betöltése dedikált SQL-készletbe a másolási tevékenység használatával

Azure Synapse Analytics elemzési motorokat kínál az adatok be- és átalakításához, modellezéséhez és elemzéséhez. A dedikált SQL-készlet T-SQL-alapú számítási és tárolási képességeket biztosít. Miután létrehozta a dedikált SQL-készletet a Synapse-munkaterületen, az adatok betölthetőek, modellelhetőek, feldolgozhatóak és kézbesíthetőek a gyorsabb elemzés érdekében.

Ebből a rövid útmutatóból megtudhatja, hogyan tölt be adatokat a *Azure SQL Database-Azure Synapse Analytics.* Hasonló lépéseket követve más típusú adattárakból is másolhat adatokat. Ez a hasonló folyamat más források és fogadók adatmásolóira is vonatkozik.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, [a](https://azure.microsoft.com/free/) kezdés előtt hozzon létre egy ingyenes fiókot.
* Azure Synapse létrehozása: Hozzon létre egy Synapse-munkaterületet a Azure Portal a [Rövid útmutató: Synapse-munkaterület létrehozása útmutatását követve.](quickstart-create-workspace.md)
* Azure SQL Database: Ez az oktatóanyag adatokat másol az Adventure Works LT mintaadatkészletből a Azure SQL Database. Ezt a mintaadatbázist a SQL Database a [Mintaadatbázis](../azure-sql/database/single-database-create-quickstart.md)létrehozása a Azure SQL Database. Vagy használhat más adattárakat is hasonló lépésekkel.
* Azure Storage-fiók: A másolási művelet átmeneti területe az Azure Storage.  Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-account-create.md) ismertető cikkben találja.
* Azure Synapse Analytics: Dedikált SQL-készletet használ fogadóadattárként. Ha még nem rendelkezik egy Azure Synapse Analytics, a létrehozás lépéseit [lásd:](quickstart-create-sql-pool-portal.md) Dedikált SQL-készlet létrehozása.

### <a name="navigate-to-the-synapse-studio"></a>Lépjen a Synapse Studio

A Synapse-munkaterület létrehozása után kétféleképpen nyithatja meg a Synapse Studio:

* Nyissa meg a Synapse-munkaterületet a [Azure Portal.](https://ms.portal.azure.com/#home) Az **Első lépések** alatt válassza Synapse Studio Megnyitás lehetőséget.
* Nyissa [Azure Synapse Analytics,](https://web.azuresynapse.net/) és jelentkezzen be a munkaterületre.

Ebben a rövid útmutatóban az "adftest2020" nevű munkaterületet használjuk példaként. Automatikusan a kezdőlapra Synapse Studio meg.

![Synapse Studio kezdőlapja](media/doc-common-process/synapse-studio-home.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben Azure Synapse Analytics szolgáltatásban definiálhatja a kapcsolati adatokat más szolgáltatásokhoz. Ebben a szakaszban kétféle összekapcsolt szolgáltatást fog létrehozni: Azure SQL Database és Azure Data Lake Storage Gen2 (ADLS Gen2) összekapcsolt szolgáltatásokat.

1. A Synapse Studio bal oldali **navigációs sávon** válassza a Kezelés lapot.
1. A Külső kapcsolatok alatt válassza a Csatolt szolgáltatások lehetőséget.
  
   ![Új társított szolgáltatás létrehozása](media/doc-common-process/new-linked-service.png)

1. Csatolt szolgáltatás hozzáadásához válassza az Új **lehetőséget.**
1. Válassza **Azure SQL Database** gombra a katalógusban, majd válassza a **Folytatás lehetőséget.** A keresőmezőbe begépelheti az "sql" parancsot az összekötők szűréséhez.

   ![Új Azure SQL Database szolgáltatás létrehozása](media/quickstart-copy-activity-load-sql-pool/new-azure-sql-linked-service.png)

1. Az Új csatolt szolgáltatás lapon válassza ki a kiszolgáló és az adatbázis nevét a legördülő listából, majd adja meg a felhasználónevet és a jelszót. Kattintson **a Kapcsolat tesztelése** elemre a beállítások érvényesítéséhez, majd válassza a **Létrehozás lehetőséget.**

   ![A Azure SQL Database szolgáltatás konfigurálása](media/quickstart-copy-activity-load-sql-pool/azure-sql-linked-service-configuration.png)

1. Ismételje meg a 3–4. lépést, Azure Data Lake Storage Gen2 **a** katalógusból válassza a Azure Data Lake Storage Gen2 lehetőséget. Az Új csatolt szolgáltatás lapon válassza ki a tárfiók nevét a legördülő listából. Kattintson **a Kapcsolat tesztelése** elemre a beállítások érvényesítéséhez, majd válassza a **Létrehozás lehetőséget.** 

   ![A Azure Data Lake Storage Gen2](media/quickstart-copy-activity-load-sql-pool/adls-gen2-linked-service-configuration.png)
 
## <a name="create-a-pipeline"></a>Folyamat létrehozása

A folyamat egy tevékenységkészlet végrehajtásának logikai folyamatát tartalmazza. Ebben a szakaszban egy másolási tevékenységet tartalmazó folyamatot fog létrehozni, amely adatokat Azure SQL Database egy dedikált SQL-készletbe.

1. Ugrás az **Integrálás lapra.** Válassza a pipelines fejléc melletti plusz ikont, majd a Pipeline (Folyamat) lehetőséget.

   ![Új folyamat létrehozása](media/doc-common-process/new-pipeline.png)

1. A *Tevékenységek ablaktáblán az* Áthelyezés és átalakítás *alatt* húzza az **Adatok másolása gombra** a folyamatvászonra.
1. Válassza a lehetőséget a másolási tevékenységben, és ugrás a Forrás lapra. Új **forrásadatkészlet** létrehozásához válassza az Új lehetőséget.

   ![Forrásadatkészlet létrehozása](media/quickstart-copy-activity-load-sql-pool/new-source-dataset.png)

1. Válassza **Azure SQL Database** lehetőséget adattárként, majd válassza a **Folytatás lehetőséget.**
1. A Tulajdonságok *beállítása panelen* válassza ki az Azure SQL Database lépésben létrehozott csatolt szolgáltatást. 
1. A Tábla neve alatt válassza ki a következő másolási tevékenységhez használni kívánt mintatáblát. Ebben a rövid útmutatóban a "SalesLT.Customer" táblát használjuk példaként. 

   ![Forrásadatkészlet tulajdonságainak beállítása](media/quickstart-copy-activity-load-sql-pool/source-dataset-properties.png)
1. Ha **elkészült, kattintson** az OK gombra.
1. Válassza ki a másolási tevékenységet, és válassza a Fogadó lapot. Új **fogadó** adatkészlet létrehozásához válassza az Új lehetőséget.
1. Válassza **Azure Synapse dedikált SQL-készlet** adattárként lehetőséget, majd válassza a **Folytatás lehetőséget.**
1. A Tulajdonságok  **beállítása panelen** válassza ki a korábban létrehozott SQL Analytics-készletet. Ha egy meglévő táblába ír, a Tábla *neve* alatt válassza ki azt a legördülő menüből. Ellenkező esetben jelölje be az Edit (Szerkesztés) jelölőnégyzetet, és írja be az új tábla nevét. Ha **elkészült, kattintson** az OK gombra.
1. A Fogadó adatkészlet beállításainál engedélyezze a **Tábla beállításmezőben** a Tábla automatikus létrehozása beállítást.

   ![Automatikus létrehozás engedélyezése](media/quickstart-copy-activity-load-sql-pool/auto-create-table.png)

1. A Beállítások **lapon** jelölje be az **Átmeneti engedélyezése jelölőnégyzetet.** Ez a lehetőség akkor érvényes, ha a forrásadatok nem kompatibilisek a PolyBase-sel. Az **Előkészítési beállítások szakaszban** válassza ki Azure Data Lake Storage Gen2 az előkészítési tárolóként a korábbi lépésben létrehozott csatolt szolgáltatást. 

    A tároló az adatok átmeneti tárolására használatos, mielőtt betöltik őket Azure Synapse Analytics PolyBase használatával. A másolás befejezése után a rendszer automatikusan Azure Data Lake Storage Gen2 ideiglenes adatokat.

   ![Előkészítés engedélyezése](media/quickstart-copy-activity-load-sql-pool/staging-linked-service.png)

1. A folyamat érvényesítéséhez válassza az **Érvényesítés lehetőséget** az eszköztáron. A folyamatérvényesítés kimenete az oldal jobb oldalán látható. 

## <a name="debug-and-publish-the-pipeline"></a>A folyamat hibakeresése és közzététele

Miután befejezte a folyamat konfigurálását, végrehajthat egy hibakeresési futtatásokat az összetevők közzététele előtt, így ellenőrizheti, hogy minden rendben van-e.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg. 

   ![Hibakeresés a folyamaton](media/quickstart-copy-activity-load-sql-pool/debugging-result.png)

1. A folyamat sikeres futtatása után a felső eszköztáron válassza az **Összes közzététele lehetőséget.** Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és Synapse Analytics szolgáltatásban.
1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetekhez kattintson a jobb felső sarokban található harang gombra. 


## <a name="trigger-and-monitor-the-pipeline"></a>A folyamat aktiválása és monitorozása

Ebben a szakaszban manuálisan aktiválhatja az előző lépésben közzétett folyamatot. 

1. Az **eszköztáron** válassza az Eseményindító hozzáadása, majd az Aktiválás **most lehetőséget.** A **Folyamatfuttassa oldalon** kattintson az **OK gombra.**  
1. A bal **oldali oldalsávon** kattintson a Figyelás lapra. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. 
1. Ha a folyamat futása sikeresen befejeződött,  válassza a Folyamat neve oszlop alatti hivatkozást a tevékenységfutat részleteinek megtekintéséhez vagy a folyamat újrafuttassa. Ebben a példában csak egy tevékenység van, így csak egy bejegyzés látható a listában. 
1. A másolási művelettel kapcsolatos részletekért válassza a **Tevékenység** neve oszlop alatti Részletek hivatkozást (szemüveg **ikon).** Figyelheti a részleteket, például a forrásból a fogadóba másolt adatok mennyiségét, az adatátviteli sebességet, a megfelelő időtartammal megadott végrehajtási lépéseket és a használt konfigurációkat.

   ![Tevékenység részletei](media/quickstart-copy-activity-load-sql-pool/activity-details.png)

1. A folyamatfut futtatás nézetre való visszaváltáshoz válassza a **felül található Minden folyamatfutta** hivatkozást. A lista frissítéséhez kattintson a **Frissítés** gombra.
1. Ellenőrizze, hogy az adatok megfelelően íródottak-e a dedikált SQL-készletbe.


## <a name="next-steps"></a>Következő lépések

A támogatási lehetőségekről a következő Azure Synapse Analytics olvashat:

> [!div class="nextstepaction"]
> [Folyamat és tevékenységek](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Az összekötő áttekintése](../data-factory/connector-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Copy tevékenység](../data-factory/copy-activity-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)