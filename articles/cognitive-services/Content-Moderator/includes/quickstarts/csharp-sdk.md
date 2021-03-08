---
title: Content Moderator .NET ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti meg a .NET-hez készült Azure Content Moderator ügyféloldali kódtárat. Tartalom-szűrési szoftvereket hozhat létre az alkalmazásban, hogy megfeleljen a szabályozásoknak, vagy megőrizze a kívánt környezetet a felhasználók számára.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: d6f27ca3635ff1efb80e5261b7a5a07269304dfd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444136"
---
Ismerkedés az Azure Content Moderator .NET-hez készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a NuGet csomagot, és próbálja ki az alapszintű feladatok példáját. 

Content Moderator egy AI-szolgáltatás, amely lehetővé teszi a potenciálisan sértő, kockázatos vagy egyéb módon nemkívánatos tartalmak kezelését. A AI-alapú Content moderációs szolgáltatással szövegeket, képeket és videókat kereshet, és automatikusan alkalmazhatja a tartalmi jelzőket. Ezután integrálhatja alkalmazását a felülvizsgálati eszközzel, amely egy online moderátori környezet az emberi véleményezők csapatának. Tartalom-szűrési szoftvereket hozhat létre az alkalmazásban, hogy megfeleljen a szabályozásoknak, vagy megőrizze a kívánt környezetet a felhasználók számára.

A .NET-hez készült Content Moderator ügyféloldali kódtára a következőre használható:

* Mérsékelt szöveg
* Közepes méretű képek
* Felülvizsgálat létrehozása

