---
title: Mi a Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Az Azure Form Recognizer szolgáltatás lehetővé teszi kulcs/érték párok és táblaadatok azonosítását és kinyerét az űrlapdokumentumok alapján, valamint a fő információkat kinyeri az értékesítési nyugtákból és névjegykártyákból.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: automatizált adatfeldolgozás, dokumentumfeldolgozás, automatizált adatbevitel, űrlapfeldolgozás
ms.openlocfilehash: 680bb612546aaffc167970c1c48a44159ef9af6f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518226"
---
# <a name="what-is-form-recognizer"></a>Mi a Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Form Recognizer egy kognitív szolgáltatás, amellyel automatizált adatfeldolgozási szoftvereket építhet gépi tanulási technológiával. Azonosíthatja és kinyerhet szövegeket, kulcs/érték párokat, kijelölési jeleket, táblákat és struktúrát a dokumentumokból. A szolgáltatás strukturált adatokat ad vissza, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat, a határolókereteket és a &mdash; megbízhatóságot. Gyorsan pontos eredményeket kap, amelyek az adott tartalomhoz vannak szabva anélkül, hogy nagy manuális beavatkozásra vagy kiterjedt adattudományi szakértelemre volna szükség. A Form Recognizer automatizálhatja az adatbevitelt az alkalmazásokban, és gazdagíthatja a dokumentumok keresési képességeit.

Form Recognizer egyéni dokumentumfeldolgozási modellekből, a számlákhoz, nyugtákhoz, kiosztási kártyákhoz és az elrendezési modellből álló, előre összeállított modellekből áll. Az összetettség csökkentése Form Recognizer és a munkafolyamatba vagy alkalmazásba való integrálása érdekében REST API kódtár-SDK-k használatával hívhatja meg a modellmodelleket.

Ez a dokumentáció a következő cikktípusokat tartalmazza:  

* [**A rövid útmutatók**](quickstarts/client-library.md) olyan első lépések, amelyek végigvezetik a szolgáltatásnak indított kérések folyamatán.  
* [**Az útmutatók a**](build-training-data-set.md) szolgáltatás pontosabb vagy testreszabottabb módon való használatával kapcsolatos utasításokat tartalmaznak.  
* [**A fogalmak**](concept-layout.md) részletes magyarázatot nyújtanak a szolgáltatás funkcióiról és funkcióiról.  
* [**Az oktatóanyagok**](tutorial-bulk-processing.md) hosszabb útmutatók, amelyekből megtudhatja, hogyan használhatja a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.  

## <a name="form-recognizer-features"></a>Form Recognizer funkciók

A Form Recognizer az alábbi funkciókkal egyszerűen kinyerheti és elemezheti az űrlapadatokat:

