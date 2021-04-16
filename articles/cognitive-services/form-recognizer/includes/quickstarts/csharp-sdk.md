---
title: 'Rövid útmutató: Form Recognizer .NET-hez való ügyféloldali kódtár létrehozása'
description: A Form Recognizer .NET-hez készült ügyféloldali kódtára segítségével létrehozhat egy űrlapfeldolgozó alkalmazást, amely kulcs/érték párokat és táblaadatokat fog kinyerni az egyéni dokumentumokból.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: b7a35046a7f170365974c50a5be99f35cb4a868f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516434"
---
<!-- markdownlint-disable MD024 -->
> [!IMPORTANT]
> A cikkben olvasható kód az egyszerűség kedvéért szinkron metódusokat és nem biztonságos hitelesítőadat-tárolót használ.

[Referenciadokumentáció](/dotnet/api/overview/azure/ai.formrecognizer-readme)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Package (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio IDE vagy](https://visualstudio.microsoft.com/vs/) a [.NET Core aktuális verziója.](https://dotnet.microsoft.com/download/dotnet-core)
* Egy Azure Storage-blob, amely betanításadatok készletét tartalmazza. A [betanítás adatkészletének összeállítására](../../build-training-data-set.md) vonatkozó tippekért és lehetőségekért tekintse meg a betanítás adatkészletének összeállítása egyéni modellhez való összeállítását. Ebben a rövid útmutatóban a mintaadatkészlet **Betanítás** mappájában [található](https://go.microsoft.com/fwlink/?linkid=2090451) fájlokat használhatja (töltse le és bontsa ki a *sample_data.zip).*
* Az Azure-előfizetés létrehozása után hozzon létre egy Form Recognizer-erőforrást, Form Recognizer a Azure Portal a kulcs és a végpont <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" "  target="_blank"> </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás elemre.**
  * Szüksége lesz a létrehozott erőforrás kulcsának és végpontjának létrehozására, hogy az alkalmazást a Form Recognizer API-hoz. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
  * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

Egy konzolablakban (például cmd, PowerShell vagy Bash) az paranccsal hozzon létre egy új konzolalkalmazást `dotnet new` `formrecognizer-quickstart` néven. Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájllal: *program.cs*.

```console
dotnet new console -n formrecognizer-quickstart
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

Az alkalmazás könyvtárában telepítse a .NET-hez Form Recognizer ügyféloldali kódtárat a következő paranccsal:

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.1.0-beta.4
```

> [!NOTE]
> A Form Recognizer 3.1.0-beta.4 SDK a _API 2.1-preview.3 verziót tükrözi.

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!NOTE]
> A Form Recognizer 3.0.0 SDK az API 2.0-s verzióját tükrözi

---

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ezt a [GitHubon találhatja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)meg, amely ebben a rövid útmutatóban található példakódokat tartalmazza.

A projektkönyvtárból nyissa meg a *Program.cs* fájlt a kívánt szerkesztőben vagy IDE-ban. Adja hozzá a következő `using` irányelveket:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

Az alkalmazás **Program** osztályában hozzon létre változókat az erőforrás kulcsához és végpontjához.

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az Form Recognizer szakaszban létrehozott  erőforrás sikeresen üzembe lett folyamatban, kattintson az **Erőforrás** ugrás gombra a **Következő lépések területen.** A kulcsot és a végpontot az  erőforrás kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.**
>
> Ne felejtse el eltávolítani a kulcsot a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információt Cognitive Services [biztonsági cikk](../../../cognitive-services-security.md) tartalmaz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

Az alkalmazás **Main** metódusában adjon hozzá egy hívást az ebben a rövid útmutatóban használt aszinkron feladatokhoz. Ezeket később fogja megvalósítani.

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_main)]

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]

---

## <a name="object-model"></a>Objektummodell

A Form Recognizer két különböző ügyféltípust hozhat létre. Az első az `FormRecognizerClient` űrlap felismert mezőinek és tartalmának lekérdezésére használható. A második a használatával hozhat létre és kezelhet egyéni modelleket, amelyek `FormTrainingClient` segítségével javíthatja a felismerést.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` A a következő műveleteket biztosítja:

* Űrlapmezők és tartalmak felismerése az egyéni űrlapok elemzéséhez betanított egyéni modellekkel.  Ezeket az értékeket objektumgyűjtemények ják `RecognizedForm` vissza. Lásd az [Egyéni űrlapok elemzése példát.](#analyze-forms-with-a-custom-model)
* Űrlaptartalmak, köztük táblák, sorok és szavak felismerése modell betanítása nélkül.  Az űrlap tartalma objektumgyűjteményben lesz `FormPage` visszaadva. Lásd az [Elrendezés elemzése példát.](#analyze-layout)
* A közös mezők felismerése az USA-nyugtákból, névjegykártyákból, számlákból és személyazonossági dokumentumokból egy előre betanított modellel a Form Recognizer szolgáltatásban.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` A a következő műveleteket biztosítja:

* Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez.  A visszaadott érték jelzi a modell által elemzett űrlaptípusokat és az egyes űrlaptípusokhoz `CustomFormModel` kinyert mezőket.
* Egyéni modellek betanítása adott mezők és értékek elemzéséhez az egyéni űrlapok címkézése segítségével.  A visszaadott érték jelzi a modell által kinyert mezőket, valamint az egyes mezők becsült `CustomFormModel` pontosságát.
* A fiókjában létrehozott modellek kezelése.
* Egyéni modell másolása egyik erőforrásból Form Recognizer másikba.

Példák a [Modell betanítása és az](#train-a-custom-model) Egyéni modellek kezelése [használatára.](#manage-custom-models)

> [!NOTE]
> A modellek grafikus felhasználói felülettel is betaníthatóak, például a [Form Recognizer eszköz használatával.](../../quickstarts/label-tool.md)

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következő feladatok elvégzését mutatják be a .NET-hez Form Recognizer ügyféloldali kódtárával:
<!-- markdownlint-disable MD001 -->

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Elrendezés elemzése](#analyze-layout)
* [Nyugták elemzése](#analyze-receipts)
* [Névjegykártyák elemzése](#analyze-business-cards)
* [Számlák elemzése](#analyze-invoices)
* [Identitásdokumentumok elemzése](#analyze-identity-documents)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Elrendezés elemzése](#analyze-layout)
* [Nyugták elemzése](#analyze-receipts)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A **Main alatt** hozzon létre egy nevű új metódust. `AuthenticateClient` Ezt más feladatokban is felhasználhatja a kérések hitelesítésére a Form Recognizer szolgáltatásban. Ez a metódus az objektumot használja, így szükség esetén új ügyfélobjektumok létrehozása nélkül frissítheti az `AzureKeyCredential` API-kulcsot.

> [!IMPORTANT]
> Szerezze be a kulcsot és a végpontot a Azure Portal. Ha az Form Recognizer szakaszban létrehozott  erőforrás sikeresen üzembe lett folyamatban, kattintson az **Erőforrás** ugrás gombra a **Következő lépések területen.** A kulcsot és a végpontot az  erőforrás kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.**
>
> Ne felejtse el eltávolítani a kulcsot a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. Például: [Azure Key Vault.](../../../../key-vault/general/overview.md)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]

Ismételje meg a fenti lépéseket a betanító ügyfél hitelesítésére használt új metódussal is.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth_training)]

## <a name="get-assets-for-testing"></a>Eszközök lekérte a teszteléshez

A betanítás és a tesztelési adatok URL-címére mutató hivatkozásokat is hozzá kell adni. Adja hozzá ezeket a Program osztály **gyökerében.**

* [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-lekérés":::
* Ezután ismételje meg a fenti lépéseket, hogy lekérte egy adott dokumentum SAS URL-címét a Blob Storage-tárolóban. Mentse egy ideiglenes helyre is.
* Végül mentse az alábbi mintakép(ek) URL-címét (elérhető a [GitHubon is).](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_urls)]

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_urls)]

---

## <a name="analyze-layout"></a>Elrendezés elemzése

A dokumentumokkal Form Recognizer, vonalakat és szavakat elemezhet a dokumentumokban anélkül, hogy modellt kellene betanítanunk. A visszaadott érték a **FormPage** objektumok gyűjteménye: egy az elküldött dokumentum minden oldalához. Az elrendezéskinyerésről az Elrendezés fogalmi [útmutatóban található további információ.](../../concept-layout.md)

Egy adott URL-címen található fájl tartalmának elemzéséhez használja a `StartRecognizeContentFromUri` metódust.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> Helyi fájlból is lekért tartalom. Lásd a [FormRecognizerClient metódusokat,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) például a **StartRecognizeContent metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

A feladat további része a tartalominformációt a konzolon nyomtatja ki.

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

## <a name="analyze-receipts"></a>Nyugták elemzése

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki közös mezőket az USA-nak megfelelő nyugtákból egy előre betanított nyugtamodell használatával. A nyugtaelemzéssel kapcsolatos további információkért lásd a [nyugták fogalmi útmutatóját.](../../concept-receipts.md)

Az URL-címről származó nyugták elemzéséhez használja a `StartRecognizeReceiptsFromUri` metódust.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> A helyi nyugták képeit is elemezheti. Lásd a [FormRecognizerClient metódusokat,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) például a **StartRecognizeReceipts metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

A visszaadott érték objektumok gyűjteménye: egy az elküldött dokumentum minden `RecognizedForm` oldalához. A következő kód feldolgozza a nyugtát az adott URI-ban, és kinyomtatja a fő mezőket és értékeket a konzolon.

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

## <a name="analyze-business-cards"></a>Névjegykártyák elemzése

####  <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki gyakori mezőket angol névjegykártyákból egy előre betanított modell használatával. A névjegykártya-elemzéssel kapcsolatos további információkért lásd a [névjegykártyák fogalmi útmutatóját.](../../concept-business-cards.md)

A névjegykártyák URL-címből való elemzéséhez használja a `StartRecognizeBusinessCardsFromUriAsync` metódust.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_call)]

