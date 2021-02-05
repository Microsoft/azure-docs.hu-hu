---
title: A Form Recognizer újdonságai
titleSuffix: Azure Cognitive Services
description: Ismerje meg az űrlap-felismerő API legújabb módosításait.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: a8eecabe71cb439949f7e0638c7339eb31b08500
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581261"
---
# <a name="whats-new-in-form-recognizer"></a>A Form Recognizer újdonságai

Az űrlap-felismerő szolgáltatás folyamatosan frissül. Ebből a cikkből megtudhatja, hogyan maradhat naprakészen a funkciók fejlesztései, a javítások és a dokumentációs frissítések segítségével.

## <a name="november-2020"></a>2020. november

### <a name="new-features"></a>Új funkciók

**Az űrlap-felismerő v 2.1 nyilvános előzetes verziója már elérhető.** V 2.1 – előzetes verzió: 2, a következő funkciókkal együtt: 

- **Új előre elkészített számla modell** – az új, előre elkészített számla modell lehetővé teszi, hogy az ügyfelek különböző formátumokban számlázzák a számlákat, és a feldolgozott adatmennyiséget a számla feldolgozásának automatizálásához adják vissza. A nagy teljesítményű optikai karakterfelismerési (OCR) képességeket ötvözi a részletes tanulási modellekkel, hogy kinyerje a legfontosabb információkat a számlákból angol nyelven. Kibontja a szöveget, a táblákat és az információkat, például az ügyfelet, a szállítót, a számla AZONOSÍTÓját, a számla esedékes dátumát, a végösszeget, az esedékes összeget, az adó mennyiségét, a szállítást és a számlázást.

  > [További információ az előre elkészített számla modelljéről](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="számlázási példa" lightbox="./media/invoice-example.jpg":::

- **Továbbfejlesztett táblázat kinyerése** – a Form-felismerő mostantól továbbfejlesztett táblázatos kitermelést biztosít, amely a hatékony optikai karakterfelismerési (OCR) képességeket ötvözi a Deep learning Table Extracting modellel. Az űrlap-felismerő képes kinyerni az adatok táblázatokból való kinyerését, beleértve az egyesített oszlopokkal, sorokkal, szegélyekkel és egyebekkel rendelkező összetett táblákat. 
 
  :::image type="content" source="./media/tables-example.jpg" alt-text="táblák – példa" lightbox="./media/tables-example.jpg":::

 
  > [További információ az elrendezés kinyeréséről](concept-layout.md)

- **Ügyféloldali kódtár frissítése** – a .net, a Python, a Java és a JavaScript [ügyféloldali kódtárainak](quickstarts/client-library.md) legújabb verziói támogatják az űrlap-felismerő 2,1 API-ját.
- **Támogatott új nyelv: Japán** – a következő új nyelvek támogatottak:, `AnalyzeLayout` és `AnalyzeCustomForm` : Japán ( `ja` ). [Nyelvi támogatás](language-support.md)
- **Szöveg vonaltípusának jelzése (kézírásos/egyéb) (csak latin nyelveken)** – az űrlap-felismerő mostantól egy olyan objektumot ad eredményül `appearance` , amely azt sorolja fel, hogy az egyes szövegfájlok kézírásos stílusúak-e, valamint a megbízhatósági pontszámmal együtt. Ez a funkció csak latin nyelveken támogatott.
- **Minőség-tökéletesítések** – a kinyerési funkciók, beleértve az egyszámjegyű extrakciós funkciókat.
- Az **űrlap-felismerő minta-és címkéző eszközének új, kipróbálható funkciója** az előre elkészített számla-, visszaigazolás-és névjegykártya-modellek és az elrendezési API kipróbálására a Form felismerő minta feliratozási eszköz használatával. Megtudhatja, hogyan lesz kinyerve az adatai a kód írása nélkül.

  > [Próbálja ki az űrlap-felismerő minta eszközt](https://fott-preview.azurewebsites.net/)

  ![FOTT példa](./media/ui-preview.jpg)
  
- **Visszajelzési hurok** – ha a fájlok elemzése a minta feliratozási eszköz segítségével végezhető el, most hozzáadhatja azt a betanítási készlethez, és szükség szerint módosíthatja a címkéket, és javíthatja a modellt.
- **Dokumentumok automatikus címkézése** – a projekt korábbi címkézett dokumentumai alapján automatikusan felcímkéz további dokumentumokat.

## <a name="august-2020"></a>2020. augusztus

### <a name="new-features"></a>Új funkciók

**Az űrlap-felismerő v 2.1 nyilvános előzetes verziója már elérhető.** V 2.1 – előzetes verzió. 1 kiadásra került, beleértve a következő funkciókat: 


- **REST API hivatkozás elérhető** – tekintse meg a [v 2.1 – preview. 1 hivatkozást](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- Az angol nyelveken **kívül** a következő [nyelvek](language-support.md) támogatottak: a `Layout` és a `Train Custom Model` : angol (), a `en` kínai (egyszerűsített) (), a holland (), a `zh-Hans` francia (), a német (), az `nl` `fr` `de` olasz (), a `it` portugál ( `pt` ) és a spanyol ( `es` ).
- **Jelölőnégyzet/kiválasztási jel észlelése** – az űrlap-felismerő támogatja a kiválasztási jelek, például a jelölőnégyzetek és választógombok észlelését és kinyerését. A kiválasztási jelek kibontása a-ben megtörténik, `Layout` és most már címkézheti és betaníthatja a `Train Custom Model`  -  _betanítást a vonaton_ , hogy kinyerje a legfontosabb érték párokat 
- **Model levélírás** – lehetővé teszi több modell összeállítását és meghívását egyetlen modell-azonosítóval. Ha egy dokumentumot egy összeállított modell-AZONOSÍTÓval kell elemezni, a rendszer először egy besorolási lépést hajt végre a megfelelő egyéni modellhez való továbbításhoz. A Model összeállítás elérhető a `Train Custom Model`  -  _feliratokkal rendelkező vonatok_ számára.
- **Modell neve** – egyszerűen kezelheti és nyomon követheti az egyéni modellek felhasználóbarát nevét.
- **[Új, előre elkészített modell a vállalati kártyákhoz](concept-business-cards.md)** a gyakori mezők angol nyelvű kinyeréséhez, valamint a Language Business Cards szolgáltatásban.
- **[Új területi beállítások az előre elkészített visszaigazolásokhoz](concept-receipts.md)** az en-us mellett a támogatás mostantól elérhető az EN-au, az en-CA, az en-GB, az EN-in
- **Minőségi fejlesztése** `Layout` , `Train Custom Model`  -  _címkék nélküli betanítás_ , _címkék_ nélkül.


a 2.0-s **verzió** a következő frissítést tartalmazza:

- A NET, a Python, a Java és a JavaScript [ügyféloldali kódtárai](quickstarts/client-library.md) általánosan elérhetővé léptek. 


Az **új minták** a githubon érhetők el. 
- A [Knowledge Extracting-receptek – az űrlapok](https://github.com/microsoft/knowledge-extraction-recipes-forms) forgatókönyve az ajánlott eljárásokat a valós Form-felismerő ügyfelek bevonásával gyűjti, és a projektek fejlesztéséhez használt, használható mintakód-mintákat, ellenőrzőlistákat és mintavételi folyamatokat biztosít. 
- A [mintául szolgáló címkéző eszköz](https://github.com/microsoft/OCR-Form-Tools) frissítve lett az új v 2.1 funkció támogatásához. Ebben a rövid útmutatóban [megismerheti az eszköz](quickstarts/label-tool.md) első lépéseit. 
- Az [intelligens kioszk](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) űrlap felismerő mintája azt mutatja be, hogyan integrálható `Analyze Receipt` és `Train Custom Model`  -  _betanítható címkék nélkül_.



## <a name="july-2020"></a>2020. július

### <a name="new-features"></a>Új funkciók

* a 2.0-s verzió **referenciája** – tekintse meg a [v 2.0 API-referenciát](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) és a [.net](/dotnet/api/overview/azure/ai.formrecognizer-readme), [Python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme)és [JavaScript](/javascript/api/overview/azure/)rendszerhez készült frissített SDK-kat.
* A **tábla fejlesztései és a kinyerési fejlesztések** – tartalmazza a pontossági fejlesztéseket és a táblázatos kivonások fejlesztéseit, és lehetővé teszik a táblázatok fejlécének és struktúráinak megtanulását _címkék nélkül_. 

* **Pénznem-támogatás** – globális pénznemű szimbólumok észlelése és kinyerése.
* Az **Azure gov** -Form felismerő mostantól elérhető az Azure gov-ben is.
* **Fokozott biztonsági funkciók**: 
   * A **saját kulcs** -űrlap felismerője automatikusan titkosítja az adatait, amikor a felhőben megőrzi a védelmet, és segít a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítésében. Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Mostantól a saját titkosítási kulcsaival is kezelheti az előfizetését. Az [ügyfél által felügyelt kulcsok – más néven saját kulcs használata (BYOK)](./form-recognizer-encryption-of-data-at-rest.md)– nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Továbbá az adatok védelméhez használt titkosítási kulcsok naplózására is lehetősége van.  
   * **Privát végpontok** – lehetővé teszi, hogy egy virtuális hálózaton (VNet) [biztonságosan hozzáférjenek az adathoz egy privát kapcsolaton keresztül.](../../private-link/private-link-overview.md)


## <a name="june-2020"></a>2020. június

### <a name="new-features"></a>Új funkciók
* **COPYMODEL API az ügyfél SDK** -k számára – mostantól az ügyfél SDK-k segítségével másolhat modelleket az egyik előfizetésből a másikba. A szolgáltatással kapcsolatos általános információkért tekintse meg a [modellek biztonsági mentése és helyreállítása](./disaster-recovery.md) című témakört.
* **Azure Active Directory integráció** – mostantól az Azure ad-beli hitelesítő adataival hitelesítheti az űrlap-felismerő ügyféloldali objektumait az SDK-k használatával.
* **SDK-specifikus változások** – ez magában foglalja mind az alszolgáltatások, mind a megszakított módosításokat. További információért tekintse meg az SDK-naplókat.
  * [C# SDK előnézet 3 changelog](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK előzetes verzió 3 – changelog](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK előzetes verzió 3 – changelog](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK előzetes verzió 3 – changelog](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020. április

### <a name="new-features"></a>Új funkciók
* **SDK-támogatás az űrlap-FELISMERŐ API 2.0** -s verziójának nyilvános előzetes verziójához – ebben a hónapban kibővítettük a szolgáltatás támogatását, amely tartalmaz egy előzetes verziójú SDK-t a Form felismerő v 2.0 (előzetes verzió) kiadásához. Használja az alábbi hivatkozásokat a választott nyelv megkezdéséhez: 
   * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
   * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
   * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
   * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  Az új SDK a v 2.0 REST API összes funkcióját támogatja az űrlap-felismerőhöz. Például betaníthat egy modellt címkékkel vagy anélkül, és kinyerheti a szöveget, a kulcs érték párokat és táblákat az űrlapokból, kinyerheti a nyugták adatait az előre elkészített visszaigazolási szolgáltatással, és kinyerheti a szöveget és a táblákat az elrendezési szolgáltatással a dokumentumaiból. Az [SDK-visszajelzési űrlapon](https://aka.ms/FR_SDK_v1_feedback)megoszthatja az SDK-kat.
 
* **Egyéni modell másolása** Most már másolhat modelleket régiók és előfizetések között az új másolás egyéni modell használatával. Az egyéni modell-API másolásának megkezdése előtt be kell szereznie az engedélyt, hogy a cél erőforrásba másolja a másolás engedélyezési műveletét a cél erőforrás-végponton.
   * [Másolási engedély létrehozása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
   * [Egyéni modell másolása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>Biztonsági fejlesztések

* Mostantól elérhetők a FormRecognizer Customer-Managed kulcsai. További információ: az adattitkosítás inaktív állapotban [az űrlap-felismerő számára](./form-recognizer-encryption-of-data-at-rest.md).
* Felügyelt identitások használata az Azure-erőforrásokhoz való hozzáféréshez Azure Active Directory. További információ: [hozzáférés engedélyezése a felügyelt identitásokhoz](../authentication.md#authorize-access-to-managed-identities).

## <a name="march-2020"></a>2020. március 

### <a name="new-features"></a>Új funkciók

* **Címkézési értékek típusai** Mostantól megadhatja, hogy milyen típusú értékeket szeretne címkézni az űrlap-felismerő minta feliratozási eszközzel. A következő típusú értékek és változatok jelenleg támogatottak:
  * `string`
    * alapértelmezett, `no-whitespaces` , `alphanumeric`
  * `number`
    * alapértelmezett `currency`
  * `date` 
    * alapértelmezett, `dmy` , `mdy` , `ymd`
  * `time`
  * `integer`

  A funkció használatának megismeréséhez tekintse meg a [minta címkézési eszköz](./quickstarts/label-tool.md#specify-tag-value-types) útmutatóját.


* **Táblázat vizualizációja** A minta feliratozási eszköz mostantól a dokumentumban felismert táblákat jeleníti meg. Ezzel a funkcióval megtekintheti a dokumentumból felismert és kinyert táblákat a címkézés és az elemzés előtt. Ez a funkció a rétegek lehetőség használatával kapcsolható be/ki.

  Az alábbi ábrán egy példa látható a táblák felismerésére és kinyerésére:

  > [!div class="mx-imgBorder"]
  > ![Táblázat vizualizációja a minta feliratozási eszköz használatával](./media/whats-new/formre-table-viz.png)

    A kibontott táblázatok a alatt található JSON-kimenetben érhetők el `"pageResults"` .

  > [!IMPORTANT]
  > A címkézési táblák nem támogatottak. Ha a táblákat nem ismeri fel és nem extrated automatikusan, akkor csak kulcs/érték párokként címkézheti őket. Amikor kulcs/érték párokként címkézi a táblákat, az egyes cellákat egyedi értékként címkézze fel.

### <a name="extraction-enhancements"></a>Extrakciós fejlesztések

Ez a kiadás magában foglalja az extrakciós fejlesztéseket és a pontosságot, pontosabban a több kulcs/érték párok címkézését és kinyerését ugyanabban a sorban. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>A minta címkéző eszköz mostantól nyílt forráskódú

Az űrlap-felismerő minta feliratozási eszköze már elérhető nyílt forráskódú projektként. Integrálhatja azt a megoldásain belül, és az ügyfél-specifikus módosításokat az igényeinek megfelelően végezheti el.

Az űrlap-felismerő minta címkézési eszközével kapcsolatos további információkért tekintse át a [githubon](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)elérhető dokumentációt.

### <a name="tls-12-enforcement"></a>A TLS 1.2 kényszerítése

A TLS 1,2 mostantól a szolgáltatáshoz tartozó összes HTTP-kérelem esetében érvénybe lép. További információ: [Azure Cognitive Services Security](../cognitive-services-security.md).

## <a name="january-2020"></a>2020. január

Ez a kiadás a 2,0 (előzetes verzió) Form felismerőt mutatja be. Az alábbi szakaszban további információkat talál az új funkciókról, a fejlesztésekről és a változásokról. 

### <a name="new-features"></a>Új funkciók

* **Egyéni modell**
  * **Tanítás címkékkel** Mostantól manuálisan címkézett adattípusú egyéni modellt is betaníthat. Ez a módszer jobb teljesítményű modelleket eredményez, és olyan modelleket hozhat létre, amelyek olyan összetett űrlapokkal vagy űrlapokkal működnek, amelyek kulcs nélküli értékeket tartalmaznak.
  * **ASZINKRON API** Használhat aszinkron API-hívásokat a nagyméretű adatkészletek és fájlok betanításához és elemzéséhez.
  * **TIFF-fájl támogatása** Mostantól a TIFF-dokumentumokból is betaníthat és kinyerheti az adatait.
  * **A kinyerési pontosság fejlesztése**

* **Előre elkészített bevételezési modell**
  * **Tipp mennyisége** Mostantól kinyerheti a tip-mennyiségeket és az egyéb kézírásos értékeket.
  * **Vonal kibontása** A sorokból kinyerheti a tételek értékeit a nyugták közül.
  * **Megbízhatósági értékek** Megtekintheti a modell megbízhatóságát minden kinyert értékhez.
  * **A kinyerési pontosság fejlesztése**

* **Elrendezés kibontása** Mostantól az elrendezési API használatával kinyerheti a szöveges és a táblázatos adatadatokat az űrlapokból.

### <a name="custom-model-api-changes"></a>Egyéni modell API-változások

A betanításra és az egyéni modellek használatára vonatkozó összes API-t átnevezték, és néhány szinkron módszer már aszinkron módon történik. A következők jelentős változások:

* A modellek betanításának folyamata már aszinkron módon történik. A **/Custom/models** API-hívással kezdeményezheti a betanítást. Ez a hívás egy műveleti azonosítót ad vissza, amelyet az **egyéni/modellek/{ModelID}** átadhat a betanítási eredmények visszaadásához.
* A kulcs/érték kinyerését mostantól a **/Custom/models/{ModelID}/Analyze** API-hívás kezdeményezte. Ez a hívás egy műveleti azonosítót ad vissza, amelyet az **egyéni/modellek/{ModelID}/analyzeResults/{resultID}** átadhat a kinyerési eredmények visszaadásához.
* A Train művelethez tartozó műveleti azonosítók mostantól a HTTP-válaszok **hely** fejlécében találhatók, nem a **művelet – hely** fejlécben.

### <a name="receipt-api-changes"></a>Beérkezési API módosításai

Az értékesítési visszaigazolások olvasására szolgáló API-k átnevezve lettek.

* A **/prebuilt/Receipt/Analyze** API-hívás kezdeményezte a beérkezési adatbontást. Ez a hívás egy műveleti azonosítót ad vissza, amelyet átadhat a **/prebuilt/Receipt/analyzeResults/{resultID}** a kinyerési eredmények visszaadásához.

### <a name="output-format-changes"></a>Kimeneti formátum módosításai

A JSON-válaszok minden API-híváshoz új formátumok tartoznak. Egyes kulcsok és értékek hozzá lettek adva, el lettek távolítva vagy átnevezve lettek. Tekintse meg az aktuális JSON-formátumok példáit.

## <a name="next-steps"></a>Következő lépések

Fejezze be [a](quickstarts/client-library.md) gyors üzembe helyezési útmutatót, amellyel megkezdheti az űrlapok feldolgozását az űrlap-felismerővel az Ön által választott fejlesztési nyelven.

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](./overview.md)
