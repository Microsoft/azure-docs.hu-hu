---
title: A Visual Studio Code-hoz készült Azure Data Lake-eszközök használata
description: Ismerje meg, hogyan hozhatja létre, tesztelheti és futtathatja a U-SQL-parancsfájlokat a Azure Data Lake Tools for Visual Studio Code használatával.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: 40e3ce17e036312e7c3fdee95fcb42d06f5845e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751359"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>A Visual Studio Code-hoz készült Azure Data Lake-eszközök használata

Ebből a cikkből megtudhatja, hogyan használhatók a Visual Studio Code-hoz készült Azure Data Lake Tools (VS Code) a U-SQL-parancsfájlok létrehozásához, teszteléséhez és futtatásához. Az adatokat az alábbi videóban is tárgyaljuk:

[![Videolejátszó: Azure Data Lake eszközök a VS Code-hoz](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Előfeltételek

A VS Code-hoz készült Azure Data Lake Tools támogatja a Windows, a Linux és a macOS rendszert. A U-SQL helyi Futtatás és a helyi hibakeresés csak Windows rendszerben működik.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

MacOS és Linux esetén:

- [.NET Core SDK 2,0](https://www.microsoft.com/net/download/core)
- [Monó 5.2. x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Azure Data Lake eszközök telepítése

Az Előfeltételek telepítése után telepítheti a VS Code Azure Data Lake eszközeit.

### <a name="to-install-azure-data-lake-tools"></a>Azure Data Lake eszközök telepítése

1. Nyissa meg a Visual Studio Code-ot.
2. A bal oldali ablaktáblán válassza a **bővítmények** lehetőséget. A keresőmezőbe írja be **Azure Data Lake eszközöket** .
3. Válassza a **telepítés** lehetőséget **Azure Data Lake eszközök** elem mellett.

   ![Data Lake eszközök telepítésének kiválasztása](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Néhány másodperc elteltével a **telepítés** gombra változik az **újratöltés**.
4. Az **Azure Data Lake-eszközök** bővítmény aktiválásához válassza az **Újratöltés** lehetőséget.
5. A megerősítéshez válassza az **újratöltési ablak** lehetőséget. A **bővítmények** ablaktáblán láthatók **Azure Data Lake eszközök** .

## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake eszközök aktiválása

Hozzon létre egy. usql fájlt, vagy nyisson meg egy meglévő. usql fájlt a bővítmény aktiválásához.

## <a name="work-with-u-sql"></a>A U-SQL használata

A U-SQL-vel való együttműködéshez meg kell nyitnia egy U-SQL-fájlt vagy egy mappát.

### <a name="to-open-the-sample-script"></a>A minta parancsfájl megnyitása

Nyissa meg a parancssort (CTRL + SHIFT + P), és írja be az **ADL: Open minta szkriptet**. Ekkor megnyílik a minta egy másik példánya. Ezen a példányon szerkesztheti, konfigurálhatja és elküldheti a parancsfájlokat is.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Mappa megnyitása a U-SQL-projekthez

1. A Visual Studio Code-ból válassza a **fájl** menüt, majd a **mappa megnyitása** lehetőséget.
2. Adjon meg egy mappát, majd kattintson a **mappa kiválasztása** lehetőségre.
3. Válassza ki a **fájl** menüt, majd válassza az **új** lehetőséget. Egy névtelen-1 fájl van hozzáadva a projekthez.
4. Adja meg a következő kódot a névtelen-1 fájlban:

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

   KIMENET @departments     a következőre: "/Output/departments.csv" Outputters.Csv ();

    A szkript létrehoz egy departments.csv fájlt, amely tartalmaz néhány, a/output mappában található adattal.

5. Mentse a fájlt **myUSQL. usql** néven a megnyitott mappában.

### <a name="to-compile-a-u-sql-script"></a>U-SQL-szkript fordítása

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: szkript fordítását**. A fordítási eredmények a **kimenet** ablakban jelennek meg. Kattintson a jobb gombbal egy parancsfájlra, majd válassza az **ADL: szkript fordítása** egy U-SQL-feladatok fordításához lehetőséget. A fordítási eredmény megjelenik a **kimenet** ablaktáblán.

### <a name="to-submit-a-u-sql-script"></a>U-SQL-szkript elküldése

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: submit feladatot**. Azt is megteheti, hogy a jobb gombbal egy parancsfájlra kattint, majd kiválasztja az **ADL: elküldés feladatot**.

A U-SQL-feladatok elküldése után a küldési naplók a VS Code **kimenet** ablakában jelennek meg. A feladatok nézet a jobb oldali ablaktáblán jelenik meg. Ha a Küldés sikeres, a feladatokhoz tartozó URL-cím is megjelenik. A valós idejű feladatok állapotának nyomon követéséhez megnyithatja a feladatok URL-címét egy böngészőben.

A feladatok nézet **Összegzés** lapján láthatja a feladatok részleteit. A fő függvények közé tartozik például a szkript újraküldése, duplikált parancsfájl, és Megnyitás a portálon. A feladatok nézet **adatok** lapján a bemeneti fájlokra, a kimeneti fájlokra és az erőforrás-fájlokra is hivatkozhat. A fájlok a helyi számítógépre tölthetők le.

![A feladatok nézet összefoglalás lapja](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Adatok lap a feladatok nézetben](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Az alapértelmezett környezet beállítása

Beállíthatja, hogy az alapértelmezett környezet alkalmazza ezt a beállítást az összes parancsfájl-fájlra, ha a fájlokhoz nem állított be paramétereket külön.

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: alapértelmezett környezet beállítása beállítást**. Vagy kattintson a jobb gombbal a parancsfájl-szerkesztőre, és válassza az **ADL: alapértelmezett környezet beállítása** lehetőséget.
3. Válassza ki a kívánt fiókot, adatbázist és sémát. A rendszer a konfigurációs fájl xxx_settings.jsmenti a beállítást.

   ![A fiók, az adatbázis és a séma beállítása alapértelmezett környezetként](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Parancsfájl paramétereinek megadása

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: parancsfájl paramétereinek beállítása** értéket.
3. A fájl xxx_settings.jsmegnyílik a következő tulajdonságokkal:

   - **fiók**: az Azure-előfizetéshez tartozó Azure Data Lake Analytics fiók, amely a U-SQL-feladatok fordításához és futtatásához szükséges. Az U-SQL-feladatok fordítása és futtatása előtt konfigurálnia kell a számítógépfiókot.
   - **adatbázis**: a fiókja alá tartozó adatbázis. Az alapértelmezett érték a **Master**.
   - **séma**: az adatbázishoz tartozó séma. Az alapértelmezett érték a **dbo**.
   - **optionalSettings**:
        - **prioritás**: a prioritási tartomány 1 és 1000 közötti, a legmagasabb prioritással 1. Az alapértelmezett érték **1000**.
        - **analyticsunits**: a párhuzamossági tartomány 1 és 150 között van. Az alapértelmezett érték a Azure Data Lake Analytics-fiókban engedélyezett maximális párhuzamosság.

   ![A JSON-fájl tartalma](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> A konfiguráció mentése után a fiók, az adatbázis és a séma adatai megjelennek az állapotsorban a megfelelő. usql fájl bal alsó sarkában, ha nincs beállítva alapértelmezett környezet.

### <a name="to-set-git-ignore"></a>A git mellőzésének beállítása

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: set git mellőzése**.

   - Ha a VS Code Working mappában nem található **. gitIgnore** -fájl, a **. gitIgnore** nevű fájl jön létre a mappában. A rendszer alapértelmezés szerint négy elemet (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **obj**) ad hozzá a fájlhoz. Szükség esetén további frissítéseket is megadhat.
   - Ha a VS Code Working mappában már van egy **. gitIgnore** -fájl, az eszköz négy elemet (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **. cache**, **obj**) helyez el a **. gitIgnore** fájlban, ha a fájl nem tartalmaz négy elemet.

   ![A. gitIgnore fájl elemei](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>A kód mögötti fájlok használata: C Sharp, Python és R

Azure Data Lake eszközök több egyéni kódot is támogatnak. Útmutatásért lásd: a [U-SQL fejlesztése a Python, az R és a C Sharp esetében Azure Data Lake Analytics a vs Code-ban](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md).

## <a name="work-with-assemblies"></a>Szerelvények használata

A szerelvények fejlesztésével kapcsolatos információkért lásd: [U-SQL-szerelvények fejlesztése Azure Data Lake Analytics feladatokhoz]().

A Data Lake Analytics-katalógusban Data Lake eszközök használatával regisztrálhat egyéni kód-szerelvényeket.

### <a name="to-register-an-assembly"></a>Szerelvény regisztrálása

A szerelvényt az **ADL: register Assembly** vagy **ADL: register Assembly (Advanced)** parancs használatával regisztrálhatja.

### <a name="to-register-through-the-adl-register-assembly-command"></a>Az ADL: register Assembly parancs használatával regisztrálhat

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: Regisztráljon szerelvényt**.
3. A helyi szerelvény elérési útjának megadása.
4. Válasszon Data Lake Analytics fiókot.
5. Válasszon ki egy adatbázist.

A portál egy böngészőben van megnyitva, és megjeleníti a szerelvény regisztrációs folyamatát.  

Az ADL kiváltásának kényelmesebb módja **: az összeállítási parancs regisztrálása** a Fájlkezelőben kattintson a jobb gombbal a. dll fájlra.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Az ADL: register Assembly (Advanced) parancs használatával történő regisztráció

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.
2. Adja meg az **ADL: Regisztráljon Assembly (speciális)**.
3. A helyi szerelvény elérési útjának megadása.
4. Megjelenik a JSON-fájl. Szükség esetén tekintse át és szerkessze a szerelvény-függőségeket és az erőforrás-paramétereket. Az utasítások a **kimenet** ablakban jelennek meg. A szerelvény regisztrációjának folytatásához mentse (CTRL + S) a JSON-fájlt.

   ![A JSON-fájl szerelvény-függőségekkel és erőforrás-paraméterekkel](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake-eszközök automatikusan felismeri, hogy a DLL-fájl rendelkezik-e szerelvény-függőségekkel. A függőségek az észlelés után a JSON-fájlban jelennek meg.
>- A DLL-erőforrásokat (például. txt,. png és. csv) a szerelvény regisztrációjának részeként töltheti fel.

Az ADL következő kiváltásának egy másik módja: a (z) **Register Assembly (speciális)** parancs, ha a jobb gombbal rákattint a. dll fájlra a fájlkezelőben.

A következő U-SQL-kód bemutatja, hogyan hívhat meg egy szerelvényt. A mintában a szerelvény neve *teszt*.

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

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>A U-SQL helyi futtatásának és helyi hibakeresésének használata Windows-felhasználók számára

A U-SQL helyi futtatása ellenőrzi a helyi adatait, és helyileg érvényesíti a szkriptet, mielőtt közzéteszi a programkódot a Data Lake Analytics. A helyi hibakeresési funkcióval a következő feladatokat hajthatja végre, mielőtt elküldte a kódot a Data Lake Analyticsba:

- A C#-kód hibakeresése.
- A kód átlépése.
- A parancsfájl helyi ellenőrzése.

A helyi Futtatás és a helyi hibakeresési funkció csak Windows-környezetekben működik, és macOS és Linux rendszerű operációs rendszereken nem támogatott.

A helyi futtatással és a helyi hibakereséssel kapcsolatos utasításokért lásd: [U-SQL helyi Futtatás és helyi hibakeresés a Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)-ban.

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Mielőtt lefordítja és futtatja a U-SQL-parancsfájlokat a Data Lake Analyticsban, csatlakoznia kell az Azure-fiókjához.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Kapcsolódás az Azure-hoz egy parancs használatával

1. A parancs paletta megnyitásához kattintson a CTRL + SHIFT + P billentyűkombinációra.

2. Írja be az **ADL: login nevet**. A bejelentkezési adatok a jobb alsó sarokban jelennek meg.

   ![A login parancs beírása](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Értesítés a bejelentkezésről és a hitelesítésről](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Válassza a **másolás & Megnyitás** lehetőséget a [bejelentkezési weblap](https://aka.ms/devicelogin)megnyitásához. Illessze be a kódot a mezőbe, majd kattintson a **Continue (folytatás**) gombra.

    ![Bejelentkezési weblap](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Kövesse az utasításokat a weblapon való bejelentkezéshez. Amikor csatlakozik, az Azure-fiók neve megjelenik az állapotsorban a VS Code ablak bal alsó sarkában.

> [!NOTE]
>
> - Data Lake eszközök automatikusan aláírják Önt a következő alkalommal, ha nem kijelentkezik.
> - Ha a fiókja két tényezőt engedélyez, javasoljuk, hogy PIN-kód használata helyett telefonos hitelesítést használjon.

A kijelentkezéshez írja be az **ADL: kijelentkezés** parancsot.

### <a name="to-connect-to-azure-from-the-explorer"></a>Kapcsolódás az Azure-hoz az Intézőből

Bontsa ki az **Azure DATALAKE**, majd a **Bejelentkezés az Azure**-ba lehetőséget, majd kövesse a 3. lépést és a 4. lépését az [Azure-hoz való kapcsolódáshoz egy parancs használatával](#sign-in-by-command).

!["Bejelentkezés az Azure-ba" kijelölés az Explorerben](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Nem lehet kijelentkezni az Intézőből. A kijelentkezéshez lásd: az Azure-hoz való [Kapcsolódás parancs használatával](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Kinyerési parancsfájl létrehozása

A. csv, a. TSV és a. txt kiterjesztésű fájlok kinyerési parancsfájlját a következő paranccsal hozhatja létre **: a KInyerési parancsfájl létrehozása** vagy a Azure Data Lake Explorerben.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Kinyerési parancsfájl létrehozása parancs használatával

1. A CTRL + SHIFT + P billentyűkombinációval nyissa meg a parancssort, és írja be az **ADL: extract szkript létrehozása** elemet.
2. Adja meg az Azure Storage-fájl teljes elérési útját, és válassza az ENTER billentyűt.
3. Válasszon egy fiókot.
4. Egy. txt kiterjesztésű fájl esetén válasszon ki egy határolójelet a fájl kibontásához.

![Kinyerési parancsfájl létrehozási folyamata](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

A kinyerési parancsfájl a bejegyzései alapján jön létre. Egy olyan parancsfájl esetében, amely nem ismeri fel az oszlopokat, válasszon egyet a két lehetőség közül. Ha nem, csak egy parancsfájl lesz létrehozva.

![Kinyerési parancsfájl létrehozásának eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Kinyerési parancsfájl létrehozása az Intézőből

A kinyerési parancsfájl létrehozásának egy másik módja a. csv,. TSV vagy. txt fájl helyi menüjében található, Azure Data Lake Store vagy az Azure Blob Storage-ban.

!["A kinyerési parancsfájl létrehozása" parancs a helyi menüből](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Következő lépések

- [A U-SQL fejlesztése Python, R és C Sharp Azure Data Lake Analytics a VS Code-ban](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [U-SQL helyi Futtatás és helyi hibakeresés a Visual Studio Code-ban](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Oktatóanyag: az Azure Data Lake Analytics első lépései](data-lake-analytics-get-started-portal.md)
- [Oktatóanyag: U-SQL-parancsfájlok fejlesztése a Visual studióhoz készült Data Lake Tools használatával](data-lake-analytics-data-lake-tools-get-started.md)