> [!TIP]
> A helyi nyugták képeit is elemezheti. Lásd a [FormRecognizerClient metódusokat,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) például a **StartRecognizeBusinessCards metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

A következő kód feldolgozza a névjegykártyát az adott URI-ban, és kiírja a fő mezőket és értékeket a konzolra.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_print)]

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="analyze-invoices"></a>Számlák elemzése

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki gyakori mezőket az értékesítési számlákból egy előre betanított modell használatával. A számlaelemzéssel kapcsolatos további információkért lásd a számla [fogalmi útmutatóját.](../../concept-invoices.md)

A számlák URL-címről való elemzéséhez használja a `StartRecognizeInvoicesFromUriAsync` metódust.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_call)]

> [!TIP]
> Helyi számlaképeket is elemezhet. Lásd a [FormRecognizerClient metódusokat,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) például a **StartRecognizeInvoices metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

A következő kód feldolgozza a számlát az adott URI-ban, és kinyomtatja a fő mezőket és értékeket a konzolon.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_print)]

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="analyze-identity-documents"></a>Identitásdokumentumok elemzése

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

Ez a szakasz azt mutatja be, hogyan elemezheti és vonhat ki kulcsfontosságú információkat a kormány által kiadott azonosítási dokumentumokból ( világszerte használt útlevelek és az Egyesült Államok sofőrlicencei) az előre összeállított Form Recognizer használatával. Az identitásdokumentumok elemzésével kapcsolatos további információkért tekintse meg az előre összeállított azonosítási modell [fogalmi útmutatóját.](../../concept-identification-cards.md)

Az URI-ről származó identitásdokumentumok elemzéséhez használja a `StartRecognizeIdDocumentsFromUriAsync` metódust.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs" id="snippet_id_call":::

> [!TIP]
> A helyi identitásdokumentum-képeket is elemezheti. Lásd a [FormRecognizerClient metódusokat,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) például a **StartRecognizeIdDocumentsAsync metódust.** A helyi rendszerképeket érintő forgatókönyvekért tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakódot is.

A következő kód feldolgozza az adott URI-ban található identitásdokumentumot, és kinyomtatja a fő mezőket és értékeket a konzolon.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs" id="snippet_id_print":::

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan betanítható egy modell a saját adataival. A betanított modell olyan strukturált adatokat képes kihozni, amelyek tartalmazzák a kulcs/érték kapcsolatokat az eredeti űrlapdokumentumban. A modell betanítása után tesztelheti és újra betaníthatja, és végül arra használhatja, hogy az igényeinek megfelelően megbízhatóan kinyerje az adatokat több űrlapról.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például a Form Recognizer [eszköz mintacímkéző eszközével.](../../quickstarts/label-tool.md)

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzésére a betanító dokumentumok manuális címkézése nélkül. Az alábbi metódus egy adott dokumentumkészleten keresztül készíti be a modellt, majd a konzolon kinyomtatja a modell állapotát.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]

A visszaadott objektum információkat tartalmaz a modell által elemzett űrlaptípusokról és az egyes űrlaptípusokból `CustomFormModel` kinyerni kívánt mezőkről. A következő kódblokk ezt az információt a konzolon nyomtatja ki.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Végül adja vissza a betanított modell azonosítóját a későbbi lépésekben való használathoz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="output"></a>Kimenet

Ez a válasz le lett csonkolva az olvashatóság érdekében.

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

