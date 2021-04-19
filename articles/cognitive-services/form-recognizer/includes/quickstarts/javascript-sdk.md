---
title: 'Rövid útmutató: Form Recognizer JavaScripthez való ügyféloldali kódtár létrehozása'
description: A Form Recognizer JavaScripthez készült ügyféloldali kódtár használatával létrehozhat egy űrlapfeldolgozó alkalmazást, amely kulcs/érték párokat és táblaadatokat von ki az egyéni dokumentumokból.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: feff8b003428fd61fba826d05f8212fa8d9788f9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601930"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * A cikkben olvasható kód az egyszerűség kedvéért szinkron metódusokat és nem biztonságos hitelesítőadat-tárolót használ. Tekintse meg az alábbi referenciadokumentációt.

[Referenciadokumentáció](../../index.yml)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Csomag (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* ANode.js[](https://nodejs.org/)
* Egy Azure Storage-blob, amely betanító adatok egy halmazát tartalmazza. A [betanítás adatkészletének összeállítására](../../build-training-data-set.md) vonatkozó tippekért és lehetőségekért tekintse meg a betanítás adatkészletének összeállítása egyéni modellhez való összeállítását. Ebben a rövid útmutatóban a mintaadatkészlet **Betanítás** mappájában [található](https://go.microsoft.com/fwlink/?linkid=2090451) fájlokat használhatja (töltse le és bontsa ki a *sample_data.zip).*
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Form Recognizer-erőforrást, Form Recognizer erőforrást a Azure Portal a kulcs és a végpont <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" "  target="_blank"> </a> lekért létrehozásához. Az üzembe helyezés után válassza az **Erőforráshoz ugrás lehetőséget.**
  * Szüksége lesz a létrehozott erőforrás kulcsra és végpontra az alkalmazás a Form Recognizer API-hoz való csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
  * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg.

```console
mkdir myapp && cd myapp
```

Az parancs `npm init` futtatásával hozzon létre egy node-alkalmazást egy `package.json` fájllal.

```console
npm init
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse az `ai-form-recognizer` NPM-csomagot:

```console
npm install @azure/ai-form-recognizer
```

Az alkalmazás `package.json` fájlja frissül a függőségekkel.

Hozzon létre egy nevű `index.js` fájlt, nyissa meg, és importálja a következő kódtárakat:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ezt a [GitHubon találhatja](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)meg, amely ebben a rövid útmutatóban található példakódokat tartalmazza.

Hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az Form Recognizer szakaszban létrehozott  erőforrás sikeresen üzembe lett folyamatban, kattintson az **Erőforrás** ugrás gombra a **Következő lépések területen.** A kulcsot és a végpontot az  erőforrás kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.**
>
> Ne felejtse el eltávolítani a kulcsot a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információt Cognitive Services [biztonsági cikk](../../../cognitive-services-security.md) tartalmaz.

## <a name="object-model"></a>Objektummodell

A Form Recognizer két különböző ügyféltípust hozhat létre. Az első az űrlap felismert mezőinek és tartalmának lekérdezésére `FormRecognizerClient` használható. A második egyéni modellek létrehozására és kezelésére használható, amelyek a felismerés `FormTrainingClient` javításához használhatók.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` A a következő műveleteket biztosítja:

* Űrlapmezők és tartalmak felismerése az egyéni űrlapok elemzéséhez betanított egyéni modellekkel. Ezeket az értékeket objektumok gyűjteményében lehet `RecognizedForm` visszaadni.
* Űrlaptartalmak, köztük táblák, vonalak és szavak felismerése modell betanítása nélkül. Az űrlap tartalma objektumgyűjteményben lesz `FormPage` visszaadva.
* A közös mezők felismerése az USA-nyugtákból, névjegykártyákból, számlákból és személyazonossági dokumentumokból a Form Recognizer modellel.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` A a következő műveleteket biztosítja:

* Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez. A visszaadott érték jelzi a modell által elemzett űrlaptípusokat és az egyes űrlaptípusokhoz `CustomFormModel` kinyert mezőket. _További_ [részletekért tekintse](#train-a-model-without-labels) meg a szolgáltatás címkézetlen modellbeképező dokumentációját.
* Egyéni modellek betanítása adott mezők és értékek elemzéséhez az egyéni űrlapok címkézése segítségével. A visszaadott érték jelzi a modell által kinyert mezőket, valamint az egyes mezők becsült `CustomFormModel` pontosságát. A címkék [betanítása](#train-a-model-with-labels) a betanítás adatkészletére való alkalmazásának részletesebb magyarázatát a szolgáltatás címkézett modellbeképező dokumentációjában találhatja meg.
* A fiókjában létrehozott modellek kezelése.
* Egyéni modell másolása egyik erőforrásból Form Recognizer másikba.

> [!NOTE]
> A modellek grafikus felhasználói felülettel, például a Form Recognizer eszközzel is [betaníthatóak.](../../quickstarts/label-tool.md)

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan kell elvégezni a következő feladatokat a JavaScripthez Form Recognizer ügyfélkódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Elrendezés elemzése](#analyze-layout)
* [Nyugták elemzése](#analyze-receipts)
* [Névjegykártyák elemzése](#analyze-business-cards)
* [Számlák elemzése](#analyze-invoices)
* [Identitásdokumentumok elemzése](#analyze-identity-documents)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hitelesítsen egy ügyfélobjektumot a megadott előfizetési változók használatával. Egy objektumot fog használni, így szükség esetén új ügyfélobjektumok létrehozása nélkül frissítheti az `AzureKeyCredential` API-kulcsot. Létre fog hozni egy betanítás ügyfélobjektumot is.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Eszközök lekérte a teszteléshez

A betanítás és a tesztelési adatok URL-címére mutató hivatkozásokat is hozzá kell adni.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-lekérés":::
* Használja az alábbi mintákban szereplő (a [GitHubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/assets)is elérhető) képekből és nyugtákból származó mintát, vagy a fenti lépésekkel le tudja szerezni egy adott dokumentum SAS URL-címét a Blob Storage-ban.

## <a name="analyze-layout"></a>Elrendezés elemzése

A dokumentumokkal Form Recognizer, vonalakat és szavakat elemezhet a dokumentumokban anélkül, hogy modellt kellene betanítanunk. Az elrendezéskinyerésről az Elrendezés [fogalmi útmutatóban található további információ.](../../concept-layout.md) Egy fájl tartalmának egy adott URI-hez való elemzéséhez használja a `beginRecognizeContentFromUrl` metódust.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]

> [!TIP]
> Helyi fájlból is lekért tartalom. Lásd a [FormRecognizerClient metódusokat,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) például **a beginRecognizeContent metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

### <a name="output"></a>Kimenet

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="analyze-receipts"></a>Nyugták elemzése

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki közös mezőket az USA-nak megfelelő nyugtákból egy előre betanított nyugtamodell használatával. A nyugtaelemzéssel kapcsolatos további információkért lásd a [nyugták fogalmi útmutatóját.](../../concept-receipts.md)

Az URI-ból származó nyugták elemzéséhez használja a `beginRecognizeReceiptsFromUrl` metódust. Az alábbi kód feldolgoz egy nyugtát az adott URI-ban, és kiírja a fő mezőket és értékeket a konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> A helyi nyugták rendszerképét is elemezheti. Lásd a [FormRecognizerClient metódusokat,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) például **a beginRecognizeReceipts metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) a helyi rendszerképekkel kapcsolatos forgatókönyveket tartalmazó mintakódot.

### <a name="output"></a>Kimenet

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="analyze-business-cards"></a>Névjegykártyák elemzése

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki általános mezőket angol nyelvű névjegykártyákból egy előre betanított modell használatával. A névjegykártya-elemzéssel kapcsolatos további információkért lásd a [névjegykártyák fogalmi útmutatóját.](../../concept-business-cards.md)

A névjegykártyák URL-címből való elemzéséhez használja a `beginRecognizeBusinessCardsFromURL` metódust.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_bc":::

> [!TIP]
> Helyi névjegykártya-képeket is elemezhet. Lásd a [FormRecognizerClient metódusokat,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) például **a beginRecognizeBusinessCards metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) a helyi rendszerképekkel kapcsolatos forgatókönyveket tartalmazó mintakódot.

## <a name="analyze-invoices"></a>Számlák elemzése

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki gyakori mezőket az értékesítési számlákból egy előre betanított modell használatával. A számlaelemzéssel kapcsolatos további információkért lásd a számla [fogalmi útmutatóját.](../../concept-invoices.md)

A számlák URL-címről való elemzéséhez használja a `beginRecognizeInvoicesFromUrl` metódust.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_invoice":::

> [!TIP]
> Helyi névjegykártya-képeket is elemezhet. Lásd a [FormRecognizerClient metódusokat,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) például **a beginRecognizeInvoices metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) a helyi rendszerképekkel kapcsolatos forgatókönyveket tartalmazó mintakódot.

## <a name="analyze-identity-documents"></a>Identitásdokumentumok elemzése

Ez a szakasz azt mutatja be, hogyan elemezheti és vonhat ki kulcsinformációkat a kormányzati jogú azonosítási dokumentumokból ( globális útlevelek és az Egyesült Államok sofőrlicencei) az Form Recognizer előre összeállított azonosítómodell használatával. A számlaelemzéssel kapcsolatos további információkért tekintse meg az előre összeállított azonosítási modell [fogalmi útmutatóját.](../../concept-identification-cards.md)

Az URL-címből származó identitásdokumentumok elemzéséhez használja a `beginRecognizeIdDocumentsFromUrl` metódust.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_id":::

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan betanítható egy modell a saját adataival. A betanított modell olyan strukturált adatokat képes kihozni, amelyek tartalmazzák a kulcs/érték kapcsolatokat az eredeti űrlapdokumentumban. A modell betanítása után tesztelheti és újra betaníthatja, majd végül arra használhatja, hogy az igényeinek megfelelően megbízhatóan kinyerje az adatokat több űrlapról.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például a Form Recognizer [eszköz mintacímkéző eszközével.](../../quickstarts/label-tool.md)

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzésére a betanítás dokumentumainak manuális címkézése nélkül.

Az alábbi függvény egy modellt készít ki egy adott dokumentumkészleten, és kiírja a modell állapotát a konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]

### <a name="output"></a>Kimenet

Ez a JavaScript SDK-ból elérhető betanításadatokkal betanított [modell kimenete.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer) Ez a mintakimenet az olvashatóság érdekében csonkolva lett.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors:
...
```

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

Egyéni modelleket is betaníthat a betanítás dokumentumainak manuális címkézése által. A címkékkel való betanítás bizonyos helyzetekben jobb teljesítményt eredményez. A címkékkel való betanításhoz speciális címkeinformációs fájlokra () van szükség a Blob Storage-tárolóban `\<filename\>.pdf.labels.json` a betanítás dokumentumai mellett. A [Form Recognizer mintacímkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkefájlok létrehozásához. Ha már megvan, a paraméterrel hívhatja meg a `beginTraining` `uselabels` `true` metódust.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]

### <a name="output"></a>Kimenet

Ez a JavaScript SDK-ból elérhető betanításadatokkal betanított [modell kimenete.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) Ez a mintakimenet az olvashatóság érdekében csonkolva lett.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a szakasz azt mutatja be, hogyan lehet kulcs/érték információkat és egyéb tartalmakat kinyerni az egyéni űrlaptípusokból a saját űrlapokkal betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már betanított egy modellt, hogy az azonosítóját az alábbi metódusnak tovább tudja adni. Lásd [a Modell betanítás szakaszát.](#train-a-model-without-labels)

A metódust fogja `beginRecognizeCustomFormsFromUrl` használni. A visszaadott érték objektumok gyűjteménye: egy az elküldött dokumentum minden `RecognizedForm` oldalához.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> Helyi fájlokat is elemezhet. Lásd a [FormRecognizerClient metódusokat,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) például **a beginRecognizeCustomForms metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) a helyi rendszerképekkel kapcsolatos forgatókönyveket tartalmazó mintakódot.

### <a name="output"></a>Kimenet

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. Az alábbi kód az összes modellkezelési feladatot egyetlen függvényben, példaként használja.

### <a name="get-number-of-models"></a>Modellek számának lekért száma

Az alábbi kódblokk a fiókban jelenleg található modellek számát tartalmazza.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]

### <a name="get-list-of-models-in-account"></a>A fiókban található modellek listájának lekért listája

Az alábbi kódblokk a fiókban elérhető modellek teljes listáját tartalmazza, beleértve a modell létrehozási dátumával és aktuális állapotával kapcsolatos információkat.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]

### <a name="output"></a>Kimenet

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>Modell-adattitkok lapról lapra lekért listája

Ez a kódblokk a modellek és a modell-adattitkok lapszámozott listáját tartalmazza.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]

