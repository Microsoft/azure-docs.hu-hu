---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtára a .NET-hez'
description: A .NET-hez készült űrlap-felismerő ügyféloldali kódtára használatával létrehozhat egy űrlap-feldolgozó alkalmazást, amely Kinyeri a kulcs/érték párokat és a tábla adatait az egyéni dokumentumokból.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/06/2020
ms.author: pafarley
ms.openlocfilehash: e85a6ad4619897a6c655874b43e6a6b1a7723d3a
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584646"
---
> [!IMPORTANT]
> Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja.

[Dokumentáció](/dotnet/api/overview/azure/ai.formrecognizer-readme)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/) vagy a [.net Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója.
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört. Ebben a rövid útmutatóban használhatja a [minta adathalmaz](https://go.microsoft.com/fwlink/?linkid=2090451) (letöltés és kibontás *sample_data.zip*) **alatt található fájlokat** .
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hozzon létre egy űrlap-felismerő erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Az Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `formrecognizer-quickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájlban: *program.cs*. 

```console
dotnet new console -n formrecognizer-quickstart
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

Az alkalmazás könyvtárában telepítse a .NET-hez készült űrlap-felismerő ügyféloldali kódtárat a következő paranccsal:

#### <a name="version-20"></a>[2,0-es verzió](#tab/ga)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!NOTE]
> Az űrlap-felismerő 3.0.0 SDK a 2,0-es API-verziót tükrözi

#### <a name="version-21-preview"></a>[2,1-es verzió előnézet](#tab/preview)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.1.0-beta.1
```

> [!NOTE]
> Az űrlap-felismerő 3.1.0 SDK az API 2,1-es verziójának előzetes verzióját tükrözi

---

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

A projekt könyvtárában nyissa meg a *program.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

Az alkalmazás **program** osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára.

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott űrlap-felismerő erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

Az alkalmazás **fő** metódusában adjon hozzá egy hívást az ebben a rövid útmutatóban használt aszinkron feladatokhoz. Ezeket később fogja megvalósítani.

#### <a name="version-20"></a>[2,0-es verzió](#tab/ga)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]
#### <a name="version-21-preview"></a>[2,1-es verzió előnézet](#tab/preview)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_main)]

---


## <a name="object-model"></a>Objektummodell 

Az űrlap-felismerő használatával két különböző típusú ügyfél hozható létre. Az első az, `FormRecognizerClient` hogy a szolgáltatás lekérdezését felismerő űrlapmezők és tartalom használatával kérdezi le. A második a `FormTrainingClient` használatával egyéni modelleket hozhat létre és kezelhet, amelyeket az elismerés javítása érdekében használhat. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` a következő műveleteit biztosítja:

 - Az űrlap mezőinek és tartalmának felismerése az egyéni űrlapok elemzéséhez betanított egyéni modellek használatával.  Ezeket az értékeket az objektumok egy gyűjteménye adja vissza `RecognizedForm` . Tekintse meg a példa [Egyéni űrlapok elemzése](#analyze-forms-with-a-custom-model)című témakört.
 - Űrlap tartalmának felismerése, beleértve a táblákat, a sorokat és a szavakat, anélkül, hogy be kellene tanítani a modellt.  Az űrlap tartalma objektumok gyűjteményében lesz visszaadva `FormPage` . Lásd: példa [elemzése elrendezés](#analyze-layout).
 - Az Egyesült államokbeli nyugták általános mezőinek felismerése egy előre képzett beérkezési modell használatával az űrlap-felismerő szolgáltatásban. Ezeket a mezőket és a metaadatokat az objektumok egy gyűjteménye adja vissza `RecognizedForm` . Lásd: példák [elemzése nyugták](#analyze-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` a következő műveleteit biztosítja:

- Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez.  A `CustomFormModel` visszatérési értéke jelzi, hogy a modell milyen típusú adattípusokat fog elemezni, és hogy milyen mezőket fog kibontani az egyes űrlapokhoz.
- Egyéni modellek betanítása az egyéni űrlapok címkézésével megadott mezők és értékek elemzéséhez.  A `CustomFormModel` visszaadja a modell által kinyert mezőket, valamint az egyes mezők becsült pontosságát.
- A fiókban létrehozott modellek kezelése.
- Egyéni modell másolása az egyik űrlap-felismerő erőforrásból egy másikba.

Tekintse át a [modell betanítására](#train-a-custom-model) és az [egyéni modellek kezelésére](#manage-custom-models)szolgáló példákat.

> [!NOTE]
> A modellek grafikus felhasználói felülettel is betanítható, például az [űrlap-felismerő címkéző eszköz](../../quickstarts/label-tool.md)használatával.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a .NET-hez készült űrlap-felismerő ügyféloldali kódtára használatával:

#### <a name="version-20"></a>[2,0-es verzió](#tab/ga)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Elrendezés elemzése](#analyze-layout)
* [Visszaigazolások elemzése](#analyze-receipts)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

#### <a name="version-21-preview"></a>[2,1-es verzió előnézet](#tab/preview)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Elrendezés elemzése](#analyze-layout)
* [Visszaigazolások elemzése](#analyze-receipts)
* [Üzleti kártyák elemzése](#analyze-business-cards)
* [Számlák elemzése](#analyze-invoices)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A **Main (fő**) alatt hozzon létre egy nevű új metódust `AuthenticateClient` . Ezt más feladatokban fogja használni a kéréseknek az űrlap-felismerő szolgáltatásba való hitelesítéséhez. Ez a metódus az `AzureKeyCredential` objektumot használja, így ha szükséges, az API-kulcsot új ügyfélalkalmazások létrehozása nélkül is frissítheti.

> [!IMPORTANT]
> Szerezze be a kulcsot és a végpontot a Azure Portalból. Ha az **Előfeltételek** szakaszban létrehozott űrlap-felismerő erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Például az [Azure Key Vault](../../../../key-vault/general/overview.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]

Ismételje meg a fenti lépéseket egy olyan új metódusnál, amely hitelesíti a betanítási ügyfelet.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth_training)]

## <a name="get-assets-for-testing"></a>Eszközök beszerzése teszteléshez 

Emellett a képzési és tesztelési adatok URL-címeihez is hozzá kell adnia a hivatkozásokat. Adja hozzá ezeket a **program** osztályának gyökeréhez.

* [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-cím lekérése":::
* Ezután ismételje meg a fenti lépéseket a blob Storage-tárolóban lévő egyes dokumentumok SAS URL-címének lekéréséhez. Mentse azt egy ideiglenes helyre is.
* Végül mentse az alább felsorolt minta-rendszerkép (ek) URL-címét (a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)is elérhető). 

#### <a name="version-20"></a>[2,0-es verzió](#tab/ga)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_urls)]
#### <a name="version-21-preview"></a>[2,1-es verzió előnézet](#tab/preview)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_urls)]

---


## <a name="analyze-layout"></a>Elrendezés elemzése

Az űrlap-felismerő használatával elemezheti a dokumentumokat, vonalakat és szavakat a dokumentumokban anélkül, hogy egy modellt kellene betanítania. A visszaadott érték egy **FormPage** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához. További információ az elrendezés kinyeréséről: az [elrendezés fogalmi útmutatója](../../concept-layout.md).

Egy adott URL-címen található fájl tartalmának elemzéséhez használja a `StartRecognizeContentFromUri` metódust.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> A tartalmat egy helyi fájlból is lekérheti. Tekintse meg a [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) metódusokat, például a **StartRecognizeContent**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakód-kódot.

A feladat többi része kiírja a tartalmat a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]

### <a name="output"></a>Kimenet

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```


## <a name="analyze-invoices"></a>Számlák elemzése

#### <a name="version-20"></a>[2,0-es verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

#### <a name="version-21-preview"></a>[2,1-es verzió előnézet](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az értékesítési számlákból származó általános mezőket egy előre betanított modell használatával. A számlázási elemzéssel kapcsolatos további információkért tekintse meg a [számla fogalmi útmutatóját](../../concept-invoices.md).

A számlák URL-címről való elemzéséhez használja a `StartRecognizeInvoicesFromUriAsync` metódust. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_call)]

> [!TIP]
> A helyi számla lemezképeit is elemezheti. Tekintse meg a [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) metódusokat, például a **StartRecognizeInvoices**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakód-kódot.

A visszaadott érték objektumok gyűjteménye `RecognizedForm` : egyet a beküldött dokumentum minden egyes számlájához. A következő kód feldolgozza a számlát a megadott URI-n, és kiírja a fő mezőket és értékeket a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_print)]

---


## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan lehet a modelleket saját adataival betanítani. A betanított modellek olyan strukturált adatokat tudnak kialakítani, amelyek az eredeti dokumentum kulcs/érték kapcsolatait tartalmazzák. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például az [űrlap-felismerő minta feliratozási eszközét](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanításával elemezheti az egyéni űrlapokon található összes mezőt és értéket anélkül, hogy manuálisan címkézi a betanítási dokumentumokat. A következő metódus egy modellt hoz létre egy adott dokumentumon, és kiírja a modell állapotát a konzolra. 


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]


A visszaadott `CustomFormModel` objektum információt tartalmaz a modell által elemezhető típusokról, valamint az egyes űrlapokból kinyerhető mezőkről. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Végül visszaállíthatja a betanított modell AZONOSÍTÓját a későbbi lépésekben való használathoz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="output"></a>Kimenet

Ez a válasz csonkolt az olvashatóság érdekében.

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ... 
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ... 
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

Egyéni modelleket is betaníthat, ha manuálisan címkézi a betanítási dokumentumokat. A címkékkel való képzés bizonyos helyzetekben jobb teljesítményt eredményez. Ha címkéket szeretne betanítani, `\<filename\>.pdf.labels.json` a blob Storage-tárolóban speciális címke-információs fájlokkal () kell rendelkeznie a betanítási dokumentumok mellett. Az [űrlap-felismerő minta címkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkék létrehozásához. Miután megadta őket, meghívhatja a metódust a következőre `StartTrainingAsync` `uselabels` beállított paraméterrel: `true` . 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

A visszaadott `CustomFormModel` érték jelzi a modell által kinyerhető mezőket, valamint az egyes mezők becsült pontosságát. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]


### <a name="output"></a>Kimenet

Ez a válasz csonkolt az olvashatóság érdekében.

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ... 
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ... 
```

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a szakasz azt mutatja be, hogyan lehet kinyerni a kulcs/érték információkat és az egyéb tartalmakat az egyéni űrlapok típusaiból, a saját űrlapjaival betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az ID-t az alábbi metódusba tudja adni.

A `StartRecognizeCustomFormsFromUri` metódust fogja használni. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> Elemezheti a helyi fájlokat is. Tekintse meg a [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) metódusokat, például a **StartRecognizeCustomForms**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakód-kódot.

A visszaadott érték objektumok gyűjteménye `RecognizedForm` : egyet a beküldött dokumentum minden lapján. A következő kód kinyomtatja az elemzési eredményeket a-konzolra. Kinyomtatja az egyes felismert mezőket és a hozzá tartozó értékeket, valamint a megbízhatósági pontszámot.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]


### <a name="output"></a>Kimenet

Ez a válasz csonkolt az olvashatóság érdekében.

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ... 
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="analyze-receipts"></a>Visszaigazolások elemzése

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az Egyesült államokbeli nyugták közös mezőit egy előre képzett beérkezési modell használatával. További információ a bevételezési elemzésről: a [nyugták fogalmi útmutatója](../../concept-receipts.md).

Az URL-címekről történő visszaigazolások elemzéséhez használja a `StartRecognizeReceiptsFromUri` metódust. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> Elemezheti a helyi visszaigazolási képeket is. Tekintse meg a [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) metódusokat, például a **StartRecognizeReceipts**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakód-kódot.

A visszaadott érték objektumok gyűjteménye `RecognizedReceipt` : egyet a beküldött dokumentum minden lapján. A következő kód dolgozza fel a nyugtát a megadott URI-n, és kiírja a fő mezőket és értékeket a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_print)]

### <a name="output"></a>Kimenet 

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

## <a name="analyze-business-cards"></a>Üzleti kártyák elemzése

#### <a name="version-20"></a>[2,0-es verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

#### <a name="version-21-preview"></a>[2,1-es verzió előnézet](#tab/preview)


Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az angol üzleti kártyákból származó általános mezőket egy előre betanított modell használatával. További információ a névjegykártya-elemzésről: a [Business Cards fogalmi útmutatója](../../concept-business-cards.md).

Az üzleti kártyák URL-címről való elemzéséhez használja a `StartRecognizeBusinessCardsFromUriAsync` metódust. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_call)]

> [!TIP]
> Elemezheti a helyi visszaigazolási képeket is. Tekintse meg a [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) metódusokat, például a **StartRecognizeBusinessCards**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakód-kódot.

A visszaadott érték objektumok gyűjteménye `RecognizedForm` : egy a dokumentum minden kártyája esetében. A következő kód feldolgozza a névjegykártyát a megadott URI-n, és kiírja a fő mezőket és értékeket a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_print)]

