---
title: A Azure Data Lake Analytics kód tesztelése
description: Megtudhatja, hogyan adhat hozzá tesztelési eseteket a U-SQL és a kiterjesztett C#-kód számára a Azure Data Lake Analyticshoz.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: 7310c67ef20a4134d4f613ea969c96802958bf62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015230"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>A Azure Data Lake Analytics kód tesztelése

Azure Data Lake a [U-SQL](data-lake-analytics-u-sql-get-started.md) nyelvet biztosítja. Az U-SQL a deklaratív SQL-t a kényszerített C#-mel kombinálva dolgozza fel az adatmennyiséget. Ebből a dokumentumból megtudhatja, hogyan hozhat létre tesztelési eseteket a U-SQL és a kiterjesztett C# felhasználó által definiált operátor (UDO) kódjához.

## <a name="test-u-sql-scripts"></a>U-SQL-szkriptek tesztelése

A U-SQL-szkript le van fordítva, és az Azure-ban vagy a helyi számítógépen futtatható végrehajtható kód futtatására van optimalizálva. A fordítási és optimalizálási folyamat a teljes U-SQL-szkriptet kezeli egészként. Minden utasításhoz nem végezheti el a hagyományos egység tesztelését. Azonban a U-SQL test SDK és a helyi Futtatás SDK használatával parancsfájl szintű teszteket végezhet.

### <a name="create-test-cases-for-u-sql-script"></a>Tesztelési esetek létrehozása U-SQL-parancsfájlhoz

A Visual studióhoz készült Azure Data Lake Tools lehetővé teszi U-SQL-parancsfájlok tesztelési eseteinek létrehozását.

1. Kattintson a jobb gombbal egy U-SQL-parancsfájlra Megoldáskezelő, majd válassza az **egység tesztelése** elemet.

