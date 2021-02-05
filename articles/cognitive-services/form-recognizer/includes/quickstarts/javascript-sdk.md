---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtár a JavaScripthez'
description: Használja az űrlap-felismerő ügyféloldali függvénytárát a JavaScripthez olyan űrlap-feldolgozó alkalmazás létrehozásához, amely Kinyeri a kulcs/érték párokat és a tábla adatait az egyéni dokumentumokból.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e5a131753829edddbb4f385766a2d8697ebd0106
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584651"
---
> [!IMPORTANT]
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Tekintse meg az alábbi dokumentációt. 

[Dokumentáció](../../index.yml)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Példák](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Node.js](https://nodejs.org/) aktuális verziója
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört. Ebben a rövid útmutatóban használhatja a [minta adathalmaz](https://go.microsoft.com/fwlink/?linkid=2090451) (letöltés és kibontás *sample_data.zip*) **alatt található fájlokat** .
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hozzon létre egy űrlap-felismerő erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Az Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` . 

```console
npm init
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ai-form-recognizer` NPM csomagot:

```console
npm install @azure/ai-form-recognizer
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

Hozzon létre egy nevű fájlt `index.js` , nyissa meg, és importálja a következő könyvtárakat:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]


> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott űrlap-felismerő erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

## <a name="object-model"></a>Objektummodell 

Az űrlap-felismerő használatával két különböző típusú ügyfél hozható létre. Az első az, `FormRecognizerClient` hogy a szolgáltatás lekérdezését felismerő űrlapmezők és tartalom használatával kérdezi le. A második a `FormTrainingClient` használatával egyéni modelleket hozhat létre és kezelhet, amelyeket az elismerés javítása érdekében használhat. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`FormRecognizerClient` a következő műveleteit biztosítja:

 * Az űrlap mezőinek és tartalmának felismerése az egyéni űrlapok elemzéséhez betanított egyéni modellek használatával. Ezeket az értékeket az objektumok egy gyűjteménye adja vissza `RecognizedForm` .
 * Űrlap tartalmának felismerése, beleértve a táblákat, a sorokat és a szavakat, anélkül, hogy be kellene tanítani a modellt. Az űrlap tartalma objektumok gyűjteményében lesz visszaadva `FormPage` .
 * A beérkezések gyakori mezőinek felismerése, az űrlap-felismerő szolgáltatásban egy előre betanított modell használatával. Ezeket a mezőket és a metaadatokat a gyűjteménye adja vissza `RecognizedReceipt` .

### <a name="formtrainingclient"></a>FormTrainingClient
`FormTrainingClient` a következő műveleteit biztosítja:

* Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez. A `CustomFormModel` visszatérési értéke jelzi, hogy a modell milyen típusú adattípusokat fog elemezni, és hogy milyen mezőket fog kibontani az egyes űrlapokhoz. A betanítási adatkészletek létrehozásával kapcsolatos részletesebb magyarázatért tekintse [meg a szolgáltatás dokumentációját a címke nélküli modell betanításához](#train-a-model-without-labels) .
* Egyéni modellek betanítása az egyéni űrlapok címkézésével megadott mezők és értékek elemzéséhez. A `CustomFormModel` visszaadja a modell által kinyert mezőket, valamint az egyes mezők becsült pontosságát. Tekintse [meg a szolgáltatás dokumentációját a modell betanítása](#train-a-model-with-labels) című témakörben, amely részletesebben ismerteti a címkék egy betanítási adatkészletbe való alkalmazásának részletes ismertetését.
* A fiókban létrehozott modellek kezelése.
* Egyéni modell másolása az egyik űrlap-felismerő erőforrásból egy másikba.

> [!NOTE]
> A modellek grafikus felhasználói felülettel is betanítható, például az [űrlap-felismerő címkéző eszköz](../../quickstarts/label-tool.md)használatával.


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat az űrlap-felismerő ügyféloldali kódtár használatával a JavaScripthez:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Elrendezés elemzése](#analyze-layout)
* [Visszaigazolások elemzése](#analyze-receipts)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése



A megadott előfizetési változók használatával hitelesítse az ügyféloldali objektumokat. Egy objektumot fog használni `AzureKeyCredential` , így ha szükséges, az API-kulcsot új ügyfélalkalmazások létrehozása nélkül is frissítheti. Létre kell hoznia egy képzési ügyfél objektumot is.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Eszközök beszerzése teszteléshez

Emellett a képzési és tesztelési adatok URL-címeihez is hozzá kell adnia a hivatkozásokat.
* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
   
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-cím lekérése":::
* Használja az alábbi mintákban található mintát és beérkezési képeket (a [githubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/test-assets)is elérhetők), vagy a fenti lépésekkel lekérheti egy egyedi dokumentum sas URL-címét a blob Storage-ban. 


## <a name="analyze-layout"></a>Elrendezés elemzése

Az űrlap-felismerő használatával elemezheti a dokumentumokat, vonalakat és szavakat a dokumentumokban anélkül, hogy egy modellt kellene betanítania. További információ az elrendezés kinyeréséről: az [elrendezés fogalmi útmutatója](../../concept-layout.md). Egy adott URI-fájl tartalmának elemzéséhez használja a `beginRecognizeContentFromUrl` metódust.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]


> [!TIP]
> A tartalmat egy helyi fájlból is lekérheti. Tekintse meg a [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) metódusokat, például a **beginRecognizeContent**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) található mintakód-kódot.

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



## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan lehet a modelleket saját adataival betanítani. A betanított modellek olyan strukturált adatokat tudnak kialakítani, amelyek az eredeti dokumentum kulcs/érték kapcsolatait tartalmazzák. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például az [űrlap-felismerő minta feliratozási eszközét](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanításával elemezheti az egyéni űrlapokon található összes mezőt és értéket anélkül, hogy manuálisan címkézi a betanítási dokumentumokat.

A következő függvény egy modellt hoz létre egy adott dokumentumon, és kiírja a modell állapotát a konzolra. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]


### <a name="output"></a>Kimenet

A [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer)-ból elérhető betanítási adatokkal betanított modell kimenete. A minta kimenete az olvashatóság érdekében csonkítva lett.

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

Egyéni modelleket is betaníthat, ha manuálisan címkézi a betanítási dokumentumokat. A címkékkel való képzés bizonyos helyzetekben jobb teljesítményt eredményez. Ha címkéket szeretne betanítani, `\<filename\>.pdf.labels.json` a blob Storage-tárolóban speciális címke-információs fájlokkal () kell rendelkeznie a betanítási dokumentumok mellett. Az [űrlap-felismerő minta címkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkék létrehozásához. Miután megadta őket, meghívhatja a metódust a következőre `beginTraining` `uselabels` beállított paraméterrel: `true` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]


### <a name="output"></a>Kimenet 

A [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)-ból elérhető betanítási adatokkal betanított modell kimenete. A minta kimenete az olvashatóság érdekében csonkítva lett.

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

Ez a szakasz azt mutatja be, hogyan lehet kinyerni a kulcs/érték információkat és az egyéb tartalmakat az egyéni űrlapok típusaiból, a saját űrlapjaival betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az ID-t az alábbi metódusba tudja adni. Lásd a [modell betanítása](#train-a-model-without-labels) szakaszt.

A `beginRecognizeCustomFormsFromUrl` metódust fogja használni. A visszaadott érték objektumok gyűjteménye `RecognizedForm` : egyet a beküldött dokumentum minden lapján.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> A helyi fájlokat is elemezheti. Tekintse meg a [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) metódusokat, például a **beginRecognizeCustomForms**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) található mintakód-kódot.


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

## <a name="analyze-receipts"></a>Visszaigazolások elemzése

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az Egyesült államokbeli nyugták közös mezőit egy előre képzett beérkezési modell használatával. További információ a bevételezési elemzésről: a [nyugták fogalmi útmutatója](../../concept-receipts.md).

Az URI-ból érkező visszaigazolások elemzéséhez használja a `beginRecognizeReceiptsFromUrl` metódust. A következő kód egy nyugtát dolgoz fel a megadott URI-n, és kiírja a fő mezőket és értékeket a konzolra.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> Elemezheti a helyi visszaigazolási képeket is. Tekintse meg a [FormRecognizerClient](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest) metódusokat, például a **beginRecognizeReceipts**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) található mintakód-kódot.

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

