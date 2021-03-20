---
title: Erőforrások elérése Data Lake eszközökkel
description: Megtudhatja, hogyan használhatja Azure Data Lake-eszközöket Azure Data Lake Analytics erőforrások eléréséhez.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754658"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Erőforrások elérése Azure Data Lake eszközökkel

A VS Code-ban egyszerűen elérheti Azure Data Lake Analytics erőforrásait az Azure Adateszközök parancsaival vagy műveleteivel.

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>Integrálás Azure Data Lake Analytics paranccsal

Hozzáférés Azure Data Lake Analytics erőforrásokhoz a fiókok listázásához, a metaadatok eléréséhez és az elemzési feladatok megtekintéséhez.

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Az Azure-előfizetéshez tartozó Azure Data Lake Analytics fiókok listázása

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: fiókok listázása**. A fiókok megjelennek a **kimenet** ablaktáblán.

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Azure Data Lake Analytics metaadatok elérése

1. Válassza a CTRL + SHIFT + P billentyűkombinációt, majd írja be az **ADL: List Tables** elemet.
2. Válassza ki a Data Lake Analytics fiókok egyikét.
3. Válassza ki a Data Lake Analytics adatbázisok egyikét.
4. Válassza ki az egyik sémát. Láthatja a táblák listáját.

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Azure Data Lake Analytics feladatok megtekintése

