---
title: Oktatóanyag – Azure Stream Analytics felhőalapú feladatok egyéni .NET-deszerializálók
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egyéni .net-deszerializáló egy Azure stream Analytics felhőalapú feladatokhoz a Visual Studio használatával.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/17/2020
ms.openlocfilehash: 6db65d85b5d5d72e7b4764949abcffed004cae85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018207"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Oktatóanyag: egyéni .NET-deszerializálók a Azure Stream Analytics

A Azure Stream Analytics [három adatformátumhoz rendelkezik beépített támogatással](stream-analytics-parsing-json.md): JSON, CSV és Avro. Az egyéni .NET-deszerializálók használatával más formátumokból, például a [protokoll pufferből](https://developers.google.com/protocol-buffers/), a [kötésből](https://github.com/Microsoft/bond) és más, a Felhőbeli és a peremhálózati feladatok esetében definiált formátumokból is beolvashatja az adatait.

Ez az oktatóanyag bemutatja, hogyan hozhat létre egyéni .net-deszerializáló egy Azure stream Analytics felhőalapú feladatokhoz a Visual Studio használatával. Ha meg szeretné tudni, hogyan hozhatók létre .NET-deszerializálók a Visual Studio Code-ban, tekintse meg [a .net-deszerializálók létrehozása a Visual Studio Code-ban Azure stream Analytics feladatokhoz](visual-studio-code-custom-deserializer.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy egyéni deszerializáló protokollt a protokoll pufferéhez.
> * Hozzon létre egy Azure Stream Analytics feladatot a Visual Studióban.
> * Konfigurálja a Stream Analytics feladatot az egyéni deszerializáló használatára.
> * Futtassa az Stream Analytics feladatot helyileg az egyéni deszerializáló teszteléséhez és hibakereséséhez.


## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Telepítse a [Visual studio 2019 (ajánlott)](https://www.visualstudio.com/downloads/) vagy a [Visual Studio 2017](https://www.visualstudio.com/vs/older-downloads/)alkalmazást. Az Enterprise (Ultimate/Premium), Professional és Community kiadások mind támogatottak. Az Express kiadás nem támogatott. 

* [Telepítse a Visual studióhoz készült stream Analytics-eszközöket](stream-analytics-tools-for-visual-studio-install.md) , vagy frissítsen a legújabb verzióra. 

* Nyissa meg a **Cloud Explorer alkalmazást** a Visual Studióban, és jelentkezzen be az Azure-előfizetésbe.

* Hozzon létre egy tárolót az Azure Storage-fiókban.
Az Ön által létrehozott tároló a Stream Analytics feladathoz kapcsolódó eszközök tárolására szolgál. Ha már van egy meglévő tárolókat tartalmazó tárfiókja, használhatja azokat. Ha nincs, [hozzon létre egy új tárolót](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Egyéni deszerializáló létrehozása

1. Nyissa meg a Visual studiót, és válassza a **fájl > új > projekt** lehetőséget. Keresse meg **stream Analytics** , és válassza ki **Azure stream Analytics egyéni deszerializáló projekt (.net)** elemet. Adjon nevet a projektnek, például **protopuf deszerializáló**.

   ![Visual Studio DotNet standard Class Library-projekt létrehozása](./media/custom-deserializer/create-dotnet-library-project.png)

2. Megoldáskezelő kattintson a jobb gombbal a **protopuf deszerializáló** projektre, és válassza a menü **NuGet-csomagok kezelése** elemét. Ezután telepítse a **Microsoft. Azure. StreamAnalytics** és a **Google. protopuf** NuGet-csomagokat.

3. Adja hozzá a [MessageBodyProto osztályt](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) és a [MessageBodyDeserializer osztályt](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) a projekthez.

4. Hozza létre a **protopuf deszerializáló** projektet.

## <a name="add-an-azure-stream-analytics-project"></a>Azure Stream Analytics projekt hozzáadása

1. A Megoldáskezelőban kattintson a jobb gombbal a **deszerializáló protopuf** -megoldásra, és válassza az **Add > New Project (új projekt hozzáadása**) lehetőséget. A **Azure Stream Analytics > stream Analytics** területen válassza a **Azure stream Analytics alkalmazás** lehetőséget. Nevezze el **ProtobufCloudDeserializer** , és kattintson **az OK gombra**. 

2. Kattintson a jobb gombbal a **ProtobufCloudDeserializer** Azure stream Analytics projekt alatt lévő **referenciák** elemre. A **projektek** területen adja hozzá az **protopuf deszerializáló**. Automatikusan ki kell tölteni.

## <a name="configure-a-stream-analytics-job"></a>Stream Analytics-feladatok konfigurálása

1. Kattintson duplán **aJobConfig.js** elemre. Használja az alapértelmezett konfigurációkat, a következő beállítások kivételével:

   |Beállítás|Ajánlott érték|
   |-------|---------------|
   |Globális tárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Globális tárolási beállítások előfizetése| < az előfizetést >|
   |Globális tárolási beállítások Storage-fiókja| < a Storage-fiókját >|
   |Egyéni kód tárolási beállításainak erőforrása|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Egyéni kód tárolási beállításainak Storage-fiókja|< a Storage-fiókját >|
   |Egyéni kód tárolási beállításainak tárolója|< a Storage-tárolót >|

2. A **bemenetek** területen kattintson duplán **aInput.js** elemre. Használja az alapértelmezett konfigurációkat, a következő beállítások kivételével:

   |Beállítás|Ajánlott érték|
   |-------|---------------|
   |Forrás|Blob Storage|
   |Erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Előfizetés|< az előfizetést >|
   |Tárfiók|< a Storage-fiókját >|
   |Tároló|< a Storage-tárolót >|
   |Esemény szerializálási formátuma|Egyéb (Protopuf, XML, tulajdonosi...)|
   |Erőforrás|Betöltés az ASA-projekt referenciájának vagy CodeBehind|
   |CSharp szerelvény neve|ProtobufDeserializer.dll|
   |Osztály neve|MessageBodyProto.MessageBodyDeserializer|
   |Esemény tömörítési típusa|Nincsenek|

3. Adja hozzá a következő lekérdezést a **script. asaql** fájlhoz.

   ```sql
   SELECT * FROM Input
   ```

4. Töltse le a [minta protopuf bemeneti fájlját](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). A **bemenetek** mappában kattintson a jobb gombbal **aInput.js** elemre, majd válassza a **helyi bemenet hozzáadása** elemet. Ezután kattintson duplán **alocal_Input.js** elemre, és konfigurálja a következő beállításokat:

   |Beállítás|Ajánlott érték|
   |-------|---------------|
   |Input Alias (Bemeneti alias)|Bevitel|
   |Source Type (Forrás típusa)|Data Stream (Adatstream)|
   |Esemény szerializálási formátuma|Egyéb (Protopuf, XML, tulajdonosi...)|
   |CSharp szerelvény neve|ProtobufDeserializer.dll|
   |Osztály neve|MessageBodyProto.MessageBodyDeserializer|
   |Helyi bemeneti fájl elérési útja|< a letöltött minta protopuf bemeneti fájljának elérési útját>|

## <a name="execute-the-stream-analytics-job"></a>A Stream Analytics feladatok végrehajtása

1. Nyissa meg a **script. asaql** parancsot, és válassza a **helyi Futtatás** lehetőséget.

2. Tekintse át az eredményeket **stream Analytics helyi Futtatás eredményei** között.

Sikeresen végrehajtotta a Stream Analytics-feladatokhoz tartozó egyéni deszerializáló megvalósítását. Ebben az oktatóanyagban helyileg tesztelte az egyéni deszerializáló. A tényleges adatok esetében helyesen kell konfigurálnia a bemenetet és a kimenetet. Ezután küldje el a feladatot az Azure-ba a Visual studióból, hogy az imént implementált egyéni deszerializáló használatával futtassa a feladatot a felhőben.

## <a name="debug-your-deserializer"></a>A deszerializáló hibakeresése

A .NET deszerializáló hibakeresését ugyanúgy végezheti el, mint a standard .NET-kód hibakeresését.

1. Kattintson a jobb gombbal a **ProtobufCloudDeserializer** projekt neve lehetőségre, és állítsa be indítási projektként.

2. Töréspontokat adhat hozzá a függvényhez.

3. Nyomja le az **F5** billentyűt a hibakeresés megkezdéséhez. A program a várt módon leáll a töréspontoknál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rá, törölheti az erőforráscsoportot, a folyamatos átviteli feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megakadályozhatja, hogy a feladat által felhasznált streamelési egységek kiszámlázásra kerüljenek. Ha a feladatot a jövőben is szeretné használni, leállíthatja, és később újraindíthatja amikor ismét szükség van rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az oktatóanyagban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** menüpontot, majd válassza ki a létrehozott erőforrás nevét.  

2. Az erőforráscsoport lapján válassza a **Törlés** elemet, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan implementálhat egy egyéni .net deszerializáló a protokoll pufferének bemeneti szerializálásához. Ha többet szeretne megtudni az egyéni deszerializálók létrehozásáról, folytassa a következő cikkel:

> [!div class="nextstepaction"]
> [Különböző .NET-deszerializálók létrehozása Azure Stream Analytics feladatokhoz](custom-deserializer-examples.md)