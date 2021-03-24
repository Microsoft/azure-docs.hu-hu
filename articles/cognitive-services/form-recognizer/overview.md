---
title: Mi a Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Az Azure Form-felismerő szolgáltatás lehetővé teszi a kulcs/érték párok és a táblák adatainak azonosítását és kinyerését az űrlapos dokumentumokból, valamint a főbb információk kinyerését az értékesítési befizetések és a névjegykártyák alapján.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: automatizált adatfeldolgozás, dokumentumok feldolgozása, automatizált adatbevitel, űrlapok feldolgozása
ms.openlocfilehash: 4465f88e3b0ccab8eace1936f426af8dd32af27b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104872251"
---
# <a name="what-is-form-recognizer"></a>Mi a Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Form-felismerő egy olyan kognitív szolgáltatás, amely lehetővé teszi a gépi tanulási technológia használatával automatizált adatfeldolgozási szoftverek készítését. Szöveg, kulcs/érték párok, kiválasztási jelek, táblák és struktúra azonosítása a dokumentumokból &mdash; a szolgáltatás olyan strukturált adatokat tartalmaz, amelyek tartalmazzák az eredeti fájlban, a határolókeretban, a megbízhatóságban és egyebekben lévő kapcsolatokat. Az adott tartalomra szabott pontos eredményeket gyorsan, nehéz manuális beavatkozás vagy kiterjedt adatelemzési szakértelem nélkül érheti el. Az űrlap-felismerő segítségével automatizálhatja az alkalmazásaiban lévő adatbevitelt, és bővítheti a dokumentumok keresési funkcióit.

Az űrlap-felismerő egyéni dokumentum-feldolgozási modellekből, előre elkészített modellből áll, számlákhoz, bevételezésekhez, azonosítóhoz és üzleti kártyákhoz, valamint az elrendezési modellhez. Az űrlap-felismerő modelleket meghívhatja egy REST API-vagy ügyféloldali függvénytár-SDK használatával, így csökkentheti a bonyolultságot, és integrálhatja azt a munkafolyamatba vagy alkalmazásba.

Ez a dokumentáció a következő cikk-típusokat tartalmazza:  

* [**A gyors**](quickstarts/client-library.md) üzembe helyezési útmutató végigvezeti Önt a szolgáltatásra irányuló kérések lépésein.  
* A [**útmutatók**](build-training-data-set.md) útmutatói a szolgáltatás részletesebb vagy testreszabott módokon történő használatára vonatkozó utasításokat tartalmaznak.  
* A [**fogalmak**](concept-layout.md) részletesen ismertetik a szolgáltatás funkcióit és funkcióit.  
* Az [**oktatóanyagok**](tutorial-bulk-processing.md) már olyan útmutatók, amelyek bemutatják, hogyan használhatja a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.  

## <a name="form-recognizer-features"></a>Űrlap-felismerő funkciók

Az űrlap-felismerő használatával könnyedén kinyerheti és elemezheti az űrlapadatokat az alábbi funkciókkal:

