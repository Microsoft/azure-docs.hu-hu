---
title: IoT Edge modulok beállítása az Azure SQL Edge-ben
description: A három részből álló Azure SQL Edge-oktatóanyag első részében a vasérc-szennyeződések előrejelzéséhez IoT Edge modulokat és kapcsolatokat kell beállítania.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 5d768fdc2540496769883d839cfbb4f009a2000c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077635"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>IoT Edge modulok és kapcsolatok beállítása

Az alábbi, a három részből álló oktatóanyagban az Iron Ore-szennyeződések előrejelzéséhez az Azure SQL Edge-ben a következő IoT Edge modulokat fogja beállítani:

- Azure SQL Edge
- Adatgenerátor IoT Edge modul

## <a name="specify-container-registry-credentials"></a>Tároló beállításjegyzékbeli hitelesítő adatainak megadása

Meg kell adni a tároló-beállításjegyzékek modul-rendszerképeket üzemeltető hitelesítő adatait. Ezek a tároló-beállításjegyzékben találhatók, amely az erőforráscsoporthoz lett létrehozva. Navigáljon a **hozzáférési kulcsok** szakaszhoz. Jegyezze fel a következő mezőket:

- Beállításjegyzék neve
- Bejelentkezési kiszolgáló
- Felhasználónév
- Jelszó

Most a IoT Edge modulban meg kell adnia a tároló hitelesítő adatait.

1. Navigáljon az erőforráscsoporthoz létrehozott IoT hubhoz.

2. Az **automatikus eszközkezelés** területen az **IoT Edge** szakaszban kattintson az **eszköz azonosítója** elemre. Ebben az oktatóanyagban az azonosító a következő: `IronOrePredictionDevice` .

3. Válassza a **modulok beállítása** szakaszt.

4. A **Container Registry hitelesítő adatok** területen adja meg a következő értékeket:

   | _Mező_   | _Érték_       |
   | -------   | -------       |
   | Name      | Beállításjegyzék neve |
   | Cím   | Bejelentkezési kiszolgáló  | 
   | Felhasználónév | Felhasználónév      | 
   | Jelszó  | Jelszó      | 
  
## <a name="build-push-and-deploy-the-data-generator-module"></a>Az adatgenerátor modul létrehozása, leküldése és üzembe helyezése

1. A [projektfájlok](https://github.com/microsoft/sqlsourabh/tree/main/SQLEdgeSamples/IoTEdgeSamples/IronOreSilica) klónozása a gépre.
2. Nyissa meg a **IronOre_Silica_Predict. SLN** fájlt a Visual Studio 2019 használatával
3. Frissítse a tároló beállításjegyzékének részleteit a **deployment.template.json** 
   ```json
   "registryCredentials":{
        "RegistryName":{
            "username":"",
            "password":""
            "address":""
        }
    }
   ```
4. A fájl **modules.js** frissítése a tároló-beállításjegyzék (vagy a modul adattára) megadásához
   ```json
   "image":{
        "repository":"samplerepo.azurecr.io/ironoresilicapercent",
        "tag":
    }
   ```
5. A projekt végrehajtása hibakeresési vagy kiadási módban annak biztosításához, hogy a projekt problémák nélkül fusson 
6. Küldje le a projektet a tároló-beállításjegyzékbe úgy, hogy a jobb gombbal a projekt nevére kattint, majd kiválasztja a **Build és a Leküldés IoT Edge modulokat**.
7. Telepítse az adatgenerátor modult IoT Edge modulként a peremhálózati eszközre. 

## <a name="deploy-the-azure-sql-edge-module"></a>Az Azure SQL Edge-modul üzembe helyezése

1. Az Azure SQL Edge-modul üzembe helyezéséhez kattintson a **+ Hozzáadás** , majd a **piactér modul** elemre. 

2. A **IoT Edge modul Marketplace** paneljén keressen rá az *Azure SQL Edge* kifejezésre, és válassza az *Azure SQL Edge Developer* szolgáltatást. 

3. Kattintson az újonnan hozzáadott *Azure SQL Edge* -modulra **IoT Edge modulok** területen az Azure SQL Edge-modul konfigurálásához. A konfigurációs beállításokkal kapcsolatos további információkért lásd: az [Azure SQL Edge üzembe helyezése](./deploy-portal.md).

4. Adja hozzá a `MSSQL_PACKAGE` környezeti változót az *Azure SQL Edge* -modul üzembe helyezéséhez, és adja meg az oktatóanyag első [részének](tutorial-deploy-azure-resources.md) 8. lépésében létrehozott adatbázis-dacpac fájl sas URL-címét.

5. Kattintson a **frissítés** gombra

6. Az **eszközön található modulok beállítása** lapon kattintson a **Tovább gombra: útvonalak >**.

7. Az **eszközön a modulok beállítása** lapon adja meg az útvonalakat a modulhoz IoT Edge hub-kommunikációhoz az alább leírtak szerint. Ügyeljen arra, hogy az alábbi útvonal-definíciókban frissítse a modulok nevét.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Például:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. Az **eszközön található modulok beállítása** lapon kattintson a **Tovább gombra: felülvizsgálat + létrehozás >**

8. Az **eszközön lévő modulok beállítása** lapon kattintson a **Létrehozás** gombra.

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Hozza létre és indítsa el a T-SQL streaming feladatot az Azure SQL Edge-ben.

1. Nyissa meg az Azure Data Studiót.

2. Az **üdvözlő** lapon kezdjen el egy új kapcsolattal a következő részletekkel:

   |_Mező_|_Érték_|
   |-------|-------|
   |Kapcsolat típusa| Microsoft SQL Server|
   |Kiszolgáló|A bemutatóhoz létrehozott virtuális gépen említett nyilvános IP-cím|
   |Felhasználónév|sa|
   |Jelszó|Az Azure SQL Edge-példány létrehozásakor használt erős jelszó|
   |Adatbázis|Alapértelmezett|
   |Kiszolgálócsoport|Alapértelmezett|
   |Name (Név) (nem kötelező)|Adjon meg egy opcionális nevet|

3. Kattintson a **kapcsolat** gombra.

4. A **fájl** menü lapon nyisson meg egy új jegyzetfüzetet, vagy használja a CTRL + N billentyűkombinációt.

5. Az új lekérdezési ablakban hajtsa végre az alábbi parancsfájlt a T-SQL streaming-feladatok létrehozásához. A szkript végrehajtása előtt ügyeljen rá, hogy módosítsa a következő változókat. 
   - *SQL_SA_Password:* Az Azure SQL Edge-modul telepítésekor megadott MSSQL_SA_PASSWORD érték. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. A következő lekérdezéssel ellenőrizheti, hogy az adatgeneráló modulból származó adatok továbbítva vannak-e az adatbázisba. 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


Ebben az oktatóanyagban központilag telepítettük az adatgenerátor modult és az SQL Edge-modult. Ezután létrehoztunk egy folyamatos átviteli feladatot az adatgenerációs modul által az SQL-hez generált adatok továbbításához. 

## <a name="next-steps"></a>Következő lépések

- [ML modell üzembe helyezése az Azure SQL Edge-ben a ONNX használatával](tutorial-run-ml-model-on-sql-edge.md)
