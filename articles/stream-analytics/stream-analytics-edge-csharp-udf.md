---
title: Oktatóanyag – C# felhasználó által definiált függvények írása Azure Stream Analytics feladatokhoz a Visual Studióban (előzetes verzió)
description: Ez az oktatóanyag bemutatja, hogyan írhat c# felhasználó által definiált függvényeket Stream Analytics feladatokhoz a Visual Studióban.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 851229e441aa2fbdf7b6eec05390c0ce2b149da2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020468"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Oktatóanyag: C# felhasználó által definiált függvény írása Azure Stream Analytics feladathoz (előzetes verzió)

A Visual Studióban létrehozott felhasználói C# függvényekkel (UDF-ekkel) kiterjesztheti az Azure Stream Analytics lekérdezéseinek nyelvét a saját függvényeire. A C#-pal meglévő kódokat használhat újra (így DLL-eket is), valamint matematikai vagy összetett logikát alkalmazhat. Az UDF-ek implementálásának három módja van: CodeBehind-fájlok egy Stream Analytics-projektben, UDF-ek egy helyi C#-projektből, valamint UDF-ek egy tárfiók meglévő csomagjából. Ez az oktatóanyag a CodeBehind metódussal implementál egy alapszintű C# függvényt. Stream Analytics feladatok UDF-funkciója jelenleg előzetes verzióban érhető el, ezért nem használható éles környezetben.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felhasználói C# függvény létrehozása CodeBehinddal.
> * A Stream Analytics-feladatok helyi tesztelése.
> * Tegye közzé a feladatot az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzálátna, győződjön meg róla, hogy megfelel az alábbi előfeltételeknek:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítse a [Stream Analytics Tools for Visual Studiót](stream-analytics-tools-for-visual-studio-install.md) és az **Azure-fejlesztési** vagy az **Adattárolási és -feldolgozási** számítási feladatokat.
* Ha IoT Edge feladatot hoz létre, tekintse meg a meglévő [stream Analytics Edge fejlesztői útmutatót](stream-analytics-tools-for-visual-studio-edge-jobs.md) .

## <a name="create-a-container-in-your-azure-storage-account"></a>Tároló létrehozása az Azure Storage-fiókban

A létrehozott tárolót a rendszer a lefordított C#-csomag tárolására fogja használni. Ha létrehoz egy Edge-feladatot, a rendszer ezt a Storage-fiókot is felhasználja a csomag IoT Edge eszközre való telepítéséhez. A Stream Analytics-feladathoz dedikált tárolót használjon. Ugyanazt a tárolót nem használhatja több Stream Analytics Edge-feladathoz. Ha már van egy meglévő tárolókat tartalmazó tárfiókja, használhatja azokat. Ha nincs, [hozzon létre egy új tárolót](../storage/blobs/storage-quickstart-blobs-portal.md). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Stream Analytics-projekt létrehozása a Visual Studióban

1. Indítsa el a Visual Studiót.

2. Válassza a **Fájl > Új projekt** lehetőséget.

3. A bal oldali sablonok listájában válassza a **stream Analytics** lehetőséget, majd válassza ki **Azure stream Analytics Edge alkalmazást** vagy **Azure stream Analytics alkalmazást**.

