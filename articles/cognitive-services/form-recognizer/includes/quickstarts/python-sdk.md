---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtár a Pythonhoz'
description: A Pythonhoz készült űrlap-felismerő ügyféloldali kódtár használatával létrehozhat egy űrlapot feldolgozó alkalmazást, amely Kinyeri a kulcs/érték párokat és a tábla adatait az egyéni dokumentumokból.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: d0c26a4b0cc860b959afc6703ee3e709c606f209
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584625"
---
> [!IMPORTANT]
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Tekintse meg az alábbi dokumentációt. 

[Dokumentáció](/python/api/azure-ai-formrecognizer)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Csomag (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Példák](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört. Ebben a rövid útmutatóban használhatja a [minta adathalmaz](https://go.microsoft.com/fwlink/?linkid=2090451) (letöltés és kibontás *sample_data.zip*) **alatt található fájlokat** .
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hozzon létre egy űrlap-felismerő erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Az Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után a következő módon telepítheti az űrlap-felismerő ügyféloldali kódtár legújabb verzióját:

#### <a name="version-20"></a>[2,0-es verzió](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> A legfrissebb űrlap-felismerő a 2,0-es API-verziót tükrözi

#### <a name="version-21-preview"></a>[2,1-es verzió előnézet](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

> [!NOTE]
> Az űrlap-felismerő előnézet SDK az API 2,1-es verziójának előzetes verzióját tükrözi

---

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. Ezután importálja a következő kódtárakat.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.


Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]


## <a name="object-model"></a>Objektummodell 

Az űrlap-felismerő használatával két különböző típusú ügyfél hozható létre. Az első az, `form_recognizer_client` hogy a szolgáltatás lekérdezését felismerő űrlapmezők és tartalom használatával kérdezi le. A második a `form_training_client` használatával egyéni modelleket hozhat létre és kezelhet, amelyeket az elismerés javítása érdekében használhat. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`form_recognizer_client` a következő műveleteit biztosítja:

 * Az űrlap mezőinek és tartalmának felismerése az egyéni űrlapok elemzéséhez betanított egyéni modellek használatával. 
 * Űrlap tartalmának felismerése, beleértve a táblákat, a sorokat és a szavakat, anélkül, hogy be kellene tanítani a modellt. 
 * A beérkezések gyakori mezőinek felismerése, az űrlap-felismerő szolgáltatásban egy előre betanított modell használatával.

### <a name="formtrainingclient"></a>FormTrainingClient
`form_training_client` a következő műveleteit biztosítja:

* Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez. A betanítási adatkészletek létrehozásával kapcsolatos részletesebb magyarázatért tekintse [meg a szolgáltatás dokumentációját a címke nélküli modell betanításához](#train-a-model-without-labels) .
* Egyéni modellek betanítása az egyéni űrlapok címkézésével megadott mezők és értékek elemzéséhez. Tekintse [meg a szolgáltatás dokumentációját a modell betanítása](#train-a-model-with-labels) című témakörben, amely részletesebben ismerteti a címkék egy betanítási adatkészletbe való alkalmazásának részletes ismertetését.
* A fiókban létrehozott modellek kezelése.
* Egyéni modell másolása az egyik űrlap-felismerő erőforrásból egy másikba.

> [!NOTE]
> A modellek grafikus felhasználói felülettel is betanítható, például az [űrlap-felismerő címkéző eszköz](../../quickstarts/label-tool.md)használatával.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Pythonhoz készült űrlap-felismerő ügyféloldali kódtár használatával:

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

Itt két ügyfél-objektumot kell hitelesítenie a fent megadott előfizetési változók használatával. **AzureKeyCredential** objektumot fog használni, így ha szükséges, az API-kulcsot új ügyfélalkalmazások létrehozása nélkül is frissítheti.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Eszközök beszerzése teszteléshez

Hozzá kell adnia a képzési és tesztelési adatok URL-címeire mutató hivatkozásokat.
* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-cím lekérése":::
* Használja az alábbi mintákban található minta-és beérkezési képeket (a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) is elérhetők), vagy a fenti lépésekkel lekérheti az egyes dokumentumok sas URL-címét a blob Storage-ban. 

> [!NOTE]
> Az útmutatóban szereplő kódrészletek az URL-címek által elért távoli űrlapokat használják. Ha ehelyett a helyi űrlapos dokumentumokat szeretné feldolgozni, tekintse meg a kapcsolódó módszereket a [dokumentációban](/python/api/azure-ai-formrecognizer) és a [mintákban](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

## <a name="analyze-layout"></a>Elrendezés elemzése

Az űrlap-felismerő használatával elemezheti a dokumentumokat, vonalakat és szavakat a dokumentumokban anélkül, hogy egy modellt kellene betanítania. További információ az elrendezés kinyeréséről: az [elrendezés fogalmi útmutatója](../../concept-layout.md).

Egy adott URL-címen található fájl tartalmának elemzéséhez használja a `begin_recognize_content_from_url` metódust. A visszaadott érték objektumok gyűjteménye `FormPage` : egyet a beküldött dokumentum minden lapján. Az alábbi kód megismétli ezeket az objektumokat, és kinyomtatja a kinyert kulcs/érték párokat és a táblák adatait.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> A helyi rendszerképekből is kérhet tartalmat. Tekintse meg a [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) metódusokat, például: `begin_recognize_content` . Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) található mintakód-kódot.

### <a name="output"></a>Kimenet

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```


## <a name="analyze-invoices"></a>Számlák elemzése

#### <a name="version-20"></a>[2,0-es verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

#### <a name="version-21-preview"></a>[2,1-es verzió előnézet](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az értékesítési számlákból származó általános mezőket egy előre betanított modell használatával. A számlázási elemzéssel kapcsolatos további információkért tekintse meg a [számla fogalmi útmutatóját](../../concept-invoices.md). A számlák URL-címről való elemzéséhez használja a `begin_recognize_invoices_from_url` metódust. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> A helyi számla lemezképeit is elemezheti. Tekintse meg a [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) metódusokat, például: `begin_recognize_invoices` . Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) található mintakód-kódot.

---

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan lehet a modelleket saját adataival betanítani. A betanított modellek olyan strukturált adatokat tudnak kialakítani, amelyek az eredeti dokumentum kulcs/érték kapcsolatait tartalmazzák. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például az [űrlap-felismerő minta feliratozási eszközét](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez a betanítási dokumentumok manuális címkézése nélkül.

A következő kód a betanítási ügyfelet használja a `begin_training` modell betanításához egy adott dokumentumon. A visszaadott `CustomFormModel` objektum információt tartalmaz a modell által elemezhető típusokról, valamint az egyes űrlapokból kinyerhető mezőkről. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]


### <a name="output"></a>Kimenet

A [PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)-ból elérhető betanítási adatokkal betanított modell kimenete.

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

Egyéni modelleket is betaníthat, ha manuálisan címkézi a betanítási dokumentumokat. A címkékkel való képzés bizonyos helyzetekben jobb teljesítményt eredményez. A visszaadott `CustomFormModel` érték jelzi a modell által kinyerhető mezőket, valamint az egyes mezők becsült pontosságát. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

> [!IMPORTANT]
> Ha címkéket szeretne betanítani, `\<filename\>.pdf.labels.json` a blob Storage-tárolóban speciális címke-információs fájlokkal () kell rendelkeznie a betanítási dokumentumok mellett. Az [űrlap-felismerő minta címkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkék létrehozásához. Ha megkapta őket, meghívhatja a `begin_training` függvényt a következőre beállított *use_training_labels* paraméterrel: `true` .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Kimenet

A [PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)-ból elérhető betanítási adatokkal betanított modell kimenete.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a szakasz azt mutatja be, hogyan lehet kinyerni a kulcs/érték információkat és az egyéb tartalmakat az egyéni űrlapok típusaiból, a saját űrlapjaival betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az ID-t az alábbi metódusba tudja adni. Lásd a [modell betanítása](#train-a-model-without-labels) szakaszt.

A `begin_recognize_custom_forms_from_url` metódust fogja használni. A visszaadott érték objektumok gyűjteménye `RecognizedForm` : egyet a beküldött dokumentum minden lapján. A következő kód kinyomtatja az elemzési eredményeket a-konzolra. Kinyomtatja az egyes felismert mezőket és a hozzá tartozó értékeket, valamint a megbízhatósági pontszámot.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> A helyi lemezképeket is elemezheti. Tekintse meg a [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) metódusokat, például: `begin_recognize_custom_forms` . Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) található mintakód-kódot.


### <a name="output"></a>Kimenet

Az előző példa modelljét használva a következő kimenet van megadva.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="analyze-receipts"></a>Visszaigazolások elemzése

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az Egyesült államokbeli nyugták közös mezőit egy előre képzett beérkezési modell használatával. További információ a bevételezési elemzésről: a [nyugták fogalmi útmutatója](../../concept-receipts.md). Az URL-címekről történő visszaigazolások elemzéséhez használja a `begin_recognize_receipts_from_url` metódust. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> Elemezheti a helyi visszaigazolási képeket is. Tekintse meg a [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) metódusokat, például: `begin_recognize_receipts` . Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) található mintakód-kódot.

### <a name="output"></a>Kimenet

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```


## <a name="analyze-business-cards"></a>Üzleti kártyák elemzése

#### <a name="version-20"></a>[2,0-es verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

#### <a name="version-21-preview"></a>[2,1-es verzió előnézet](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az angol üzleti kártyákból származó általános mezőket egy előre betanított modell használatával. További információ a névjegykártya-elemzésről: a [Business Cards fogalmi útmutatója](../../concept-business-cards.md). Az üzleti kártyák URL-címről való elemzéséhez használja a `begin_recognize_business_cards_from_url` metódust. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> A helyi névjegykártya-lemezképeket is elemezheti. Tekintse meg a [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) metódusokat, például: `begin_recognize_business_cards` . Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) található mintakód-kódot.

---

## <a name="manage-your-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>A FormRecognizer-erőforrás fiókban található modellek számának megkeresése

A következő kódrészlet ellenőrzi, hogy az űrlap-felismerő fiókban hány modell lett mentve, és összehasonlítja azt a fiókra vonatkozó korláttal.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Kimenet

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Az erőforrás-fiókban jelenleg tárolt modellek listázása

A következő kódrészlet felsorolja a fiókban lévő aktuális modelleket, és kiírja az adataikat a-konzolra. Emellett az első modellre mutató hivatkozást is ment.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Kimenet

Ez egy minta kimenet a teszt fiókhoz.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Adott modell beszerzése a modell AZONOSÍTÓjának használatával

A következő kódrészlet az előző szakaszban mentett modell AZONOSÍTÓját használja, és a használatával kéri le a modell részleteit.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Kimenet

Ez az előző példában létrehozott egyéni modell kimenete.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrás-fiókból

Az AZONOSÍTÓra hivatkozva egy modellt is törölhet a fiókjából. Ez a kód törli az előző szakaszban használt modellt.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `python` fájlban található paranccsal.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="general"></a>Általános

Az űrlap-felismerő ügyféloldali kódtára az [Azure Core](https://aka.ms/azsdk-python-azure-core)-ban definiált kivételeket fogja növelni.

### <a name="logging"></a>Naplózás

Ez a függvénytár a [szabványos naplózási függvénytárat](https://docs.python.org/3/library/logging.html) használja a naplózáshoz. A HTTP-munkamenetek (URL-címek, fejlécek stb.) alapszintű információit a rendszer az információs szinten naplózza.

A HIBAKERESÉSi szint részletes naplózása, beleértve a kérés/válasz törzseit és a nem kitakart fejléceket is, a kulcsszó argumentummal rendelkező ügyfélen engedélyezhető `logging_enable` :

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


Hasonlóképpen, `logging_enable` egy művelet részletes naplózását is engedélyezheti, még akkor is, ha nincs engedélyezve az ügyfél számára:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az űrlap felismerő Python ügyféloldali függvénytárát használta a modellek tanításához és az űrlapok különböző módokon történő elemzéséhez. Következő lépésként Ismerkedjen meg a jobb betanítási adatkészlet létrehozásával és a pontosabb modellek előállításával.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

* [Mi a Form Recognizer?](../../overview.md)
* A jelen útmutatóban található mintakód a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py)érhető el.