1. Nyissa meg a parancssort (CTRL + SHIFT + P), és válassza az **ADL: feladatok megjelenítése** lehetőséget.
2. Válasszon Data Lake Analytics vagy helyi fiókot.
3. Várjon, amíg megjelenik a Feladatlista a fiókhoz.
4. Válasszon ki egy feladatot a feladatlistából. Data Lake eszközök megnyitják a feladatok nézetet a jobb oldali ablaktáblán, és a VS Code kimenetben jelenítenek meg néhány információt.

    ![Feladatlisták](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>Integrálás Azure Data Lake Store paranccsal

Azure Data Lake Store kapcsolódó parancsok a következőhöz használhatók:

- [Tallózás a Azure Data Lake Store-erőforrások között](#list-the-storage-path)
- [A Azure Data Lake Store fájl előnézetének megtekintése](#preview-the-storage-file)
- Töltse fel a fájlt közvetlenül a VS Code Azure Data Lake Storeba
- A fájl letöltése közvetlenül Azure Data Lake Store a VS Code-ból

### <a name="list-the-storage-path"></a>A tárolási útvonal listázása

### <a name="to-list-the-storage-path-through-the-command-palette"></a>A tár elérési útjának listázása a parancs palettáján

1. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, és válassza az **ADL: lista elérési útja** lehetőséget.
2. Válassza ki a mappát a listában, vagy válassza az **elérési út megadása** vagy a **gyökér elérési útja alapján való Tallózás** lehetőséget. (A **megadott elérési utat** példaként használjuk.)
3. Válassza ki a Data Lake Analytics fiókját.
4. Tallózással keresse meg vagy adja meg a tárolási mappa elérési útját (például/output/).  

A parancs paletta felsorolja az elérési út adatait a bejegyzései alapján.

![Tárolási útvonal eredményei](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

A relatív elérési út kilistázásának kényelmesebb módja a helyi menün keresztül.

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>A tár elérési útjának listázása a helyi menüben

Kattintson a jobb gombbal az elérési út sztringre, és válassza a **lista elérési útja** lehetőséget.

!["Lista elérési útja" a helyi menüben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>A tárolási fájl előnézete

1. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, és válassza az **ADL: előnézet fájl** elemet.
2. Válassza ki a Data Lake Analytics fiókját.
3. Adja meg az Azure Storage-fájl elérési útját (például/output/SearchLog.txt).

A fájl a VS Code-ban nyílik meg.

![A tárolási fájl előzetes megtekintésének lépései és eredményei](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

A fájl előnézetének egy másik módja a fájl teljes elérési útjának helyi menüjében vagy a fájl relatív elérési útja a parancsfájl-szerkesztőben.

### <a name="upload-a-file-or-folder"></a>Fájl vagy mappa feltöltése

1. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, és válassza a **fájl feltöltése** vagy a **feltöltés mappa** lehetőséget.
2. Ha a **fájl feltöltése** lehetőséget választotta, válasszon egy fájlt vagy több fájlt, vagy válassza a teljes mappát, ha a **mappa feltöltése** lehetőséget választotta. Ezután válassza a **Feltöltés** lehetőséget.
3. Válassza ki a Storage mappát a listában, vagy válassza az **elérési út megadása** vagy a **gyökér elérési útja alapján való Tallózás** lehetőséget. (A **megadott elérési utat** példaként használjuk.)
4. Válassza ki a Data Lake Analytics fiókját.
5. Tallózással keresse meg vagy adja meg a tárolási mappa elérési útját (például/output/).
6. A feltöltés célhelyének megadásához válassza az **aktuális mappa kiválasztása** lehetőséget.

![Fájlok vagy mappák feltöltésének lépései és eredményei](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

A fájlok tárterületre való feltöltésének másik módja a fájl teljes elérési útjának helyi menüjében vagy a fájl relatív elérési útja a parancsfájl-szerkesztőben.

Nyomon követheti [a feltöltés állapotát](#check-storage-tasks-status).

### <a name="download-a-file"></a>Fájl letöltése

A fájl letöltéséhez használja az **ADL: fájl letöltése** vagy **ADL: fájl letöltése (speciális)** parancsot.

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>Fájl letöltése az ADL: fájl letöltése (speciális) parancs használatával

1. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **Fájl letöltése (speciális)** lehetőséget.
2. A VS Code egy JSON-fájlt jelenít meg. Megadhatja a fájlelérési utakat, és egyszerre több fájlt is letölthet. Az utasítások a **kimenet** ablakban jelennek meg. A fájl vagy fájlok letöltésének folytatásához mentse (CTRL + S) a JSON-fájlt.

    ![Fájl letöltésére szolgáló elérési úttal rendelkező JSON-fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

A **kimeneti** ablak a fájl letöltési állapotát jeleníti meg.

![Kimeneti ablak letöltési állapottal](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

Nyomon követheti [a letöltési állapotot](#check-storage-tasks-status).

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>Fájl letöltése az ADL: fájl letöltése paranccsal

1. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, válassza a **Fájl letöltése** parancsot, majd a **mappa kiválasztása** párbeszédpanelen válassza ki a célmappát.

1. Válassza ki a mappát a listában, vagy válassza az **elérési út megadása** vagy a **gyökér elérési útja alapján való Tallózás** lehetőséget. (A **megadott elérési utat** példaként használjuk.)

1. Válassza ki a Data Lake Analytics fiókját.

1. Tallózással keresse meg vagy adja meg a tárolási mappa elérési útját (például/output/), majd válassza ki a letölteni kívánt fájlt.

![A fájlok letöltésének lépései és eredményei](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

A tárolási fájlok letöltésének egy másik módja a fájl teljes elérési útjának helyi menüjében vagy a fájl relatív elérési útja a parancsfájl-szerkesztőben.

Nyomon követheti [a letöltési állapotot](#check-storage-tasks-status).

### <a name="check-storage-tasks-status"></a>Tárolási feladatok állapotának keresése

Megjelenik a feltöltés és a letöltés állapot az állapotsoron. Válassza ki az állapotsort, majd az állapot megjelenik a **kimenet** lapon.

![Állapotsor és kimenet részletei](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>Integrálás Azure Data Lake Analytics az Intézőből

A bejelentkezést követően az Azure-fiók összes előfizetése megjelenik a bal oldali panelen az **Azure DATALAKE** alatt.

![Data Lake Explorer](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics metaadatok navigációja

Bővítse ki Azure-előfizetését. A **u-SQL-adatbázisok** csomópont alatt böngészhet a u-SQL-adatbázison, és megtekintheti a mappákat, például a **sémákat**, a **hitelesítő adatokat**, a **szerelvényeket**, a **táblákat** és az **indexeket**.

### <a name="data-lake-analytics-metadata-entity-management"></a>Metaadat-entitások Data Lake Analytics kezelése

Bontsa ki az **U-SQL-adatbázisok** csomópontot. Létrehozhat egy adatbázist, sémát, táblázatot, táblát, típust, indexet vagy statisztikát úgy, hogy a jobb gombbal a megfelelő csomópontra kattint, majd kiválasztja a helyi menüben **létrehozni kívánt parancsfájlt** . A megnyitott parancsfájl lapon szerkessze a szkriptet az igényeinek megfelelően. Ezt követően küldje el a feladatot úgy, hogy a jobb gombbal rákattint, és kiválasztja az **ADL: submit feladatot**.

Miután befejezte az elem létrehozását, kattintson a jobb gombbal a csomópontra, majd válassza a **frissítés** lehetőséget az elem megjelenítéséhez. Az elemet úgy is törölheti, ha a jobb gombbal kattint, majd kiválasztja a **Törlés** lehetőséget.

!["Parancsfájl létrehozása" parancs a Data Lake Explorer helyi menüjében](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![Az új elemmel kapcsolatos parancsfájl lapja](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics szerelvény regisztrációja

A megfelelő adatbázisban lévő szerelvények regisztrálásához kattintson a jobb gombbal a **szerelvények** csomópontra, majd válassza a **szerelvény regisztrálása** lehetőséget.

!["Assembly regisztrálása" parancs a szerelvények csomópont helyi menüjében](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>Integrálás Azure Data Lake Store az Intézőből

**Data Lake Store** keresése:

- Kattintson a jobb gombbal a mappa csomópontjára, majd a **frissítés**, a **Törlés**, a **feltöltés**, a **mappa feltöltése**, a **relatív elérési út másolása** és a **teljes elérésiút** -parancsok másolása parancsra a helyi menüben.

   ![A Data Lake Explorerben található mappa csomópontjának helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- Kattintson a jobb gombbal a fájl csomópontra, majd használja az **előnézet**, a **Letöltés**, a **Törlés**, a **kinyerési parancsfájl létrehozása** (csak CSV-, TSV-és txt-fájlok esetén elérhető), a **relatív elérési út másolása** és a **teljes elérésiút** -parancsok másolása a helyi menüben.

   ![A Data Lake Explorerben található fájlhoz tartozó helyi menüparancsok](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>Integráció az Azure Blob Storage-nal az Intézőből

Tallózás a blob Storage-ban:

- Kattintson a jobb gombbal a blob-tároló csomópontra, majd használja a helyi menüben a **frissítés**, a **blob-tároló** és a **Blobok feltöltése** parancsot.

   ![BLOB-tároló csomópontjának helyi menü parancsai a blob Storage-ban](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- Kattintson a jobb gombbal a mappa csomópontjára, majd használja a helyi menüben a **Blobok** **frissítése** és feltöltése parancsot.

   ![A blob Storage alatti mappa csomópontjának helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- Kattintson a jobb gombbal a fájl csomópontra, majd használja az **előnézet/szerkesztés**, **Letöltés**, **Törlés**, **létrehozási parancsfájl** (csak CSV-, TSV-és txt-fájlok esetén elérhető), a **relatív elérési út másolása** és a **teljes elérésiút** -parancsok másolása a helyi menüben.

    ![A blob Storage alatt lévő fájl csomópontjának helyi menü parancsai](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>A Data Lake Explorer megnyitása a portálon

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg a **nyílt webes Azure Storage Explorer** , vagy kattintson a jobb gombbal egy relatív elérési útra, vagy a parancsfájl-szerkesztő teljes elérési útjára, majd válassza a **webes Azure Storage Explorer megnyitása** lehetőséget.
3. Válasszon Data Lake Analytics fiókot.

Data Lake eszközök megnyitják az Azure Storage elérési útját a Azure Portalban. A fájl elérési útját és előnézetét a webről tekintheti meg.

## <a name="additional-features"></a>További funkciók

A VS Code-hoz készült Data Lake Tools a következő funkciókat támogatja:

- **IntelliSense automatikus kiegészítés**: a javaslatok az előugró ablakokban jelennek meg, például a kulcsszavakat, a metódusokat és a változókat. A különböző ikonok különböző típusú objektumokat jelölnek:

  - Scala adattípus
  - Összetett adattípus
  - Beépített UDT
  - .NET-gyűjtemény és-osztályok
  - C#-kifejezések
  - Beépített C# UDF, Udo és UDAAGs
  - U-SQL függvények
  - U-SQL-ablakkezelő függvények

    ![IntelliSense-objektumok típusai](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **IntelliSense automatikus kiegészítése Data Lake Analytics metaadatokon**: Data Lake eszközök helyileg töltik le a Data Lake Analytics metaadat-információkat. Az IntelliSense szolgáltatás automatikusan kitölti az objektumokat a Data Lake Analytics metaadataiból. Ezek az objektumok a következők: adatbázis, séma, tábla, nézet, tábla értékű függvény, eljárások és C# szerelvények.

  ![IntelliSense-metaadatok](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense-hibajelző**: Data Lake eszközök az U-SQL és a C# szerkesztési hibáit aláhúzzák.
- A **szintaxis kiemeli**: Data Lake eszközök színeket használnak az elemek, például változók, kulcsszavak, adattípusok és függvények megkülönböztetéséhez.

    ![Szintaxis különböző színekkel](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Javasoljuk, hogy a Visual Studio 2.3.3000.4 vagy újabb verziójára frissítsen Azure Data Lake-eszközökre. A korábbi verziók már nem tölthetők le, és mostantól elavultnak számítanak.  

## <a name="next-steps"></a>Következő lépések

- [A U-SQL fejlesztése Python, R és C Sharp Azure Data Lake Analytics a VS Code-ban](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL helyi Futtatás és helyi hibakeresés a Visual Studio Code-ban](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Oktatóanyag: az Azure Data Lake Analytics első lépései](data-lake-analytics-get-started-portal.md)
- [Oktatóanyag: U-SQL-parancsfájlok fejlesztése a Visual studióhoz készült Data Lake Tools használatával](data-lake-analytics-data-lake-tools-get-started.md)