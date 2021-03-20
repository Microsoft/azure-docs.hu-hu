---
title: Előrejelzések készítése a AutoML ONNX-modellel a .NET-ben
description: Ismerje meg, hogyan készíthet előrejelzéseket AutoML ONNX-modellel a .NET-ben a ML.NET használatával
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to, automl
ms.openlocfilehash: 4fb147dc5c57c3a98607a025f566fa583bf87460
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93358813"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>Előrejelzések készítése AutoML ONNX-modellel a .NET-ben

Ebből a cikkből megtudhatja, hogyan használhatja az automatikus ML (AutoML) nyílt neurális hálózati Exchange (ONNX) modellt, hogy előrejelzéseket készítsen a C# .NET Core konzol alkalmazásban a ML.NET-mel.

A [ml.net](/dotnet/machine-learning/) egy nyílt forráskódú, platformfüggetlen, gépi tanulási keretrendszer a .net-ökoszisztémához, amely lehetővé teszi az egyéni gépi tanulási modellek betanítását és felhasználását a C# vagy F # kód-első megközelítéssel, valamint az alacsony kódú eszközök, például a [Model Builder](/dotnet/machine-learning/automate-training-with-model-builder) és a [ml.net CLI](/dotnet/machine-learning/automate-training-with-cli)használatával. A keretrendszer bővíthető is, és lehetővé teszi más népszerű gépi tanulási keretrendszerek, például a TensorFlow és a ONNX használatát.