1. Hozzon létre egy új tesztelési projektet, vagy szúrja be a tesztelési esetet egy meglévő tesztelési projektbe.

   ![Data Lake Tools for Visual Studio – a U-SQL-teszt projekt konfigurációjának létrehozása](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>A teszt adatforrás kezelése

U-SQL-parancsfájlok tesztelésekor be kell írnia a bemeneti fájlokat. A tesztelési célú adatkezeléshez kattintson a jobb gombbal a U-SQL projektre **megoldáskezelő**, majd válassza a **Tulajdonságok parancsot**. Megadhat egy forrást a **teszt adatforrásban**.

![A Visual Studio Data Lake eszközei – a Project test-adatforrás konfigurálása](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Ha a `Initialize()` U-SQL test SDK-ban hívja meg az illesztőfelületet, a rendszer létrehoz egy ideiglenes helyi adatgyökér-mappát a teszt projekt munkakönyvtárában. Az U-SQL-parancsfájl tesztelési eseteinek futtatása előtt a rendszer a teszt adatforrás mappában található összes fájlt és mappát átmásolja az ideiglenes helyi adatgyökér mappába. A teszt adatmappa elérési útjának pontosvesszővel való felosztásával további tesztelési adatforrás-mappákat is hozzáadhat.

### <a name="manage-the-database-environment-for-testing"></a>Az adatbázis-környezet kezelése teszteléshez

Ha a U-SQL-parancsfájlok U-SQL Database-objektumokat használnak vagy kérdeznek le, a U-SQL tesztelési esetek futtatása előtt inicializálnia kell az adatbázis-környezetet. Ez a megközelítés akkor lehet szükséges, ha tárolt eljárásokat hív meg. A `Initialize()` u-SQL test SDK felülete segít az u-SQL-projekt által hivatkozott összes adatbázis központi telepítéséhez a tesztelési projekt munkakönyvtárában lévő ideiglenes helyi adatgyökér-mappához.

További információ az u-SQL-projekthez tartozó U-SQL-adatbázis-projekt-referenciák kezeléséről: u-SQL-projektre mutató [hivatkozás](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Teszt eredményeinek ellenőrzése

Az `Run()` illesztőfelület a feladatok végrehajtási eredményét adja vissza. a *0* érték azt jelenti, hogy sikeres, és *1* hibát jelez. A kimenetek ellenőrzéséhez C#-érvényesítő függvények is használhatók.

### <a name="run-test-cases-in-visual-studio"></a>Tesztelési esetek futtatása a Visual Studióban

A U-SQL-szkriptek tesztelésére szolgáló projekt egy C# egység tesztelési keretrendszerre épül. A projekt összeállítása után válassza a   >  **Windows**  >  **test Explorer** tesztelése elemet. Tesztelési eseteket futtathat a **test Explorerben**. Másik lehetőségként kattintson a jobb gombbal a. cs fájlra az egység tesztben, és válassza a **tesztek futtatása** lehetőséget.

## <a name="test-c-udos"></a>C# Udo tesztelése

### <a name="create-test-cases-for-c-udos"></a>Tesztelési esetek létrehozása C# Udo

C# egység tesztelési keretrendszert használhat a C# felhasználó által definiált operátorok (Udo-k) teszteléséhez. A Udo tesztelésekor bemenetként kell előkészítenie a megfelelő **IRowset** -objektumokat.

Egy **IRowset** -objektumot kétféleképpen lehet létrehozni:

- Adatok betöltése fájlból **IRowset** létrehozásához:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Adatgyűjtési adatok használata **IRowset** létrehozásához:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Teszt eredményeinek ellenőrzése

A UDO függvények meghívása után a séma és a sorhalmaz értékének ellenőrzése a C# állítási függvények használatával végezhető el. Egy **U-SQL C# Udo Unit test projektet** adhat a megoldásához. Ehhez válassza a **fájl > új > projekt** elemet a Visual Studióban.

### <a name="run-test-cases-in-visual-studio"></a>Tesztelési esetek futtatása a Visual Studióban

A projekt összeállítása után válassza a   >  **Windows**  >  **test Explorer** tesztelése elemet. Tesztelési eseteket futtathat a **test Explorerben**. Másik lehetőségként kattintson a jobb gombbal a. cs fájlra az egység tesztben, és válassza a **tesztek futtatása** lehetőséget.

## <a name="run-test-cases-in-azure-pipelines"></a>Tesztelési esetek futtatása az Azure-folyamatokban<a name="run-test-cases-in-azure-devops"></a>

Az **U-SQL-parancsfájlok tesztelési projektjei** és a **c# Udo tesztelési projektek** is öröklik a c# egység tesztelési projektjeit. A [Visual Studio tesztelési feladata](/azure/devops/pipelines/test/getting-started-with-continuous-testing) az Azure-folyamatokban ezeket a tesztelési eseteket futtathatja.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>U-SQL-tesztelési esetek futtatása az Azure-folyamatokban

U-SQL-teszt esetén győződjön meg arról, hogy betölti a `CPPSDK` felépített számítógépet, majd adja át az `CPPSDK` elérési utat a következőnek: `USqlScriptTestRunner(cppSdkFolderFullPath: @"")` .

#### <a name="what-is-cppsdk"></a>Mi az a CPPSDK?

A CPPSDK egy olyan csomag, amely a Microsoft Visual C++ 14 és a Windows SDK 10.0.10240.0 tartalmazza. Ez a csomag tartalmazza azt a környezetet, amelyre a U-SQL futtatókörnyezet szükséges. Ezt a csomagot a Azure Data Lake Tools for Visual Studio telepítési mappája alatt érheti el:

- A Visual Studio 2015 esetében a `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- A Visual Studio 2017 esetében a `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- A Visual Studio 2019 esetében a `C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>CPPSDK előkészítése az Azure-folyamatok Build-ügynökével

Az Azure-folyamatokban a CPPSDK függőség előkészítésének leggyakoribb módja a következő:

1. Zip-fájl, amely tartalmazza a CPPSDK-kódtárakat.

1. Keresse meg a. zip-fájlt a verziókövetés rendszerébe. A. zip fájl gondoskodik arról, hogy a CPPSDK mappa minden könyvtárában bejelentkezzen, hogy a fájlok ne legyenek figyelmen kívül hagyva egy `.gitignore` fájl miatt.

1. Bontsa ki a. zip fájlt a Build folyamatban.

1. Mutasson `USqlScriptTestRunner` erre a kibontott mappára a Build számítógépén.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>C# UDO-tesztelési esetek futtatása az Azure-folyamatokban

C# UDO-teszt esetén ügyeljen arra, hogy a következő szerelvényekre hivatkozzon, amelyek a Udo esetében szükségesek.

- Microsoft. Analytics. interfaces
- Microsoft. Analytics. types
- Microsoft. Analytics. UnitTest

Ha [a Microsoft. Azure. DataLake. USQL. interfaces Nuget-csomagon](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/)keresztül hivatkozik rájuk, ügyeljen arra, hogy a létrehozási folyamat során vegyen fel egy Nuget-visszaállítási feladatot.

## <a name="next-steps"></a>Következő lépések

- [CI/CD-folyamat beállítása Azure Data Lake Analyticshoz](data-lake-analytics-cicd-overview.md)
- [U-SQL-szkript futtatása a helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
- [A U-SQL Database-projekt használata a U-SQL-adatbázis fejlesztéséhez](data-lake-analytics-data-lake-tools-develop-usql-database.md)