Egyéni modelleket is betaníthat a betanítás dokumentumainak manuális címkézése által. A címkékkel való betanítás bizonyos helyzetekben jobb teljesítményt eredményez. A címkékkel való betanításhoz speciális címkeinformációs fájlokra () van szükség a Blob Storage-tárolóban `\<filename\>.pdf.labels.json` a betanítás dokumentumai mellett. A [Form Recognizer mintacímkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkefájlok létrehozásához. Ha már megvan, a paraméterrel hívhatja meg a `StartTrainingAsync` `uselabels` `true` metódust.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

A visszaadott érték jelzi a modell által kinyerhető mezőket, valamint az egyes mezőkben található becsült `CustomFormModel` pontosságot. A következő kódblokk ezt az információt a konzolon nyomtatja ki.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]

### <a name="output"></a>Kimenet

Ez a válasz le lett csonkolva az olvashatóság érdekében.

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

Ez a szakasz azt mutatja be, hogyan lehet kulcs/érték információkat és egyéb tartalmakat kinyerni az egyéni űrlaptípusokból a saját űrlapokkal betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az azonosítóját az alábbi metódusnak tovább tudja adni.

A metódust fogja `StartRecognizeCustomFormsFromUri` használni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> Helyi fájlokat is elemezhet. Lásd a [FormRecognizerClient metódusokat,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) például a **StartRecognizeCustomForms metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

A visszaadott érték objektumok gyűjteménye: egy az elküldött dokumentum minden `RecognizedForm` oldalához. A következő kód kinyomtatja az elemzési eredményeket a konzolon. Kinyomtatja az egyes felismert mezőket és a hozzájuk tartozó értékeket, valamint egy megbízhatósági pontszámot.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]

### <a name="output"></a>Kimenet

Ez a válasz le lett csonkolva az olvashatóság érdekében.

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

## <a name="manage-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. A következő metóduson belül több műveletet is el kell majd végrehajtásához:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Ellenőrizze a modellek számát a FormRecognizer erőforrásfiókban

A következő kódblokk ellenőrzi, hogy hány modellt mentett a Form Recognizer-fiókjába, és összehasonlítja azt a fiókkorláthoz.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>Kimenet

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Az erőforrásfiókban jelenleg tárolt modellek felsorolása

A következő kódblokk felsorolja a fiókjában lévő aktuális modelleket, és megjeleníti azok részleteit a konzolon.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]

### <a name="output"></a>Kimenet

Ez a válasz le lett csonkolva az olvashatóság érdekében.

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

### <a name="get-a-specific-model-using-the-models-id"></a>Adott modell lekérte a modell azonosítóját

Az alábbi kódblokk betanít egy új modellt (csakúgy, mint [a](#train-a-model-without-labels) Modell betanítás szakaszában), majd lekér egy második hivatkozást az azonosítójával.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="output"></a>Kimenet

Ez a válasz le lett csonkolva az olvashatóság érdekében.

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

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrásfiókból

A modell a fiókjából is törölhető, ha az azonosítójára hivatkozik. Ez a lépés a metódust is bezárja.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazáskönyvtárból az `dotnet run` paranccsal.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services előfizetést, törölheti az erőforrást vagy erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az ügyféloldali Cognitive Services Form Recognizer .NET SDK-val használja, a szolgáltatás által visszaadott hibák a következőt eredményezik: `RequestFailedException` . Ugyanazt a HTTP-állapotkódot tartalmazzák, amelyet egy kérés REST API vissza.

Ha például egy érvénytelen URI-val küld egy nyugtaképet, a rendszer egy "Hibás kérés" `400` hibaüzenetet ad vissza.

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

Figyelje meg, hogy a rendszer további információkat naplóz, például a művelet ügyfélkérés-azonosítóját.

``

Üzenet: Azure.RequestFailedException: A szolgáltatáskérés sikertelen volt.
Állapot: 400 (Hibás kérés)

Content: {"error":{"code":"FailedToDownloadImage","innerError": {"requestId":"8ca04feb-86db-4552-857c-fde903251518"}, "message":"Failed to download image from input URL."}}

Fejlécek: Transfer-Encoding: darabolt x-envoy-upstream-service-time: REDACTED apim-request-id: REDACTED Strict-Transport-Security: REDACTED X-Content-Type-Options: REDACTED Date: Mon, 2020. április 20. 22:48:35 GMT Content-Type: application/json; charset=utf-8 ''

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a .NET Form Recognizer kódtárat használta modellek betanítása és űrlapok különböző módon való elemzéséhez. Ezután megtudhatja, hogyan hozhat létre egy jobb betanítású adathalmazt, és készíthet pontosabb modelleket.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

* [Mi a Form Recognizer?](../../overview.md)
* Az útmutató mintakódja (és még sok más) megtalálható a [GitHubon.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)