### <a name="output"></a>Kimenet

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Modell lekérte azonosító alapján

Az alábbi függvény egy modellazonosítót vesz fel, és lekérte az egyező modellobjektumot. Ezt a függvényt a rendszer alapértelmezés szerint nem hívja meg.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrásfiókból

Az azonosítójára való hivatkozás segítségével törölheti is a modellt a fiókjából. Ez a függvény törli a modellt a megadott azonosítóval. Ezt a függvényt a rendszer alapértelmezés szerint nem hívja meg.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]

### <a name="output"></a>Kimenet

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `node` gyorsindítási fájlban található paranccsal.

```console
node index.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services előfizetést, törölheti az erőforrást vagy erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="enable-logs"></a>Naplók engedélyezése

A következő környezeti változóval láthatja a hibakeresési naplókat a kódtár használata során.

```console
export DEBUG=azure*
```

A naplók engedélyezésére vonatkozó részletes útmutatásért tekintse meg a [ @azure/logger csomagokat.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az Form Recognizer JavaScript ügyféloldali kódtár használatával betanított modelleket és elemezte az űrlapokat. Ezután megtudhatja, hogyan hozhat létre egy jobb betanítású adathalmazt, és készíthet pontosabb modelleket.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](../../overview.md)
* Az útmutatóban található mintakód a [GitHubon található.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)
