---
title: A digitális Twins modell elemző ismertetése | Microsoft Docs
description: Fejlesztőként megtudhatja, hogyan érvényesítheti a modelleket a DTDL-elemző használatával.
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d68abe8548dac3306228683e4b6ce8935a248ebc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92331787"
---
# <a name="understand-the-digital-twins-model-parser"></a>A Digital Twins modellelemző megismerése

A digitális ikrek definíciós nyelvét (DTDL) a [DTDL-specifikáció](https://github.com/Azure/opendigitaltwins-dtdl)írja le. A felhasználók a _digitális Twins Model Parser_ NuGet-csomagot használva ellenőrizhetik és lekérhetik a több fájlban definiált modelleket.

## <a name="install-the-dtdl-model-parser"></a>A DTDL Model Parser telepítése

Az elemző a NuGet.org-ben érhető el az azonosító: [Microsoft. Azure. DigitalTwins. Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Az elemző telepítéséhez használja a kompatibilis NuGet csomagkezelő (például a Visual Studióban vagy a parancssori felület) egyikét `dotnet` .

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> Az írás időpontjában az elemző verziója a következő: `3.12.5` .

## <a name="use-the-parser-to-validate-a-model"></a>A modell érvényesítése az elemző használatával

Egy modell a JSON-fájlokban leírt egy vagy több illesztőfelületből is állhat. Az elemző használatával betöltheti az adott mappában található összes fájlt, és az elemző használatával ellenőrizheti az összes fájlt egészként, beleértve a fájlok közötti hivatkozásokat is:

1. Hozzon létre egy `IEnumerable<string>` listát az összes modell tartalmáról:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. A és a `ModelParser` hívás példánya `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Ellenőrzési hibák ellenőrzése. Ha az elemző hibát talál, a hibát tartalmazó `ParsingException` listát tartalmaz:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. Vizsgálja meg a `Model` . Ha az ellenőrzés sikeres, a Model Parser API használatával ellenőrizheti a modellt. A következő kódrészlet azt mutatja be, hogyan lehet megismételni az összes elemzett modellt, és megjeleníti a meglévő tulajdonságokat:

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>Következő lépések

A cikkben áttekintett Model Parser API számos forgatókönyvet tesz lehetővé a DTDL-modelltől függő feladatok automatizálásához és érvényesítéséhez. Például dinamikusan hozhat létre felhasználói felületet a modellben található információk alapján.
