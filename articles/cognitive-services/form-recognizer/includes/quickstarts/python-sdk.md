---
title: 'Rövid útmutató: Form Recognizer Pythonhoz készült ügyféloldali kódtár létrehozása'
description: A Form Recognizer Pythonhoz készült ügyféloldali kódtár használatával létrehozhat egy űrlapfeldolgozó alkalmazást, amely kulcs/érték párokat és táblaadatokat von ki az egyéni dokumentumokból.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/09/2021
ms.author: lajanuar
ms.openlocfilehash: 606755333856f6dd97ab6c5158ac67f122a1237d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516427"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * A cikkben olvasható kód az egyszerűség kedvéért szinkron metódusokat és nem biztonságos hitelesítőadat-tárolót használ. Tekintse meg az alábbi referenciadokumentációt.

[Referenciadokumentáció](/python/api/azure-ai-formrecognizer)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Csomag (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
  * A Python-telepítésnek tartalmaznia kell a [pip et.](https://pip.pypa.io/en/stable/) A parancssorban futtatva ellenőrizheti, hogy telepítve `pip --version` van-e a pip. A python legújabb verziójának telepítésével szerezze be a pip-et.
* Egy Azure Storage-blob, amely betanító adatok egy halmazát tartalmazza. A [betanítás adatkészletének összeállítására](../../build-training-data-set.md) vonatkozó tippekért és lehetőségekért tekintse meg a betanítás adatkészletének összeállítása egyéni modellhez való összeállítását. Ebben a rövid útmutatóban a mintaadatkészlet **Betanítás** mappájában [található](https://go.microsoft.com/fwlink/?linkid=2090451) fájlokat használhatja (töltse le és bontsa ki a *sample_data.zip).*
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Form Recognizer-erőforrást, Form Recognizer erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás gombra.**
  * A létrehozott erőforrás kulcsának és végpontjának szüksége lesz az alkalmazás a Form Recognizer API-hoz való csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
  * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

A Python telepítése után az ügyféloldali kódtár legújabb verzióját Form Recognizer a következővel:

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

> [!NOTE]
> A Form Recognizer 3.1.0b4 az SDK legújabb előzetes verziója, amely az _API 2.1 preview.3-as verzióját tükrözi._

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> A Form Recognizer 3.0.0 SDK az API 2.0-s verzióját tükrözi

---

### <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

Hozzon létre egy új Python-alkalmazást a kívánt szerkesztőben vagy IDE-ban. Ezután importálja a következő kódtárakat.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ezt a [GitHubon találhatja](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py)meg, amely ebben a rövid útmutatóban található példakódokat tartalmazza.

Hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]

## <a name="object-model"></a>Objektummodell

A Form Recognizer két különböző ügyféltípust hozhat létre. Az első a `form_recognizer_client` használatával lekérdezi a szolgáltatást az űrlapmezők és -tartalmak felismeréséhez. A második egyéni modellek létrehozására és kezelésére használható, amelyek a felismerés `form_training_client` javításához használhatók. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`form_recognizer_client` A a következő műveleteket biztosítja:

* Űrlapmezők és tartalmak felismerése az egyéni űrlapok elemzéséhez betanított egyéni modellekkel.
* Űrlaptartalmak, köztük táblák, vonalak és szavak felismerése modell betanítása nélkül.
* Gyakori mezők felismerése nyugtákból egy előre betanított nyugtamodell használatával a Form Recognizer szolgáltatásban.

### <a name="formtrainingclient"></a>FormTrainingClient

`form_training_client` A a következő műveleteket biztosítja:

* Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez. A [betanítási adatkészletek](#train-a-model-without-labels) létrehozásának részletesebb magyarázatát a címke nélküli modellek betanítása szolgáltatásdokumentációjában találhatja meg.
* Egyéni modellek betanítása adott mezők és értékek elemzéséhez az egyéni űrlapok címkézése segítségével. A címkék [betanítása](#train-a-model-with-labels) a betanítás adatkészletére való alkalmazásának részletesebb magyarázatát a szolgáltatás címkézett modellbeképező dokumentációjában találhatja meg.
* A fiókjában létrehozott modellek kezelése.
* Egyéni modell másolása egyik erőforrásból Form Recognizer másikba.

> [!NOTE]
> A modellek grafikus felhasználói felülettel, például a Form Recognizer eszközzel is [betaníthatóak.](../../quickstarts/label-tool.md)

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következő feladatok elvégzését mutatják be a Pythonhoz készült Form Recognizer kódtárával:
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Elrendezés elemzése](#analyze-layout)
* [Nyugták elemzése](#analyze-receipts)
* [Névjegykártyák elemzése](#analyze-business-cards)
* [Számlák elemzése](#analyze-invoices)
* [Identitásdokumentumok elemzése](#analyze-identity-documents)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modell betanítása](#train-a-custom-model)
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

Itt két ügyfélobjektumot fog hitelesíteni a fent meghatározott előfizetési változók használatával. Egy **AzureKeyCredential** objektumot fog használni, így szükség esetén új ügyfélobjektumok létrehozása nélkül frissítheti az API-kulcsot.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Eszközök lekérte a teszteléshez

A betanítás és a tesztelési adatok URL-címére mutató hivatkozásokat kell hozzáadnia.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-lekérés":::

* Használja az alábbi mintákban található mintaűrlapokat és nyugtaképeket (amelyek szintén elérhetők a [GitHubon,](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) vagy a fenti lépésekkel le tudja szerezni egy adott dokumentum SAS URL-címét a Blob Storage-ban). 

> [!NOTE]
> Az útmutatóban a kódrészletek URL-címek által elért távoli űrlapokat használnak. Ha inkább helyi űrlapdokumentumokat szeretne feldolgozni, tekintse meg a kapcsolódó metódusokat a [referenciadokumentációban és](/python/api/azure-ai-formrecognizer) [példákban.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="analyze-layout"></a>Elrendezés elemzése

A dokumentumokkal Form Recognizer, vonalakat és szavakat elemezhet a dokumentumokban anélkül, hogy modellt kellene betanítanunk. Az elrendezéskinyerésről az Elrendezés fogalmi [útmutatóban található további információ.](../../concept-layout.md)

Egy adott URL-címen található fájl tartalmának elemzéséhez használja a `begin_recognize_content_from_url` metódust. A visszaadott érték objektumok gyűjteménye: egy az elküldött dokumentum minden `FormPage` oldalához. A következő kód végig lépked ezeken az objektumokon, és kinyomtatja a kinyert kulcs/érték párokat és táblaadatokat.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> Helyi képekről is lekért tartalom. Lásd a [FormRecognizerClient metódusokat,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) például: `begin_recognize_content` . Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

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

## <a name="analyze-receipts"></a>Nyugták elemzése

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki közös mezőket az USA-nyugtákból egy előre betanított nyugtamodell használatával. A nyugták elemzésével kapcsolatos további információkért lásd a [nyugta fogalmi útmutatóját.](../../concept-receipts.md) Az URL-címről származó nyugták elemzéséhez használja a `begin_recognize_receipts_from_url` metódust.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> A helyi nyugták rendszerképét is elemezheti. Lásd a [FormRecognizerClient metódusokat,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) például: `begin_recognize_receipts` . Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) a helyi rendszerképekkel kapcsolatos forgatókönyveket tartalmazó mintakódot.

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

## <a name="analyze-business-cards"></a>Névjegykártyák elemzése

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki általános mezőket angol névjegykártyákból egy előre betanított modell használatával. A névjegykártya-elemzéssel kapcsolatos további információkért lásd a [névjegykártyák fogalmi útmutatóját.](../../concept-business-cards.md) 

A névjegykártyák URL-címből való elemzéséhez használja a `begin_recognize_business_cards_from_url` metódust.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> Helyi névjegykártya-képeket is elemezhet. Lásd a [FormRecognizerClient metódusokat,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) például: `begin_recognize_business_cards` . Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) a helyi rendszerképekkel kapcsolatos forgatókönyveket tartalmazó mintakódot.

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="analyze-invoices"></a>Számlák elemzése

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki gyakori mezőket az értékesítési számlákból egy előre betanított modell használatával. A számlaelemzéssel kapcsolatos további információkért lásd a számla [fogalmi útmutatóját.](../../concept-invoices.md) 

A számlák URL-címről való elemzéséhez használja a `begin_recognize_invoices_from_url` metódust.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> Helyi számlaképeket is elemezhet. Lásd a [FormRecognizerClient metódusokat,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) például: `begin_recognize_invoices` . Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="analyze-identity-documents"></a>Identitásdokumentumok elemzése

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

Ez a szakasz azt mutatja be, hogyan elemezheti és vonhat ki kulcsfontosságú információkat a kormány által kiadott azonosítási dokumentumokból ( világszerte használt útlevelek és az Egyesült Államok sofőrlicencei) az előre összeállított Form Recognizer használatával. A számlaelemzéssel kapcsolatos további információkért tekintse meg az előre összeállított azonosítási modell [fogalmi útmutatóját.](../../concept-identification-cards.md)

Az URL-címből származó identitásdokumentumok elemzéséhez használja a `begin_recognize_id_documents_from_url` metódust.

:::code language="python" source="~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py" id="snippet_id":::

> [!TIP]
> Az identitásdokumentum-képeket is elemezheti. _Lásd a_ [FormRecognizerClient metódusokat,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true#methods) például: `begin_recognize_id_documents` . _A helyi rendszerképeket_ érintő forgatókönyvekért tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) található mintakódot is.

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan betanítható egy modell a saját adataival. A betanított modell olyan strukturált adatokat képes kihozni, amelyek tartalmazzák a kulcs/érték kapcsolatokat az eredeti űrlapdokumentumban. A modell betanítása után tesztelheti és újra betaníthatja, és végül arra használhatja, hogy az igényeinek megfelelően megbízhatóan kinyerje az adatokat több űrlapról.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például a Form Recognizer [eszköz mintacímkéző eszközével.](../../quickstarts/label-tool.md)

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzésére a betanító dokumentumok manuális címkézése nélkül.

A következő kód a betanító ügyfelet használja a függvénnyel egy modell `begin_training` adott dokumentumkészleten való betanítához. A visszaadott objektum információkat tartalmaz a modell által elemzett űrlaptípusokról és az egyes űrlaptípusokból `CustomFormModel` kinyerni kívánt mezőkről. A következő kódblokk kinyomtatja ezt az információt a konzolon.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]

### <a name="output"></a>Kimenet

Ez a Python SDK-ból elérhető betanításadatokkal betanított [modell kimenete.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)

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

Egyéni modelleket is betaníthat a betanítás dokumentumainak manuális címkézése által. A címkékkel való betanítás bizonyos helyzetekben jobb teljesítményt eredményez. A visszaadott érték jelzi a modell által kinyerhető mezőket, valamint az egyes mezőkben található becsült `CustomFormModel` pontosságot. A következő kódblokk kinyomtatja ezt az információt a konzolon.

> [!IMPORTANT]
> A címkékkel való betanításhoz speciális címkeinformációs fájlokra () van szükség a Blob Storage-tárolóban a `\<filename\>.pdf.labels.json` betanítás dokumentumai mellett. A [Form Recognizer mintacímkéző eszköz](../../quickstarts/label-tool.md) felhasználói felülettel segít létrehozni ezeket a címkefájlokat. Ha már rendelkezik velük, a függvényt úgy hívhatja meg, hogy a `begin_training` *use_training_labels* paraméter értéke `true` lesz.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Kimenet

Ez a Python SDK-ból elérhető betanításadatokkal betanított [modell kimenete.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)

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

Ez a szakasz azt mutatja be, hogyan lehet kulcs/érték információkat és egyéb tartalmakat kinyerni az egyéni űrlaptípusokból a saját űrlapokkal betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már betanított egy modellt, hogy az azonosítóját az alábbi metódusnak tovább tudja adni. Lásd [a Modell betanítás szakaszát.](#train-a-model-without-labels)

A metódust fogja `begin_recognize_custom_forms_from_url` használni. A visszaadott érték objektumok gyűjteménye: egy az elküldött dokumentum minden `RecognizedForm` oldalához. Az alábbi kód kinyomtatja az elemzési eredményeket a konzolon. Kinyomtatja az egyes felismert mezőket és a hozzájuk tartozó értékeket, valamint egy megbízhatósági pontszámot.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> Helyi képeket is elemezhet. Lásd a [FormRecognizerClient metódusokat,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) például: `begin_recognize_custom_forms` . Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

### <a name="output"></a>Kimenet

Az előző példában használt modell a következő kimenetet tartalmazza.

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


## <a name="manage-your-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket.

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Ellenőrizze a modellek számát a FormRecognizer erőforrásfiókban

A következő kódblokk ellenőrzi, hogy hány modellt mentett a Form Recognizer-fiókjába, és összehasonlítja azt a fiókkorláthoz.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Kimenet

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Az erőforrásfiókban jelenleg tárolt modellek felsorolása

A következő kódblokk felsorolja a fiókjában lévő aktuális modelleket, és megjeleníti azok részleteit a konzolon. Az első modellre való hivatkozást is menti.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Kimenet

Ez egy mintakimenet a tesztfiókhoz.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Adott modell lekérte a modell azonosítóját

A következő kódblokk az előző szakaszban mentett modellazonosítót használja a modell részleteinek lekéréshez.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Kimenet

Ez az előző példában létrehozott egyéni modell mintakimenete.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrásfiókból

A modell a fiókjából is törölhető, ha az azonosítójára hivatkozik. Ez a kód törli az előző szakaszban használt modellt.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `python` gyorsindítási fájlban található paranccsal.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services előfizetést, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="general"></a>Általános kérdések

A Form Recognizer ügyféloldali kódtár az Azure Core-ban definiált [kivételeket fogja kihozni.](https://aka.ms/azsdk-python-azure-core)

### <a name="logging"></a>Naplózás

Ez a kódtár a [szabványos naplózási kódtárat használja a](https://docs.python.org/3/library/logging.html) naplózáshoz. A HTTP-munkamenetekkel (URL-címekkel, fejlécekkel stb.) kapcsolatos alapvető információkat az INFO szinten naplózza a rendszer.

A HIBAKERESÉSi szint részletes naplózása, beleértve a kérések/válaszok szövegeit és a nem válaszoló fejléceket, engedélyezhető az ügyfélen a `logging_enable` kulcsszó argumentummal:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


A hasonlóképpen lehetővé teszi a részletes naplózást egyetlen művelethez, még akkor is, ha az ügyfél számára nincs `logging_enable` engedélyezve:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az Form Recognizer Python-ügyféloldali kódtárat használta modellek betanítása és űrlapok különböző módon való elemzéséhez. Ezután tippeket tanulhat egy jobb betanítású adathalmaz létrehozásához és pontosabb modellek létrehozásához.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

* [Mi a Form Recognizer?](../../overview.md)
* Az útmutatóban található mintakód a [GitHubon található.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py)