4.  Adja meg az alkalmazás **nevét**, **helyét** és a **megoldás nevét**, majd kattintson az **OK** gombra.

    ![Azure Stream Analytics Edge-projekt létrehozása a Visual Studióban](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Szerelvénycsomag elérési útjának konfigurálása

1. Nyissa meg a Visual Studio **Megoldáskezelőjét**.

2. Kattintson duplán a feladat konfigurációs fájljára (`EdgeJobConfig.json`).

3. Bontsa ki a **Felhasználói kód konfigurációja** szakaszt, és töltse ki a konfigurációt az alábbi javasolt értékekkel:

   |**Beállítás**|**Javasolt érték**|
   |-------|---------------|
   |Globális tárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Globális tárolási beállítások előfizetése| < az előfizetést >|
   |Globális tárolási beállítások Storage-fiókja| < a Storage-fiókját >|
   |Egyéni kód tárolási beállításainak erőforrása|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Egyéni kód tárolási beállításainak Storage-fiókja|< a Storage-fiókját >|
   |Egyéni kód tárolási beállításainak tárolója|< a Storage-tárolót >|


## <a name="write-a-c-udf-with-codebehind"></a>C# UDF írása a CodeBehind segítségével
A CodeBehind-fájlok egyetlen ASA-lekérdezési parancsfájlhoz társított C#-fájlok. A Visual Studio eszközei automatikusan tömörítik a CodeBehind fájlt, majd a beküldés után feltöltik az Azure Storage-fiókba. Minden osztályt *nyilvánosként*, minden objektumot pedig *statikus nyilvánosként* kell definiálni.

1. A **Megoldáskezelőben** bontsa ki a **Script.asql** fájlt a **Script.asaql.cs** CodeBehind fájl megkereséséhez.

2. Cserélje le a kódot az alábbi mintára:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Az UDF implementálása

1. A **Megoldáskezelőben** nyissa meg a **Script.asaql** fájlt.

2. Cserélje le a meglévő lekérdezést a következőre:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Helyi tesztelés

1. Töltse le a [hőmérséklet-szimulátor minta adatfájlját](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json).

2. A **Megoldáskezelőben** bontsa ki a **Bemenetek** csomópontot, majd kattintson a jobb gombbal az **Input.json** fájlra, és válassza a **Helyi bemenet hozzáadása** lehetőséget.

   ![Helyi bemenet hozzáadása Stream Analytics feladathoz a Visual Studióban](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Adja meg a helyi bemeneti fájl útvonalát a letöltött mintaadatokban, majd **mentse** a módosításokat.

    ![Helyi beviteli konfiguráció a Stream Analytics-feladathoz a Visual Studióban](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. A szkriptszerkesztőben kattintson a **Helyi futtatás** lehetőségre. Miután a helyi futtatás elmentette a kimeneti eredményeket, nyomjon le egy billentyűt az eredmények táblázatos megjelenítéséhez. 

    ![Az Azure Stream Analytics-feladat helyi futtatása a Visual Studióval](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. A nyersfájlokat JSON és CSV formátumban is megtekinthet az **Eredmények mappa megnyitása** lehetőséggel.

    ![A helyi Azure Stream Analytics-feladat eredményeinek megtekintése Visual Studióval](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>UDF hibakeresése
A C# UDF-en helyileg, a szabványos C#-kódokhoz hasonlóan végezhet hibakeresést. 

1. Adjon töréspontokat a C# függvényhez.

    ![Töréspontok hozzáadása a felhasználó által definiált függvényhez a Visual Studióban Stream Analytics](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Nyomja le az **F5** billentyűt a hibakeresés megkezdéséhez. A program a várt módon leáll a töréspontoknál.

    ![A felhasználó által definiált függvények hibakeresési eredményeinek megtekintése Stream Analytics](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>A feladat közzététele az Azure-ban
A lekérdezés helyi tesztelése után válassza a szkriptszerkesztő **Küldés az Azure-ba** lehetőségét a feladat az Azure-ban való közzétételéhez.

![A Stream Analytics Edge-feladat Azure-ban való közzététele a Visual Studióból](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Üzembe helyezés IoT Edge-eszközökön
Ha úgy dönt, hogy létrehoz egy Stream Analytics Edge-feladatot, ez mostantól IoT Edge modulként is telepíthető. Az [IoT Edge – gyors üzembe helyezés](../iot-edge/quickstart.md) lépéseit követve létrehozhat egy IoT Hubot, regisztrálhat egy IoT Edge-eszközt, majd telepítheti és elindíthatja az IoT Edge-futtatókörnyezetet a eszközön. Ezután [A feladat üzembe helyezése](../iot-edge/tutorial-deploy-stream-analytics.md#deploy-the-job) oktatóanyagot követve üzembe helyezheti a Stream Analytics-feladatot IoT Edge-modulként. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy egyszerű C# felhasználó által definiált függvényt hozott létre a CodeBehind használatával, közzétette a feladatot az Azure-ban, és üzembe helyezte a feladatot az Azure-ban vagy IoT Edge eszközön. 

Ha többet szeretne megtudni a C# felhasználó által definiált függvények Stream Analytics feladatokhoz való használatának különböző módjairól, folytassa a következő cikkel:

> [!div class="nextstepaction"]
> [C# függvények írása Azure Stream Analyticshoz](stream-analytics-edge-csharp-udf-methods.md)