---
title: 'ML Studio (klasszikus): az Adatimportálási/-exportálási szolgáltatás az Azure-ban'
description: Bemutatjuk, hogyan lehet az adatok importálása és az adatmodulok exportálása az adatok küldésére és fogadására egy webszolgáltatásból.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
ms.date: 03/28/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 4824b7a4233bc65d521e1c6ded7d1ea276b2a929
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520561"
---
# <a name="deploy-azure-machine-learning-studio-classic-web-services-that-use-data-import-and-data-export-modules"></a>Az adatimportálást és az adatexportálási modulokat használó Azure Machine Learning Studio (klasszikus) webszolgáltatások üzembe helyezése

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Prediktív kísérlet létrehozásakor általában egy webszolgáltatás bemenetét és kimenetét kell hozzáadnia. A kísérlet telepítésekor a felhasználók adatokat küldhetnek és fogadhatnak a webszolgáltatástól a bemeneteken és kimeneteken keresztül. Egyes alkalmazások esetében előfordulhat, hogy a fogyasztó adatait adatcsatornán keresztül vagy egy külső adatforrásban, például az Azure Blob Storage-ban tárolja. Ezekben az esetekben nem szükséges az adatok olvasása és írása a webszolgáltatás-bemenetek és-kimenetek használatával. Ehelyett a Batch-végrehajtási szolgáltatás (BES) használatával olvashatnak az adatforrásból az adatok importálásával, és a pontozási eredményeket egy másik adatterületre írhatja az exportálási adatok modul használatával.

Az adatok importálása és az adatexportálási modulok beolvasása és írása különböző adatterületekre, például weburl-címekre HTTP-n keresztül, egy struktúra-lekérdezés, egy Azure SQL Database, az Azure Table Storage, az Azure Blob Storage, az adatcsatorna vagy egy SQL Server-adatbázis használatával végezhető el.

Ebben a témakörben az "5. példa: Train, test, reértékelés a bináris besorolás: felnőtt adatkészlet" mintát használja, és azt feltételezi, hogy az adatkészlet már be van töltve egy censusdata nevű Azure SQL-táblába.

## <a name="create-the-training-experiment"></a>A betanítási kísérlet létrehozása
Amikor megnyitja az "5. minta: betanítás, tesztelés, kiértékelés a bináris besoroláshoz: felnőtt adatkészlet" mintát, a minta felnőtt népszámlálás bevétel bináris besorolási adatkészletet használja. A vásznon lévő kísérlet a következő képhez hasonlóan fog kinézni:

![A kísérlet kezdeti konfigurálása.](./media/web-services-that-use-import-export-modules/initial-look-of-experiment.png)

Az Azure SQL-táblából származó adatok beolvasása:

1. Az adatkészlet moduljának törlése.
2. Az összetevők keresése mezőbe írja be az importálás kifejezést.
3. Az eredmények listájában adjon hozzá egy *adatimportálási* modult a kísérlet vászonhoz.
4. Az *adatok importálása* modul kimenetének összekötése a *tiszta hiányzó* adatmodul bemenetével.
5. A Tulajdonságok ablaktáblán válassza a **Azure SQL Database** lehetőséget az **adatforrás** legördülő menüben.
6. Az adatbázis- **kiszolgáló neve**, az **adatbázis neve**, a **Felhasználónév** és a **jelszó** mezőben adja meg az adatbázishoz szükséges adatokat.
7. Az adatbázis-lekérdezés mezőben adja meg a következő lekérdezést.

    ```tsql
     select [age],
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     from dbo.censusdata;
    ```
8. A kísérlet vászon alján kattintson a **Futtatás** elemre.

## <a name="create-the-predictive-experiment"></a>A prediktív kísérlet létrehozása
Ezután állítsa be azt a prediktív kísérletet, amelyről üzembe helyezi a webszolgáltatását.

1. A kísérlet vászon alján kattintson a **webszolgáltatás beállítása** elemre, és válassza a **prediktív webszolgáltatás elemet [ajánlott]**.
2. Távolítsa el a *webszolgáltatás bemeneti* és *webszolgáltatási kimeneti modulját* a prediktív kísérletből.
3. Az összetevők keresése mezőbe írja be az Exportálás kifejezést.
4. Az eredmények listájában adjon hozzá egy *adatexportálási* modult a kísérlet vászonhoz.
5. A *score Model* modul kimenetének összekötése az *adatexportálási* modul bemenetével.
6. A Tulajdonságok ablaktáblán válassza a **Azure SQL Database** lehetőséget az adatcél legördülő menüben.
7. Az adatbázis- **kiszolgáló neve**, az **adatbázis neve**, a **kiszolgáló felhasználói fiókjának neve** és a **kiszolgáló felhasználói fiók jelszava** mezőkben adja meg az adatbázishoz szükséges adatokat.
8. A **menteni kívánt oszlopok vesszővel tagolt listájában** írja be a következőt: pontozásos címkék.
9. Az **adattábla neve mezőbe** írja be a következőt: dbo. ScoredLabels. Ha a tábla nem létezik, akkor a kísérlet futtatásakor vagy a webszolgáltatás meghívásakor jön létre.
10. A **DataTable oszlopok vesszővel tagolt listája** mezőjébe írja be a következőt: ScoredLabels.

