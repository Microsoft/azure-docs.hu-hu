---
title: A Form Recognizer újdonságai
titleSuffix: Azure Cognitive Services
description: Az Form Recognizer API legújabb Form Recognizer.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: e952d481daf53b1806dc3cfbb658c8c0c21f6984
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516297"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-form-recognizer"></a>A Form Recognizer újdonságai

A Form Recognizer szolgáltatás folyamatosan frissül. Ebből a cikkből tájékozódhat a funkciók fejlesztésével, javításával és dokumentációjának frissítésével kapcsolatban.

## <a name="april-2021"></a>2021. április
<!-- markdownlint-disable MD029 -->

### <a name="sdk-updates-api--version-21-preview3"></a>SDK-frissítések (API-verzió: 2.1-preview.3)

### <a name="c-version-310-beta4"></a>**C# 3.1.0-beta.4-es verzió**

* **Új módszerek az identitásdokumentumok adatainak elemzésére:**

   **[StartRecognizeIdDocumentsFromUriAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   **[StartRecognizeIdDocumentsAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   A mezőértékek listájáért tekintse meg _a_ kinyert [mezőket](concept-identification-cards.md#fields-extracted) a Form Recognizer dokumentációjában.

* Kibontva a **[StartRecognizeContent](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)** metódushoz elérhető dokumentumnyelvek készlete.

* **Az alábbi `Pages` osztályok által támogatott új tulajdonság:**

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   A tulajdonság lehetővé teszi többoldalas PDF- és TIFF-dokumentumok egyéni vagy többoldalas `Pages` oldaltartományának kiválasztását. Az egyes oldalakhoz adja meg az oldal számát, `3` például: . Több oldal (például a 2. oldal és az 5–7. oldal) számára adja meg a p korszámot és a vesszővel elválasztott tartományokat: `2, 5-7` .    

* **A következő `ReadingOrder` osztályhoz támogatott új tulajdonság:**

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  A tulajdonság egy opcionális paraméter, amellyel megadhatja, hogy melyik olvasásirend-algoritmust ( vagy ) kell alkalmazni a szöveges elemek `ReadingOrder` `basic` kinyerési `natural` sorrendjének sorrendjéhez. Ha nincs megadva, az alapértelmezett érték `basic` .

#### <a name="breaking-changes"></a>Kompatibilitástörő változások

* Az ügyfél alapértelmezés szerint a legújabb támogatott szolgáltatásverziót használja, amely jelenleg **2.1-preview.3.**

* **[A StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** metódus most egy metódust ad `RequestFailedException()` vissza, ha érvénytelen fájlt ad át.

### <a name="java-version-310-beta3"></a>**Java 3.1.0-beta.3-as verzió**

* **Új módszerek az identitásdokumentumok adatainak elemzésére:**

  **[beginRecognizeIdDocumentsFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocumentsfromurl?view=azure-java-preview&preserve-view=true)**

  **[beginRecognizeIdDocuments](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocuments?view=azure-java-preview&preserve-view=true)**

   A mezőértékek listájáért tekintse meg _a_ kinyert [mezőket](concept-identification-cards.md#fields-extracted) a Form Recognizer dokumentációjában.

* **Bitmap-képfájl (.bmp) támogatása `FormContentType`** az egyéni űrlapokhoz és a betanítás metódusához a felsorolásban:

* `image/bmp`

* **Az alábbi `Pages` osztályok által támogatott új tulajdonság:**

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  A tulajdonság lehetővé teszi többoldalas PDF- és TIFF-dokumentumok egyéni vagy többoldalas `Pages` oldaltartományának kiválasztását. Az egyes oldalakhoz adja meg az oldal számát, `3` például: . Több oldal (például 2. oldal és 5–7. oldal) számára adja meg az oldalszámokat és -tartományokat vesszővel elválasztva: `2, 5-7` .

* Bitmap-képfájl (.bmp) támogatása egyéni űrlapokhoz és betanító metódusokhoz a **[FormContentType mezőkben:](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields)**

  `image/bmp`

* **Az új kulcsszó `ReadingOrder` argumentum a következő metódusok esetében támogatott:**

* **[beginRecognizeContent](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?view=azure-java-preview&preserve-view=true)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   A kulcsszó argumentum egy opcionális paraméter, amellyel megadhatja, hogy melyik olvasásirend-algoritmust ( vagy ) kell alkalmazni a szöveges elemek `ReadingOrder` `basic` `natural` kinyerési sorrendjének megadásához. Ha nincs megadva, az alapértelmezett érték `basic` .

* Az ügyfél alapértelmezés szerint a legújabb támogatott szolgáltatásverziót használja, amely jelenleg **2.1-preview.3.**

### <a name="javascript-version-310-beta3"></a>**JavaScript 3.1.0-beta.3-as verzió**

* **Új módszerek az identitásdokumentumok adatainak elemzésére:**

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    A mezőértékek listáját  [](concept-identification-cards.md#fields-extracted) a dokumentációnkban kinyert mezőket Form Recognizer meg.

* **Új mezőértékek hozzáadva a FieldValue felülethez:**

    `gender`– a lehetséges értékek a `M` `F` vagy a `X` .</br>
   `country`– a lehetséges értékek az [ISO alpha-3 hárombetűs](https://www.iso.org/obp/ui/#search) országkódsringet követik.

* **Új `pages` lehetőség, amelyet az összes űrlapfelismerési módszer támogat (egyéni űrlapok és az összes előre összeállított modell). A argumentum lehetővé teszi többoldalas PDF- és TIFF-dokumentumok egyéni vagy többoldalas oldaltartományának kiválasztását. Az egyes oldalakhoz adja meg az oldal számát, `3` például: . Több oldal (például a 2. oldal és az 5–7. oldal) számára adja meg az oldalszámokat és -tartományokat vesszővel elválasztva: `2, 5-7` .

* A **[ReadingOrder](/javascript/api/@azure/ai-form-recognizer/readingorder?view=azure-node-preview&preserve-view=true)** típus támogatása hozzáadva a tartalomfelismerési módszerekhez. Ez a beállítás lehetővé teszi annak az algoritmusnak a szabályozását, amellyel a szolgáltatás meghatározza a felismert szövegsorok megállapításához használt vonalat. Megadhatja, hogy melyik olvasásirend-algoritmust ( vagy ) kell alkalmazni a szöveges elemek `basic` `natural` kinyerési sorrendjének sorrendjéhez. Ha nincs megadva, az alapértelmezett érték `basic` .

* A **[FormField típus](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** felosztása több különböző felületre. Ez nem okozhat API-kompatibilitási problémákat, kivéve bizonyos peremhálózati esetekben (nem meghatározott valueType).

* Migrálva a **2.1-preview.3** Form Recognizer szolgáltatásvégpontra minden REST API hívás esetén.

### <a name="python-version--310b4"></a>**Python 3.1.0b4-es verzió**

* **Új módszerek az identitásdokumentumok adatainak elemzésére:**

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  A mezőértékek listájáért tekintse meg _a_ kinyert [mezőket](concept-identification-cards.md#fields-extracted) a Form Recognizer dokumentációjában.

* **Új mezőértékek hozzáadva a [FieldValueType enumhoz:](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true)**

   gender – a lehetséges értékek vagy `M` `F` `X` .

  ország – a lehetséges értékek az [ISO alpha-3 országkódokat követik.](https://www.iso.org/obp/ui/#search)

* Bitmap-képfájl (.bmp) támogatása az egyéni űrlapokhoz és a betanítás metódusához a **[FormContentType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true) enumban:**

    image/bmp

* **Új kulcsszó `pages` argumentum, amelyet a következő metódusok támogatnak:**

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   A kulcsszó argumentum lehetővé teszi többoldalas PDF- és TIFF-dokumentumok egyéni vagy többoldalas `pages` oldaltartományának kiválasztását. Az egyes oldalakhoz adja meg az oldal számát, `3` például: . Több oldal (például 2. oldal és 5–7. oldal) számára adja meg az oldalszámokat és -tartományokat vesszővel elválasztva: `2, 5-7` .

* **Az új kulcsszó `readingOrder` argumentum támogatott a következő metódusok esetében:**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   A kulcsszó argumentum egy opcionális paraméter, amellyel megadhatja, hogy melyik olvasásirend-algoritmust ( vagy ) kell alkalmazni a szöveges elemek `readingOrder` `basic` kinyerési `natural` sorrendjének sorrendjéhez. Ha nincs megadva, az alapértelmezett érték `basic` .

## <a name="march-2021"></a>2021. március

**Form Recognizer 2.1-es nyilvános előzetes verzió 3-as verziója már elérhető.** Megjelent a v2.1-preview.3, amely a következő funkciókat tartalmazza:

* **Új előre összeállított azonosítómodell** Az új, előre összeállított azonosítómodell lehetővé teszi az ügyfelek számára, hogy azonosítókat használjanak, és strukturált adatokat adjanak vissza a feldolgozás automatizálásához. A hatékony optikai karakterfelismerési (OCR) képességeket és az azonosítófelismerési modelleket kombinálva kulcsinformációkat képes kinyerni az útlevelek és az Egyesült Államok illesztőprogram-licenceiből, például a nevet, a születési dátumot, a kibocsátás dátumát, a lejárati dátumot stb.

  [További információ az előre összeállított azonosítómodellről](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="passport példa" lightbox="./media/id-canada-passport-example.png":::

* **Sorelem kinyerése előre összeállított számlamodellhez** – Az előre összeállított számlamodell már támogatja a sortételek kinyerése; Most már kinyeri a teljes elemeket és azok részeit – leírás, mennyiség, mennyiség, termékazonosító, dátum stb. Egy egyszerű API-/SDK-hívással hasznos adatokat ( szöveg, tábla, kulcs-érték párok és sorelemek) is kinyerhet a számlákból.

   [További információ az előre összeállított számlamodellről](concept-invoices.md)

* Felügyelt táblacímkézés és -betanítás, **empty-value** (üres érték) [](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)címkézés – A Form Recognizer legkeletkűbb mélytanulási automatikus táblakinyerési képességei mellett mostantól lehetővé teszi az ügyfelek számára a táblák címkézését és betanítását is. Ez az új kiadás lehetővé teszi a sorelemek/táblák (dinamikus és rögzített) címkézését és betanítását, valamint egy egyéni modell betanítását kulcs-érték párok és sorelemek kinyerésére. A modell betanítása után a modell kinyeri a sorelemeket a documentResults szakaszban található JSON-kimenet részeként.

    :::image type="content" source="./media/table-labeling.png" alt-text="Táblacímkék" lightbox="./media/table-labeling.png":::

    A táblák címkézése mellett most már üres értékeket és régiókat is címkézhet; Ha a betanítás készletében egyes dokumentumok nem tartalmaznak értékeket bizonyos mezőkhöz, felcímkézheti őket, hogy a modell megfelelően kinyerje az értékeket az elemzett dokumentumokból.

* **66 új** nyelv támogatása – Form Recognizer Layout API és az egyéni modellek mostantól 73 nyelvet támogatnak.

  [További információ a Form Recognizer nyelvi támogatásról](language-support.md)

* Természetes olvasási **sorrend,** kézírás-osztályozás és oldalkijelölés – Ezzel a frissítéssel a szöveges sorok kimenetét természetes olvasási sorrendben is leküldheti az alapértelmezett balról jobbra, illetve fentről lefelé sorrendbe. Használja az új readingOrder lekérdezési paramétert, és állítsa "természetes" értékre egy emberbarátabb olvasási sorrendű kimenethez. Emellett a latin nyelvű nyelvek esetében a Form Recognizer a szöveges sorokat kézzel írott stílusként vagy nem osztályozza, és megbízhatósági pontszámot ad.

* **Előre összeállított nyugtamodellek minőségének fejlesztései** Ez a frissítés számos minőségi fejlesztést tartalmaz az előre összeállított nyugtamodellhez, különösen a sorelem kinyerése terén.

## <a name="november-2020"></a>2020. november

### <a name="new-features"></a>Új funkciók

**Form Recognizer 2.1-es verzió nyilvános előzetes verziója már elérhető.** Megjelent a v2.1-preview.2, amely a következő funkciókat tartalmazza:

- **Új, előre összeállított számlamodell** – Az új, előre összeállított számlamodell lehetővé teszi az ügyfelek számára, hogy különböző formátumokban számláznak, és strukturált adatokat adjanak vissza a számlafeldolgozás automatizálásához. Ötvözi a hatékony optikai karakterfelismerési (OCR) képességeket, valamint a számlák megértésére használható mélytanulásos modelleket, hogy a legfontosabb információkat kinyerje a számlákból angol nyelven. Kinyeri a kulcsszöveget, a táblákat és az olyan információkat, mint az ügyfél, a szállító, a számlaazonosító, a számla esedékes dátuma, az összeg, az esedékes összeg, az adó összege, a kiszállító és a számla.

  > [További információ az előre összeállított számlamodellről](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="példa számla" lightbox="./media/invoice-example.jpg":::

- **Továbbfejlesztett táblakinyerés** – a Form Recognizer továbbfejlesztett táblakinyerést biztosít, amely a hatékony optikai karakterfelismerési (OCR) képességeket egy mélytanulási táblakinyerési modellel kombinálja. Form Recognizer kinyerhet adatokat a táblákból, beleértve az egyesített oszlopokat, sorokat, szegélyek nélkül stb. összetett táblákat.

  :::image type="content" source="./media/tables-example.jpg" alt-text="példatáblák" lightbox="./media/tables-example.jpg":::


  > [További információ az elrendezés kinyerésről](concept-layout.md)

- **Ügyfélkódtár frissítése** – A [](quickstarts/client-library.md) .NET, Python, Java és JavaScript ügyfélkódtárainak legújabb verziói támogatják a Form Recognizer 2.1 API-t.
- **Új támogatott nyelv: Japán** – A következő új nyelvek támogatottak: `AnalyzeLayout` és : Japán ( `AnalyzeCustomForm` `ja` ). [Nyelvi támogatás](language-support.md)
- Szövegsor stílusának jelzése (csak kézzel **írt/egyéb) (** csak latin nyelvű) – Form Recognizer kimenete egy objektum, amely azt sorolja be, hogy minden szövegsor kézzel írott stílus-e vagy sem, valamint egy megbízhatósági `appearance` pontszámot. Ez a funkció csak latin nyelvű nyelveken támogatott.
- **Minőségbeli fejlesztések** – A kinyerés fejlesztései, beleértve az egy számjegyből álló kinyerés fejlesztései is.
- Az Form Recognizer Sample and Labeling Tool új **try-it-out** funkciója – Az előre összeállított számla-, nyugta- és névjegykártya-modellek, valamint a Layout API kipróbálása az Form Recognizer Sample Labeling eszközzel. Láthatja, hogyan lesznek kinyerve az adatok kód írása nélkül.

  > [Az Form Recognizer eszköz kipróbálja](https://fott-preview.azurewebsites.net/)

  ![FOTT-példa](./media/ui-preview.jpg)

- **Visszajelzési** hurok – Ha a mintacímkéző eszközzel elemez fájlokat, hozzáadhatja azt a betanításkészlethez, szükség esetén módosíthatja a címkéket, és betaníthatja őket a modell javítására.
- **Dokumentumok automatikus címkézése** – Automatikusan felcímkéz további dokumentumokat a projektben korábban címkével jelölt dokumentumok alapján.

## <a name="august-2020"></a>2020. augusztus

### <a name="new-features"></a>Új funkciók

**Form Recognizer 2.1-es verzió nyilvános előzetes verziója már elérhető.** Megjelent a V2.1-preview.1, amely a következő funkciókat tartalmazza:


- **REST API referencia elérhető** – A [v2.1-preview.1 referencia megtekintése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
- Új **támogatott** nyelvek Az angol [](language-support.md) mellett a következő nyelvek támogatottak: és : és : angol ( ), kínai `Layout` `Train Custom Model` `en` (egyszerűsített) ( ), holland ( ), francia ( `zh-Hans` ), német ( ), olasz ( ), portugál ( `nl` `fr` `de` `it` `pt` ) `es` és spanyol ( ).
- **Jelölőnégyzet/ Kijelölésjelző** észlelése – Form Recognizer támogatja a kijelölési jelek, például a jelölőnégyzetek és választógombok észlelését és kinyerét. A kijelölési jelek ki vannak bontva a -ban, és mostantól a Betanítás címkékkel beállításban is címkézheti és betaníthatja őket, így kulcs-érték párokat is kinyerhet `Layout` `Train Custom Model`  -   a kijelölési jelekhez.
- **Model Compose** – lehetővé teszi több modell összeírását és egyetlen modellazonosítóval való megírását. Amikor elküld egy dokumentumot elemzésre egy létrehozott modellazonosítóval, a rendszer először egy besorolási lépést hajt végre, amely a megfelelő egyéni modellhez irányítja azt. A Model Compose címkékkel való `Train Custom Model`  -  _betanításhoz érhető el._
- **Modell neve** – a könnyebb felügyelet és nyomon követés érdekében adjon hozzá egy rövid nevet az egyéni modellekhez.
- **[Új, előre felépített modell névjegykártyákhoz](concept-business-cards.md)** az angol nyelvű névjegykártyák gyakori mezőinek kinyeréséhez.
- **[Az EN-US](concept-receipts.md)** mellett új területi beállítás érhető el az előre felépített nyugtákhoz, mostantól az EN-AU, az EN-CA, az EN-GB, az EN-IN és az EN-IN is támogatott
- **Minőségbeli fejlesztések a** `Layout` `Train Custom Model`  -  _,Betanítás címkék nélkül és_ _Betanítás címkékkel_.

**A 2.0-s** verzió a következő frissítést tartalmazza:

- A [](quickstarts/client-library.md) NET, Python, Java és JavaScript ügyfélkódtárai általánosan elérhetőek.

**Új minták érhetők** el a GitHubon.

- A [Tudáskinyerési](https://github.com/microsoft/knowledge-extraction-recipes-forms) módszerek – Űrlap-forgatókönyv valós Form Recognizer-ügyfelektől származó ajánlott eljárásokat gyűjt, és használható kódmintákat, ellenőrzőlistákat és mintaadatokat biztosít a projektek fejlesztéséhez.
- A [mintacímkéző eszköz](https://github.com/microsoft/OCR-Form-Tools) frissítve lett, hogy támogassa az új v2.1 funkciót. Tekintse meg [ezt a rövid útmutatót](quickstarts/label-tool.md) az eszköz első lépésekhez.
- Az [Intelligent Kioszk](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) Form Recognizer minta bemutatja, hogyan integrálható és `Analyze Receipt` `Train Custom Model`  -  _betanítható címkék nélkül._

## <a name="july-2020"></a>2020. július

### <a name="new-features"></a>Új funkciók
<!-- markdownlint-disable MD004 -->
* **Elérhető 2.0-s** verziós referencia – Megtekintheti a [2.0-s](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) verzió api-referenciáját és a [.NET,](/dotnet/api/overview/azure/ai.formrecognizer-readme) [Python,](/python/api/overview/azure/) [Java](/java/api/overview/azure/ai-formrecognizer-readme)és JavaScript frissített [API-ját.](/javascript/api/overview/azure/)
* **A táblafejlécek** és a kinyerés fejlesztései – pontossági és táblakinyerési fejlesztéseket tartalmaz, pontosabban azt a képességet, hogy az egyéni betanítás táblafejléceket és -struktúrákat tanul meg címkék _nélkül._

* **Pénznemtámogatás** – Globális pénznemszimbólum észlelése és kinyerése.
* **Azure Gov** – Form Recognizer már az Azure Gov-ban is elérhető.
* **Továbbfejlesztett biztonsági funkciók:**
  * **Saját kulcs létrehozása** – a Form Recognizer automatikusan titkosítja az adatokat, amikor a felhőben vannak megőrzve, hogy megvédjék őket, és segítsenek a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítésében. Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Most már saját titkosítási kulcsokkal is kezelheti előfizetését. Az ügyfél által kezelt kulcsok, más néven a saját kulcs használata [(BYOK)](./encrypt-data-at-rest.md)nagyobb rugalmasságot nyújtanak a hozzáférés-vezérlés létrehozásához, váltásához, letiltásához és visszavonásához. Továbbá az adatok védelméhez használt titkosítási kulcsok naplózására is lehetősége van.
  * **Privát végpontok** – Lehetővé teszi, hogy egy virtuális hálózaton biztonságosan hozzáférjen az adatokhoz [egy Private Link.](../../private-link/private-link-overview.md)

## <a name="june-2020"></a>2020. június

### <a name="new-features"></a>Új funkciók

* **CopyModel API hozzáadva az** ügyféloldali API-khoz – Mostantól az ügyféloldali API-k használatával modelleket másolhat egyik előfizetésből a másikba. A [funkcióval kapcsolatos általános információkért](./disaster-recovery.md) lásd a modellek biztonsági és helyreállításával kapcsolatos információkat.
* **Azure Active Directory integrációja** – Mostantól az Azure AD hitelesítő adataival hitelesítheti a Form Recognizer ügyfélobjektumait az SDK-kban.
* **SDK-specifikus módosítások** – Ez a változás kisebb funkció-kiegészítéseket és a feltörést is magában foglalja. További információért tekintse meg az SDK változásnaplóit.
  * [C# SDK Preview 3 változásnapló](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK – 3. előzetes verzió változásnaplója](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK Preview 3 változásnapló](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK – 3. előzetes verzió változásnaplója](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020. április

### <a name="new-features"></a>Új funkciók

* **SDK-támogatás a Form Recognizer API 2.0** nyilvános előzetes verziójához – Ebben a hónapban kiterjesztettük a szolgáltatás támogatását, hogy tartalmazza a Form Recognizer 2.0-s verzió (előzetes verzió) előzetes verziójához készült előzetes SDK-t. Használja az alábbi hivatkozásokat a választott nyelv használatának első lépésekhez:
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  Az új SDK a 2.0-s és újabb REST API összes Form Recognizer. Betaníthat például egy modellt címkékkel vagy anélkül, és szöveget, kulcs-érték párokat és táblákat is kinyerhet az űrlapokból, adatokatnyerhet ki a nyugtákból az előre felépített nyugtaszolgáltatással, valamint szöveget és táblázatokat kinyerhet a dokumentumokból az elrendezési szolgáltatással. Az SDK visszajelzési űrlapon keresztül küldhet visszajelzést az [SDK-król.](https://aka.ms/FR_SDK_v1_feedback)

* **Egyéni modell másolása** Az új Egyéni modell másolása funkcióval mostantól modelleket másolhat régiók és előfizetések között. Az Egyéni modell másolása API hívása előtt be kell szereznie a célerőforrásba való másoláshoz szükséges engedélyt a másolási engedélyezési műveletnek a cél erőforrásvégponton való hívásához.

  * [Másolási engedélyezés létrehozása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [Egyéni modell másolása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API

### <a name="security-improvements"></a>Biztonsági fejlesztések

* Customer-Managed Kulcsok már elérhetők a FormRecognizer számára. További információ: [Adattitkosítás](./encrypt-data-at-rest.md)az Form Recognizer.
* Felügyelt identitások használata az Azure-erőforrásokhoz való hozzáféréshez Azure Active Directory. További információ: [Felügyelt identitásokhoz való hozzáférés jogosultsága.](../authentication.md#authorize-access-to-managed-identities)

## <a name="march-2020"></a>2020. március

### <a name="new-features"></a>Új funkciók

* **Értéktípusok címkézéshez** Most már megadhatja a címkézni kívánt értéktípusokat a Form Recognizer eszközével. Jelenleg a következő értéktípusok és változatok támogatottak:
  * `string`
    * alapértelmezett, `no-whitespaces` , `alphanumeric`
  * `number`
    * Alapértelmezett `currency`
  * `date`
    * alapértelmezett, `dmy` , `mdy` , `ymd`
  * `time`
  * `integer`

  Ennek a [funkciónak](./quickstarts/label-tool.md#specify-tag-value-types) a használatát a mintacímkéző eszköz útmutatója alapján saját használhatja.


* **Táblavizualizáció** A mintacímkéző eszköz most már a dokumentumban felismert táblákat jeleníti meg. Ezzel a funkcióval a címkézés és elemzés előtt megtekintheti a dokumentumban felismert és kinyert táblákat. Ez a funkció a rétegek beállítással ki- és bekapcsolható.

  Az alábbi képen a táblák felismerésének és kinyerének példái láthatóak:

  > [!div class="mx-imgBorder"]
  > ![Táblavizualizáció a mintacímkéző eszközzel](./media/whats-new/table-viz.png)

    A kinyert táblák a JSON-kimenetben érhetők el a `"pageResults"` alatt.

  > [!IMPORTANT]
  > A táblák címkézése nem támogatott. Ha a rendszer nem ismeri fel és extratálta automatikusan a táblákat, csak kulcs/érték párokként címkézheti fel őket. Ha kulcs/érték párként címkéz meg táblákat, mindegyik cellát egyedi értékként címkézze fel.

### <a name="extraction-enhancements"></a>Kinyerési fejlesztések

Ez a kiadás kinyerési fejlesztéseket és pontosságbeli fejlesztéseket tartalmaz, különösen azt a képességet, hogy több kulcs/érték párt címkézhet és vonhat ki ugyanabban a szövegsorban.

### <a name="sample-labeling-tool-is-now-open-source"></a>A mintacímkéző eszköz mostantól nyílt forráskódú

A Form Recognizer mintacímkéző eszköz nyílt forráskódú projektként érhető el. Integrálhatja a megoldásokba, és az igényeinek megfelelően ügyfélspecifikus módosításokat is végrehajtott.

A mintacímkéző eszközről Form Recognizer a GitHubon elérhető [dokumentációban talál további információt.](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)

### <a name="tls-12-enforcement"></a>A TLS 1.2 kényszerítése

A TLS 1.2 mostantól kényszerítve van a szolgáltatásnak szóló összes HTTP-kéréshez. További információ: biztonsági [Azure Cognitive Services.](../cognitive-services-security.md)

## <a name="january-2020"></a>2020. január

Ez a kiadás a Form Recognizer 2.0 (előzetes verzió) kiadást vezet be. Az alábbi szakaszokban további információt talál az új funkciókról, fejlesztésekről és változásokról.

### <a name="new-features"></a>Új funkciók

* **Egyéni modell**
  * **Betanítás címkékkel** Most már betaníthat egy egyéni modellt manuálisan címkével jelölt adatokkal. Ez a módszer jobb teljesítő modelleket hoz létre, és olyan modelleket képes előállítani, amelyek kulcsok nélküli értékeket tartalmazó, összetett űrlapokkal vagy űrlapokkal működnek.
  * **Aszinkron API** Az aszinkron API-hívásokkal nagy adatkészleteket és fájlokat betaníthat és elemezhet.
  * **TIFF-fájlok támogatása** Most már betaníthat TIFF-dokumentumokból, és kinyerheti az adatokat.
  * **Kinyerési pontosság fejlesztései**

* **Előre összeállított nyugtamodell**
  * **Tipp összegei** Most már kinyerheti a tippek összegét és más kézzel írt értékeket.
  * **Sorelem kinyerése** A nyugtákból kinyerheti a sortételek értékeit.
  * **Megbízhatósági értékek** Megtekintheti a modell megbízhatóságát minden kinyert érték esetében.
  * **Kinyerési pontosság fejlesztései**

* **Elrendezés kinyerése** Mostantól a Layout API használatával szöveges adatokat és táblázatadatokat is kinyerhet az űrlapokról.

### <a name="custom-model-api-changes"></a>Egyéni modell API-változásai

Az egyéni modellek betanítása és használata során az összes API át lett nevezve, és egyes szinkron metódusok mostantól aszinkronok. A főbb változások a következők:

* A modell betanítási folyamata mostantól aszinkron. A betanítás a **/custom/models API-híváson** keresztül indítható el. Ez a hívás egy műveletazonosítót ad vissza, amelyet átadhat a **custom/models/{modelID}** metódusnak a betanítás eredményeinek visszaadása érdekében.
* A kulcs/érték kinyerése most a **/custom/models/{modelID}/analyze** API-hívással kezdeményezhető. Ez a hívás egy műveletazonosítót ad vissza, amelyet átadhat a **custom/models/{modelID}/analyzeResults/{resultID}** elemnek a kinyerési eredmények visszaadása érdekében.
* A betanítás műveletének műveleti száma mostantól a **HTTP-válaszok** **Hely** fejlécében található, nem pedig a Műveleti hely fejlécben.

### <a name="receipt-api-changes"></a>Nyugta API-módosítások

Az értékesítési nyugták olvasásának API-ját átnevezték.

* A nyugtaadatok kinyerése most a **/prebuilt/receipt/analyze** API-hívással kezdeményezhető. Ez a hívás egy műveletazonosítót ad vissza, amelyet a **/prebuilt/receipt/analyzeResults/{resultID}** függvénynek át lehet adni a kinyerési eredmények visszaadása érdekében.

### <a name="output-format-changes"></a>Kimeneti formátum változásai

Az összes API-hívás JSON-válaszai új formátumokkal vannak formázva. Néhány kulcs és érték hozzá lett adva, el lett távolítva vagy átnevezve. Az aktuális JSON-formátumokra a rövid útmutatókban talál példákat.

## <a name="next-steps"></a>Következő lépések

Rövid útmutató [segítségével elkezdhet](quickstarts/client-library.md) űrlapfeldolgozó alkalmazást írni Form Recognizer a választott fejlesztői nyelven.

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](./overview.md)