---
title: Azure Data Lake Analytics lekérdezése – Visual Studio
description: Ismerje meg, hogyan telepítheti a Data Lake Tools for Visual Studio eszközt, valamint hogyan fejleszthet és tesztelhet U-SQL-parancsfájlokat.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: d3812cb27d2d9ea7a49038f566f8b4cc5d779172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95241692"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure Data Lake és Stream Analytics eszközök közé tartoznak a két Azure-szolgáltatáshoz, a Azure Data Lake Analyticshoz és a Azure Stream Analytics kapcsolódó funkciók. A Azure Stream Analytics forgatókönyvekkel kapcsolatos további információkért lásd: [Azure stream Analytics Tools for Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Ez a cikk azt ismerteti, hogyan használható a Visual Studio Azure Data Lake Analytics-fiókok létrehozásához. Az [U-SQL](data-lake-analytics-u-sql-get-started.md)-ben megadhatja a feladatokat, és feladatokat küldhet a Data Lake Analytics szolgáltatásnak. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

> [!IMPORTANT]
> Javasoljuk, hogy a Visual Studio 2.3.3000.4 vagy újabb verziójára frissítsen Azure Data Lake-eszközökre. A korábbi verziók már nem tölthetők le, és mostantól elavultnak számítanak.
>
> 1. Ellenőrizze, hogy az Azure Data Lake Tools for Visual Studio 2.3.3000.4-esnél korábbi verzióját használja-e.
>
>    ![Az eszköz verziószámának ellenőrzése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Amennyiben egy, a 2.3.3000.4-esnél korábbi verziót használ, az Azure Data Lake Tools for Visual Studiót a letöltőközpont felkeresésével frissítheti:
>    - [A Visual Studio 2017 és 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 és 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Előfeltételek

* **Visual Studio**: Az Express kivételével minden kiadás támogatott.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK for .NET** 2.7.1-es vagy újabb verzió. Telepítse a [Webplatform-telepítővel](https://www.microsoft.com/web/downloads/platform.aspx).
* Egy **Data Lake Analytics** -fiók. Fiók létrehozásához lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Az Azure Data Lake Tools for Visual Studio telepítése

Ehhez az oktatóanyaghoz feltétel, hogy telepítve legyen a Data Lake Tools for Visual Studio. További információ: [Data Lake Tools for Visual Studio telepítése](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Csatlakozás Azure Data Lake Data Lake Analytics-fiókhoz

1. Nyissa meg a Visual Studiót.

1. Nyissa meg **Data Lake Analytics Explorert** az   >  **Data Lake Analytics Explorer** megtekintése lehetőség kiválasztásával.

1. Kattintson a jobb gombbal az **Azure** elemre, majd válassza **a kapcsolódás Microsoft Azure előfizetéshez** lehetőséget. **Jelentkezzen be a fiókjába**, és kövesse az utasításokat.

1. A **Server Explorerben** válassza az **Azure**  >  **Data Lake Analytics** elemet. Ekkor megjelenik a Data Lake Analytics-fiókok listája.

## <a name="write-your-first-u-sql-script"></a>Az első U-SQL parancsfájl megírása

A következő szöveg egy igen egyszerű U-SQL parancsfájl. Definiál egy kisebb adatkészletet, és az alapértelmezett Data Lake Store-ba írja az adatkészletet, amely a `/data.csv` nevű fájl.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Data Lake Analytics-feladat küldése

1. A Visual Studióban válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.

1. Válassza ki a **U-SQL-projekt** típusát, majd kattintson a **tovább** gombra. Az **új projekt konfigurálása** lapon válassza a **Létrehozás** lehetőséget.

   A Visual Studio létrehoz egy olyan megoldást, amely egy **script. usql** fájlt tartalmaz.

1. Illessze be a szkriptet az [első U-SQL-szkript írásához](#write-your-first-u-sql-script) a **script. usql** ablakba.

1. A **megoldáskezelő** kattintson a jobb gombbal a **script. usql** elemre, majd válassza a **parancsfájl küldése** lehetőséget.

1. A **küldési feladatokban** válassza ki a Data Lake Analytics fiókját, és válassza a **Küldés** lehetőséget.

   ![U-SQL Visual Studio-projekt elküldése](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

A feladat beküldése után megnyílik a **Feladat nézet** lap, amelyen megtekinthető a feladat előrehaladása.

* A **Feladat összegzése** a feladat összegzését jeleníti meg.
* A **Feladatgrafikon** a feladat előrehaladását jeleníti meg képi formában.
* A **Metaadat-műveletek** bemutatja a U-SQL-katalóguson végrehajtott összes műveletet.
* Az **Adatok** az összes bemenetet és kimenetet jeleníti meg.
* Az **Állapotelőzmények** az idővonallal és az állapottal kapcsolatos adatokat jeleníti meg.
* Az **au Analysis** azt mutatja, hogy hány au-t használt fel a feladatokban, és megismerheti a különböző au-kiosztási stratégiák szimulációit.
* A **Diagnosztika** részletes elemzést nyújt a feladat végrehajtásához és a teljesítmény optimalizálásához.

![U-SQL Visual Studio Data Lake Analytics-feladat teljesítménygrafikonja](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

A legutóbbi feladatok állapotának megtekintéséhez és a képernyő frissítéséhez válassza a **frissítés** lehetőséget.

## <a name="check-job-status"></a>Feladat állapotának ellenőrzése

1. A **Server Explorerben** válassza az **Azure**  >  **Data Lake Analytics** elemet.

1. Bontsa ki a Data Lake Analytics-fiók nevét.

1. Kattintson duplán a **Feladatok** elemre.

1. Válassza ki a korábban elküldött feladatot.

## <a name="see-the-job-output"></a>Feladat kimenetének megtekintése

1. A **Server Explorerben** keresse meg az elküldött feladatot.

1. Kattintson az **Adatok** lapra.

1. A **Feladatkimenetek** lapon jelölje be a `"/data.csv"` fájlt.

## <a name="next-steps"></a>Következő lépések

* [U-SQL-szkript futtatása a munkaállomáson teszteléshez és hibakereséshez](data-lake-analytics-data-lake-tools-local-run.md)
* [C#-kód hibaelhárítása U-SQL-feladatokban az Azure Data Lake Tools for Visual Studio Code használatával](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)
