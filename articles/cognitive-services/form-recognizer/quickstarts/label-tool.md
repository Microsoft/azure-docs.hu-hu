---
title: 'Rövid útmutató: űrlapok címkézése, modell betanítása és űrlapok elemzése a minta feliratozási eszköz – űrlap felismerő használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az űrlap-felismerő minta címkézési eszköz használatával manuálisan címkézheti az űrlapos dokumentumokat. Ezután betanít egy egyéni dokumentum-feldolgozási modellt a címkével ellátott dokumentumokkal, és a modell használatával kinyerheti a kulcs/érték párokat.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: dokumentumok feldolgozása
ms.openlocfilehash: 3814eb9d71f38406533d0bcecf594bbdcd42d5b7
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095797"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Űrlap-felismerő modell betanítása címkékkel a minta feliratozási eszköz használatával

Ebben a rövid útmutatóban az űrlap-felismerő REST APIt fogja használni a minta feliratozási eszközzel, amely manuálisan címkézett adatfeldolgozási modellt alkalmaz egy egyéni dokumentum-feldolgozási modell betanításához. Tekintse meg az áttekintés című témakör [címkék](../overview.md#train-with-labels) című szakaszát, és ismerkedjen meg a felügyelt tanulással az űrlap-felismerővel.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőket kell tennie:

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hozzon létre egy űrlap-felismerő erőforrást "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Az Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
  * Az alkalmazás az űrlap-felismerő API-hoz való összekapcsolásához szüksége lesz a létrehozott erőforrás kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
  * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* Legalább hat egyforma típusú formátumból álló készlet. Ezeket az adattípusokat fogja használni a modell betanításához és egy űrlap teszteléséhez. Ehhez a rövid útmutatóhoz használhat egy [minta adatkészletet](https://go.microsoft.com/fwlink/?linkid=2090451) ( *sample_data.zip* letöltése és kibontása). Töltse fel a betanítási fájlokat egy blob Storage-tároló gyökerébe egy standard teljesítményű Azure Storage-fiókban.

## <a name="create-a-form-recognizer-resource"></a>Űrlap-felismerő erőforrás létrehozása

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="try-it-out"></a>Próbálja ki

Az űrlap-felismerő minta feliratozási eszközének online kipróbálásához lépjen a [FOTT webhelyére](https://fott-preview.azurewebsites.net/).

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

> [!div class="nextstepaction"]
> [Előre elkészített modellek kipróbálása](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

> [!div class="nextstepaction"]
> [Előre elkészített modellek kipróbálása](https://fott.azurewebsites.net/)

---

Az űrlap-felismerő szolgáltatás kipróbálásához szüksége lesz egy Azure-előfizetésre ([hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)) és egy [űrlap-felismerő erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) -végpontot és kulcsot.

## <a name="set-up-the-sample-labeling-tool"></a>A minta feliratozási eszköz beállítása

A minta címkéző eszköz futtatásához a Docker-motort fogja használni. A Docker-tároló beállításához kövesse az alábbi lépéseket. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).

> [!TIP]
> Az OCR űrlap címkéző eszköze nyílt forráskódú projektként is elérhető a GitHubon. Az eszköz a reakciós + Redux használatával létrehozott, írógéppel használható webalkalmazás. További információért vagy a közreműködés megismeréséhez tekintse meg az [OCR űrlap feliratozási eszközének](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application) tárházát. Az eszköz online kipróbálásához lépjen a [FOTT webhelyére](https://fott.azurewebsites.net/).

1. Először telepítse a Docker-t egy gazdagépre. Ez az útmutató bemutatja, hogyan használható a helyi számítógép gazdagépként. Ha Docker-üzemeltetési szolgáltatást szeretne használni az Azure-ban, tekintse meg a [minta címkézési eszköz üzembe helyezése](../deploy-label-tool.md) útmutató című témakört.

   A gazdagépnek meg kell felelnie a következő hardverkövetelmények követelményeinek:

    | Tároló | Minimális | Ajánlott|
    |:--|:--|:--|
    |Minta címkéző eszköz|2 mag, 4 GB memória|4 mag, 8 GB memória|

   Telepítse a Docker-t a gépre az operációs rendszerének megfelelő utasítások követésével:

   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Szerezze be a minta címkéző eszköz tárolóját a `docker pull` paranccsal.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```console
 docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```console
docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
```

---
</br>
  3. Most már készen áll a tároló futtatására a használatával `docker run` .

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

```console
 docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview eula=accept
```

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

```console
docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
```

---

   Ezzel a paranccsal a minta feliratozási eszköz elérhetővé válik egy webböngészőn keresztül. Nyissa meg a következőt: `http://localhost:3000`.

> [!NOTE]
> A dokumentumokat és a betanítási modelleket a Form felismerő REST API használatával is címkézheti. A REST API betanításához és elemzéséhez lásd: [a betanítás a REST API és a Python használatával](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="set-up-input-data"></a>Bemeneti adatok beállítása

Először győződjön meg arról, hogy az összes betanítási dokumentum formátuma azonos. Ha űrlapjai többféle formátumban vannak, rendezze őket almappákba formátum szerint. A betanítás során egy almappára kell hivatkoznia az API-nak.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Tartományok közötti erőforrás-megosztás (CORS) konfigurálása

Engedélyezze a CORS a Storage-fiókban. Válassza ki a Storage-fiókját a Azure Portalban, és kattintson a bal oldali ablaktábla **CORS** fülére. Az alsó sorban adja meg a következő értékeket. Ezután kattintson a felső **Mentés** gombra.

* Engedélyezett Origins = *
* Engedélyezett metódusok = \[ összes kijelölése\]
* Engedélyezett fejlécek = *
* Elérhető fejlécek = *
* Max Age = 200

> [!div class="mx-imgBorder"]
> ![CORS-telepítő a Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Kapcsolódás a minta feliratozási eszközhöz

A minta feliratozási eszköz egy forráshoz (az eredeti űrlapokhoz) és egy célhoz (ahol a létrehozott címkék és a kimeneti adatokat exportálja) csatlakozik.

A kapcsolatok beállítható és megoszthatók a projektek között. Egy bővíthető szolgáltatói modellt használnak, így egyszerűen hozzáadhat új forrás-és célkiszolgáló-szolgáltatókat.

Új kapcsolat létrehozásához kattintson az **új kapcsolatok** (beépülő) ikonra a bal oldali navigációs sávon.

Töltse ki a mezőket a következő értékekkel:

* **Megjelenítendő név** – a kapcsolatok megjelenítendő neve.
* **Leírás** – a projekt leírása.
* **Sas URL-cím** – az Azure Blob Storage tároló megosztott hozzáférés-aláírási (SAS) URL-címe. [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="SAS URL-cím lekérése":::

:::image type="content" source="../media/label-tool/connections.png" alt-text="A mintául szolgáló címkéző eszköz csatlakoztatási beállításai.":::


## <a name="create-a-new-project"></a>Új projekt létrehozása

A minta feliratozási eszközben a projektek a konfigurációkat és a beállításokat tárolják. Hozzon létre egy új projektet, és töltse ki a mezőket a következő értékekkel:

* **Megjelenítendő név** – a projekt megjelenítendő neve
* **Biztonsági jogkivonat** – egyes Project-beállítások bizalmas értékeket is tartalmazhatnak, például API-kulcsokat vagy más közös titkokat. Minden projekt egy biztonsági jogkivonatot állít elő, amely a bizalmas projektek beállításainak titkosítására és visszafejtésére használható. A biztonsági jogkivonatokat a bal oldali navigációs sáv alján található fogaskerék ikonra kattintva tekintheti meg az Alkalmazásbeállítások között.
* **Forrásoldali kapcsolódás** – a projekthez használni kívánt előző lépésben létrehozott Azure Blob Storage-kapcsolódás.
* **Mappa elérési útja** – nem kötelező – ha a forrás űrlapjai a blob tároló egyik mappájában találhatók, itt adja meg a mappa nevét.
* **Űrlap-felismerő szolgáltatás URI-ja** – az űrlap-felismerő végpontjának URL-címe.
* **API-kulcs** – az űrlap-felismerő előfizetési kulcsa.
* **Leírás** – nem kötelező – a projekt leírása

:::image type="content" source="../media/label-tool/new-project.png" alt-text="Új projekt lap a minta feliratozási eszközön.":::

## <a name="label-your-forms"></a>Űrlapok címkézése

Amikor létrehoz vagy megnyit egy projektet, megnyílik a fő címke-szerkesztő ablak. A címke szerkesztője három részből áll:

* Egy átméretezhető betekintő ablaktábla, amely a forrás-és az űrlapok görgethető listáját tartalmazza.
* A főszerkesztő ablaktábla, amely lehetővé teszi a címkék alkalmazását.
* A címkék szerkesztő panelje lehetővé teszi a felhasználók számára címkék módosítását, zárolását, átrendezését és törlését.

### <a name="identify-text-elements"></a>Szöveges elemek azonosítása

Kattintson az OCR futtatása elemre a bal oldali ablaktábla **összes fájlján** az egyes dokumentumok szöveg-elrendezési adatainak lekéréséhez. A címkézési eszköz az egyes szöveges elemek köré rajzolja meg a határoló mezőket.

Azt is megmutatja, hogy mely táblákat kell automatikusan kibontani. A kibontott táblázat megjelenítéséhez kattintson a dokumentum bal oldalán található tábla/rács ikonra. Ebben a rövid útmutatóban, mivel a táblázat tartalma automatikusan ki van kibontva, a táblázat tartalma nem lesz felcímkézve, hanem az automatikus kivonásra támaszkodik.

:::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Táblázat vizualizációja a minta címkézési eszközben.":::

### <a name="apply-labels-to-text"></a>Feliratok alkalmazása szövegre

Ezután létre kell hoznia címkéket (címkéket), és alkalmaznia kell azokat a szöveges elemekre, amelyeket elemezni kíván a modellben.

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

1. Először a címkék szerkesztő paneljén hozza létre a azonosítani kívánt címkéket:
   * Kattintson ide **+** új címke létrehozásához.
   * Adja meg a címke nevét.
   * Nyomja le az ENTER billentyűt a címke mentéséhez.
1. A főszerkesztőben kattintson ide a Kiemelt szöveges elemek szavainak kiválasztásához. A _v 2.1 preview. 2_ API-ban a _kiválasztási jelek_ , például a választógombok és a jelölőnégyzetek megadására is lehetőség van. Az űrlap-felismerő azonosítja, hogy a kijelölési jel "kijelölt" vagy "nem kijelölt" értékű-e.
1. Kattintson az alkalmazni kívánt címkére, vagy nyomja le a megfelelő billentyűt. A kulcsok az első 10 címkéhez gyorsbillentyűként vannak hozzárendelve. A címkéket átrendezheti a címke-szerkesztő ablaktábla fel és le nyíl ikonjának használatával.
    > [!Tip]
    > Az űrlapok címkézése során tartsa szem előtt az alábbi tippeket:
    >
    > * Csak egy címkét alkalmazhat az egyes kijelölt szöveges elemekre.
    > * Az egyes címkék csak egyszer alkalmazhatók oldalanként. Ha egy érték többször is megjelenik ugyanazon az űrlapon, hozzon létre különböző címkéket az egyes példányokhoz. Például: "számla # 1", "számla # 2" és így tovább.
    > * A címkék nem terjedhetnek át a lapokra.
    > * Az űrlapon megjelenő címkézett értékek ne próbáljon két részre osztani egy értéket két különböző címkével. Például egy cím mezőt egyetlen címkével kell megcímkézni, még akkor is, ha több sort is felölel.
    > * A címkézett mezőkben ne szerepeljenek kulcsok, &mdash; csak az értékek.
    > * A tábla adatokat automatikusan kell észlelni, és a végső kimeneti JSON-fájlban lesznek elérhetők. Ha azonban a modell nem ismeri fel az összes tábla adatait, manuálisan is címkézheti ezeket a mezőket. Címkézze fel a tábla minden celláját egy másik címkével. Ha az űrlapok különböző számú sort tartalmazó táblázatokkal rendelkeznek, ügyeljen arra, hogy legalább egy űrlapot címkével lássa el a lehető legnagyobb táblázattal.
    > * A **+** címkék kereséséhez, átnevezéséhez, átrendezéséhez és törléséhez kattintson a jobb oldalon található gombokra.
    > * Ha el szeretné távolítani egy alkalmazott címkét a címke törlése nélkül, válassza ki a címkézett téglalapot a dokumentum nézetben, és nyomja le a DELETE billentyűt.
    >

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

1. Először a címkék szerkesztő paneljén hozza létre az azonosítani kívánt címkéket.
   1. Kattintson ide **+** új címke létrehozásához.
   1. Adja meg a címke nevét.
   1. Nyomja le az ENTER billentyűt a címke mentéséhez.
1. A főszerkesztőben kattintson ide a Kiemelt szöveges elemek szavainak kiválasztásához.
1. Kattintson az alkalmazni kívánt címkére, vagy nyomja le a megfelelő billentyűt. A kulcsok az első 10 címkéhez gyorsbillentyűként vannak hozzárendelve. A címkéket átrendezheti a címke-szerkesztő ablaktábla fel és le nyíl ikonjának használatával.
    > [!Tip]
    > Az űrlapok címkézése során tartsa szem előtt az alábbi tippeket:
    >
    > * Csak egy címkét alkalmazhat az egyes kijelölt szöveges elemekre.
    > * Az egyes címkék csak egyszer alkalmazhatók oldalanként. Ha egy érték többször is megjelenik ugyanazon az űrlapon, hozzon létre különböző címkéket az egyes példányokhoz. Például: "számla # 1", "számla # 2" és így tovább.
    > * A címkék nem terjedhetnek át a lapokra.
    > * Az űrlapon megjelenő címkézett értékek ne próbáljon két részre osztani egy értéket két különböző címkével. Például egy cím mezőt egyetlen címkével kell megcímkézni, még akkor is, ha több sort is felölel.
    > * A címkézett mezőkben ne szerepeljenek kulcsok, &mdash; csak az értékek.
    > * A tábla adatokat automatikusan kell észlelni, és a végső kimeneti JSON-fájlban lesznek elérhetők. Ha azonban a modell nem ismeri fel az összes tábla adatait, manuálisan is címkézheti ezeket a mezőket. Címkézze fel a tábla minden celláját egy másik címkével. Ha az űrlapok különböző számú sort tartalmazó táblázatokkal rendelkeznek, ügyeljen arra, hogy legalább egy űrlapot címkével lássa el a lehető legnagyobb táblázattal.
    > * A **+** címkék kereséséhez, átnevezéséhez, átrendezéséhez és törléséhez kattintson a jobb oldalon található gombokra.
    > * Ha el szeretné távolítani egy alkalmazott címkét a címke törlése nélkül, válassza ki a címkézett téglalapot a dokumentum nézetben, és nyomja le a DELETE billentyűt.
>

---

:::image type="content" source="../media/label-tool/main-editor-2-1.png" alt-text="A minta-címkéző eszköz főszerkesztő ablaka.":::

Kövesse a fenti lépéseket az űrlapok legalább öt megjelöléséhez.

### <a name="specify-tag-value-types"></a>Címke típusú értékek megadása

Opcionálisan megadhatja az egyes címkék várt adattípusát. Nyissa meg a címke jobb oldalán a helyi menüt, és válasszon egy típust a menüből. Ez a funkció lehetővé teszi, hogy az észlelési algoritmus bizonyos feltételezéseket tegyen elérhetővé, amelyek javítják a szöveg-észlelési pontosságot. Azt is biztosítja, hogy a rendszer az észlelt értékeket a végső JSON-kimenet szabványosított formátumában adja vissza. Az érték típusának adatait a rendszer a (z) fájlban lévő *fields.js* menti, a címkézett fájlokkal megegyező elérési úton.

> [!div class="mx-imgBorder"]
> ![Értéktípus kiválasztása a minta címkézési eszközzel](../media/whats-new/value-type.png)

A következő típusú értékek és változatok jelenleg támogatottak:

* `string`
  * alapértelmezett, `no-whitespaces` , `alphanumeric`

* `number`
  * alapértelmezett `currency`

* `date`
  * alapértelmezett, `dmy` , `mdy` , `ymd`

* `time`
* `integer`
* `selectionMark` – _Újdonság a v 2.1-ben – előzetes verzió. 1!_

> [!NOTE]
> A dátum formázásához tekintse meg a következő szabályokat:
>
> Meg kell adnia egy formátumot ( `dmy` , `mdy` , `ymd` ) a dátum formázásához.
>
> A következő karakterek használhatók dátum határolójelként: `, - / . \` . Szóközök nem használhatók határolójelként. Például:
>
> * 01, 01, 2020
> * 01-01-2020
> * 01/01/2020
>
> A nap és a hónap egy vagy két számjegyből állhat, az év pedig két vagy négy számjegyből állhat:
>
> * 1-1-2020
> * 1-01-20
>
> Ha egy dátum sztring nyolc számjegyből áll, a határolójel nem kötelező:
>
> * 01012020
> * 01 01 2020
>
> A hónap teljes vagy rövid neveként is megadható. Ha a név használatos, a határoló karakterek nem kötelezőek. Ez a formátum azonban a többinél kevésbé pontosan felismerhető.
>
> * 01/Jan/2020
> * 01Jan2020
> * 01 Jan 2020

## <a name="train-a-custom-model"></a>Egyéni modell betanítása

Kattintson a vonat ikonra a bal oldali ablaktáblán a képzés lap megnyitásához. Ezután kattintson a **vonat** gombra a modell tanításának megkezdéséhez. A betanítási folyamat befejezése után a következő információk láthatók:

* **Modell azonosítója** – a létrehozott és betanított modell azonosítója. Minden betanítási hívás létrehoz egy új modellt a saját azonosítójával. A karakterlánc másolása biztonságos helyre; szüksége lesz rá, ha az [REST API](./client-library.md?pivots=programming-language-rest-api) vagy az [ügyféloldali függvénytáron](./client-library.md)keresztül szeretne előrejelzési hívásokat végezni.
* **Átlagos pontosság** – a modell átlagos pontossága. A modell pontosságát úgy javíthatja, ha további űrlapokat és képzést is felcímkéz, és új modellt hoz létre. Javasoljuk, hogy öt űrlap feliratozásával kezdjen hozzá, és szükség esetén további űrlapokat adjon hozzá.
* A címkék és a becsült pontosság a címkén.


:::image type="content" source="../media/label-tool/train-screen.png" alt-text="Képzés nézet.":::

A betanítás befejezése után vizsgálja meg az **átlagos pontossági** értéket. Ha alacsony, adjon hozzá további bemeneti dokumentumokat, és ismételje meg a fenti lépéseket. A már címkézett dokumentumok a projekt indexében maradnak.

> [!TIP]
> A betanítási folyamatot REST API hívással is futtathatja. Ennek megismeréséhez tekintse meg a [címkék a Python használatával történő betanítását](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)ismertető témakört.

## <a name="compose-trained-models"></a>Betanított modellek összeállítása

### <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)

A Model Compose segítségével akár 100 modellt is létrehozhat egyetlen modellazonosítóhoz. Ha ezzel a létrehozott modellazonosítóval hívja meg az Elemzés funkciót, a Form Recognizer először besorolja a beküldött űrlapot, párosítja a neki leginkább megfelelő modellel, majd az adott modell eredményeit adja vissza. Ez akkor hasznos, ha a beérkező űrlapok számos sablon egyikéhez tartozhatnak.

A minta feliratozási eszköz modelljeinek összeállításához kattintson a bal oldalon található Model levélírás (egyesítési nyíl) ikonra. A bal oldalon válassza ki azokat a modelleket, amelyeket össze szeretne állítani. A Arrows ikonnal rendelkező modellek már modellekből állnak.
Kattintson a "levélírás" gombra. Az előugró ablakban nevezze el az új komponált modellt, majd kattintson a "levélírás" elemre. Ha a művelet befejeződik, az új komponált modellnek szerepelnie kell a listában.

:::image type="content" source="../media/label-tool/model-compose.png" alt-text="Model komponált UX nézet.":::

### <a name="v20"></a>[2.0-s verzió](#tab/v2-0)

Ez a funkció jelenleg a 2.1-es verzióban érhető el. előnézet.

---

## <a name="analyze-a-form"></a>Űrlap elemzése

Kattintson a bal oldali előrejelzés (villanykörte) ikonra a modell teszteléséhez. Töltse fel a betanítási folyamatban még nem használt űrlap-dokumentumot. Ezután kattintson a jobb oldali **Előrejelzés** gombra az űrlaphoz tartozó kulcs/érték előrejelzések beszerzéséhez. Az eszköz címkét fog alkalmazni a határolókeret mezőiben, és az egyes címkék megbízhatóságát fogja jelenteni.

> [!TIP]
> Az elemzés API-t REST-hívással is futtathatja. Ennek megismeréséhez tekintse meg a [címkék a Python használatával történő betanítását](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)ismertető témakört.

## <a name="improve-results"></a>Az eredmények javítása

A jelentett pontosságtól függően érdemes lehet további képzést végezni a modell fejlesztéséhez. Miután elvégezte az előrejelzést, vizsgálja meg az egyes alkalmazott címkék megbízhatósági értékeit. Ha az átlagos pontossági érték magas volt, de a megbízhatósági pontszámok alacsonyak (vagy az eredmények pontatlanok), adja hozzá az előrejelzéshez használt fájlt a betanítási készlethez, címkézze fel, és ismételje meg a betanítást.

A jelentett átlagos pontosság, a megbízhatósági pontszám és a tényleges pontosság inkonzisztens lehet, ha az elemzett dokumentumok eltérnek a betanításban használt adatoktól. Ne feledje, hogy egyes dokumentumok ugyanúgy néznek ki, mint a felhasználók, de az AI-modellre is kitűnnek. Előfordulhat például, hogy a betanítás két változattal rendelkezik, ahol a betanítási készlet 20%-os és 80%-os változatot tartalmaz. Az előrejelzés során az A variációs dokumentumok megbízhatósági pontszámai valószínűleg alacsonyabbak lesznek.

## <a name="save-a-project-and-resume-later"></a>Projekt mentése és későbbi folytatás

Ha a projektet egy másik időpontban vagy egy másik böngészőben szeretné folytatni, mentse a projekt biztonsági jogkivonatát, és később adja meg újra.

### <a name="get-project-credentials"></a>Projekt hitelesítő adatainak beolvasása

Lépjen a Project Settings (csúszka ikon) lapra, és jegyezze fel a biztonsági jogkivonat nevét. Ezután nyissa meg az alkalmazás beállításait (fogaskerék ikon), amely megjeleníti az aktuális böngésző-példány összes biztonsági jogkivonatát. Keresse meg a projekt biztonsági jogkivonatát, és másolja a nevét és a kulcs értékét egy biztonságos helyre.

### <a name="restore-project-credentials"></a>A projekt hitelesítő adatainak visszaállítása

Ha folytatni szeretné a projekt folytatását, először létre kell hoznia egy kapcsolódást ugyanahhoz a blob Storage-tárolóhoz. Ehhez ismételje meg a fenti lépéseket. Ezután nyissa meg az Alkalmazásbeállítások lapot (fogaskerék ikon), és ellenőrizze, hogy van-e a projekt biztonsági jogkivonata. Ha nem, adjon hozzá egy új biztonsági jogkivonatot, és másolja át a token nevét és kulcsát az előző lépésből. Ezután kattintson a beállítások mentése gombra.

### <a name="resume-a-project"></a>Projekt folytatása

Végül nyissa meg a Főoldalt (ház ikon), és kattintson a Cloud Project megnyitása lehetőségre. Ezután válassza ki a blob Storage-kapcsolatokat, és válassza ki a projekt *. fott* fájlját. Az alkalmazás betölti a projekt összes beállítását, mert a biztonsági jogkivonattal rendelkezik.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja az űrlap-felismerő minta címkézési eszközt egy olyan modell betanításához, amely manuálisan címkézett adattal rendelkezik. Ha saját eszközt szeretne felépíteni a betanítási adatok címkézéséhez, használja a megcímkézett adatok betanításával foglalkozó REST API-kat.

> [!div class="nextstepaction"]
> [Betanítás címkékkel a Python használatával](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

* [Mi a Form Recognizer?](../overview.md)
* [Űrlap-felismerő rövid útmutatója](client-library.md)