* **[Layout API](#layout-api)** – Szöveget, kijelölési jeleket és táblázatstruktúrákat, valamint azok határolókeret-koordinátáit is kinyeri a dokumentumokból.
* **[Egyéni modellek](#custom-models)** – Szöveg-, kulcs/érték párok, kijelölési jelek és táblaadatok kinyerása űrlapokból. Ezek a modellek a saját adataival vannak betanítva, így az Ön űrlapjaihoz vannak szabva.

* **[Előre összeállított modellek](#prebuilt-models)** – Adatok kinyerása egyedi dokumentumtípusokból előre összeállított modellekkel. Jelenleg a következő előre összeállított modellek érhetők el

  * [Számlák](./concept-invoices.md)
  * [Értékesítési nyugták](./concept-receipts.md)
  * [Névjegykártyák](./concept-business-cards.md)
  * [Azonosítási (ID) kártyák](./concept-identification-cards.md)


## <a name="get-started"></a>Bevezetés

A Sample Form Recognizer Tool használatával kipróbálhatja az elrendezést, az előre felépített modelleket, és betaníthat egy egyéni modellt a dokumentumokhoz. Szüksége lesz egy Azure-előfizetésre (hozzon [](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) létre egyet ingyenesen), valamint egy Form Recognizer erőforrásvégpontra és kulcsra a Form Recognizer kipróbálása érdekében.[](https://azure.microsoft.com/free/cognitive-services)

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

> [!div class="nextstepaction"]
> [Próbálja Form Recognizer](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

> [!div class="nextstepaction"]
> [Próbálja Form Recognizer](https://fott.azurewebsites.net/)

---
Kövesse az [Ügyféloldali kódtár /REST API rövid útmutatót a](./quickstarts/client-library.md) dokumentumok adatainak kinyeréséhez. Javasoljuk, hogy a technológia tanulásakor használja az ingyenes szolgáltatást. Ne feledje, hogy az ingyenes oldalak száma havonta legfeljebb 500 lehet.

Az első lépésekhez a REST-mintákat (GitHub) is használhatja. 

* Szöveg, kijelölési jelek és táblastruktúra kinyerása dokumentumokból
  * [Elrendezési adatok kinyerása – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Egyéni modellek betanítása és űrlapadatok kinyere
  * [Betanítás címkék nélkül – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Betanítás címkékkel – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Adatok kinyerása számlákból
  * [Számlaadatok kinyerása – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Adatok kinyerása értékesítési nyugtákból
  * [Nyugtaadatok kinyerása – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Adatok kinyerése névjegykártyákból
  * [Névjegykártyaadatok kinyerása – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>A REST API-k áttekintése

A következő API-kat fogja használni modellek betanítása és strukturált adatok űrlapokból való kinyeréséhez.

|Név |Leírás |
|---|---|
| **Elrendezés elemzése** | Streamként átadott dokumentum elemzése szöveg, kijelölési jelek, táblák és struktúra dokumentumból való kinyeréhez |
| **Egyéni modell betanítás**| Betanít egy új modellt, hogy azonos típusú öt űrlap használatával elemezze az űrlapokat. A _useLabelFile paramétert állítsa_ a következőre: `true` manuálisan címkézett adatok betanítása. |
| **Elemzés űrlap** |Elemezhet egy streamként átadott űrlapot, amely szöveget, kulcs/érték párokat és táblákat fog kinyerni az űrlapról az egyéni modellel.  |
| **Számla elemzése** | A számla elemzésével kinyerhetők a legfontosabb információk, táblák és egyéb számlaszövegek.|
| **Nyugta elemzése** | Elemezhet egy nyugtadokumentumot a kulcsinformációk és egyéb nyugtaszöveg kinyeréhez.|
| **Azonosító elemzése** | Egy azonosítókártya-dokumentum elemzése a kulcsinformációk és az azonosítókártya egyéb szövegének kinyeréhez.|
| **Névjegykártya elemzése** | Névjegykártya elemzése a legfontosabb információk és szövegek kinyeréhez.|

### <a name="v21-preview"></a>[2.1-es verzió előzetes verziója](#tab/v2-1)

További [információért tekintse REST API referenciadokumentációját.](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) Ha már ismeri az API egy korábbi verzióját, az Újdonságok cikkben talál további információt a legutóbbi változásokról. [](./whats-new.md)

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

További [információért tekintse REST API referenciadokumentációját.](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync) Ha már ismeri az API egy korábbi verzióját, az Újdonságok cikkben talál további információt a legutóbbi változásokról. [](./whats-new.md)

---

## <a name="layout-api"></a>Layout API

Form Recognizer nagy felbontású optikai karakterfelismeréssel (OCR) és egy továbbfejlesztett mélytanulásmodellel kinyerheti a szövegeket, a kijelölési jeleket és a táblastruktúrát (a szöveghez társított sor- és oszlopszámokat). További [információért](./concept-layout.md) tekintse meg az elrendezés fogalmi útmutatóját.

:::image type="content" source="./media/tables-example.jpg" alt-text="példatáblákra" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Egyéni modellek

Form Recognizer modellek betanítása a saját adataira, és csak öt minta beviteli űrlapra van szükség a kezdéshez. A betanított dokumentumfeldolgozási modell olyan strukturált adatokat képes kihozni, amelyek tartalmazzák az eredeti űrlapdokumentumban található kapcsolatokat. A modell betanítása után tesztelheti és újra betaníthatja, és végül arra használhatja, hogy az igényeinek megfelelően megbízhatóan kinyerje az adatokat több űrlapról.

Egyéni modellek betanításakor a következő lehetőségek állnak rendelkezésre: betanítás címkével jelölt adatokkal és címkével nem jelölt adatokkal.

### <a name="train-without-labels"></a>Betanítás címkék nélkül

Form Recognizer nem felügyelet nélküli tanulással érti meg az űrlapok mezőinek és bejegyzéseinek elrendezését és kapcsolatait. A bemeneti űrlapok elküldésekor az algoritmus típus szerint csoportosítja az űrlapokat, felderíti, hogy milyen kulcsok és táblák vannak jelen, és kulcsokkal és bejegyzésekkel társítja az értékeket a táblákhoz. A címkék nélküli betanítás nem igényel manuális adatcímkézást vagy intenzív kódolást és karbantartást, ezért javasoljuk, hogy először próbálja ki ezt a módszert.

A [betanítás dokumentumainak gyűjtésével](./build-training-data-set.md) kapcsolatos tippekért lásd: Képzési adatkészlet létrehozása.

### <a name="train-with-labels"></a>Betanítás címkékkel

A címkével jelölt adatokkal való betanításkor a modell felügyelt tanulással kinyeri a hasznos értékeket az Ön által megadott címkével jelölt űrlapokkal. A címkével jelölt adatok jobb teljesítő modelleket hoznak létre, és olyan modelleket hoznak létre, amelyek összetett űrlapokkal vagy kulcs nélküli értékeket tartalmazó űrlapokkal működnek.

Form Recognizer a Layout [API](#layout-api) használatával tanulja meg a nyomtatott és kézzel írt szövegelemek várható méretét és pozícióit, és kinyeri a táblákat. Ezután felhasználó által megadott címkékkel tanulja meg a dokumentumok kulcs/érték társításait és tábláit. Javasoljuk, hogy egy új modell betanításakor használjon öt manuálisan címkével jelölt, azonos típusú (azonos szerkezetű) űrlapot az első lépésekhez, és szükség szerint adjon hozzá további címkével jelölt adatokat a modell pontosságának javításához. Form Recognizer modell betanítható a kulcs-érték párok és táblák kinyerére felügyelt tanulási képességekkel. 

[A Betanítás címkékkel – első lépések](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Előre összeállított modellek

Form Recognizer előre összeállított modelleket is tartalmaz az egyedi űrlaptípusok automatizált adatfeldolgozásához.

### <a name="prebuilt-invoice-model"></a>Előre összeállított számlamodell

Az előre összeállított számlamodell különböző formátumokban kinyeri az adatokat a számlákból, és strukturált adatokat ad vissza. Ez a modell kinyeri a legfontosabb információkat, például a számlaazonosítót, az ügyféladatokat, a szállító adatait, a kiszállító adatait, a kiszállítót, a számlázási adatokat, az összegeket, az adókat, a részösszegeket, a sorelemeket stb. Emellett az előre összeállított számlamodell úgy van betanítva, hogy elemezze és visszaadja a számlán szereplő összes szöveget és táblát. További [információért tekintse meg](./concept-invoices.md) a Számlák fogalmi útmutatóját.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="mintaszámla" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Előre összeállított nyugtamodell

Az előre összeállított nyugtamodell az Ausztrália, Kanada, Nagy-Egyesült Királyság, India és az éttermek, benzinkutak, kiskereskedelem stb. által használt Egyesült Államok angol értékesítési nyugták beolvasására &mdash; használható. Ez a modell kinyeri a kulcsfontosságú információkat, például a tranzakció időpontját és dátumát, a kereskedő adatait, az adókat, a sorelemeket, az összegeket stb. Emellett az előre összeállított nyugtamodell úgy van betanítva, hogy elemezze és visszaadja a nyugtán található összes szöveget. További [információért tekintse meg](./concept-receipts.md) a nyugták fogalmi útmutatóját.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="minta-nyugta" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Előre összeállított azonosítási (ID) kártyák modellje

Az azonosítási (ID) kártyamodell lehetővé teszi, hogy kulcsinformációkat kinyerje a világ minden tájáról származó passportból és usa-jú sofőrlicencekből. Olyan adatokat is kinyer, mint például a dokumentum azonosítója, a születési dátum, a lejárat dátuma, a név, az ország, a régió, a gép által olvasható zóna stb. További információért tekintse meg az [Azonosítási (AZONOSÍTÓ) kártyák fogalmi](./concept-identification-cards.md) útmutatóját.

:::image type="content" source="./media/overview-id.jpg" alt-text="mintaazonosító kártya" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Előre összeállított névjegykártyák modellje

A Névjegykártyák modell lehetővé teszi olyan információk kinyerét a névjegykártyákról, mint például a személy neve, beosztása, címe, e-mail-címe, vállalata és telefonszámai angol nyelven. További [információért tekintse meg a névjegykártyák](./concept-business-cards.md) fogalmi útmutatóját.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="névjegykártyaminta" lightbox="./media/overview-business-card.jpg":::

## <a name="input-requirements"></a>Bemeneti követelmények

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Helyszíni üzembe helyezés Docker-tárolók használatával

[Az Form Recognizer (előzetes verzió) használatával](form-recognizer-container-howto.md) üzembe helyezheti az API-funkciókat a helyszínen. Ez a Docker-tároló lehetővé teszi, hogy megfelelőségi, biztonsági vagy egyéb működési okokból közelebb hozza a szolgáltatást az adataihoz.

## <a name="service-availability-and-redundancy"></a>Szolgáltatás rendelkezésre állása és redundancia

### <a name="is-form-recognizer-service-zone-resilient"></a>Rugalmas Form Recognizer-e a szolgáltatászóna?

Igen. A Form Recognizer szolgáltatás alapértelmezés szerint rugalmas a zónák számára.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Hogyan a Form Recognizer szolgáltatást zónatred rugalmasra konfigurálni?

A zóna rugalmasságának engedélyezéséhez nincs szükség ügyfélkonfigurációra. Az erőforrások zónatűrése Form Recognizer alapértelmezés szerint elérhető, és maga a szolgáltatás kezeli.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Mint minden kognitív szolgáltatást, a Form Recognizer szolgáltatást használó fejlesztőknek is tisztában kell lenniük az ügyféladatokra vonatkozó Microsoft-szabályzatokkal. További Cognitive Services [a](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) Microsoft Biztonsági és biztonsági központ Cognitive Services oldalon.

## <a name="next-steps"></a>Következő lépések

Próbálja ki online eszközünket, és rövid útmutatónk segítségével többet is megtudhat az Form Recognizer szolgáltatásról.

* [**Form Recognizer eszköz**](https://fott-preview.azurewebsites.net/)
* [**Ügyféloldali kódtár és REST API rövid útmutató**](quickstarts/client-library.md)
