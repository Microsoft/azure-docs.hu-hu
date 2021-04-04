---
title: .NET-es szabványos függvények fejlesztése Azure Stream Analytics feladatokhoz (előzetes verzió)
description: Útmutató C# felhasználó által definiált függvények írásához Stream Analytics feladatokhoz.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/10/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 52fa6f05db5452a2e7b8ec4f93d65525873c8c7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020570"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>.NET-es szabványos felhasználó által definiált függvények fejlesztése Azure Stream Analytics feladatokhoz (előzetes verzió)

A Azure Stream Analytics SQL-szerű lekérdezési nyelvet kínál az adatstreamek közötti átalakítások és számítások végrehajtásához. Számos beépített függvény létezik, de bizonyos összetett forgatókönyvek további rugalmasságot igényelnek. A .NET Standard felhasználó által definiált függvények (UDF) használatával meghívhatja a saját függvényeit bármely .NET Standard nyelven (C#, F # stb.), hogy kiterjessze a Stream Analytics lekérdezési nyelvet. A UDF lehetővé teszi összetett matematikai számítások végrehajtását, egyéni ML-modellek importálását a ML.NET használatával, és a hiányzó adatokhoz egyéni imputálási logikát használhat. Stream Analytics feladatok UDF-funkciója jelenleg előzetes verzióban érhető el, ezért nem használható éles környezetben.

A felhőalapú feladatok .NET-felhasználó által definiált függvénye a (z) rendszerben érhető el:
* USA nyugati középső régiója
* Észak-Európa
* USA keleti régiója
* USA nyugati régiója
* USA 2. keleti régiója
* Nyugat-Európa

Ha más régiókban szeretné használni ezt a funkciót, [hozzáférés kérhető](https://aka.ms/ccodereqregion).

## <a name="package-path"></a>Csomag elérési útja

Az UDF-csomagok formátuma az elérési út `/UserCustomCode/CLR/*` . A rendszer a dinamikus csatolású kódtárakat (DLL-eket) és az erőforrásokat a `/UserCustomCode/CLR/*` mappában másolja, ami segít a felhasználói DLL-fájlok elkülönítésében a rendszer-és Azure stream Analytics dll-eken. Ez a csomag elérési útja minden függvényhez használatos, függetlenül az azok alkalmazására használt módszertől.

## <a name="supported-types-and-mapping"></a>Támogatott típusok és leképezés

A C# nyelvben használandó Azure Stream Analytics értékeket az egyik környezetből a másikba kell átadni. A átadás az UDF összes bemeneti paramétere esetében történik. Minden Azure Stream Analytics típusnak megfelelő típusúnak kell lennie a C#-ban az alábbi táblázatban látható módon:

|**Azure Stream Analytics típusa** |**C# típus** |
|---------|---------|
|bigint | hosszú |
|float | double |
|nvarchar (max.) | sztring |
|dátum/idő | DateTime |
|Rekord | Szótár\<string, object> |
|Tömb | Objektum [] |

Ugyanez igaz, ha az adatokat a C# rendszerből Azure Stream Analyticsre kell átadni, ami az UDF kimeneti értékén történik. Az alábbi táblázat a támogatott típusokat mutatja:

|**C# típus**  |**Azure Stream Analytics típusa**  |
|---------|---------|
|hosszú  |  bigint   |
|double  |  float   |
|sztring  |  nvarchar (max.)   |
|DateTime  |  dateTime   |
|struct  |  Rekord   |
|object  |  Rekord   |
|Objektum []  |  Tömb   |
|Szótár\<string, object>  |  Rekord   |

## <a name="develop-a-udf-in-visual-studio-code"></a>UDF fejlesztése a Visual Studio Code-ban

A [Visual Studio Code Tools for Azure stream Analytics](quick-create-visual-studio-code.md) megkönnyíti a UDF írását, a feladatok helyi tesztelését (még kapcsolat nélküli üzemmódban), valamint a stream Analytics feladat közzétételét az Azure-ban.

A .NET Standard szintű UDF kétféleképpen valósítható meg a Visual Studio Code-eszközökben.

* UDF helyi dll-eken
* UDF helyi projektből

### <a name="local-project"></a>Helyi projekt

A felhasználó által definiált függvények olyan szerelvényben írhatók be, amely később egy Azure Stream Analytics lekérdezésben hivatkozik rá. Ez az ajánlott lehetőség olyan összetett függvények esetén, amelyek a .NET szabvány nyelvének teljes hatékonyságát igénylik a kifejezés nyelvén, például az eljárási logikán vagy a rekurzión kívül. Helyi projektből származó UDF is használható, ha több Azure Stream Analytics lekérdezésben kell megosztania a függvény logikáját. A UDF helyi projekthez való hozzáadása lehetővé teszi a függvények helyi hibakeresését és tesztelését.

Helyi projektre való hivatkozás:

1. Hozzon létre egy új .NET Standard szintű könyvtárat a helyi gépen.
2. Írja be a kódot az osztályba. Ne feledje, hogy az osztályokat *nyilvánosként* kell definiálni, és az objektumokat *statikus nyilvánosként* kell definiálni.
3. Vegyen fel egy új CSharp-függvény konfigurációs fájlt a Azure Stream Analytics projektbe, és hivatkozzon a CSharp Class Library projektre.
4. Konfigurálja a szerelvény elérési útját a feladatok konfigurációs fájljában, a `JobConfig.json` **CustomCodeStorage** szakaszban. Ez a lépés nem szükséges a helyi teszteléshez.

### <a name="local-dlls"></a>Helyi dll-EK

A felhasználó által definiált függvényeket tartalmazó helyi DLL-eket is hivatkozhat.

### <a name="example"></a>Példa

Ebben a példában a **CSharpUDFProject** egy C#-beli függvénytár-projekt, és a **ASAUDFDemo** a Azure stream Analytics projekt, amely a **CSharpUDFProject**-re hivatkozik.

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-demo.png" alt-text="Azure Stream Analytics projekt a Visual Studio Code-ban":::

A következő UDF egy függvényt tartalmaz, amely egy egész számot szorozva hozza létre az egész szám négyzetét. Az osztályokat *nyilvánosként* kell definiálni, és az objektumokat *statikus nyilvánosként* kell definiálni.

```csharp
using System;

namespace CSharpUDFProject
{
    // 
    public class Class1
    {
        public static Int64 SquareFunction(Int64 a)
        {
            return a * a;
        }
    }
}
```

A következő lépések bemutatják, hogyan adhat hozzá C# UDF-függvényt a Stream Analytics projekthez.

1. Kattintson a jobb gombbal a **függvények** mappára, és válassza az **elem hozzáadása** lehetőséget.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function.png" alt-text="Új függvény hozzáadása Azure Stream Analytics projektben":::

2. Adjon hozzá egy C#-függvényt a Azure Stream Analytics projekthez **SquareFunction** .

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-2.png" alt-text="CSharp függvény kiválasztása Stream Analytics projektből a VS Code-ban":::

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-add-function-name.png" alt-text="Adja meg a CSharp függvény nevét a VS Code-ban":::

3. A C#-függvény konfigurációjában válassza a **tár projekt elérési útjának** kiválasztása lehetőséget a c#-projekt kiválasztásához a legördülő listából, és válassza a **projekt összeállítása** lehetőséget a projekt felépítéséhez. Ezután válassza az **osztály kiválasztása** lehetőséget, **válassza** ki a kapcsolódó osztályt és metódust a legördülő listából. A Stream Analytics lekérdezés módszereire, típusaira és funkcióira vonatkozóan az osztályokat *nyilvánosként* kell definiálni, és az objektumokat *statikus nyilvánosként* kell definiálni.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-project.png" alt-text="Stream Analytics C Sharp Function konfiguráció VS kód":::

    Ha a C# UDF-t egy DLL-fájlból szeretné használni, válassza a DLL **elérési útja** kiválasztása lehetőséget a dll kiválasztásához. Ezután válassza az **osztály kiválasztása** lehetőséget, **válassza** ki a kapcsolódó osztályt és metódust a legördülő listából.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-choose-dll.png" alt-text="Stream Analytics C Sharp függvény konfigurálása":::

4. Hívja meg az UDF-t a Azure Stream Analytics lekérdezésben.

   ```sql
    SELECT price, udf.SquareFunction(price)
    INTO Output
    FROM Input 
   ```

5. Mielőtt elküldi a feladatot az Azure-ba, konfigurálja a csomag elérési útját a feladatok konfigurációs fájljában, a `JobConfig.json` **CustomCodeStorage** szakaszban. Válassza **ki az előfizetését** a codelensben-ben az előfizetés kiválasztásához, és válassza ki a Storage-fiókot és a tároló nevét a legördülő listából. Hagyja meg az **elérési utat** alapértelmezettként. Ez a lépés nem szükséges a helyi teszteléshez.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/visual-studio-code-csharp-udf-configure-storage-account.png" alt-text="Könyvtár elérési útjának kiválasztása":::

## <a name="develop-a-udf-in-visual-studio"></a>UDF fejlesztése a Visual Studióban

A Visual Studio-eszközökben háromféle módon valósítható meg a UDF.

* CodeBehind-fájlok egy ASA-projektben
* UDF helyi projektből
* Egy meglévő csomag egy Azure Storage-fiókból

### <a name="codebehind"></a>CodeBehind

Felhasználó által definiált függvényeket írhat a **script. asql** Codebehind. A Visual Studio Tools automatikusan lefordítja a CodeBehind-fájlt egy Assembly-fájlba. A szerelvények zip-fájlként vannak csomagolva, és feltöltve lesznek a Storage-fiókjába, amikor elküldi a feladatot az Azure-ba. Megtudhatja, hogyan írhat egy C# UDF-t az CodeBehind használatával a [C# UDF for stream Analytics Edge feladatok](stream-analytics-edge-csharp-udf.md) oktatóanyagának követésével. 

### <a name="local-project"></a>Helyi projekt

Helyi projektre való hivatkozás a Visual Studióban:

1. Új .NET Standard szintű függvénytár létrehozása a megoldásban
2. Írja be a kódot az osztályba. Ne feledje, hogy az osztályokat *nyilvánosként* kell definiálni, és az objektumokat *statikus nyilvánosként* kell definiálni. 
3. Hozza létre a projektet. Az eszközök a bin mappában lévő összes összetevőt egy zip-fájlba csomagolják, és feltöltik a zip-fájlt a Storage-fiókba. Külső hivatkozások esetében a NuGet-csomag helyett használjon szerelvény-hivatkozást.
4. Hivatkozzon az új osztályra a Azure Stream Analytics projektben.
5. Új függvény hozzáadása a Azure Stream Analytics projektben.
6. Konfigurálja a szerelvény elérési útját a feladatok konfigurációs fájljában `JobConfig.json` . Állítsa be a szerelvény elérési útját a **helyi projekt referenciájának vagy a Codebehind**.
7. Hozza létre újra a Function projektet és a Azure Stream Analytics projektet.  

### <a name="example"></a>Példa

Ebben a példában a **UDFTest** egy C#-beli függvénytár-projekt, és a **ASAUDFDemo** a Azure stream Analytics projekt, amely a **UDFTest**-re hivatkozik.

:::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png" alt-text="Azure Stream Analytics IoT Edge-projekt a Visual Studióban":::

1. Hozzon létre egy C#-projektet, amely lehetővé teszi a C# UDF-re mutató hivatkozás hozzáadását a Azure Stream Analytics lekérdezésből.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png" alt-text="Azure Stream Analytics IoT Edge-projekt létrehozása a Visual Studióban":::

2. Adja hozzá a C#-projektre mutató hivatkozást az ASA projektben. Kattintson a jobb gombbal a hivatkozások csomópontra, majd válassza a hivatkozás hozzáadása parancsot.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png" alt-text="Hivatkozás hozzáadása C#-projekthez a Visual Studióban":::

3. Válassza ki a C#-projekt nevét a listából.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png" alt-text="Válassza ki a C#-projekt nevét a hivatkozási listából":::

4. Meg kell jelennie a **Megoldáskezelőban** található **referenciák** alatt felsorolt **UDFTest** .

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png" alt-text="A felhasználó által definiált függvény hivatkozásának megtekintése a megoldás Explorerben":::

5. Kattintson a jobb gombbal a **functions** mappára, és válassza az **új elem** lehetőséget.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png" alt-text="Új elem hozzáadása a functions szolgáltatáshoz Azure Stream Analytics Edge-megoldásban":::

6. Adjon hozzá egy C#-függvényt **SquareFunction.jsa** Azure stream Analytics projekthez.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png" alt-text="A Visual Studióban Stream Analytics Edge-elemek CSharp függvényének kiválasztása":::

7. Kattintson duplán a függvényre **megoldáskezelő** a konfigurációs párbeszédpanel megnyitásához.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png" alt-text="C Sharp Function konfiguráció a Visual Studióban":::

8. A C# függvény konfigurációjában válassza a **Betöltés az ASA-projekthez** és a kapcsolódó szerelvény, osztály és metódus nevét a legördülő listából. A Stream Analytics lekérdezés módszereire, típusaira és funkcióira vonatkozóan az osztályokat *nyilvánosként* kell definiálni, és az objektumokat *statikus nyilvánosként* kell definiálni.

   :::image type="content" source="media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png" alt-text="Stream Analytics C Sharp Function Configuration Visual Studio":::

## <a name="existing-packages"></a>Meglévő csomagok

Bármilyen IDE-ban létrehozhat .NET Standard szintű UDF, és meghívhatja őket a Azure Stream Analytics-lekérdezésből. Először fordítsa le a kódot, és csomagolja ki a DLL-eket. A csomag formátuma az elérési út `/UserCustomCode/CLR/*` . Ezután töltse fel `UserCustomCode.zip` Az Azure Storage-fiókjában lévő tároló gyökerébe.

Miután feltöltötte az összeállítási zip-csomagokat az Azure Storage-fiókjába, használhatja Azure Stream Analytics lekérdezések funkcióit. Mindössze annyit kell tennie, hogy tartalmazza a tárolási adatokat a Stream Analytics feladatok konfigurációjában. Ezzel a beállítással helyileg nem tesztelheti a függvényt, mert a Visual Studio-eszközök nem töltik le a csomagot. A csomag elérési útját közvetlenül a szolgáltatásra kell elemezni. 

A szerelvény elérési útjának konfigurálásához a feladatok konfigurációs fájljában `JobConfig.json` :

Bontsa ki a **Felhasználói kód konfigurációja** szakaszt, és töltse ki a konfigurációt az alábbi javasolt értékekkel:

   |**Beállítás**|**Javasolt érték**|
   |-------|---------------|
   |Globális tárolási beállítások erőforrás|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Globális tárolási beállítások előfizetése| < az előfizetést >|
   |Globális tárolási beállítások Storage-fiókja| < a Storage-fiókját >|
   |Egyéni kód tárolási beállításainak erőforrása|Choose data source from current account (Adatforrás kiválasztása az aktuális fiókból)|
   |Egyéni kód tárolási beállításainak Storage-fiókja|< a Storage-fiókját >|
   |Egyéni kód tárolási beállításainak tárolója|< a Storage-tárolót >|
   |Egyéni kód szerelvényének forrása|Meglévő szerelvény-csomagok a felhőből|
   |Egyéni kód szerelvényének forrása|UserCustomCode.zip|

## <a name="user-logging"></a>Felhasználói naplózás

A naplózási mechanizmus lehetővé teszi, hogy egyéni adatokat rögzítsen a feladatok futtatása közben. A naplózási adatok segítségével valós időben végezhet hibakeresést vagy felmérni az egyéni kód helyességét.

A `StreamingContext` osztály lehetővé teszi a diagnosztikai adatok közzétételét a `StreamingDiagnostics.WriteError` függvény használatával. Az alábbi kód a Azure Stream Analytics által elérhetővé tett felületet mutatja.

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext` a rendszer az UDF metódus bemeneti paramétereként adja át, és az UDF-n belül használható az egyéni napló adatainak közzétételéhez. Az alábbi példában `MyUdfMethod` egy adatbevitelt  határoz meg, amelyet a lekérdezés biztosít, valamint egy **környezeti** bemenetet, amelyet a `StreamingContext` futásidejű motor biztosít. 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

Az `StreamingContext` értéket nem kell átadni az SQL-lekérdezésnek. A Azure Stream Analytics automatikusan helyi objektumot biztosít, ha van bemeneti paraméter. A `MyUdfMethod` nem változik, ahogy az a következő lekérdezésben is látható:

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

A naplózási üzeneteket a [diagnosztikai naplókon](data-errors.md)keresztül érheti el.

## <a name="limitations"></a>Korlátozások

Az UDF előzetes verziója jelenleg a következő korlátozásokkal rendelkezik:

* A .NET Standard UDF csak a Visual Studio Code vagy a Visual Studióban hozhatók létre és tehetők közzé az Azure-ban. A .NET Standard UDF csak olvasható verzióit tekintheti meg a Azure Portal **függvények** területén. A .NET Standard függvények készítése nem támogatott a Azure Portalban.

* A Azure Portal Query Editor hibaüzenetet jelenít meg, amikor a .NET Standard UDF-t használja a portálon. 

* Mivel az egyéni kódok Azure Stream Analytics motorral vannak megosztva, az egyéni kód nem hivatkozhat olyanra, amely ütköző névtérrel/dll_name Azure Stream Analytics kóddal rendelkezik. Nem hivatkozhat például a *Newtonsoft JSON*-ra.

* A projektben található támogató fájlokat a rendszer átmásolja a felhasználói egyéni kód zip-fájlba, amelyet a rendszer a feladatok felhőbe való közzétételekor használ. Az almappákban található összes fájl közvetlenül a felhasználó egyéni kód mappájának gyökerére lesz másolva a kibontott kibontáskor. A zip "lapított", ha ki van tömörítve.

* A felhasználó egyéni kódja nem támogatja az üres mappákat. Ne adjon üres mappákat a projektben található támogató fájlokhoz.

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: egy Azure Stream Analytics feladathoz tartozó C# felhasználó által definiált függvény írása (előzetes verzió)](stream-analytics-edge-csharp-udf.md)
* [Oktatóanyag: Az Azure Stream Analytics felhasználói JavaScript-függvényei](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md)