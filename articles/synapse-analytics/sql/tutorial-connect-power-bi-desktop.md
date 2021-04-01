---
title: 'Oktatóanyag: kiszolgáló nélküli SQL-készlet összekötése Power BI Desktop & jelentés létrehozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan kapcsolódhat a kiszolgáló nélküli SQL-készlethez az Azure szinapszis Analytics Power BI szolgáltatásban, és hogyan hozhat létre egy bemutató jelentést egy nézet alapján.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 8dd3edd25d21bfcd0fde1bc8b5f103877d968c8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119985"
---
# <a name="tutorial-use-serverless-sql-pool-with-power-bi-desktop--create-a-report"></a>Oktatóanyag: kiszolgáló nélküli SQL-készlet használata a Power BI Desktop & jelentés létrehozása

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
>
> - Bemutató adatbázis létrehozása
> - Jelentéshez használt nézet létrehozása
> - Power BI Desktop összekötése kiszolgáló nélküli SQL-készlettel
> - Jelentés létrehozása nézet alapján

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- [Power bi Desktop](https://powerbi.microsoft.com/downloads/) – az adatmegjelenítéshez és a jelentés létrehozásához szükséges.
- [Azure szinapszis-munkaterület](../get-started-create-workspace.md) – adatbázis, külső adatforrás és nézet létrehozásához szükséges.

Nem kötelező:

- Egy SQL-lekérdezési eszköz, például [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)vagy [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

A következő paraméterek értékei:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Kiszolgáló nélküli SQL Pool szolgáltatás végpontjának címe    | Kiszolgáló neveként használatos                                   |
| Kiszolgáló nélküli SQL Pool szolgáltatás végponti régiója     | A mintákban használt tárterület meghatározására szolgál |
| A végpontok hozzáférésének felhasználóneve és jelszava | Végponthoz való hozzáféréshez használatos                               |
| A nézetek létrehozásához használni kívánt adatbázis     | A mintákban kiindulási pontként használt adatbázis       |

## <a name="1---create-database"></a>1 – adatbázis létrehozása

A bemutató környezethez hozzon létre egy saját bemutató-adatbázist. Ezzel az adatbázissal megtekintheti a metaadatokat, és nem tárolhatja a tényleges adatokat.

A következő Transact-SQL (T-SQL) parancsfájl futtatásával hozza létre a bemutató adatbázist (és szükség esetén dobja el a meglévő adatbázist):

```sql
-- Drop database if it exists
DROP DATABASE IF EXISTS Demo
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-data-source"></a>2 – adatforrás létrehozása

Egy adatforrás szükséges ahhoz, hogy a kiszolgáló nélküli SQL Pool szolgáltatás hozzáférjen a fájlokhoz a tárolóban. Hozzon létre egy olyan Storage-fiók adatforrását, amely ugyanabban a régióban található, mint a végpont. Bár a kiszolgáló nélküli SQL-készlet hozzáférhet a különböző régiókból származó Storage-fiókokhoz, és az azonos régióban található tárolók és végpontok jobb teljesítményt biztosítanak.

Hozza létre az adatforrást a következő Transact-SQL (T-SQL) parancsfájl futtatásával:

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a secret.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="3---prepare-view"></a>3 – nézet előkészítése

A következő Transact-SQL-(T-SQL-) parancsfájl futtatásával hozza létre a nézetet a Power BI külső bemutatójának alapján:

Hozza létre a nézetet az `usPopulationView` adatbázison belül `Demo` a következő lekérdezéssel:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS uspv;
```

A bemutatóban szereplő adat a következő adatkészleteket tartalmazza:

Az USA népessége nemek és faji hovatartozás szerint, a 2000-es és a 2010-es Decennial-népszámlálás a parketta formátumában.

| Mappa elérési útja                                                  | Leírás                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /Release parancsot                                                    | A demo Storage-fiókban lévő adatszülő mappa               |
| /Release/us_population_county/                               | Az USA-beli populációs adatfájlok parkettás formátumban, évről évre particionálva a kaptár/Hadoop particionálási sémával. |

## <a name="4---create-power-bi-report"></a>4 – Power BI jelentés létrehozása

Hozza létre a Power BI Desktop jelentést a következő lépések végrehajtásával:

1. Nyissa meg a Power BI Desktop alkalmazást, és válassza az **adatlekérdezés** lehetőséget.

   ![Nyissa meg Power BI asztali alkalmazást, és válassza az adatlekérdezés lehetőséget.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Válassza az **Azure**  >  **Azure SQL Database** lehetőséget. 

   ![Válassza ki az adatforrást.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Írja be annak a kiszolgálónak a nevét, ahol az adatbázis található a **kiszolgáló** mezőben, majd írja be az `Demo` adatbázis nevét. Válassza az **Importálás** lehetőséget, majd kattintson **az OK gombra**. 

   ![Válassza ki az adatbázist a végponton.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Válassza ki az előnyben részesített hitelesítési módszert:

    - Példa HRE 
  
        ![Kattintson a Bejelentkezés elemre.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Példa az SQL-bejelentkezéshez – írja be a felhasználónevét és a jelszavát.

        ![SQL-bejelentkezés használata.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Válassza ki a nézetet `usPopulationView` , majd válassza a **Betöltés** lehetőséget. 

   ![Válasszon ki egy nézetet a kiválasztott adatbázison.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Várjon, amíg a művelet befejeződik, majd megjelenik egy előugró üzenet `There are pending changes in your queries that haven't been applied` . Válassza a **módosítások alkalmazása** lehetőséget. 

   ![Kattintson a módosítások alkalmazása lehetőségre.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Várjon, amíg a **lekérdezési módosítások alkalmazása** párbeszédpanel eltűnik, ami eltarthat néhány percig. 

   ![Várjon, amíg a lekérdezés befejeződik.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. A betöltés befejeződése után válassza ki a következő oszlopokat ebben a sorrendben a jelentés létrehozásához:
   - countyName
   - feltöltése
   - stateName

   ![Válassza ki a kívánt oszlopokat a térképes jelentés létrehozásához.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a jelentéssel, törölje az erőforrásokat a következő lépésekkel:

1. A Storage-fiók hitelesítő adatainak törlése

   ```sql
   DROP EXTERNAL DATA SOURCE AzureOpenData
   ```

2. A nézet törlése

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Az adatbázis eldobása

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Következő lépések

Folytassa a [lekérdezési tároló fájljaival](develop-storage-files-overview.md) , hogy megtudja, hogyan kérdezheti le a tárolási fájlokat a szinapszis SQL használatával.