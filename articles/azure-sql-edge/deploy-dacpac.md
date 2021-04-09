---
title: SQL Database DACPAC és BACPAC-csomagok használata – Azure SQL Edge
description: Tudnivalók a dacpacs és a bacpacs az Azure SQL Edge-ben való használatáról
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392248"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>DACPAC-és BACPAC-csomagok beSQL Databasea az SQL Edge-ben

Az Azure SQL Edge egy IoT- és peremhálózati környezetekre optimalizált relációsadatbázis-motor. A Microsoft SQL Database Engine legújabb verziójára épül, amely piacvezető teljesítményt, biztonságot és lekérdezés-feldolgozási képességeket biztosít. Az Azure SQL Edge a SQL Server piacvezető, az adatbázis-kezelési képességeivel együtt biztosítja a valós idejű elemzési és összetett események feldolgozására szolgáló, beépített folyamatos átviteli képességet.

Az Azure SQL Edge olyan natív mechanizmust biztosít, amely lehetővé teszi [SQL Database DACPAC és BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) -csomagok üzembe helyezését az SQL Edge üzembe helyezése során vagy azt követően.

SQL Database dacpac-és bacpac-csomagok telepíthetők az SQL Edge-be a `MSSQL_PACKAGE` környezeti változó használatával. A környezeti változót a következők bármelyikével lehet konfigurálni.  
- A dacpac-és bacpac-fájlokat tartalmazó SQL-tárolóban található helyi mappa. Ez a mappa csatlakoztatási pontok vagy adatmennyiség-tárolók használatával képezhető le egy gazdagép kötetére. 
- Egy helyi fájl elérési útja az SQL-tárolón belül a dacpac vagy a bacpac fájlhoz. A fájl elérési útja a csatlakoztatási pontok vagy az adatmennyiség-tárolók használatával képezhető le egy gazdagép kötetére. 
- Egy helyi fájl elérési útja az SQL-tárolóban, amely a dacpac-vagy bacpac-fájlokat tartalmazó Zip-fájlhoz van hozzárendelve. A fájl elérési útja a csatlakoztatási pontok vagy az adatmennyiség-tárolók használatával képezhető le egy gazdagép kötetére. 
- Egy Azure Blob SAS URL-cím egy zip-fájlhoz, amely tartalmazza a dacpac és a bacpac fájlokat.
- Azure Blob SAS URL-cím egy dacpac vagy egy bacpac-fájlhoz. 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>SQL Database DAC-csomag használata az SQL Edge használatával

Ha SQL Database DAC-csomagot vagy BACPAC-fájlt szeretne telepíteni (vagy importálni) az `(*.dacpac)` `(*.bacpac)` Azure Blob Storage és egy zip-fájl használatával, kövesse az alábbi lépéseket. 

1. Hozzon létre/bontson ki egy DAC-csomagot, vagy exportáljon egy Bacpac-fájlt az alább említett mechanizmus használatával. 
    - SQL Database DAC-csomag létrehozása vagy kibontása. Lásd: [DAC kibontása egy adatbázisból](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) egy meglévő SQL Server adatbázis DAC-csomagjának létrehozásával kapcsolatban.
    - Telepített DAC-csomag vagy-adatbázis exportálása. Az [adatrétegbeli alkalmazások exportálásával](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) kapcsolatos információkért lásd: bacpac-fájl létrehozása meglévő SQL Server-adatbázishoz.

2. Zip a `*.dacpac` vagy a `*.bacpac` fájlt, és töltse fel egy Azure Blob Storage-fiókba. A fájlok Azure Blob Storage-ba való feltöltésével kapcsolatos további információkért lásd: [Blobok feltöltése, letöltése és listázása a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Közös hozzáférési aláírás létrehozása a zip-fájlhoz a Azure Portal használatával. További információ: [hozzáférés delegálása közös hozzáférésű aláírásokkal (SAS)](../storage/common/storage-sas-overview.md).

4. Frissítse az SQL Edge-modul konfigurációját, hogy tartalmazza a DAC-csomag közös hozzáférésű URI-JÁT. Az SQL Edge-modul frissítéséhez hajtsa végre a következő lépéseket:

    1. A Azure Portal lépjen a IoT Hub üzemelő példányra.

    2. A bal oldali ablaktáblán válassza a **IoT Edge** lehetőséget.

    3. A **IoT Edge** lapon keresse meg és válassza ki azt a IoT Edge, amelyben az SQL Edge-modul telepítve van.

    4. A **IoT Edge eszköz** eszköz lapon válassza a **modul beállítása** lehetőséget.

    5. A **modulok beállítása** lapon kattintson az Azure SQL Edge-modulra.

    6. A **IoT Edge frissítése** panelen válassza a **környezeti változók** lehetőséget. Adja hozzá a `MSSQL_PACKAGE` környezeti változót, és adja meg a fenti 3. lépésben létrehozott sas URL-címet a környezeti változó értékeként. 

    7. Válassza a **Frissítés** lehetőséget.

    8. A **modulok beállítása** lapon válassza a **felülvizsgálat + létrehozás** elemet.

    9. A **modulok beállítása** lapon válassza a **Létrehozás** lehetőséget.

5. A modul frissítése után a rendszer letölti a csomagokat, kicsomagolja és üzembe helyezi az SQL Edge-példányon.

Az Azure SQL Edge-tároló minden újraindításakor az SQL Edge megkísérli a tömörített fájl letöltését és a módosítások kiértékelését. Ha a rendszer a dacpac fájl új verzióját észlelte, a módosításokat a rendszer az adatbázishoz az SQL Edge-ben telepíti.

## <a name="known-issue"></a>Ismert probléma

Egyes DACPAC-vagy BACPAC-telepítések során a felhasználók a parancs időtúllépésével találkozhatnak, ami a DACPAC telepítési művelet hibáját eredményezi. Ha ezzel a problémával találkozik, használja a SQLPackage.exe (vagy az SQL-ügyfél eszközeit) a DACPAC vagy a BACPAC maually alkalmazásához. 

## <a name="next-steps"></a>Következő lépések

- [Az SQL Edge üzembe helyezése Azure Portalon keresztül](deploy-portal.md).
- [Adatfolyam-adatforrások](stream-data.md)
- [Gépi tanulás és AI a ONNX az SQL Edge-ben](onnx-overview.md)