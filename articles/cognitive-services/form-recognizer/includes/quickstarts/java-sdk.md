---
title: 'Rövid útmutató: Form Recognizer Java-ügyféloldali kódtár létrehozása'
description: A Form Recognizer Java-ügyfélkönyvtár használatával létrehozhat egy űrlapfeldolgozó alkalmazást, amely kulcs/érték párokat és táblaadatokat von ki az egyéni dokumentumokból.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.custom: devx-track-java
ms.author: lajanuar
ms.openlocfilehash: cd5e6383e71e3f37a26b866156b64c86302f6990
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516406"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
> A cikkben olvasható kód az egyszerűség kedvéért szinkron metódusokat és nem biztonságos hitelesítőadat-tárolót használ.

[Referenciadokumentáció](/java/api/overview/azure/ai-formrecognizer-readme)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  [Csomag (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  [Minták](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services)
* A Java fejlesztői készlet [(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle buildeszköz ,](https://gradle.org/install/)vagy egy másik függőségkezelő.
* Az Azure-előfizetés létrehozása után hozzon létre egy Form Recognizer-erőforrást, Form Recognizer a Azure Portal a kulcs és a végpont <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" "  target="_blank"> </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás elemre.**
  * Szüksége lesz a létrehozott erőforrás kulcsának és végpontjának létrehozására, hogy az alkalmazást a Form Recognizer API-hoz. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
  * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.
* Egy Azure Storage-blob, amely betanításadatok készletét tartalmazza. A [betanítás adatkészletének összeállítására](../../build-training-data-set.md) vonatkozó tippekért és lehetőségekért tekintse meg a betanítás adatkészletének összeállítása egyéni modellhez való összeállítását. Ebben a rövid útmutatóban a mintaadatkészlet **Betanítás** mappájában [található](https://go.microsoft.com/fwlink/?linkid=2090451) fájlokat használhatja (töltse le és bontsa ki a *sample_data.zip).*

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir myapp && cd myapp
```

Futtassa `gradle init` a parancsot a munkakönyvtárból. Ez a parancs alapvető buildfájlokat hoz létre a Gradle-hez, beleértve a *build.gradle.kts* fájlt, amelyet futásidőben az alkalmazás létrehozásához és konfigurálásához használ.

```console
gradle init --type basic
```

Amikor a rendszer kéri, hogy válasszon **egy DSL-t,** válassza a **Kotlin lehetőséget.**

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Ez a rövid útmutató a Gradle függőségkezelőt használja. Az ügyféloldali kódtárat és az egyéb függőségkezelőkre vonatkozó információkat a Maven központi [adattárában találja.](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)

A projekt *build.gradle.kts* fájljában utasításként foglalja bele az ügyféloldali kódtárat, valamint a szükséges beépülő modulokat `implementation` és beállításokat.

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.3")
}
```

> [!NOTE]
> A Form Recognizer 3.1.0-beta.3 SDK az _API 2.1-preview.3 verzióját tükrözi._

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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.3")
}
```

> [!NOTE]
> A Form Recognizer 3.0.0 SDK az API v2.1-preview.3-as verzióját tükrözi

---

### <a name="create-a-java-file"></a>Java-fájl létrehozása


Futtassa a következő parancsot a munkakönyvtárban:

```console
mkdir -p src/main/java
```

Lépjen az új mappába, és hozzon létre egy *FormRecognizer.java nevű fájlt.* Nyissa meg a kívánt szerkesztőben vagy IDE-ban, és adja hozzá a következő `import` utasításokat:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java)amely a rövid útmutatóban található példakódokat tartalmazza.


Az alkalmazás **FormRecognizer** osztályában hozzon létre változókat az erőforrás kulcsához és végpontjához.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az Form Recognizer szakaszban létrehozott  erőforrás sikeresen üzembe lett stb., kattintson a Következő lépések területen található **Erőforrás** ugrás **gombra.** A kulcsot és a végpontot az  erőforrás kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.**
>
> Ne felejtse el eltávolítani a kulcsot a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információt Cognitive Services [biztonsági cikk](../../../cognitive-services-security.md) tartalmaz.

Az alkalmazás fő **metódusában** adja hozzá az ebben a rövid útmutatóban használt metódusok hívásait. Ezeket később fogja meghatározni. A betanítás és a tesztelési adatok URL-címére mutató hivatkozásokat is hozzá kell adni.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="SAS URL-lekérés":::
* A tesztelni kívánt űrlap URL-címének lehívása érdekében a fenti lépésekkel lekérte egy adott dokumentum SAS URL-címét a Blob Storage-ban. Másik, ha egy máshol található dokumentum URL-címét veszi fel.
* A fenti metódussal egy nyugtakép URL-címét is le kell kapnia.
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]

---

## <a name="object-model"></a>Objektummodell

A Form Recognizer két különböző ügyféltípust hozhat létre. Az első az űrlap felismert mezőinek és tartalmának lekérdezésére `FormRecognizerClient` használható. A második a használatával hozhat létre és kezelhet egyéni modelleket, amelyek `FormTrainingClient` segítségével javíthatja a felismerést.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` A a következő műveleteket biztosítja:

* Űrlapmezők és tartalmak felismerése az egyéni űrlapok elemzéséhez betanított egyéni modellekkel.  Ezeket az értékeket objektumok gyűjteményében lehet `RecognizedForm` visszaadni. Lásd az [Egyéni űrlapok elemzése példát.](#analyze-forms-with-a-custom-model)
* Űrlaptartalmak, köztük táblák, vonalak és szavak felismerése modell betanítása nélkül.  Az űrlap tartalma objektumgyűjteményben lesz `FormPage` visszaadva. Lásd az [Elrendezés elemzése példát.](#analyze-layout)
* A közös mezők felismerése az USA-nyugtákból, névjegykártyákból, számlákból és személyazonossági dokumentumokból a Form Recognizer modellel.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` A a következő műveleteket biztosítja:

* Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez.  A visszaadott érték jelzi a modell által elemzett űrlaptípusokat és az egyes űrlaptípusokhoz `CustomFormModel` kinyert mezőket.
* Egyéni modellek betanítása adott mezők és értékek elemzéséhez az egyéni űrlapok címkézése segítségével.  A visszaadott érték jelzi a modell által kinyert mezőket, valamint az egyes mezők becsült `CustomFormModel` pontosságát.
* A fiókjában létrehozott modellek kezelése.
* Egyéni modell másolása egyik erőforrásból Form Recognizer másikba.

> [!NOTE]
> A modellek grafikus felhasználói felülettel, például a Form Recognizer eszközzel is [betaníthatóak.](../../quickstarts/label-tool.md)

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan kell elvégezni a következő feladatokat Form Recognizer Java-ügyfélkódtárban:
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

A fő metódus tetején **adja** hozzá a következő kódot. Itt két ügyfélobjektumot fog hitelesíteni a fent meghatározott előfizetési változók használatával. Egy **AzureKeyCredential** objektumot fog használni, így szükség esetén új ügyfélobjektumok létrehozása nélkül frissítheti az API-kulcsot.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>Elrendezés elemzése

A dokumentumokkal Form Recognizer, vonalakat és szavakat elemezhet a dokumentumokban anélkül, hogy modellt kellene betanítanunk. Az elrendezéskinyerésről az Elrendezés fogalmi [útmutatóban található további információ.](../../concept-layout.md)

Egy adott URL-címen található fájl tartalmának elemzéséhez használja a **beginRecognizeContentFromUrl metódust.**

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> Helyi fájlból is lekért tartalom. Lásd a [FormRecognizerClient metódusokat,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) például **a beginRecognizeContent metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

A visszaadott érték a **FormPage** objektumok gyűjteménye: egy az elküldött dokumentum minden oldalához. A következő kód végig lépked ezeken az objektumokon, és kinyomtatja a kinyert kulcs/érték párokat és táblaadatokat.

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
## <a name="analyze-receipts"></a>Nyugták elemzése

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki közös mezőket az USA-nak megfelelő nyugtákból egy előre betanított nyugtamodell használatával. A nyugtaelemzéssel kapcsolatos további információkért lásd a [nyugták fogalmi útmutatóját.](../../concept-receipts.md)

Egy URI nyugtáinak elemzéséhez használja a **beginRecognizeReceiptsFromUrl metódust.**

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> A helyi nyugták képeit is elemezheti. Lásd a [FormRecognizerClient metódusokat,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) például **a beginRecognizeReceipts metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

A visszaadott érték **RecognizedReceipt** objektumok gyűjteménye: egy az elküldött dokumentum minden oldalához. A következő kódblokk végigveszi a nyugtákat, és kinyomtatja az adataikat a konzolon.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

A következő kódblokk végigveszi a nyugtán észlelt egyes elemeket, és kiírja az adataikat a konzolra.

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

## <a name="analyze-business-cards"></a>Névjegykártyák elemzése

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki gyakori mezőket angol névjegykártyákból egy előre betanított modell használatával. A névjegykártya-elemzéssel kapcsolatos további információkért lásd a [névjegykártyák fogalmi útmutatóját.](../../concept-business-cards.md)

A névjegykártyák URL-címből való elemzéséhez használja a `beginRecognizeBusinessCardsFromUrl` metódust.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> Helyi névjegykártya-képeket is elemezhet. Lásd a [FormRecognizerClient metódusokat,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) például **a beginRecognizeBusinessCards metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

A visszaadott érték **RecognizedForm-objektumok** gyűjteménye: egy a dokumentum minden kártyájára. A következő kód feldolgozza a névjegykártyát az adott URI-ban, és kiírja a fő mezőket és értékeket a konzolra.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="analyze-invoices"></a>Számlák elemzése

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

Ez a szakasz bemutatja, hogyan elemezhet és vonhet ki gyakori mezőket az értékesítési számlákból egy előre betanított modell használatával. A számlaelemzéssel kapcsolatos további információkért lásd a számla [fogalmi útmutatóját.](../../concept-invoices.md)

A számlák URL-címről való elemzéséhez használja a `beginRecognizeInvoicesFromUrl` metódust.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> A helyi számlákat is elemezheti. Lásd a [FormRecognizerClient metódusokat,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) például **a beginRecognizeInvoices metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

A visszaadott érték **RecognizedForm-objektumok** gyűjteménye: egyet a dokumentum minden egyes számlájához. A következő kód feldolgozza a számlát az adott URI-ban, és kinyomtatja a fő mezőket és értékeket a konzolon.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="analyze-identity-documents"></a>Identitásdokumentumok elemzése

#### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/preview)

Ez a szakasz azt mutatja be, hogyan elemezheti és vonhat ki kulcsfontosságú információkat a kormány által kiadott azonosítási dokumentumokból ( világszerte használt útlevelek és az Egyesült Államok sofőrlicencei) az előre összeállított Form Recognizer használatával. Az identitásdokumentumok elemzésével kapcsolatos további információkért tekintse meg az előre összeállított azonosítási modell [fogalmi útmutatóját.](../../concept-identification-cards.md)

Az URI-ről származó identitásdokumentumok elemzéséhez használja a `beginRecognizeIdDocumentsFromUrl` metódust.

:::code language="java" source="~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java" id="snippet_id_call":::

> [!TIP]
> A helyi identitásdokumentum-képeket is elemezheti. Lásd a [FormRecognizerClient metódusokat,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) például **a beginRecognizeIdDocuments metódust.** A helyi rendszerképeket érintő forgatókönyvekért tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) található mintakódot is.

A következő kód feldolgozza az adott URI-ban található identitásdokumentumot, és kinyomtatja a fő mezőket és értékeket a konzolon.

:::code language="java" source="~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java" id="snippet_id_print":::

#### <a name="v20"></a>[2.0-s verzió](#tab/ga)

> [!IMPORTANT]
> Ez a funkció nem érhető el a kiválasztott API-verzióban.

---

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Ez a szakasz bemutatja, hogyan betanítható egy modell a saját adataival. A betanított modell olyan strukturált adatokat képes kihozni, amelyek tartalmazzák az eredeti űrlapdokumentumban található kulcs/érték kapcsolatokat. A modell betanítása után tesztelheti és újra betaníthatja azt, és végül arra használhatja, hogy az igényeinek megfelelően megbízhatóan kinyerje az adatokat több űrlapból.

> [!NOTE]
> A modelleket grafikus felhasználói felülettel, például a mintacímkéző eszközzel [Form Recognizer betaníthatja.](../../quickstarts/label-tool.md)

### <a name="train-a-model-without-labels"></a>Modell betanítása címkék nélkül

Egyéni modellek betanítása az egyéni űrlapokon található összes mező és érték elemzéséhez a betanítás dokumentumainak manuális címkézése nélkül.

Az alábbi metódus egy adott dokumentumkészleten keresztül készíti el a modellt, majd kinyomtatja a modell állapotát a konzolon.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

A **visszaadott CustomFormModel** objektum információkat tartalmaz a modell által elemzható űrlaptípusokról és az egyes űrlaptípusokból kinyerni kívánt mezőkről. A következő kódblokk kinyomtatja ezt az információt a konzolon.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Végül ez a metódus a modell egyedi azonosítóját adja vissza.

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

Egyéni modelleket is betaníthat a betanítás dokumentumainak manuális címkézése által. A címkékkel való betanítás bizonyos helyzetekben jobb teljesítményt eredményez. A címkékkel való betanításhoz speciális címkeinformációs fájlokra van szükség a Blob Storage-tárolóban.pdf.labels.jsa betanítás dokumentumai mellett.*\<filename\>* A [Form Recognizer mintacímkéző eszköz](../../quickstarts/label-tool.md) felhasználói felülettel segít létrehozni ezeket a címkefájlokat. Ha már megvan, a **beginTraining** metódust a *useTrainingLabels* paraméter értékével hívhatja `true` meg.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


A **visszaadott CustomFormModel** jelzi a modell által kinyerhető mezőket, valamint az egyes mezőkben található becsült pontosságot. A következő kódblokk kinyomtatja ezt az információt a konzolon.

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

Ez a szakasz azt mutatja be, hogyan lehet kulcs/érték információkat és egyéb tartalmakat kinyerni az egyéni űrlaptípusokból a saját űrlapokkal betanított modellek használatával.

> [!IMPORTANT]
> Ennek a forgatókönyvnek a megvalósításához már be kell tanítania egy modellt, hogy az azonosítóját az alábbi metódusnak tovább tudja adni. Lásd [a Modell betanítás szakaszát.](#train-a-model-without-labels)

A **beginRecognizeCustomFormsFromUrl metódust fogja** használni.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> Helyi fájlokat is elemezhet. Lásd a [FormRecognizerClient metódusokat,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) például **a beginRecognizeCustomForms metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) található mintakódot a helyi rendszerképeket érintő forgatókönyvekért.

A visszaadott érték **RecognizedForm-objektumok** gyűjteménye: egy az elküldött dokumentum minden oldalához. A következő kód kinyomtatja az elemzési eredményeket a konzolon. Kinyomtatja az egyes felismert mezőket és a hozzájuk tartozó értékeket, valamint egy megbízhatósági pontszámot.

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



## <a name="manage-custom-models"></a>Egyéni modellek kezelése

Ez a szakasz bemutatja, hogyan kezelheti a fiókjában tárolt egyéni modelleket. Az alábbi kód az összes modellkezelési feladatot egyetlen metódusban, példaként használja. Először másolja ki az alábbi metódus-aláírást:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Ellenőrizze a modellek számát a FormRecognizer erőforrásfiókban

A következő kódblokk ellenőrzi, hogy hány modellt mentett a Form Recognizer-fiókjába, és összehasonlítja azt a fiókkorláthoz.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Kimenet

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Az erőforrásfiókban jelenleg tárolt modellek felsorolása

A következő kódblokk felsorolja a fiókjában lévő aktuális modelleket, és megjeleníti azok részleteit a konzolon.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Kimenet

Ez a válasz le lett csonkolva az olvashatóság érdekében.

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

### <a name="delete-a-model-from-the-resource-account"></a>Modell törlése az erőforrásfiókból

A modell a fiókjából is törölhető, ha az azonosítójára hivatkozik.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Lépjen vissza a projekt fő könyvtárába. Ezután készítse el az alkalmazást a következő paranccsal:

```console
gradle build
```

Futtassa az alkalmazást a következő `run` céllal:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services előfizetést, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Hibaelhárítás

A Recognizer-ügyfelek `ErrorResponseException` kivételeket képeznek. Ha például érvénytelen fájlforrás URL-címet próbál meg adni, az a hiba okát jelző `ErrorResponseException` hibával jelenik meg. Az alábbi kódrészletben a hibát a rendszer elegánsan kezeli a kivétel elfogása és a hibával kapcsolatos további információk megjelenítése által.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Ügyfélnaplózás engedélyezése

A Javához való Azure SDK-k konzisztens naplózási történetet kínálnak, amely segít az alkalmazáshibák elhárításában és a megoldásuk felgyorsításában. A naplók rögzítik az alkalmazás áramlását, mielőtt elérnék a terminál állapotát, hogy segítsenek a probléma gyökerének megtalálásában. A naplózás [engedélyezésével kapcsolatos](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) útmutatásért tekintse meg a naplózási wikit.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a Form Recognizer Java ügyféloldali kódtár használatával betanított modelleket és elemezte az űrlapokat. Ezután tippeket tanulhat egy jobb betanítású adatkészlet létrehozásához és pontosabb modellek létrehozásához.

> [!div class="nextstepaction"]
> [Betanítási adathalmaz létrehozása](../../build-training-data-set.md)

* [Mi a Form Recognizer?](../../overview.md)
* Az útmutató mintakódja (és még sok más) megtalálható a [GitHubon.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples)