Ha olyan alkalmazást ír, amely meghívja a végső webszolgáltatást, előfordulhat, hogy egy másik bemeneti lekérdezést vagy egy célként megadott táblázatot kell megadnia a futási időben. Ezen bemenetek és kimenetek konfigurálásához használja a webszolgáltatás paramétereit, és állítsa be az adatok *importálása* modul *adatforrás* tulajdonságát és az adatok *exportálása* a cél tulajdonságot.  A webszolgáltatási paraméterekkel kapcsolatos további információkért tekintse meg a Cortana Intelligence és Machine Learning blog [Azure Machine learning Studio webszolgáltatási paraméterek bejegyzését](/archive/blogs/machinelearning/azureml-web-service-parameters) .

A webszolgáltatás paramétereinek konfigurálása az importálási lekérdezés és a céltábla számára:

1. Az *adatimportálási* modul tulajdonságok paneljén kattintson az **adatbázis-lekérdezés** mező jobb felső sarkában található ikonra, és válassza a **beállítás webszolgáltatásként paramétert**.
2. Az *adatexportálási* modul tulajdonságok paneljén kattintson az **adattábla neve** mező jobb felső sarkában található ikonra, és válassza a **beállítás webszolgáltatásként paramétert**.
3. Az *adatok exportálása* modul tulajdonságai ablaktábla alján, a **webszolgáltatás paramétereinek** szakaszban kattintson az adatbázis-lekérdezés elemre, és nevezze át a lekérdezést.
4. Kattintson **az adattábla neve** elemre, és nevezze át a **táblát**.

Ha elkészült, a kísérletnek az alábbi képhez hasonlóan kell kinéznie:

![A kísérlet végső megjelenése.](./media/web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Most már üzembe helyezheti a kísérletet webszolgáltatásként.

## <a name="deploy-the-web-service"></a>A webszolgáltatás üzembe helyezése
Telepítheti klasszikus vagy új webszolgáltatásra is.

### <a name="deploy-a-classic-web-service"></a>Klasszikus webszolgáltatás üzembe helyezése
A klasszikus webszolgáltatásként való üzembe helyezéshez és a használatba venni kívánt alkalmazás létrehozásához:

1. A kísérlet vászon alján kattintson a Futtatás elemre.
2. A Futtatás befejeződése után kattintson a **webszolgáltatás üzembe** helyezése elemre, és válassza a **webszolgáltatás telepítése [klasszikus]** lehetőséget.
3. Keresse meg az API-kulcsot a webszolgáltatás irányítópultján. Másolja ki és mentse el később a használathoz.
4. Az **alapértelmezett végpont** táblában kattintson a Batch- **végrehajtás** hivatkozásra az API-Súgó oldal megnyitásához.
5. A Visual Studióban hozzon létre egy C# konzolos alkalmazást: **új**  >  **Project**  >  **Visual C#**  >  **Windows klasszikus asztali**  >  **konzol alkalmazás (.NET-keretrendszer)**.
6. Az API-Súgó lapon keresse meg a kódlap alján található **mintakód** szakaszt.
7. Másolja és illessze be a C#-mintakód programot a program. cs fájlba, és távolítsa el az összes hivatkozást a blob Storage-ba.
8. Frissítse a *apiKey* változó értékét a korábban mentett API-kulccsal.
9. Keresse meg a kérelem deklarációját, és frissítse az adatok *importálása* és *exportálása* modulba átadott webszolgáltatás-paraméterek értékeit. Ebben az esetben az eredeti lekérdezést kell használnia, de meg kell adnia egy új táblanév nevet.

    ```csharp
    var request = new BatchExecutionRequest()
    {
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
        }
    };
    ```
10. Futtassa az alkalmazást.

A Futtatás befejezésekor új tábla kerül a pontozási eredményeket tartalmazó adatbázisba.

### <a name="deploy-a-new-web-service"></a>Új webszolgáltatás üzembe helyezése

> [!NOTE]
> Új webszolgáltatás telepítéséhez megfelelő engedélyekkel kell rendelkeznie ahhoz az előfizetéshez, amelyhez a webszolgáltatást telepíti. További információ: [webszolgáltatások kezelése a Azure Machine learning webszolgáltatások portálján](manage-new-webservice.md).

Új webszolgáltatásként való üzembe helyezéshez és alkalmazás létrehozásához:

1. A kísérlet vászon alján kattintson a **Futtatás** elemre.
2. Ha a Futtatás befejeződött, kattintson a **webszolgáltatás üzembe helyezése** elemre, és válassza a **webszolgáltatás üzembe helyezése [új]** lehetőséget.
3. A kísérlet telepítése lapon adja meg a webszolgáltatás nevét, és válasszon ki egy díjszabási csomagot, majd kattintson a **telepítés** elemre.
4. A rövid **útmutató lapon kattintson a felhasználás** elemre **.**
5. A **mintakód** szakaszban kattintson a **Batch** elemre.
6. A Visual Studióban hozzon létre egy C# konzolos alkalmazást: **új**  >  **Project**  >  **Visual C#**  >  **Windows klasszikus asztali**  >  **konzol alkalmazás (.NET-keretrendszer)**.
7. Másolja és illessze be a C#-mintakód a program. cs fájlba.
8. Frissítse a *apiKey* változó értékét az **alapszintű felhasználás adatai** szakaszban található **elsődleges kulccsal** .
9. Keresse meg a *scoreRequest* -deklarációt, és frissítse az adatok *importálása* és *exportálása* modulba átadott webszolgáltatás-paraméterek értékeit. Ebben az esetben az eredeti lekérdezést kell használnia, de meg kell adnia egy új táblanév nevet.

    ```csharp
    var scoreRequest = new
    {
        Inputs = new Dictionary<string, StringTable>()
        {
        },
        GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable3" },
        }
    };
    ```
10. Futtassa az alkalmazást.