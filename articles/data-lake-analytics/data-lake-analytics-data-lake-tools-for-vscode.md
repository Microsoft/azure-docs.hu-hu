---
title: A Visual Studio Code-hoz készült Azure Data Lake-eszközök használata
description: Megtudhatja, hogyan használhatja az Azure Data Lake Tools for Visual Studio Code-et U-SQL-szkriptek létrehozására, tesztelésére és futtatására.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e1d74795fd25019e205f4b7b1d2bac1b67107e2d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878577"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>A Visual Studio Code-hoz készült Azure Data Lake-eszközök használata

Ebből a cikkből megtudhatja, hogyan hozhat létre, tesztelhet és futtathat U-SQL-szkripteket az Azure Data Lake Tools for Visual Studio Code (VS Code) használatával. Az alábbi videó az alábbi információkat is tartalmazza:

[![Videólejátszó: Azure Data Lake Tools for VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Előfeltételek

Az Azure Data Lake Tools for VS Code a Windows, Linux és macOS rendszert támogatja. A U-SQL helyi futtatása és a helyi hibakeresés csak Windows rendszeren működik.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

macOS és Linux esetén:

- [.NET 5.0 SDK](https://dotnet.microsoft.com/download)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Az Azure Data Lake Tools telepítése

Az előfeltételek telepítése után telepítheti az Azure Data Lake Tools for VS Code-et.

### <a name="to-install-azure-data-lake-tools"></a>Az Azure Data Lake Tools telepítése

1. Nyissa meg a Visual Studio Code-ot.
2. A **bal oldali panelen** válassza a Bővítmények lehetőséget. A **keresőmezőbe írja be az Azure Data Lake Tools (Azure Data** Lake-eszközök) adatokat.
3. Válassza **az** **Azure Data Lake Tools** melletti Telepítés lehetőséget.

   ![A Data Lake Tools telepítésének kiválasztásai](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Néhány másodperc elteltével az **Install (Telepítés) gomb Reload** **(Újratöltés) lesz.**
4. Az  Azure Data Lake Tools bővítmény aktiválásához válassza az **Újrabetöltés** lehetőséget.
5. A megerősítéshez válassza az **Ablak újratöltése** lehetőséget. Az **Azure Data Lake Tools a** Bővítmények **panelen látható.**

## <a name="activate-azure-data-lake-tools"></a>Az Azure Data Lake Tools aktiválása

Hozzon létre egy .usql fájlt, vagy nyisson meg egy meglévő .usql fájlt a bővítmény aktiválásához.

## <a name="work-with-u-sql"></a>U-SQL használata

A U-SQL-hez meg kell nyitnia egy U-SQL-fájlt vagy egy mappát.

### <a name="to-open-the-sample-script"></a>A minta szkript megnyitása

Nyissa meg a parancskataccsal (Ctrl+Shift+P), és írja be az **ADL: Open Sample Script parancsot.** Megnyitja a minta egy másik példányát. Ezen a példányon egy szkriptet is szerkeszthet, konfigurálhat és elküldhet.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>A U-SQL-projekt mappájának megnyitása

1. A Visual Studio Code-ból válassza a **Fájl menüt,** majd a **Mappa megnyitása lehetőséget.**
2. Adjon meg egy mappát, majd válassza a **Mappa kiválasztása lehetőséget.**
3. Válassza a **Fájl menüt,** majd az Új **lehetőséget.** A rendszer hozzáad egy Untitled-1 fájlt a projekthez.
4. Adja meg a következő kódot a Untitled-1 fájlban:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   KIMENET @departments     A "/Output/departments.csv" KIMENETHEZ A Outputters.Csv();

    A szkript létrehoz egy departments.csv fájlt, amely a /output mappában található adatokat tartalmaz.

5. Mentse a fájlt **myUSQL.usql** fájlként a megnyitott mappába.

### <a name="to-compile-a-u-sql-script"></a>U-SQL-szkript fordítása

1. A Ctrl+Shift+P billentyűkombinációval nyissa meg a parancskataccsal.
2. Adja meg **az ADL: Fordítási szkript szöveget.** A fordítás eredményei a Kimenet **ablakban jelennek** meg. Jobb gombbal is kattinthat egy szkriptfájlra, majd az **ADL: Compile Script (ADL:** Szkript fordítása) lehetőséget választva lefordíthat egy U-SQL-feladatot. A fordítás eredménye megjelenik a Kimenet **panelen.**

### <a name="to-submit-a-u-sql-script"></a>U-SQL-szkript elküldése

1. A Ctrl+Shift+P billentyűkombinációval nyissa meg a parancskatapalettat.
2. Adja meg **az ADL: Submit Job (ADL: Feladat elküldése) adhatja meg a következőt:**. Jobb gombbal is kattinthat egy szkriptfájlra, majd kiválaszthatja az **ADL: Submit Job (ADL: Feladat elküldése) lehetőséget.**

Miután elküld egy U-SQL-feladatot, a beküldési naplók a VS Code **Output (Kimenet)** ablakában jelennek meg. A feladat nézet a jobb oldali panelen jelenik meg. Ha a beküldés sikeres, a feladat URL-címe is megjelenik. A feladat URL-címét megnyithatja egy webböngészőben a valós idejű feladat állapotának nyomon követéséhez.

A feladatnézet **SUMMARY (ÖSSZEGZÉS)** lapján megtekintheti a feladat részleteit. A fő függvények közé tartozik a szkriptek újrakubálása, a szkriptek megkettőzése és a portálon való megnyitás. A feladatnézet **DATA** (ADATOK) lapján megtekintheti a bemeneti fájlokat, a kimeneti fájlokat és az erőforrásfájlokat. A fájlok letölthetők a helyi számítógépre.

![Összegzés lap a feladatnézetben](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Adatok lap a feladatnézetben](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Az alapértelmezett környezet beállítása

Ha nem adott meg paramétereket a fájlokhoz, beállíthatja az alapértelmezett környezetet, hogy ezt a beállítást az összes szkriptfájlra alkalmazza.

1. A Ctrl+Shift+P billentyűkombinációval nyissa meg a parancskatapalettat.
2. Adja meg **az ADL-t: Állítsa be az alapértelmezett környezetet.** Vagy kattintson a jobb gombbal a szkriptszerkesztőre, és válassza **az ADL: Set Default Context (ADL: Alapértelmezett környezet beállítása) lehetőséget.**
3. Válassza ki a kívánt fiókot, adatbázist és sémát. A rendszer a konfigurációs fájlban xxx_settings.jsmenti a beállítást.

   ![Alapértelmezett környezetként beállított fiók, adatbázis és séma](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Szkriptparaméterek beállítása

1. A Ctrl+Shift+P billentyűkombinációval nyissa meg a parancskataccsal.
2. Adja meg **az ADL: Set Script Parameters (ADL: Szkriptparaméterek beállítása) szöveget.**
3. A xxx_settings.jsfájlon a következő tulajdonságokkal nyílik meg:

   - **account:** Azure Data Lake Analytics fiók az Azure-előfizetésében, amely a U-SQL-feladatok fordításához és futtatásához szükséges. A U-SQL-feladatok fordítása és futtatása előtt konfigurálnia kell a számítógépfiókot.
   - **database:** Egy adatbázis a fiókjában. Az alapértelmezett érték a **master.**
   - **schema:** Egy séma az adatbázis alatt. Az alapértelmezett érték **a dbo.**
   - **optionalSettings**:
        - **priority**: A prioritási tartomány 1 és 1000 között van, és 1 a legmagasabb prioritás. Az alapértelmezett érték **1000**.
        - **degreeOfParallelism:** A párhuzamosság tartománya 1 és 150 között van. Az alapértelmezett érték a fiókban engedélyezett maximális párhuzamosság Azure Data Lake Analytics.

   ![A JSON-fájl tartalma](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> A konfiguráció mentése után a fiók, az adatbázis és a séma adatai megjelennek a megfelelő .usql fájl bal alsó sarkában található állapotsávon, ha nincs alapértelmezett környezet beállítva.

### <a name="to-set-git-ignore"></a>A Git figyelmen kívül hagyása beállításának beállítása

1. A Ctrl+Shift+P billentyűkombinációval nyissa meg a parancskatapalettat.
2. Adja meg **az ADL-t: Állítsa be a Git Ignore (Git figyelmen kívül hagyása) beállítását.**

   - Ha nem található **.gitIgnore** fájl a VS Code munkamappában, a rendszer létrehoz egy **.gitIgnore** nevű fájlt a mappában. Alapértelmezés szerint négy elem (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) van hozzáadva a fájlhoz. Szükség esetén további frissítéseket is el lehet edni.
   - Ha már rendelkezik **.gitIgnore** fájllal a VS Code munkamappában, az eszköz négy elemet **(usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj)** a **.gitIgnore** fájlba ad hozzá, ha a négy elem nem szerepel a fájlban.

   ![A .gitIgnore fájl elemei](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>A kód mögötti fájlok: C Sharp, Python és R

Az Azure Data Lake Tools több egyéni kódot is támogat. Útmutatásért lásd: [Develop U-SQL with Python, R, and C Sharp for Azure Data Lake Analytics in VS Code (U-SQL fejlesztése Python, R és C Sharp használatával Azure Data Lake Analytics VS Code-ban).](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)

## <a name="work-with-assemblies"></a>Szerelvények

A szerelvények fejlesztésére vonatkozó információkért lásd: [U-SQL-szerelvények fejlesztése Azure Data Lake Analytics feladatokhoz.]()

A Data Lake Tools használatával egyéni kódszerelmeket regisztrálhat a Data Lake Analytics katalógusban.

### <a name="to-register-an-assembly"></a>Szerelvény regisztrálása

A szerelvényt az **ADL: Szerelvény** regisztrálása vagy **az ADL: Szerelvény regisztrálása (Speciális) paranccsal regisztrálhatja.**

### <a name="to-register-through-the-adl-register-assembly-command"></a>Regisztrálás az ADL: Szerelvény regisztrálása paranccsal

1. A Ctrl+Shift+P billentyűkombinációval nyissa meg a parancskatapalettat.
2. Írja be **a következőt: ADL: Szerelvény regisztrálása.**
3. Adja meg a helyi szerelvény elérési útját.
4. Válasszon ki egy Data Lake Analytics fiókot.
5. Válasszon ki egy adatbázist.

A portál megnyílik egy böngészőben, és megjeleníti a szerelvényregisztrációs folyamatot.  

Az **ADL:** Szerelvény regisztrálása parancs aktiválásának kényelmesebb módja, ha a jobb gombbal a .dll fájlra kattint a Fájlkezelő.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Regisztráció az ADL: Szerelvény regisztrálása (speciális) paranccsal

1. A Ctrl+Shift+P billentyűkombinációval nyissa meg a parancskataccsal.
2. Írja be **az ADL: Register Assembly (Advanced) (Szerelvény regisztrálása (Speciális) ) gépet.**
3. Adja meg a helyi szerelvény elérési útját.
4. Megjelenik a JSON-fájl. Szükség esetén tekintse át és szerkessze a szerelvényfüggőségeket és az erőforrás-paramétereket. Az utasítások a Kimenet **ablakban jelennek** meg. A szerelvényregisztrációhoz való folytatáshoz mentse (Ctrl+S) a JSON-fájlt.

   ![Szerelvényfüggőségeket és erőforrás-paramétereket is tartalmazó JSON-fájl](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Az Azure Data Lake Tools automatikusan jelzi, hogy a DLL rendelkezik-e szerelvényfüggőségekkel. A függőségek az észlelésük után megjelennek a JSON-fájlban.
>- A DLL-erőforrásokat (például .txt, .png és .csv) a szerelvényregisztráció részeként töltheti fel.

Az **ADL: Szerelvény regisztrálása (speciális)** parancs aktiválásának másik módja, ha a jobb gombbal a .dll fájlra kattint a Fájlkezelő.

Az alábbi U-SQL-kód egy szerelvény hívását mutatja be. A mintában a szerelvény neve *test*.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Helyi U-SQL-futtatás és helyi hibakeresés használata Windows-felhasználók számára

A U-SQL helyi futtatása helyileg teszteli a helyi adatokat, és ellenőrzi a szkriptet, mielőtt a kód közzé Data Lake Analytics. A helyi hibakeresési funkcióval a következő feladatokat használhatja, mielőtt a rendszer elküldné a kódot a Data Lake Analytics:

- Hibakeresés a C#-kód mögött.
- Lépjen végig a kódon.
- Ellenőrizze helyileg a szkriptet.

A helyi futtatás és a helyi hibakeresési funkció csak Windows-környezetekben működik, és macOS- és Linux-alapú operációs rendszereken nem támogatott.

A helyi futtatás és a helyi hibakeresés utasításaiért lásd: [U-SQL](data-lake-tools-for-vscode-local-run-and-debug.md)helyi futtatás és helyi hibakeresés a Visual Studio kód használatával.

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Mielőtt U-SQL-szkripteket fordíthat és futtathat Data Lake Analytics, csatlakoznia kell az Azure-fiókjához.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Csatlakozás az Azure-hoz parancs használatával

1. A Ctrl+Shift+P billentyűkombinációval nyissa meg a parancskatapalettat.

2. Írja be **az ADL: Bejelentkezés adatokat.** A bejelentkezési adatok a jobb alsó sarokban jelennek meg.

   ![A bejelentkezési parancs beírása](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Bejelentkezéssel és hitelesítéssel kapcsolatos értesítések](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Válassza **a Másolás & Megnyitás lehetőséget** a bejelentkezési weblap [megnyitásához.](https://aka.ms/devicelogin) Illessze be a kódot a mezőbe, majd válassza a **Folytatás lehetőséget.**

    ![Bejelentkezési weblap](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Kövesse az utasításokat a weboldalról való bejelentkezéshez. A csatlakozás után az Azure-fiók neve megjelenik a VS Code ablakának bal alsó sarkában található állapotsávon.

> [!NOTE]
>
> - A Data Lake Tools automatikusan aláírja a következő alkalommal, ha nem jelentkezik ki.
> - Ha a fiókjában két tényező van engedélyezve, javasoljuk, hogy PIN-kód használata helyett használjon telefonos hitelesítést.

A kijelentkezéshez írja be az **ADL: Kijelentkezés parancsot.**

### <a name="to-connect-to-azure-from-the-explorer"></a>Csatlakozás az Azure-hoz az Explorerből

**Bontsa ki az AZURE DATALAKE** szolgáltatásokat, válassza a Bejelentkezés az **Azure-ba** lehetőséget, majd kövesse a 3. lépést és a Csatlakozás az Azure-hoz parancs használatával 4. [lépését.](#sign-in-by-command)

!["Bejelentkezés az Azure-ba" beállítás az Explorerben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Az Explorerből nem tud kijelentkezni. A kijelentkezről lásd: [Csatlakozás az Azure-hoz parancs használatával.](#sign-in-by-command)

## <a name="create-an-extraction-script"></a>Kinyerési szkript létrehozása

A .csv-, .tsv- és .txt-fájlok kinyerési szkriptjéhez az **ADL: EXTRACT-szkript** létrehozása vagy az Azure Data Lake Explorer használatával hozhat létre.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Kinyerési szkript létrehozása parancs használatával

1. Válassza a Ctrl+Shift+P billentyűkombinációt a parancskatapaletta megnyitásához, majd írja be az **ADL: EXTRACT Script létrehozása parancsot.**
2. Adja meg egy Azure Storage-fájl teljes elérési útját, majd válassza az Enter billentyűt.
3. Válasszon ki egy fiókot.
4. .txt fájlnál válassza ki az elválasztó karaktert a fájl kinyeréhez.

![Kinyerési szkript létrehozásának folyamata](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

A kinyerési szkript a bejegyzések alapján jön létre. Az oszlopokat nem észlelő szkriptek közül válasszon egyet a két lehetőség közül. Ha nem, csak egy szkript jön létre.

![Kinyerési szkript létrehozásának eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Kinyerési szkript létrehozása az Explorerből

A kinyerési szkript létrehozására egy másik módszer, ha az Azure-ban vagy az Azure Blob Storage-ban található .csv-, .tsv- vagy .txt-Data Lake Store helyi menüt használja.

!["EXTRACT-szkript létrehozása" parancs a helyi menüből](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Következő lépések

- [U-SQL fejlesztése Python, R és C Sharp használatával Azure Data Lake Analytics VS Code-ban](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL helyi futtatás és helyi hibakeresés Visual Studio Code használatával](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Oktatóanyag: A Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Oktatóanyag: U-SQL-szkriptek fejlesztése a Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
