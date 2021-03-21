---
title: Az Qlik-értelem összekapcsolásával Azure Cosmos DBhatja és megjelenítheti az adatait
description: Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek ahhoz, hogy a Azure Cosmos DB összekapcsolásával Qlik az értelemben, és láthatóvá tegye az adatait.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 2401786a82b6a3e5bf6c6a893a8e7cd3656f3402
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996957"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Az Qlik-értelem összekapcsolásával Azure Cosmos DBhatja és megjelenítheti az adatait
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

A Qlik Sense egy adatvizualizációs eszköz, amely különböző forrásokból származó adatok egyetlen nézetbe való egyesítésére szolgál. A Qlik Sense indexeli az összes lehetséges kapcsolatot az adataiban, így azonnali elemzéseket nyerhet az adatairól. A Qlik értelem használatával megjelenítheti Azure Cosmos DBi adataikat. Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek ahhoz, hogy a Azure Cosmos DB összekapcsolásával Qlik az értelemben, és láthatóvá tegye az adatait. 

> [!NOTE]
> A Qlik-hez való csatlakozás Azure Cosmos DB jelenleg támogatott az SQL API és a Azure Cosmos DB API-k csak MongoDB-fiókokhoz.

A Qlik-hez kapcsolódó Azure Cosmos DB a következővel lehet csatlakozni:

* Cosmos DB az SQL API-t az ODBC-összekötő használatával.

* Azure Cosmos DB API-MongoDB az Qlik Sense MongoDB-összekötő használatával (jelenleg előzetes verzióban érhető el).

* Azure Cosmos DB API-t a MongoDB és az SQL API-hoz a Qlik-alapú REST API-összekötő használatával.

* Cosmos DB Mongo DB API-t a Qlik Core-hoz készült gRPC-összekötő használatával.
Ez a cikk a Cosmos DB SQL API-hoz az ODBC-összekötő használatával történő kapcsolódás részleteit ismerteti.

Ez a cikk a Cosmos DB SQL API-hoz az ODBC-összekötő használatával történő kapcsolódás részleteit ismerteti.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő utasítások követése előtt győződjön meg arról, hogy a következő erőforrások állnak készen:

* Töltse le a [Qlik Sense Desktopot](https://www.qlik.com/us/try-or-buy/download-qlik-sense) , vagy állítsa be a Qlik-érzékelést az Azure-ban [az Qlik Sense Marketplace-elemek telepítésével](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* A [videós játékhoz tartozó adatfájlok](https://www.kaggle.com/gregorut/videogamesales)letöltése CSV formátumú. Ezeket az adattárakat egy Cosmos DB-fiókban fogja tárolni, és a Qlik értelemben láthatóvá válik.

* Hozzon létre egy Azure Cosmos DB SQL API-fiókot a rövid útmutató című cikk [fiók létrehozása](create-sql-api-dotnet.md#create-account) című szakaszában ismertetett lépések végrehajtásával.

* [Adatbázis és gyűjtemény létrehozása](create-sql-api-java.md#add-a-container) – a gyűjtemény átviteli sebességének értékeként 1000 ru/s értéket adhat meg. 

* Töltse be a minta videójáték értékesítési adatait a Cosmos DB-fiókjába. Az adatimportálást Azure Cosmos DB adatáttelepítési eszköz használatával végezheti el, vagy egy [szekvenciális](import-data.md#SQLSeqTarget) vagy [tömeges adatimportálást](import-data.md#SQLBulkTarget) is végezhet. Az Cosmos DB-fiókba való importáláshoz körülbelül 3-5 percet vesz igénybe.

* Töltse le, telepítse és konfigurálja az ODBC-illesztőt a [kapcsolódás Cosmos db ODBC-illesztővel](odbc-driver.md) című cikkben ismertetett lépések segítségével. A videojáték-alapú adatkészletek egyszerű adathalmazok, és nem kell szerkesztenie a sémát, csak az alapértelmezett gyűjtemény-leképezési sémát kell használnia.

## <a name="connect-qlik-sense-to-cosmos-db"></a>A Qlik Cosmos DB összekapcsolásának érzékelése

1. Nyissa meg a Qlik értelmet, és válassza az **új alkalmazás létrehozása** lehetőséget. Adja meg az alkalmazás nevét, majd válassza a **Létrehozás** lehetőséget.

   :::image type="content" source="./media/visualize-qlik-sense/create-new-qlik-sense-app.png" alt-text="Új Qlik Sense-alkalmazás létrehozása":::

2. Az új alkalmazás létrehozása után kattintson az **alkalmazás megnyitása** lehetőségre, és válassza az **adatok hozzáadása fájlokból és más forrásokból** lehetőséget. 

3. Az adatforrások területen válassza az **ODBC** lehetőséget az új kapcsolódási beállítás ablak megnyitásához. 

4. Váltson a **felhasználói DSN** -re, és válassza ki a korábban létrehozott ODBC-kapcsolatokat. Adja meg a kapcsolatok nevét, majd válassza a **Létrehozás** lehetőséget. 

   :::image type="content" source="./media/visualize-qlik-sense/create-new-connection.png" alt-text="Új kapcsolat létrehozása":::

5. A kapcsolatok létrehozása után kiválaszthatja az adatbázist, a gyűjtemény helyét, ahol a videojáték-adatforrások találhatók, majd megtekintheti azt.

   :::image type="content" source="./media/visualize-qlik-sense/choose-database-and-collection.png" alt-text="Az adatbázis és a gyűjtemény kiválasztása"::: 

6. Ezután válassza az **adathozzáadás** lehetőséget az Qlik értelembe való betöltéséhez. Az adatok Qlik való betöltését követően elemzéseket készíthet, és elemzéseket végezhet az adatokon. Használhatja az elemzéseket, vagy létrehozhat egy saját alkalmazást, amely a videojátékok értékesítéseit vizsgálja. Az alábbi képen látható 

   :::image type="content" source="./media/visualize-qlik-sense/visualize-data.png" alt-text="Adatok vizualizációja":::

### <a name="limitations-when-connecting-with-odbc"></a>Az ODBC-vel való csatlakozás korlátozásai 

Cosmos DB egy séma nélküli elosztott adatbázis, amely a fejlesztői igények alapján modellezhető illesztőprogramokkal rendelkezik. Az ODBC-illesztőhöz a sémával rendelkező adatbázis szükséges az oszlopok, az adattípusok és más tulajdonságok kikötéséhez. A hagyományos SQL-lekérdezés vagy a nem megfelelő DML-szintaxis Cosmos DB SQL API esetében nem alkalmazható, mert az SQL API nem ANSI SQL. Emiatt az ODBC-illesztőn keresztül kiadott SQL-utasítások fordítása Cosmos DB-specifikus SQL-szintaxisba történik, amely nem felel meg az összes szerkezetnek. A fordítási problémák megelőzése érdekében sémát kell alkalmaznia az ODBC-kapcsolatok beállításakor. A [Kapcsolódás ODBC-illesztővel](odbc-driver.md) című cikk javaslatokat és metódusokat biztosít a séma konfigurálásához. Hozza létre ezt a leképezést minden adatbázishoz/gyűjteményhez a Cosmos DB fiókon belül.

## <a name="next-steps"></a>Következő lépések

Ha más vizualizációs eszközt (például Power BI) használ, az alábbi dokumentum utasításait követve csatlakozhat hozzá.

* [Cosmos DB-adatmegjelenítés az Power BI-összekötő használatával](powerbi-visualize.md)
