---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtár Java-hoz'
description: A Javához készült űrlap-felismerő ügyféloldali kódtár használatával létrehozhat egy űrlapot feldolgozó alkalmazást, amely Kinyeri a kulcs/érték párokat és a tábla adatait az egyéni dokumentumokból.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/19/2021
ms.custom: devx-track-java
ms.author: lajanuar
ms.openlocfilehash: a709f82b04ed5c1fe70f6927b33605cfff20ed6b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104761123"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
> Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja.

[Dokumentáció](/java/api/overview/azure/ai-formrecognizer-readme)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  [Csomag (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  [Példák](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hozzon létre egy űrlap-felismerő erőforrást "  target="_blank"> </a> Az Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
  * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
  * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* Egy Azure Storage-blob, amely betanítási adathalmazt tartalmaz. A betanítási adatkészletek összeállításával kapcsolatos tippekért és lehetőségekért tekintse meg az [Egyéni modell képzési adatkészletének](../../build-training-data-set.md) létrehozása című témakört. Ebben a rövid útmutatóban használhatja a [minta adathalmaz](https://go.microsoft.com/fwlink/?linkid=2090451) (letöltés és kibontás *sample_data.zip*) **alatt található fájlokat** .

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárból. Ez a parancs alapvető Build-fájlokat hoz létre a Gradle számára, beleértve a *Build. Gradle. KTS* fájlt, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL** kiválasztására, válassza a **Kotlin** lehetőséget.

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Ez a rövid útmutató a Gradle függőség-kezelőt használja. Megtalálhatja az ügyféloldali függvénytárat és az egyéb függőségi kezelők információit a [Maven központi adattárában](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

A projekt *Build. gradle. KTS* fájljában adja meg az ügyféloldali függvénytárat `implementation` utasításként, valamint a szükséges beépülő modulokat és beállításokat.

#### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/preview)

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.1")
}
```

> [!NOTE]
> Az űrlap-felismerő 3.1.0 SDK az _API 2,1-es verziójának előzetes verzióját_ tükrözi. 2. Használja az 2,1 [](../../quickstarts/client-library.md) -es _API-verzióhoz tartozó REST API előzetes verzióját. 3_.

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

> [!NOTE]
> Az űrlap-felismerő 3.0.0 SDK az API 2.0-s verzióját tükrözi

---

### <a name="create-a-java-file"></a>Java-fájl létrehozása


A munkakönyvtárból futtassa a következő parancsot:

```console
mkdir -p src/main/java
```

Navigáljon az új mappára, és hozzon létre egy *FormRecognizer. Java* nevű fájlt. Nyissa meg a kívánt szerkesztőben vagy IDE, és adja hozzá a következő `import` utasításokat:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.


Az alkalmazás **FormRecognizer** osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott űrlap-felismerő erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

Az alkalmazás **fő** metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később is megadhatja. Emellett a képzési és tesztelési adatok URL-címeihez is hozzá kell adnia a hivatkozásokat.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-cím lekérése":::
* Egy olyan űrlap URL-címének lekéréséhez, amely a teszteléshez használható, a fenti lépésekkel lekérheti egy egyedi dokumentum SAS URL-címét a blob Storage-ban. Vagy vegyen fel egy máshol található dokumentum URL-címét.
* A fenti módszer használatával beolvashatja a beérkezési képek URL-címét is.
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/preview)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]

---

## <a name="object-model"></a>Objektummodell

Az űrlap-felismerő használatával két különböző típusú ügyfél hozható létre. Az első az, `FormRecognizerClient` hogy a szolgáltatás lekérdezését felismerő űrlapmezők és tartalom használatával kérdezi le. A második a `FormTrainingClient` használatával egyéni modelleket hozhat létre és kezelhet, amelyeket az elismerés javítása érdekében használhat.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` a következő műveleteit biztosítja:

* Az űrlap mezőinek és tartalmának felismerése az egyéni űrlapok elemzéséhez betanított egyéni modellek használatával.  Ezeket az értékeket az objektumok egy gyűjteménye adja vissza `RecognizedForm` . Tekintse meg a példa [Egyéni űrlapok elemzése](#analyze-forms-with-a-custom-model)című témakört.
* Űrlap tartalmának felismerése, beleértve a táblákat, a sorokat és a szavakat, anélkül, hogy be kellene tanítani a modellt.  Az űrlap tartalma objektumok gyűjteményében lesz visszaadva `FormPage` . Lásd: példa [elemzése elrendezés](#analyze-layout).
* Az Egyesült államokbeli nyugták általános mezőinek felismerése egy előre képzett beérkezési modell használatával az űrlap-felismerő szolgáltatásban.  Ezeket a mezőket és a metaadatokat az objektumok egy gyűjteménye adja vissza `RecognizedForm` . Lásd: példák [elemzése nyugták](#analyze-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` a következő műveleteit biztosítja:

* Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez.  A `CustomFormModel` visszatérési értéke jelzi, hogy a modell milyen típusú adattípusokat fog elemezni, és hogy milyen mezőket fog kibontani az egyes űrlapokhoz.
* Egyéni modellek betanítása az egyéni űrlapok címkézésével megadott mezők és értékek elemzéséhez.  A `CustomFormModel` visszaadja a modell által kinyert mezőket, valamint az egyes mezők becsült pontosságát.
* A fiókban létrehozott modellek kezelése.
* Egyéni modell másolása az egyik űrlap-felismerő erőforrásból egy másikba.

> [!NOTE]
> A modellek grafikus felhasználói felülettel is betanítható, például az [űrlap-felismerő címkéző eszköz](../../quickstarts/label-tool.md)használatával.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Javához készült űrlap-felismerő ügyféloldali kódtár használatával:
<!-- markdownlint-disable MD001 -->
#### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/preview)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Elrendezés elemzése](#analyze-layout)
* [Visszaigazolások elemzése](#analyze-receipts)
* [Üzleti kártyák elemzése](#analyze-business-cards)
* [Számlák elemzése](#analyze-invoices)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Elrendezés elemzése](#analyze-layout)
* [Visszaigazolások elemzése](#analyze-receipts)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)


---

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A **Main** metódus tetején adja hozzá a következő kódot. Itt két ügyfél-objektumot kell hitelesítenie a fent megadott előfizetési változók használatával. **AzureKeyCredential** objektumot fog használni, így ha szükséges, az API-kulcsot új ügyfélalkalmazások létrehozása nélkül is frissítheti.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>Elrendezés elemzése

Az űrlap-felismerő használatával elemezheti a dokumentumokat, vonalakat és szavakat a dokumentumokban anélkül, hogy egy modellt kellene betanítania. További információ az elrendezés kinyeréséről: az [elrendezés fogalmi útmutatója](../../concept-layout.md).

Egy adott URL-címen található fájl tartalmának elemzéséhez használja a **beginRecognizeContentFromUrl** metódust.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> A tartalmat egy helyi fájlból is lekérheti. Tekintse meg a [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) metódusokat, például a **beginRecognizeContent**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) található mintakód-kódot.

A visszaadott érték egy **FormPage** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához. Az alábbi kód megismétli ezeket az objektumokat, és kinyomtatja a kinyert kulcs/érték párokat és a táblák adatait.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]
### <a name="output"></a>Kimenet

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="analyze-invoices"></a>Számlák elemzése

#### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az értékesítési számlákból származó általános mezőket egy előre betanított modell használatával. A számlázási elemzéssel kapcsolatos további információkért tekintse meg a [számla fogalmi útmutatóját](../../concept-invoices.md).

A számlák URL-címről való elemzéséhez használja a `beginRecognizeInvoicesFromUrl` metódust. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> A helyi számlákat is elemezheti. Tekintse meg a [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) metódusokat, például a **beginRecognizeInvoices**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) található mintakód-kódot.

A visszaadott érték a **RecognizedForm** objektumok gyűjteménye: egyet a dokumentumban lévő egyes számlákhoz. A következő kód feldolgozza a számlát a megadott URI-n, és kiírja a fő mezőket és értékeket a konzolra.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan lehet a modelleket saját adataival betanítani. A betanított modellek olyan strukturált adatokat tudnak kialakítani, amelyek az eredeti dokumentum kulcs/érték kapcsolatait tartalmazzák. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel is betaníthatja, például az [űrlap-felismerő minta feliratozási eszközét](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez a betanítási dokumentumok manuális címkézése nélkül.

A következő metódus egy modellt hoz létre egy adott dokumentumon, és kiírja a modell állapotát a konzolra. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

A visszaadott **CustomFormModel** objektum információt tartalmaz a modell által elemezhető típusokról, valamint az egyes űrlapok típusaiból kinyerhető mezőkről. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Végezetül ez a metódus a modell egyedi AZONOSÍTÓját adja vissza.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]


### <a name="output"></a>Kimenet

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Modell betanítása címkékkel

Egyéni modelleket is betaníthat, ha manuálisan címkézi a betanítási dokumentumokat. A címkékkel való képzés bizonyos helyzetekben jobb teljesítményt eredményez. Ha címkéket szeretne betanítani, a blob Storage-tárolóban a betanítási dokumentumokkal együtt speciális feliratú információs fájlokat (*\<filename\>.pdf.labels.js*) kell használnia. Az [űrlap-felismerő minta címkéző eszköz](../../quickstarts/label-tool.md) egy felhasználói felületet biztosít a címkék létrehozásához. Miután megadta őket, meghívhatja a **beginTraining** metódust a *useTrainingLabels* paraméterrel, amely a következőre van beállítva: `true` .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


A visszaadott **CustomFormModel** jelzi a modell által kinyerhető mezőket, valamint az egyes mezők becsült pontosságát. A következő kódrészlet kinyomtatja ezeket az információkat a konzolra.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]


### <a name="output"></a>Kimenet

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Űrlapok elemzése egyéni modellel

Ez a szakasz azt mutatja be, hogyan lehet kinyerni a kulcs/érték információkat és az egyéb tartalmakat az egyéni űrlapok típusaiból, a saját űrlapjaival betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az ID-t az alábbi metódusba tudja adni. Lásd a [modell betanítása](#train-a-model-without-labels) szakaszt.

A **beginRecognizeCustomFormsFromUrl** metódust fogja használni. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> Elemezheti a helyi fájlokat is. Tekintse meg a [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) metódusokat, például a **beginRecognizeCustomForms**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) található mintakód-kódot.

A visszaadott érték egy **RecognizedForm** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához. A következő kód kinyomtatja az elemzési eredményeket a-konzolra. Kinyomtatja az egyes felismert mezőket és a hozzá tartozó értékeket, valamint a megbízhatósági pontszámot.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]


### <a name="output"></a>Kimenet

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```

## <a name="analyze-receipts"></a>Visszaigazolások elemzése

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az Egyesült államokbeli nyugták közös mezőit egy előre képzett beérkezési modell használatával. További információ a bevételezési elemzésről: a [nyugták fogalmi útmutatója](../../concept-receipts.md).

A visszaigazolások URI-ból való elemzéséhez használja a **beginRecognizeReceiptsFromUrl** metódust. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> Elemezheti a helyi visszaigazolási képeket is. Tekintse meg a [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) metódusokat, például a **beginRecognizeReceipts**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) található mintakód-kódot.

A visszaadott érték egy **RecognizedReceipt** -objektum gyűjteménye: egy a beküldött dokumentum minden oldalához. A kód következő blokkja megismétli a visszaigazolásokat, és kinyomtatja az adatokat a-konzolra.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

A kód következő blokkja megismétli a nyugtán észlelt egyes elemeket, és kiírja az adataikat a-konzolra.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>Kimenet 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="analyze-business-cards"></a>Üzleti kártyák elemzése

#### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezheti és kinyerheti az angol üzleti kártyákból származó általános mezőket egy előre betanított modell használatával. További információ a névjegykártya-elemzésről: a [Business Cards fogalmi útmutatója](../../concept-business-cards.md).

Az üzleti kártyák URL-címről való elemzéséhez használja a `beginRecognizeBusinessCardsFromUrl` metódust. 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> A helyi névjegykártya-lemezképeket is elemezheti. Tekintse meg a [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) metódusokat, például a **beginRecognizeBusinessCards**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) található mintakód-kódot.

A visszaadott érték a **RecognizedForm** objektumok gyűjteménye: egyet a dokumentum minden kártyáján. A következő kód feldolgozza a névjegykártyát a megadott URI-n, és kiírja a fő mezőket és értékeket a konzolra.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="manage-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. A következő kód egyetlen metódusban végzi el az összes modell-felügyeleti feladatot, példaként. Először másolja az alábbi metódus-aláírást:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>A FormRecognizer-erőforrás fiókban található modellek számának megkeresése

A következő kódrészlet ellenőrzi, hogy az űrlap-felismerő fiókban hány modell lett mentve, és összehasonlítja azt a fiókra vonatkozó korláttal.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Kimenet 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Az erőforrás-fiókban jelenleg tárolt modellek listázása

A következő kódrészlet felsorolja a fiókban lévő aktuális modelleket, és kiírja az adataikat a-konzolra.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Kimenet 

Ez a válasz csonkolt az olvashatóság érdekében.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrás-fiókból

Az AZONOSÍTÓra hivatkozva egy modellt is törölhet a fiókjából.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Váltson vissza a fő projekt könyvtárába. Ezután hozza létre az alkalmazást a következő paranccsal:

```console
gradle build
```

Futtassa az alkalmazást a `run` célnak megfelelően:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

A felismerő ügyfelek `ErrorResponseException` kivételeket vetnek fel. Ha például egy érvénytelen forrásfájl URL-címet próbál meg megadni, a hiba `ErrorResponseException` okát jelző hibaüzenettel fog kiemelni. A következő kódrészletben a hiba a kivétel kifogásával, valamint a hibával kapcsolatos további információk megjelenítésével van kezelve.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Ügyfél naplózásának engedélyezése

A Javához készült Azure SDK-k egységes naplózási történetet biztosítanak az alkalmazások hibáinak elhárításához és a megoldásuk felgyorsításához. A létrehozott naplók rögzítik az alkalmazások folyamatát, mielőtt elérnék a terminál állapotát, hogy megtalálják a probléma gyökerét. Tekintse meg a naplózási [wikit](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) , amely útmutatást nyújt a naplózás engedélyezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az űrlap felismerő Java ügyféloldali függvénytárát használta a modellek tanításához és az űrlapok különböző módokon történő elemzéséhez. Következő lépésként Ismerkedjen meg a jobb betanítási adatkészlet létrehozásával és a pontosabb modellek előállításával.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

* [Mi a Form Recognizer?](../../overview.md)
* A jelen útmutatóban szereplő mintakód (és egyebek) megtalálható a [githubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).
