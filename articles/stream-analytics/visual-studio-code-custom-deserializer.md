---
title: Egyéni .NET-deszerializálók létrehozása a Visual Studio Code használatával Azure Stream Analytics Felhőbeli feladatokhoz
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egyéni .net-deszerializáló egy Azure stream Analytics felhőalapú feladatokhoz a Visual Studio Code használatával.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 1813fb222bca74f355fec52252ce3d77fef06e5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013923"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Egyéni .NET-deszerializálók létrehozása a Visual Studio Code Azure Stream Analyticshoz

A Azure Stream Analytics [három adatformátumhoz rendelkezik beépített támogatással](stream-analytics-parsing-json.md): JSON, CSV és Avro. Az egyéni .NET-deszerializálók használatával más formátumokból, például [protokoll-pufferből](https://developers.google.com/protocol-buffers/),- [kötésből](https://github.com/Microsoft/bond) és más, a Felhőbeli feladatokhoz definiált felhasználói formátumokból is olvashat.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Egyéni .NET-deszerializálók a Visual Studio Code-ban

A Visual Studio Code használatával egy Azure stream Analytics Felhőbeli feladatokhoz egyéni .net deszerializáló hozhat létre, tesztelheti és hibakeresést végezhet.

### <a name="prerequisites"></a>Előfeltételek

* Telepítse a [.net Core SDK](https://dotnet.microsoft.com/download) -t, és indítsa újra a Visual Studio Code-ot.

* Ebből a rövid útmutatóból megtudhatja [, hogyan](quick-create-visual-studio-code.md) hozhat létre egy stream Analytics feladatot a Visual Studio Code használatával.

### <a name="create-a-custom-deserializer"></a>Egyéni deszerializáló létrehozása

1. Nyisson meg egy terminált, és futtassa a következő parancsot egy .NET-osztály könyvtára létrehozásához a Visual Studio Code-ban az egyéni deszerializáló nevű **ProtobufDeserializer**.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Nyissa meg a **ProtobufDeserializer** -projekt könyvtárat, és telepítse a [Microsoft. Azure. StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) és a [Google. protopuf](https://www.nuget.org/packages/Google.Protobuf/) NuGet csomagokat.

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Adja hozzá a [MessageBodyProto osztályt](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) és a [MessageBodyDeserializer osztályt](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) a projekthez.

4. Hozza létre a **ProtobufDeserializer** projektet.

### <a name="add-an-azure-stream-analytics-project"></a>Azure Stream Analytics projekt hozzáadása

1. Nyissa meg a parancssort a Visual Studio Code-ban, és válassza a **CTRL + SHIFT + P** billentyűkombinációt. Ezután írja be az ASA értéket, és válassza az **ASA: új projekt létrehozása** lehetőséget. Nevezze el **ProtobufCloudDeserializer**.

### <a name="configure-a-stream-analytics-job"></a>Stream Analytics-feladatok konfigurálása

1. Kattintson duplán **aJobConfig.js** elemre. Használja az alapértelmezett konfigurációkat, a következő beállítások kivételével:

   |Beállítás|Ajánlott érték|
   |-------|---------------|
   |Globális tárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Globális tárolási beállítások előfizetése| < az előfizetést >|
   |Globális tárolási beállítások Storage-fiókja| < a Storage-fiókját >|
   |CustomCodeStorage-beállítások Storage-fiókja|< a Storage-fiókját >|
   |CustomCodeStorage-beállítások tárolója|< a Storage-tárolót >|

2. A **bemenetek** mappában Nyissa meg **ainput.json**. Válassza az **élő bemenet hozzáadása** lehetőséget, és adjon hozzá egy bemenetet Azure Data Lake Storage Gen2/blob Storage-ból, majd válassza az **Azure-előfizetésből** válassza a kiválasztás lehetőséget. Használja az alapértelmezett konfigurációkat, a következő beállítások kivételével:

   |Beállítás|Ajánlott érték|
   |-------|---------------|
   |Name|Bevitel|
   |Előfizetés|< az előfizetést >|
   |Tárfiók|< a Storage-fiókját >|
   |Tároló|< a Storage-tárolót >|
   |Szerializálás típusa|**Egyéni** elem kiválasztása|
   |SerializationProjectPath|Válassza a **tár projekt elérési útjának** kiválasztása a codelensben lehetőséget, és válassza ki az előző szakaszban létrehozott **ProtobufDeserializer** -függvénytár-projektet. A projekt létrehozásához válassza a **projekt összeállítása** lehetőséget|
   |SerializationClassName|Válassza a **deszerializálási osztály kiválasztása** a codelensben lehetőséget az osztály nevének és a DLL elérési útjának automatikus feltöltéséhez|
   |Osztály neve|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Adja hozzá az egyéni deszerializáló bemenetét.":::

3. Adja hozzá a következő lekérdezést a **ProtobufCloudDeserializer. asaql** fájlhoz.

   ```sql
   SELECT * FROM Input
   ```

4. Töltse le a [minta protopuf bemeneti fájlját](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). A **bemenetek** mappában kattintson a jobb gombbal **ainput.js** elemre, majd válassza a **helyi bemenet hozzáadása** elemet. Ezután kattintson duplán a **local_input1.js** elemre, és használja az alapértelmezett konfigurációkat, kivéve a következő beállításokat.

   |Beállítás|Ajánlott érték|
   |-------|---------------|
   |Helyi fájl elérési útjának kiválasztása|Kattintson a Codelensben elemre, és válassza ki < a letöltött minta protopuf bemeneti fájljának elérési útját>|

### <a name="execute-the-stream-analytics-job"></a>A Stream Analytics feladatok végrehajtása

1. Nyissa meg a **ProtobufCloudDeserializer. asaql** parancsot, és válassza a helyi **Futtatás** a codelensben lehetőséget, majd válassza a **helyi bemenet használata** lehetőséget a legördülő listából.

2. Figyelje meg az eredményeket a jobb oldalon található Job diagram nézet **eredmények lapján.** A köztes eredmény megtekintéséhez a feladatütemezés lépéseire is kattinthat. További részletekért lásd: [helyi hibakeresés a feladatok diagramjának használatával](debug-locally-using-job-diagram-vs-code.md).

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="A helyi Futtatás eredményének ellenőrzéséhez.":::

Sikeresen végrehajtotta a Stream Analytics-feladatokhoz tartozó egyéni deszerializáló megvalósítását. Ebben az oktatóanyagban a helyi bemeneti adatokkal helyileg tesztelte az egyéni deszerializáló. [A felhőben élő adatbevitel használatával](visual-studio-code-local-run-live-input.md)is tesztelheti. A feladatnak a felhőben való futtatásához megfelelően konfigurálnia kell a bemenetet és a kimenetet. Ezt követően küldje el a feladatot az Azure-ba a Visual Studio Code-ból, hogy az imént implementált egyéni deszerializáló használatával futtassa a feladatot a felhőben.

### <a name="debug-your-deserializer"></a>A deszerializáló hibakeresése

A .NET deszerializáló hibakeresését ugyanúgy végezheti el, mint a standard .NET-kód hibakeresését. 

1. Töréspontokat adhat hozzá a .NET-függvényhez.

2. Kattintson a **Futtatás** a Visual Studio Code tevékenység sávjában elemre, és válassza **a fájl létrehozása launch.js** elemet.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Hozzon létre egy indító fájlt.":::

   Válassza a **ProtobufCloudDeserializer** lehetőséget, majd **Azure stream Analytics** a legördülő listából.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Hozza létre a 2. indítási fájlt.":::

   Szerkessze a fájl **launch.jsa** helyére <ASAScript> . asaql a ProtobufCloudDeserializer. asaql fájllal.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Az indítási fájl konfigurálása.":::

3. Nyomja le az **F5** billentyűt a hibakeresés megkezdéséhez. A program a várt módon leáll a töréspontoknál. Ez a helyi bemenet és az élő bemeneti adatok esetében is működik.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Egyéni deszerializáló hibakeresése.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az oktatóanyagban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** menüpontot, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport lapján válassza a **Törlés** elemet, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan implementálhat egy egyéni .net deszerializáló a protokoll pufferének bemeneti szerializálásához. Ha többet szeretne megtudni az egyéni deszerializálók létrehozásáról, folytassa a következő cikkel:

> [!div class="nextstepaction"]
> * [Különböző .NET-deszerializálók létrehozása Azure Stream Analytics feladatokhoz](custom-deserializer-examples.md)
> * [Azure Stream Analytics feladatok helyi tesztelése élő bevitelsel a Visual Studio Code használatával](visual-studio-code-local-run-live-input.md)