A ONNX az AI-modellek nyílt forráskódú formátuma. A ONNX támogatja a keretrendszerek közötti együttműködési képességet. Ez azt jelenti, hogy a modelleket a számos népszerű Machine learning-keretrendszer (például a PyTorch) egyikében betaníthatja, ONNX formátumba alakíthatja át, és a ONNX modellt más, például ML.NET-keretrendszerben is használhatja. További információért látogasson el a [ONNX webhelyére](https://onnx.ai/).

## <a name="prerequisites"></a>Előfeltételek

- [.NET Core SDK 3,1 vagy újabb](https://dotnet.microsoft.com/download)
- Szövegszerkesztő vagy IDE (például a [Visual Studio](https://visualstudio.microsoft.com/vs/) vagy a [Visual Studio Code](https://code.visualstudio.com/Download))
- ONNX modell. A AutoML ONNX-modellek betanításának megismeréséhez tekintse meg a következő [banki marketing besorolási jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb).
- [Netron](https://github.com/lutzroeder/netron) (nem kötelező)

## <a name="create-a-c-console-application"></a>C# konzolos alkalmazás létrehozása

Ebben a példában a a .NET Core parancssori felülete használatával hozza létre az alkalmazást, de ugyanezeket a feladatokat a Visual Studióval is végrehajthatja. További információ a [a .net Core parancssori felületeról](/dotnet/core/tools/).

1. Nyisson meg egy terminált, és hozzon létre egy új C# .NET Core Console-alkalmazást. Ebben a példában az alkalmazás neve: `AutoMLONNXConsoleApp` . A címtárat ugyanazzal a névvel hozza létre az alkalmazás tartalmával.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. A terminálban navigáljon a *AutoMLONNXConsoleApp* könyvtárhoz.

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>Szoftvercsomagok hozzáadása

1. Telepítse a **Microsoft.ml**, a **Microsoft. ml. OnnxRuntime** és a **Microsoft. ml. OnnxTransformer** NuGet-csomagokat a a .net Core parancssori felülete használatával.

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    Ezek a csomagok tartalmazzák azokat a függőségeket, amelyek szükségesek egy ONNX-modell .NET-alkalmazásokban való használatához. A ML.NET olyan API-t biztosít, amely a [ONNX futtatókörnyezetet](https://github.com/Microsoft/onnxruntime) használja az előrejelzésekhez.

1. Nyissa meg a *program. cs* fájlt, és adja hozzá a következő `using` utasításokat a felső részen a megfelelő csomagokra való hivatkozáshoz.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>Hivatkozás hozzáadása a ONNX-modellhez

A ONNX modell eléréséhez a konzol alkalmazásnak hozzá kell adnia azt a Build output könyvtárba.  Az MSBuild általános elemeivel kapcsolatos további tudnivalókért tekintse meg az [MSBuild útmutatót](/visualstudio/msbuild/common-msbuild-project-items).

Adjon hozzá egy hivatkozást a ONNX-modell fájljához az alkalmazásban

1. Másolja a ONNX modellt az alkalmazás *AutoMLONNXConsoleApp* gyökérkönyvtárára.
1. Nyissa meg a *AutoMLONNXConsoleApp. csproj* fájlt, és adja hozzá a következő tartalmat a `Project` csomóponton belül.

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    Ebben az esetben a ONNX-modell fájljának neve *automl. ONNX*.

1. Nyissa meg a *program. cs* fájlt, és adja hozzá a következő sort az `Program` osztályban belül.

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>MLContext inicializálása

A `Main` osztály metódusán belül `Program` hozzon létre egy új példányt [`MLContext`](xref:Microsoft.ML.MLContext) .

```csharp
MLContext mlContext = new MLContext();
```

Az [`MLContext`](xref:Microsoft.ML.MLContext) osztály az összes ml.net művelet kiindulási pontja, és az inicializálás `mlContext` egy új ml.net-környezetet hoz létre, amely megosztható a modell életciklusa alatt. Ez hasonló, elméletileg a Entity Framework DbContext.

## <a name="define-the-model-data-schema"></a>A modell adatsémájának megadása

A modell a bemeneti és kimeneti adatokat egy adott formátumban várja. A ML.NET lehetővé teszi az adatformátumok meghatározását az osztályokon keresztül. Néha előfordulhat, hogy már tudja, hogy a formátum hogyan néz ki. Abban az esetben, ha nem ismeri az adatformátumot, használhatja a Netron-t a ONNX-modell vizsgálatához.

A példában használt modell a NYC TLC taxi Trip adatkészletből származó adatokat használ. Az alábbi ábrán egy minta látható:

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3,75|CRD|15,5|
|VTS|1|1|480|2,72|CRD|10,0|
|VTS|1|1|1680|7,8|CSH|26,5|

### <a name="inspect-the-onnx-model-optional"></a>A ONNX modell vizsgálata (nem kötelező)

A modell bemeneteit és kimeneteit például a Netron eszközzel ellenőrizheti.

1. Nyissa meg a Netron.
1. A felső menüsorban válassza a **fájl > Megnyitás** elemet, és a böngésző használatával válassza ki a modellt.
1. Megnyílik a modell. A *automl-Model. Onnx* modell szerkezete például a következőhöz hasonlít:

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML ONNX-modell":::

1. Válassza ki az utolsó csomópontot a gráf alján ( `variable_out1` ebben az esetben) a modell metaadatainak megjelenítéséhez. Az oldalsáv bemenetei és kimenetei megmutatják a modell várt bemeneteit, kimeneteit és adattípusait. Ezekkel az adatokkal határozhatja meg a modell bemeneti és kimeneti sémáját.

### <a name="define-model-input-schema"></a>Modell bemeneti sémájának definiálása

Hozzon létre egy új, nevű osztályt a `OnnxInput` *program. cs* fájljában a következő tulajdonságokkal.

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

Az egyes tulajdonságok az adatkészlet egyik oszlopához vannak leképezve. A tulajdonságok további megjegyzésekkel rendelkeznek az attribútumokkal.

Az [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) attribútum segítségével megadhatja, hogyan hivatkozzon a ml.net az oszlopra az adatkezeléskor. Például, bár a `TripDistance` tulajdonság a .net-es szabványos elnevezési konvenciókat követi, a modell csak a néven ismert oszlop vagy szolgáltatás ismeretét ismeri `trip_distance` . Az elnevezési eltérés megoldásához az [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) attribútum a név alapján leképezi a `TripDistance` tulajdonságot egy oszlopra vagy szolgáltatásra `trip_distance` .
  
Numerikus értékek esetén a ML.NET csak az [`Single`](xref:System.Single) értékek típusán működik. Az egyes oszlopok eredeti adattípusa azonban egész szám. Az [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute) attribútum a ONNX és a ml.net közötti típusokat képezi le.

Az adatattribútumokkal kapcsolatos további tudnivalókért tekintse meg a [ml.net betöltési útmutatója](/dotnet/machine-learning/how-to-guides/load-data-ml-net)című témakört.

### <a name="define-model-output-schema"></a>Modell kimeneti sémájának definiálása

Az adatfeldolgozás után egy adott formátum kimenetét állítja elő. Adja meg az adatkimeneti sémát. Hozzon létre egy új, nevű osztályt a `OnnxOutput` *program. cs* fájljában a következő tulajdonságokkal.

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

A `OnnxInput` verzióhoz hasonlóan a (z [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) ) attribútum használatával képezhető le a `variable_out1` kimenet egy leíró névre `PredictedFare` .

## <a name="define-a-prediction-pipeline"></a>Előrejelzési folyamat definiálása

A ML.NET-ben a folyamat jellemzően olyan láncolt átalakítások sorozata, amelyek a bemeneti adatokon működnek, és kimenetet hoznak létre. További információ az adatátalakításokról: [ml.net-Adatátalakítási útmutató](/dotnet/machine-learning/resources/transforms).

1. Hozzon létre egy új, nevű metódust `GetPredictionPipeline` az `Program` osztályon belül

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. Adja meg a bemeneti és a kimeneti oszlopok nevét. Adja hozzá a következő kódot a `GetPredictionPipeline` metóduson belül.

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. A folyamat definiálása. A a [`IEstimator`](xref:Microsoft.ML.IEstimator%601) folyamat műveleteinek, bemeneti és kimeneti sémáinak tervezetét tartalmazza.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    Ebben az esetben [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) az egyetlen átalakítás a folyamatban, amely a bemeneti és kimeneti oszlopok nevét, valamint a ONNX-modell elérési útját veszi át.

1. A [`IEstimator`](xref:Microsoft.ML.IEstimator%601) csak az adatokra alkalmazandó műveletek körét határozza meg. Az adatain alapuló működést nevezzük [`ITransformer`](xref:Microsoft.ML.ITransformer) . A `Fit` metódus használatával hozzon létre egyet a ból `onnxPredictionPipeline` .

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    A [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A) metódus egy [`IDataView`](xref:Microsoft.ML.IDataView) bemenetként vár a műveletek végrehajtásához. Az a [`IDataView`](xref:Microsoft.ML.IDataView) ml.net táblázatos formátumban jeleníti meg az adatokat. Mivel ebben az esetben a folyamat csak előrejelzésekhez használatos, üresen is megadhatja a [`IDataView`](xref:Microsoft.ML.IDataView) [`ITransformer`](xref:Microsoft.ML.ITransformer) szükséges bemeneti és kimeneti séma adatait. Ezután a [`ITransformer`](xref:Microsoft.ML.ITransformer) rendszer visszaküldi az alkalmazást a további használatra.

    > [!TIP]
    > Ebben a példában a folyamat definiálva van, és ugyanazon az alkalmazáson belül használatos. Javasoljuk azonban, hogy külön alkalmazások használatával határozza meg és használja a folyamatot az előrejelzések készítéséhez. A ML.NET a folyamatokat szerializálhatja és mentheti további használatra a .NET végfelhasználói alkalmazásaiban. A ML.NET különböző telepítési célokat támogat, például asztali alkalmazásokat, webszolgáltatásokat, webszerelvény-alkalmazásokat * és sok más szolgáltatást. Ha többet szeretne megtudni a folyamatok mentéséről, tekintse meg a [ml.net Save and load betanított modellek útmutatót](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net).
    >
    > * A webszerelvény csak a .NET Core 5 vagy újabb verzióban támogatott

1. A `Main` metóduson belül hívja meg a `GetPredictionPipeline` metódust a szükséges paraméterekkel.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>Előrejelzések készítése a modell használatával

Most, hogy már rendelkezik egy folyamattal, itt az ideje, hogy előrejelzéseket készítsen. A ML.NET egy kényelmi API-t biztosít, amellyel előrejelzéseket készíthet egyetlen, nevű adatpéldányon [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) .

1. A `Main` metóduson belül hozzon létre egy-t [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) a [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) metódus használatával.

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. Hozzon létre egy teszt adat bemenetet.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. A (z) segítségével az `predictionEngine` új adatai alapján készíthet előrejelzéseket a `testInput` [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) metódus használatával.

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. Az előrejelzés eredményének kimenete a konzolon.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. Az alkalmazás futtatásához használja a a .NET Core parancssori felülete.

    ```dotnetcli
    dotnet run
    ```

    Az eredménynek a következő kimenethez hasonlóan kell kinéznie:

    ```text
    Predicted Fare: 15.621523
    ```

Ha többet szeretne megtudni az előrejelzések létrehozásáról a ML.NET-ben, tekintse meg a [modell használata az előrejelzések készítéséhez című útmutatót](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net).

## <a name="next-steps"></a>Következő lépések

- [A modell üzembe helyezése ASP.NET Core webes API-ként](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [A modell üzembe helyezése kiszolgáló nélküli .NET Azure-függvényként](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)