* **[Layout API](#layout-api)** – szöveg-, kiválasztási és táblázatos struktúrák kinyerése, valamint a hozzájuk tartozó mezők koordinátái a dokumentumokból.
* **[Egyéni modellek](#custom-models)** – szöveg, kulcs/érték párok, kiválasztási jelek és táblák adatainak kinyerése űrlapokból. Ezek a modellek a saját adataival vannak betanítva, így az űrlapokra vannak szabva.

* Előre **[elkészített modellek](#prebuilt-models)** – az egyedi dokumentumtípusok adatainak kinyerése előre elkészített modellek használatával. Jelenleg a következő előre elkészített modellek érhetők el

  * [Számlák](./concept-invoices.md)
  * [Értékesítési nyugták](./concept-receipts.md)
  * [Névjegykártyák](./concept-business-cards.md)
  * [Azonosító kártyák](./concept-identification-cards.md)


## <a name="get-started"></a>Bevezetés

A minta űrlap-felismerő eszköz használatával kipróbálhatja az elrendezést, az előre elkészített modelleket, és betaníthatja a dokumentumaihoz tartozó egyéni modellt. Az űrlap-felismerő szolgáltatás kipróbálásához szüksége lesz egy Azure-előfizetésre ([**hozzon létre egyet ingyen**](https://azure.microsoft.com/free/cognitive-services)) és egy [**űrlap-felismerő erőforrás**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) -végpontot és kulcsot.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

> [!div class="nextstepaction"]
> [Az űrlap-felismerő kipróbálása](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

> [!div class="nextstepaction"]
> [Az űrlap-felismerő kipróbálása](https://fott.azurewebsites.net/)

---
A dokumentumokból származó adatok kinyerésének megkezdéséhez kövesse az [ügyféloldali kódtár/REST API](./quickstarts/client-library.md) rövid útmutatóját. Javasoljuk, hogy az ingyenes szolgáltatást használja a technológia megismerése során. Ne feledje, hogy a szabad lapok száma legfeljebb 500 havonta.

Az első lépésekhez használhatja a REST-mintákat (GitHub) is 

* Szöveg, kiválasztási jelek és tábla szerkezetének kinyerése dokumentumokból
  * [Elrendezési adatok kinyerése – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Egyéni modellek betanítása és űrlapadatok kinyerése
  * [Betanítás címkék nélkül – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Betanítás címkékkel – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Adatok kinyerése a számlákból
  * [Számlázási adatok kinyerése – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Adatok kinyerése az értékesítési visszaigazolásokból
  * [Bevételezési adatok kinyerése – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Adatok kinyerése névjegykártyákból
  * [Névjegykártya-adatok kinyerése – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>A REST API-k áttekintése

A következő API-kat fogja használni a modellek betanításához és a strukturált adatok űrlapokból való kinyeréséhez.

|Név |Leírás |
|---|---|
| **Elrendezés elemzése** | Az átadott dokumentum elemzése a szöveg, a kiválasztási jelek, a táblák és a struktúra kinyeréséhez a dokumentumból |
| **Egyéni modell betanítása**| Egy új modell betanításával elemezheti az űrlapokat öt azonos típusú űrlap használatával. A _useLabelFile_ paramétert beállíthatja úgy, hogy a `true` manuálisan címkézett adatként legyen betanítva. |
| **Űrlap elemzése** |Egy adatfolyamként átadott űrlap elemzése szöveg, kulcs/érték párok és táblák az egyéni modellel való kinyeréséhez.  |
| **Számla elemzése** | Egy számla elemzése a legfontosabb információk, táblák és egyéb számlázási szövegek kinyeréséhez.|
| **Visszaigazolás elemzése** | Egy bevételezési dokumentum elemzése a legfontosabb információk kinyeréséhez és a más bevételezési szövegekhez.|
| **Elemzési azonosító** | Egy azonosító kártya dokumentumának elemzése a legfontosabb információk kinyeréséhez és más azonosító kártya szövegéhez.|
| **Névjegykártya elemzése** | Egy névjegykártya elemzése a legfontosabb információk és szövegek kinyeréséhez.|

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

További információért olvassa el a [REST API dokumentációját](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) . Ha már ismeri az API korábbi verzióját, tekintse [meg](./whats-new.md) a újdonságokról szóló cikket a legutóbbi változások megismeréséhez.

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

További információért olvassa el a [REST API dokumentációját](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) . Ha már ismeri az API korábbi verzióját, tekintse [meg](./whats-new.md) a újdonságokról szóló cikket a legutóbbi változások megismeréséhez.

---

## <a name="layout-api"></a>Elrendezési API

Az űrlap-felismerő kinyerheti a szöveg, a kiválasztási jelek és a tábla szerkezetét (a szöveghez társított sorok és oszlopok számát) a nagy felbontású optikai karakterfelismerés (OCR) és a dokumentumokból kibővített mély tanulási modell használatával. További információért tekintse meg az [elrendezés](./concept-layout.md) fogalmi útmutatóját.

:::image type="content" source="./media/tables-example.jpg" alt-text="táblák – példa" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Egyéni modellek

Az űrlap-felismerő egyéni modelljeinek a saját adataihoz kell betanítania, és a kezdéshez csak öt minta bemeneti űrlapra van szükség. A betanított dokumentum-feldolgozási modell olyan strukturált adatokat tud kialakítani, amelyek tartalmazzák az eredeti dokumentum kapcsolatait. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

Az egyéni modellek betanításakor a következő lehetőségek állnak rendelkezésére: képzés címkézett és címkézett adatbevitel nélkül.

### <a name="train-without-labels"></a>Betanítás címkék nélkül

Az űrlap-felismerő nem felügyelt tanulási lehetőséget használ az űrlapok mezőinek és bejegyzéseinek elrendezésére és kapcsolatainak megismerésére. Ha beküldi a bemeneti űrlapokat, az algoritmus a következő típus szerint állítja be az űrlapokat, felfedi a kulcsokat és táblákat, és értékeket társít a kulcsokhoz és bejegyzésekhez a táblákhoz. A címkék nélküli képzések nem igénylik a kézi adatcímkézést vagy az intenzív kódolást és karbantartást, és azt javasoljuk, hogy először próbálja meg ezt a módszert.

A képzési dokumentumok gyűjtésével kapcsolatos tippekért tekintse meg a [Training-adatkészlet](./build-training-data-set.md) létrehozása című témakört.

### <a name="train-with-labels"></a>Tanítás címkékkel

Ha címkével ellátott adatokkal látja el a képzést, a modell felügyelt tanulással Kinyeri a kamatot, és az Ön által megadott címkézett űrlapokat használja. A címkézett adatok jobb teljesítményű modelleket eredményeznek, és olyan modelleket hozhatnak létre, amelyek olyan összetett űrlapokkal vagy űrlapokkal működnek, amelyek kulcs nélküli értékeket tartalmaznak.

Az űrlap-felismerő az [elrendezési API](#layout-api) -val megtanulja a nyomtatott és a kézírásos szöveges elemek és a kinyerési táblázatok várt méretét és elhelyezkedését. Ezután a felhasználó által megadott címkéket használja a dokumentumok kulcs/érték társítások és táblák megismeréséhez. Azt javasoljuk, hogy az új modell betanítása és a modell pontosságának javítása érdekében használjon öt, azonos típusú manuálisan címkézett űrlapot (azonos struktúrát). Az űrlap-felismerő lehetővé teszi a modell képzését a kulcs érték párok és táblák kinyeréséhez a felügyelt tanulási funkciók használatával. 

[Ismerkedés a vonattal címkékkel](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Előre összeállított modellek

Az űrlap-felismerő olyan előre elkészített modelleket is tartalmaz, amelyek egyedi típusú adatfeldolgozást tartalmaznak.

### <a name="prebuilt-invoice-model"></a>Előre elkészített számla modellje

Az előre elkészített számla modell a különböző formátumú számlákból származó adatok kinyerése és a strukturált adatok visszaadása. Ez a modell Kinyeri a legfontosabb adatokat, például a számla AZONOSÍTÓját, az ügyfél adatait, a szállító adatait, a szállítást, a számlázást, a teljes, az adó, a részösszeg és a sorok számát. Emellett az előre elkészített számlázási modell úgy van betanítva, hogy elemezze és visszaállítsa a számla összes szövegét és tábláját. További információért tekintse meg a [számlák](./concept-invoices.md) fogalmi útmutatóját.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="minta számla" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Előre elkészített bevételezési modell

Az előkészített beérkezési modell az ausztrál, Kanada, Nagy-Britannia, India, valamint Egyesült Államok az &mdash; éttermek, a benzinkutak, a kereskedelmi és egyéb szolgáltatások által használt típus angol értékesítési nyugtáinak olvasására használatos. Ez a modell Kinyeri a legfontosabb adatokat, például a tranzakció dátumát és időpontját, a kereskedelmi adatokat, az adók mennyiségét, a sorok számát, az összesítéseket és egyebeket. Emellett az előre elkészített beérkezési modell úgy van betanítva, hogy elemezze és visszaállítsa az összes szöveget a nyugtán. További információért tekintse meg a [nyugták](./concept-receipts.md) fogalmi útmutatóját.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="minta nyugtája" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Előre elkészített azonosító (ID) kártyák modellje

Az azonosító kártyák modellje lehetővé teszi a legfontosabb információk kinyerését a globális Passport és az USA-beli illesztőprogram-licencből. Az adatok kinyerése, például a dokumentum azonosítója, a születési idő, a lejárat dátuma, a név, az ország, a régió, a géppel olvasható zóna stb. További információért tekintse meg az [azonosító (ID) Cards](./concept-identification-cards.md) fogalmi útmutatóját.

:::image type="content" source="./media/overview-id.jpg" alt-text="minta azonosító kártya" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Előre elkészített névjegykártya-modell

A Business Cards modell lehetővé teszi olyan információk kinyerését, mint például a személy neve, a beosztás, a cím, az e-mail, a vállalat és a telefonszámok az üzleti kártyáról angol nyelven. További információért lásd a [Business Cards](./concept-business-cards.md) fogalmi útmutatóját.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="névjegykártya minta" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Bemeneti követelmények

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Helyszíni üzembe helyezés Docker-tárolók használatával

A helyszíni API-funkciók üzembe helyezéséhez [használjon űrlap-felismerő tárolókat (előzetes verzió)](form-recognizer-container-howto.md) . Ez a Docker-tároló lehetővé teszi, hogy a szolgáltatás a megfelelőségi, biztonsági vagy egyéb működési okokból közelebb kerüljön az adataihoz.

## <a name="service-availability-and-redundancy"></a>Szolgáltatás rendelkezésre állása és redundancia

### <a name="is-form-recognizer-service-zone-resilient"></a>Az űrlap-felismerő szolgáltatás zónája – rugalmas?

Igen. Az űrlap-felismerő szolgáltatás alapértelmezés szerint zóna-rugalmas.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Hogyan konfigurálja az űrlap-felismerő szolgáltatást zónákra rugalmasan?

A zóna rugalmasságának engedélyezéséhez nincs szükség ügyfél-konfigurációra. Zóna – az űrlap-felismerő erőforrások rugalmassága alapértelmezés szerint elérhető, és maga a szolgáltatás kezeli.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes kognitív szolgáltatás esetében, az űrlap-felismerő szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft-szabályzatokat az ügyféladatok alapján. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

Próbálja ki az online eszközt és a gyors üzembe helyezést, és ismerkedjen meg az űrlap-felismerő szolgáltatással.

* [**Űrlap-felismerő eszköz**](https://fott-preview.microsoft.com/)
* [**Ügyféloldali kódtár és REST API rövid útmutató**](quickstarts/client-library.md)
