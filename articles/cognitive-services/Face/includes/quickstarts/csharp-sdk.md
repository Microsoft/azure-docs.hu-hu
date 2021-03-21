---
title: Face .NET ügyféloldali kódtár – rövid útmutató
description: Használja a Face ügyféloldali kódtárat a .NET-hez az arcok észleléséhez, hasonló kereséséhez (képkeresés a rendszerkép alapján), az arcok azonosításához (arc-felismerési keresés) és az adatok migrálása.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: b06a35274082e09ec0973c5123b39935a93e354d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444975"
---
Ismerkedés az Arcfelismerés szolgáltatással a .NET-hez készült Face ügyféloldali kódtár használatával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Face szolgáltatás hozzáférést biztosít a speciális algoritmusokhoz a képeken található emberi arcok észleléséhez és felismeréséhez.

A .NET-hez készült Face ügyféloldali kódtár a következőhöz használható:

* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása](#create-a-person-group)
* [Arc azonosítása](#identify-a-face)

[Dokumentáció](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)  |  [Példák](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Előfeltételek


* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/) vagy a [.net Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Hozzon létre egy új .NET Core-alkalmazást a Visual Studióval. 

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat úgy, hogy a **megoldáskezelő** , majd a **NuGet-csomagok kezelése** lehetőségre kattint a jobb gombbal a projekt megoldásra. A megnyíló csomagkezelő válassza a **Tallózás** lehetőséget, jelölje be az **előzetes verzió** használata jelölőnégyzetet, és keressen rá `Microsoft.Azure.CognitiveServices.Vision.Face` . Válassza `2.6.0-preview.1` a verzió, majd a **telepítés** lehetőséget. 

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `face-quickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: *program. cs*. 

```console
dotnet new console -n face-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Az alkalmazás könyvtárában telepítse a .NET-hez készült Face ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.


A projekt könyvtárában nyissa meg a *program. cs* fájlt, és adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Az alkalmazás **program** osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára.


> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Face erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

Az alkalmazás **fő** metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később fogja megvalósítani.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Face .NET ügyféloldali kódtár főbb funkcióit:

|Név|Leírás|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden arc funkcióhoz szüksége van rá. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja. |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|Ez az osztály az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat kezeli. |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|Ez az osztály a rendszerkép egyetlen arca által észlelt összes adatmennyiséget jelképezi. Ezzel az oldallal kapcsolatos részletes információkat kérhet le.|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|Ez az osztály kezeli a felhőben tárolt **FaceList** -szerkezeteket, amelyek az arcok válogatott készleteit tárolják. |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| Ez az osztály kezeli a felhőalapú tárolt **személyek** szerkezetét, amelyek egyetlen személyhez tartozó arcok készletét tárolják.|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** -szerkezeteket, amelyek a különböző **személyre** kiterjedő objektumok készletét tárolják. |

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a .NET-hez készült Face ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [Személy csoport létrehozása](#create-a-person-group)
* [Arc azonosítása](#identify-a-face)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új metódusban hozza létre az ügyfelet a végponttal és a kulccsal. Hozzon létre egy **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** objektumot a kulccsal, és használja a végpontján egy **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)** objektum létrehozásához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>Segítő mezők deklarálása

A következő mezők szükségesek a később hozzáadott arc-műveletekhez. A **program** osztály gyökérkönyvtárában adja meg a következő URL-karakterláncot. Ez az URL-cím egy minta lemezképek mappájára mutat.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

A **Main** metódusban definiáljon karakterláncokat, hogy a különböző felismerési modellek típusaira mutassanak. Később meg tudja határozni, hogy melyik felismerési modellt szeretné használni az Arcfelismerés során. A beállításokkal kapcsolatos információkért tekintse [meg a felismerési modell megadása](../../Face-API-How-to-Topics/specify-recognition-model.md) című témakört.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

### <a name="get-detected-face-objects"></a>Észlelt Arcfelismerés-objektumok

Hozzon létre egy új metódust az arcok észleléséhez. A `DetectFaceExtract` metódus három képet dolgoz fel a megadott URL-címen, és létrehozza a **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)** objektumok listáját a program memóriájában. A **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** értékek listája határozza meg a kinyerni kívánt szolgáltatásokat. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> A helyi rendszerképben is felderítheti az arcokat. Tekintse meg a [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations) metódusokat, például a **DetectWithStreamAsync**.

### <a name="display-detected-face-data"></a>Észlelt Arcfelismerés

A metódus többi része `DetectFaceExtract` elemezi és kinyomtatja az összes észlelt arc attribútum-adatát. Minden attribútumot külön kell megadni az eredeti Arcfelismerés API-hívásban (a **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** listában). A következő kód dolgozza fel az összes attribútumot, de valószínűleg csak egy vagy néhányat kell használnia.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot (forrást) használ, és a találatok között megkeresi a többi arcot (TARGET) Ha egyezést talál, kinyomtatja az egyeztetett arc AZONOSÍTÓját a konzolon.

### <a name="detect-faces-for-comparison"></a>Arcok észlelése összehasonlításhoz

Először határozza meg a második Arcfelismerés módszert. A képek összevetése előtt fel kell ismernie az arcokat, és ez az észlelési módszer összehasonlítási műveletekre van optimalizálva. Nem kibontja a részletes Arcfelismerés-attribútumokat, például a fenti részben, és egy másik felismerési modellt használ.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Egyezések keresése

A következő metódus észleli a megcélzott lemezképekben található arcokat és egyetlen forrás képét. Ezután összehasonlítja őket, és megkeresi a forrás képéhez hasonló összes megcélzott képet.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Egyezések nyomtatása

A következő kód a megfeleltetés részleteit jeleníti meg a konzolon:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Arc azonosítása

Az azonosítási művelet egy személy (vagy több személy) képét veszi igénybe, és megkeresi a rendszerképben lévő egyes arcok identitását (Arcfelismerés-keresés). Összehasonlítja az észlelt elemeket egy **PersonGroup**, egy olyan, a különböző **személy** objektumokat tartalmazó adatbázissal, amelyek az arc funkciói ismertek. Az azonosítási művelet végrehajtásához először létre kell hoznia és be kell tanítania egy **PersonGroup**

### <a name="create-a-person-group"></a>Személy csoport létrehozása

A következő kód egy **PersonGroup** hoz létre hat különböző **személy** objektummal. Az egyes **személyeket** a képek egy halmazával társítja, majd az egyes személyeket az arc jellemzői alapján elismerik. A **személy** -és **PersonGroup** objektumok az ellenőrzési, azonosítási és csoportosítási műveletekben használatosak.

Deklaráljon egy karakterlánc-változót az osztály gyökérkönyvtárában, hogy az a létrehozandó **PERSONGROUP** azonosítóját jelöli.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Új metódusban adja hozzá a következő kódot. Ez a metódus az azonosítási műveletet fogja végrehajtani. A kód első blokkja a személyek nevét társítja a képekkel.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Figyelje meg, hogy ez a kód definiál egy változót `sourceImageFileName` . Ez a változó a forrás rendszerképének felel meg, &mdash; amely az azonosított személyeket tartalmazza.

Ezután adja hozzá a következő kódot egy **személy** objektum létrehozásához a szótárban, és adja hozzá a megfelelő rendszerképekből a Face (adatok) elemet. Minden **személy** objektum ugyanahhoz a **PersonGroup** van társítva egyedi azonosító sztringje alapján. Ne felejtse el átadni a változókat `client` , `url` és `RECOGNITION_MODEL1` ebbe a metódusba.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> Helyi rendszerképekből is létrehozhat **PersonGroup** . Tekintse meg a [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson) metódusokat, például a **AddFaceFromStreamAsync**.

### <a name="train-the-persongroup"></a>A PersonGroup betanítása

Miután kibontotta az adatokból a képeket, és azokat különböző **személy** objektumokra rendezi, be kell tanítania a **PersonGroup** , hogy azonosítsa az egyes **személy** objektumaihoz társított vizuális funkciókat. A következő kód meghívja az aszinkron **betanítási** módszert, és lekérdezi az eredményeket, kinyomtatja az állapotot a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> A Face API a természetben statikus, előre elkészített modelleken fut (a modell teljesítménye nem visszafejlődés meg és nem lesz fejlesztve, mivel a szolgáltatás fut). A modell által előállított eredmények megváltozhatnak, ha a Microsoft frissíti a modell hátterét anélkül, hogy teljesen új modellre kellene migrálni. A modell újabb verziójának kihasználása érdekében áttaníthatja a **PersonGroup**, és megadhatja az újabb modellt ugyanazon beléptetési képpel rendelkező paraméterként.

Most már készen áll arra, hogy az ellenőrzési, azonosítási vagy csoportosítási műveletekben felhasználja ezt a **személy** csoportot és a hozzá tartozó **személyeket** .

### <a name="identify-faces"></a>Arcok azonosítása

A következő kód a forrás képét veszi fel, és létrehozza a rendszerképben észlelt összes arc listáját. Ezek az arcok lesznek azonosítva a **PersonGroup**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

A következő kódrészlet meghívja a **IdentifyAsync** műveletet, és kiírja az eredményeket a-konzolra. Itt a szolgáltatás megkísérli a forrás-rendszerképből származó összes arc egyeztetését az adott **PersonGroup** lévő **személynek** . Ez kizárja az azonosítási módszert.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Futtassa az alkalmazást az IDE-ablak felső részén található **Debug (hibakeresés** ) gombra kattintva.

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Ha ebben a rövid útmutatóban létrehozott egy **PersonGroup** , és törölni szeretné, futtassa a következő kódot a programban:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Adja meg a törlési módszert a következő kóddal:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a .NET-hez készült Face ügyféloldali kódtárat az alapszintű arc-felismerési feladatok elvégzésére. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
> [Face API-hivatkozás (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)

* [Mi az a Face szolgáltatás?](../../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs)található.
