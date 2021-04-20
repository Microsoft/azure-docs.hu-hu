---
title: Content Moderator .NET ügyféloldali kódtár – rövid útmutató
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan ismerkedik meg az Azure Content Moderator .NET-hez való ügyféloldali kódtárával. Tartalomszűrő szoftvereket építhet be az alkalmazásába, hogy megfeleljen a szabályozásnak, vagy fenntartsa a kívánt környezetet a felhasználók számára.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: 573778316802b6e445b4c9d78576a8813af514a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726137"
---
Első lépések az Azure Content Moderator .NET-hez való ügyféloldali kódtárával. Kövesse az alábbi lépéseket a NuGet-csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz szükséges példakódot. 

Content Moderator egy olyan AI-szolgáltatás, amely lehetővé teszi a potenciálisan sértő, kockázatos vagy egyéb nem kívánt tartalmak kezelését. Az AI-alapú tartalommoderálási szolgáltatással szöveget, képet és videókat vizsgál, és automatikusan alkalmazhat tartalomjelölőket. Ezután integrálja az alkalmazást a felülvizsgálati eszközzel, amely egy online moderator-környezet egy emberi felülvizsgáló csapat számára. Tartalomszűrő szoftvereket építhet be az alkalmazásába, hogy megfeleljen a szabályozásnak, vagy fenntartsa a kívánt környezetet a felhasználók számára.

Használja a Content Moderator .NET-hez használható ügyféloldali kódtárát a következőre:

* Szöveg moderálása
* Képek moderálása
* Felülvizsgálat létrehozása

[Referenciadokumentáció](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator)  |  [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)  |  [Minták](../../samples-dotnet.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) vagy a [.NET Core aktuális verziója.](https://dotnet.microsoft.com/download/dotnet-core)
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Content Moderator-erőforrást, és hozzon létre egy Content Moderator-erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Várja meg az üzembe helyezést, és kattintson az **Erőforráshoz ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrás kulcsra és végpontra ahhoz, hogy az alkalmazást a Content Moderator. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

A Visual Studio hozzon létre egy új .NET Core-alkalmazást. 

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat. Kattintson a jobb gombbal a projektmegoldásra a Megoldáskezelő majd válassza a **NuGet-csomagok** **kezelése gombra.** A megnyíló csomagkezelőben válassza a **Tallózás** lehetőséget, jelölje be **az Include prerelease**(Előzetes) jelölőnégyzetet, és keressen a következőre: `Microsoft.Azure.CognitiveServices.ContentModerator` . Válassza ki a `2.0.0` verziót, majd telepítse a **alkalmazást.** 

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Egy konzolablakban (például cmd, PowerShell vagy Bash) az paranccsal hozzon létre egy új konzolalkalmazást `dotnet new` `content-moderator-quickstart` néven. Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappába. Az alkalmazást a következővel építheti fel:

```console
dotnet build
```

A build kimenete nem tartalmazhat figyelmeztetéseket vagy hibákat. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Telepítse a .NET-hez Content Moderator ügyféloldali kódtárat az alkalmazáskönyvtárban a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs)amely a rövid útmutatóban található példakódokat tartalmazza.

A projektkönyvtárból nyissa meg a *Program.cs* fájlt a kívánt szerkesztőben vagy IDE-ban. Adja hozzá a következő `using`-utasításokat:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