---

## <a name="manage-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. A következő módszeren belül több műveletet is végrehajthat:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>A FormRecognizer-erőforrás fiókban található modellek számának megkeresése

A következő kódrészlet ellenőrzi, hogy az űrlap-felismerő fiókban hány modell lett mentve, és összehasonlítja azt a fiókra vonatkozó korláttal.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>Kimenet 

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Az erőforrás-fiókban jelenleg tárolt modellek listázása

A következő kódrészlet felsorolja a fiókban lévő aktuális modelleket, és kiírja az adataikat a-konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]


### <a name="output"></a>Kimenet 

Ez a válasz csonkolt az olvashatóság érdekében.

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>Adott modell beszerzése a modell AZONOSÍTÓjának használatával

A következő kód blokkolja az új modellt (akárcsak a [betanítási modell](#train-a-model-without-labels) szakaszban), majd egy második hivatkozást is beolvas az azonosítójának használatával.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="output"></a>Kimenet 

Ez a válasz csonkolt az olvashatóság érdekében.

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrás-fiókból

Az AZONOSÍTÓra hivatkozva egy modellt is törölhet a fiókjából. Ez a lépés a metódust is kizárja.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]


## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```dotnet
dotnet run
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a .NET SDK-val használja a Cognitive Services űrlap-felismerő ügyféloldali függvénytárát, a szolgáltatás által visszaadott hibák a-t fogják eredményezni `RequestFailedException` . A rendszer ugyanazt a HTTP-állapotkódot fogja tartalmazni, amelyet egy REST API kérelem visszaadott volna.

Ha például egy érvénytelen URI azonosítóval ellátott nyugtát küld, a rendszer `400` hibaüzenetet küld, amely a "hibás kérést" jelzi.

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Megfigyelheti, hogy a művelethez hasonló további információk (például az ügyfél-kérelmek azonosítója) naplózva vannak.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az űrlap-felismerő .NET ügyféloldali kódtárat használta a modellek betanításához és az űrlapok különböző módokon történő elemzéséhez. Következő lépésként Ismerkedjen meg a jobb betanítási adatkészlet létrehozásával és a pontosabb modellek előállításával.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

* [Mi a Form Recognizer?](../../overview.md)
* A jelen útmutatóban szereplő mintakód (és egyebek) megtalálható a [githubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