## <a name="manage-your-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. A következő kód az összes modell-felügyeleti feladatot egyetlen függvényben hajtja végre, példaként. 

### <a name="get-number-of-models"></a>Modellek számának beolvasása

A következő kódrészlet lekéri a fiókban jelenleg található modellek számát.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]


### <a name="get-list-of-models-in-account"></a>A fiókban található modellek listájának beolvasása

A következő kódrészlet a fiókban elérhető modellek teljes listáját tartalmazza, beleértve a modell létrehozásának és aktuális állapotának adatait.

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

### <a name="get-list-of-model-ids-by-page"></a>Modell-azonosítók listájának beolvasása oldal alapján

Ez a kódrészlet a modellek és a modell-azonosítók többoldalas listáját tartalmazza.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]


### <a name="output"></a>Kimenet

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Modell beolvasása azonosító alapján

A következő függvény a modell AZONOSÍTÓját veszi át, és beolvassa a megfelelő modell objektumot. Ez a függvény alapértelmezés szerint nincs meghívva.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]


### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrás-fiókból

Az AZONOSÍTÓra hivatkozva egy modellt is törölhet a fiókjából. Ez a függvény törli a modellt a megadott AZONOSÍTÓval. Ez a függvény alapértelmezés szerint nincs meghívva.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]


### <a name="output"></a>Kimenet

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `node` fájlban található paranccsal.

```console
node index.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="enable-logs"></a>Naplók engedélyezése

A következő környezeti változó megadásával megtekintheti a hibakeresési naplókat a tár használatakor.

```
export DEBUG=azure*
```

A naplók engedélyezésével kapcsolatos részletes információkért lásd a [ @azure/logger csomag dokumentációját](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az űrlap-felismerő JavaScript ügyféloldali függvénytárát használta a modellek tanításához és az űrlapok különböző módokon történő elemzéséhez. Következő lépésként Ismerkedjen meg a jobb betanítási adatkészlet létrehozásával és a pontosabb modellek előállításával.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](../../overview.md)
* A jelen útmutatóban található mintakód a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)érhető el.