A **Program osztályban** hozzon létre változókat az erőforrás kulcsának és végpontjának.

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az Content Moderator szakaszban létrehozott  erőforrás sikeresen üzembe lett folyamatban, kattintson az **Erőforrás** ugrás gombra a **Következő lépések területen.** A kulcsot és a végpontot az  erőforrás kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.** 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információt a biztonsági Cognitive Services [talál.](../../../cognitive-services-security.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


Az alkalmazás metódusában adja hozzá az ebben a rövid útmutatóban használt metódusok `main()` hívásait. Ezeket később fogja létrehozni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Objektummodell

Az alábbi osztályok a .NET ügyféloldali kódtár néhány fő funkcióját Content Moderator kezelik.

|Név|Leírás|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient)|Erre az osztályra az összes Content Moderator szükség. Példányosodhat az előfizetés adataival, és más osztályok példányainak előállítására használhatja.|
|[ImageModeration (Képmoderálás)](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)|Ez az osztály a felnőtt tartalmak, személyes adatok vagy emberi arcok képelemzési funkcióját biztosítja.|
|[TextModeration (Szövegmoderálás)](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation)|Ez az osztály biztosítja a nyelv, trágárság, hibák és személyes adatok szövegelemzési funkcióját.|
|[Felülvizsgálatok](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)|Ez az osztály biztosítja a Felülvizsgálati API-k funkcióit, beleértve a feladatok létrehozásának módszereit, az egyéni munkafolyamatokat és az emberi felülvizsgálatokat.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következő feladatok elvégzését mutatják be a .NET-hez Content Moderator ügyféloldali kódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Szöveg moderálása](#moderate-text)
* [Képek moderálása](#moderate-images)
* [Felülvizsgálat létrehozása](#create-a-review)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új metódusban példányosíthatja az ügyfélobjektumokat a végponttal és a kulccsal.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Szöveg moderálása

A következő kód egy Content Moderator használ a szöveg törzsének elemzéséhez és az eredmények konzolra való ki kinyomtatása érdekében. A Program osztály **gyökerében** adja meg a bemeneti és kimeneti fájlokat:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Ezután a projekt gyökerében adjon hozzá egyTextFile.txt *fájlt.* Adja hozzá a saját szövegét ehhez a fájlhoz, vagy használja a következő mintaszöveget:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Ezután határozza meg a szövegmoderálási metódust valahol a **Program osztályban:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Képek moderálása

A következő kód egy Content Moderator és egy [ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation) objektumot használ a távoli képek felnőtteknek szóló és ritka tartalmú elemzéséhez.

> [!NOTE]
> Egy helyi kép tartalmát is elemezheti. A helyi [rendszerképekkel](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) használható metódusokat és műveleteket a referenciadokumentációban találhatja meg.

### <a name="get-sample-images"></a>Mintaképek lekérte

Adja meg a bemeneti és kimeneti fájlokat a **Program osztály gyökerében:**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Ezután hozza létre a bemenetiImageFiles.txt *a* projekt gyökerében. Ebben a fájlban hozzáadja a képek URL-címeit, hogy minden sorban egy &mdash; URL-címet elemezze. Az alábbi mintaképeket használhatja:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Segítőosztály meghatározása

Adja hozzá a következő osztálydefiníciót a **Program osztályon** belül. Ez a belső osztály kezeli a képmoderálási eredményeket.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>A képmoderálási metódus meghatározása

A következő metódus végig lépked a kép URL-címeit egy szövegfájlban, létrehoz egy **EvaluationData** példányt, és elemzi a képet a felnőtteknek szóló/ritka tartalom, szöveg és emberi arcok alapján. Ezután hozzáadja a végső **EvaluationData** példányt egy listához, és kiírja a visszaadott adatok teljes listáját a konzolra.

#### <a name="iterate-through-images"></a>Iteráljon a képeken

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Tartalom elemzése

A képernyőkhöz Content Moderator képattribútumokkal kapcsolatos további információkért tekintse meg a [Képmoderálási fogalmak útmutatót.](../../image-moderation-api.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Moderálási eredmények írása fájlba

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Felülvizsgálat létrehozása

A .NET Content Moderator kódtár használatával tartalmat használhatja [](https://contentmoderator.cognitive.microsoft.com) a felülvizsgálati eszközbe, hogy az emberi moderátorok át tudjanak vizsgálni. A felülvizsgálati eszközzel kapcsolatos további információkért tekintse meg a felülvizsgálati [eszköz fogalmi útmutatóját.](../../review-tool-user-guide/human-in-the-loop.md)

Az ebben a szakaszban található metódus a [Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews) osztály használatával létrehoz egy felülvizsgálatot, lekéri az azonosítóját, és ellenőrzi annak részleteit, miután emberi bevitelt kapott a felülvizsgálati eszköz webes portálján. Ezt az információt egy kimeneti szövegfájlban naplózza. 

### <a name="get-sample-images"></a>Mintaképek lekérte

Deklarálja a következő tömböt a **Program osztály gyökerében.** Ez a változó egy mintaképre hivatkozik, amely a felülvizsgálat létrehozásához használható.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Hitelesítő adatok áttekintése

Jelentkezzen be a felülvizsgálati [eszközbe,](https://contentmoderator.cognitive.microsoft.com) és olvassa be a csapat nevét. Ezután rendelje hozzá a Program osztályban a **megfelelő változóhoz.** Igény szerint be is állíthat egy visszahívási végpontot, hogy megkapja a felülvizsgálat tevékenységével kapcsolatos frissítéseket.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Segítőosztály meghatározása

Adja hozzá a következő osztálydefiníciót a **Program osztályhoz.** Ez az osztály egyetlen felülvizsgálati példányt képvisel, amely a felülvizsgálati eszköznek lesz elküldve.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Segítő metódus meghatározása

Adja hozzá a következő metódust a **Program osztályhoz.** Ez a metódus a kimeneti szövegfájlba írja a felülvizsgálati lekérdezések eredményeit.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>A felülvizsgálati létrehozási módszer meghatározása

Most már készen áll az áttekintés létrehozását és lekérdezését kezelő metódus meghatározására. Adjon hozzá egy **új, CreateReviews** metódust, és határozza meg a következő helyi változókat.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Felülvizsgálatok közzététe a felülvizsgálati eszközben

Ezután adja hozzá a következő kódot, amely végig lépked az adott mintaképeken, hozzáadja a metaadatokat, és egyetlen kötegben elküldi őket a felülvizsgálati eszköznek. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Az API-hívásból visszaadott objektum minden feltöltött kép egyedi azonosítóértékét tartalmazza. Az alábbi kód ezeket az adatokat elemezi, majd Content Moderator a kötegben az egyes lemezképek állapotát.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Áttekintés részleteinek lekérte

Az alábbi kód hatására a program megvárja a felhasználói bevitelt. Ha futásidőben kerül erre a lépésre, [](https://contentmoderator.cognitive.microsoft.com) ön is használhatja a felülvizsgálati eszközt, ellenőrizheti, hogy a mintakép fel lett-e töltve, és használhatja azt. A felülvizsgálatokkal kapcsolatos további információkért tekintse meg a [Felülvizsgálatok útmutatót.](../../review-tool-user-guide/review-moderated-images.md) Ha végzett, bármelyik billentyű lenyomása után folytathatja a programot, és lekérheti a felülvizsgálati folyamat eredményeit.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Ha ebben a forgatókönyvben visszahívási végpontot használt, a következő formátumú eseményt kell kapnia:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Futtassa az alkalmazást  az IDE ablakának tetején található Hibakeresés gombra kattintva.

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Futtassa az alkalmazást az alkalmazáskönyvtárból az `dotnet run` paranccsal.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services előfizetést, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Content Moderator .NET-kódtárat moderálási feladatok elvégzésére. Ezután egy fogalmi útmutatóban további információt olvashat a képek vagy más médiafájlok moderálásának módjáról.

> [!div class="nextstepaction"]
> [Képmoderálási fogalmak](../../image-moderation-api.md)