[Dokumentáció](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)  |  [Példák](../../samples-dotnet.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/) vagy a [.net Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" hozzon létre egy Content moderator erőforrást, "  target="_blank"> és hozzon létre egy Content moderator-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Content Moderatorhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Hozzon létre egy új .NET Core-alkalmazást a Visual Studióval. 

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat úgy, hogy a **megoldáskezelő** , majd a **NuGet-csomagok kezelése** lehetőségre kattint a jobb gombbal a projekt megoldásra. A megnyíló csomagkezelő válassza a **Tallózás** lehetőséget, jelölje be az **előzetes verzió** használata jelölőnégyzetet, és keressen rá `Microsoft.Azure.CognitiveServices.ContentModerator` . Válassza `2.0.0` a verzió, majd a **telepítés** lehetőséget. 

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `content-moderator-quickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájlban: *program.cs*.

```console
dotnet new console -n content-moderator-quickstart
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

Az alkalmazás könyvtárában telepítse az Content Moderator .NET-hez készült ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

A projekt könyvtárában nyissa meg a *program.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő `using`-utasításokat:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

A **program** osztályban hozzon létre változókat az erőforrás kulcsa és végpontja számára.

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Content moderator erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


Az alkalmazás `main()` metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később fogja létrehozni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Objektummodell

A következő osztályok a Content Moderator .NET ügyféloldali kódtár főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient)|Ez az osztály minden Content Moderator funkcióhoz szükséges. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja.|
|[ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)|Ez az osztály lehetővé teszi a képek elemzését a felnőtt tartalmak, a személyes adatok vagy az emberi arcok számára.|
|[TextModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation)|Ez az osztály a nyelv, a káromkodás, a hibák és a személyes adatok szövegének elemzéséhez nyújt funkciókat.|
|[Felülvizsgálatok](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)|Ez az osztály a felülvizsgálati API-k funkcionalitását biztosítja, beleértve a feladatok létrehozására, az egyéni munkafolyamatokra és az emberi felülvizsgálatokra vonatkozó módszereket is.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat az Content Moderator .NET-hez készült ügyféloldali kódtára használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Mérsékelt szöveg](#moderate-text)
* [Közepes méretű képek](#moderate-images)
* [Felülvizsgálat létrehozása](#create-a-review)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új metódusban hozza létre az ügyféloldali objektumokat a végponttal és a kulccsal.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Mérsékelt szöveg

A következő kód egy Content Moderator ügyfelet használ egy szöveg szövegtörzsének elemzésére és az eredmények a konzolra való kinyomtatására. A **program** osztály gyökérkönyvtárában adja meg a bemeneti és kimeneti fájlokat:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Ezután a projekt gyökerében adjon hozzá egy *TextFile.txt* fájlt. Adja hozzá saját szövegét a fájlhoz, vagy használja a következő mintaszöveg:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Ezután adja meg a Text moderációs metódust a **program** osztályban:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Közepes méretű képek

A következő kód egy Content Moderator ügyfelet használ a [ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation) objektummal együtt, hogy elemezze a felnőtteknek és a zamatos tartalomnak a távoli lemezképeit.

> [!NOTE]
> Elemezheti egy helyi rendszerkép tartalmát is. A helyi rendszerképekkel működő módszerekhez és műveletekhez a [dokumentációban](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) talál útmutatást.

### <a name="get-sample-images"></a>Mintaképek beolvasása

Adja meg a bemeneti és kimeneti fájlokat a **program** osztályának gyökerében:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Ezután hozza létre a bemeneti fájlt *ImageFiles.txt* a projekt gyökerében. Ebben a fájlban hozzá kell adnia a lemezképek URL-címeit az &mdash; egyes sorokon egy URL-cím elemzéséhez. A következő minta lemezképeket használhatja:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Segítő osztály definiálása

Adja hozzá a következő osztály-definíciót a **program** osztályon belül. Ez a belső osztály a képek moderálásának eredményeit fogja kezelni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>A képmoderálási módszer megadása

A következő módszer a képurl-címek egy szövegfájlban való megismétlését, egy **EvaluationData** -példány létrehozását, valamint a felnőtt/zamatos tartalom, a szöveg és az emberi arcok rendszerképének elemzését végzi. Ezután hozzáadja a végső **EvaluationData** -példányt egy listához, és a visszaadott adatot a konzolra írja.

#### <a name="iterate-through-images"></a>Iteráció a képeken

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Tartalom elemzése

További információ a Content Moderator képernyőt tartalmazó képattribútumokról: a [képek moderálási fogalmi](../../image-moderation-api.md) útmutatója.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Moderációs eredmények írása fájlba

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Felülvizsgálat létrehozása

A Content Moderator .NET ügyféloldali kódtár használatával tartalmat adhat a [felülvizsgálati eszközhöz](https://contentmoderator.cognitive.microsoft.com) , így az emberi moderátorok áttekinthetik azt. További információ a felülvizsgálati eszközről: a [felülvizsgálati eszköz fogalmi útmutatója](../../review-tool-user-guide/human-in-the-loop.md).

Az ebben a szakaszban szereplő metódus a [Reviews osztály használatával](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews) hozza létre a felülvizsgálatot, beolvassa az azonosítóját, és az emberi adatoknak a felülvizsgálati eszköz webportálon való fogadása után ellenőrizze annak adatait. Az összes információt egy kimeneti szövegfájlban naplózza. 

### <a name="get-sample-images"></a>Mintaképek beolvasása

Deklarálja a következő tömböt a **program** osztályának gyökerében. Ez a változó a felülvizsgálat létrehozásához használandó mintát ábrázolja.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Felülvizsgálati hitelesítő adatok beolvasása

Jelentkezzen be a [felülvizsgálati eszközbe](https://contentmoderator.cognitive.microsoft.com) , és kérje le a csapat nevét. Ezután rendelje hozzá a **program** osztály megfelelő változóhoz. Megadhat egy visszahívási végpontot is, amely a felülvizsgálat tevékenységére vonatkozó frissítéseket fogad.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Segítő osztály definiálása

Adja hozzá a következő osztály-definíciót a **program** osztályán belül. Ez az osztály a felülvizsgálati eszközhöz elküldött egyetlen felülvizsgálati példányra lesz felhasználva.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Segítő módszer definiálása

Adja hozzá a következő metódust a **program** osztályhoz. Ez a metódus a lekérdezési lekérdezések eredményét fogja írni a kimeneti szövegfájlba.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>A felülvizsgálati módszer megadása

Most már készen áll arra, hogy meghatározza azt a metódust, amely kezeli a felülvizsgálat létrehozását és lekérdezését. Adjon hozzá egy új metódust, **CreateReviews**, és adja meg a következő helyi változókat.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>A felülvizsgálati eszközre vonatkozó felülvizsgálatok közzététele

Ezután adja hozzá a következő kódot, hogy megismételje az adott mintaképeket, adjon hozzá metaadatokat, és küldje el őket a felülvizsgálati eszközre egyetlen kötegben. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Az API-hívás által visszaadott objektum egyedi azonosító értékeket tartalmaz minden feltöltött rendszerképhez. A következő kód elemzi ezeket az azonosítókat, majd a kötegben lévő egyes rendszerképek állapotának lekérdezéséhez használja azokat Content Moderator.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Áttekintés részleteinek beolvasása

A következő kód azt eredményezi, hogy a program megvárja a felhasználói bevitelt. Ha ezt a lépést futásidőben futtatja, nyissa meg a [felülvizsgálati eszközt](https://contentmoderator.cognitive.microsoft.com) , és ellenőrizze, hogy a minta rendszerképe fel lett-e töltve, és hogyan használhatja azt. A felülvizsgálattal való interakcióval kapcsolatos információkért tekintse meg a következő témakört: ismertető [útmutató](../../review-tool-user-guide/review-moderated-images.md). Ha elkészült, a billentyű lenyomásával folytathatja a programot, és lekérheti a felülvizsgálati folyamat eredményét.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Ha ebben a forgatókönyvben visszahívási végpontot használt, akkor a következő formátumú eseményt kell kapnia:

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

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan használhatja a Content Moderator .NET-függvénytárat moderálási feladatok elvégzésére. Következő lépésként tekintse meg a képek vagy más média moderálását egy elméleti útmutató elolvasásával.

> [!div class="nextstepaction"]
> [Képek moderálási fogalmai](../../image-moderation-api.md)

* [Mi az az Azure Content Moderator?](../../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs)található.
