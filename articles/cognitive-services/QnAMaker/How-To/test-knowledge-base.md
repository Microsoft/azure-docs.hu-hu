---
title: Tudásbázis tesztelése – QnA Maker
description: A QnA Maker Tudásbázis tesztelése egy iterációs folyamat fontos részét képezi, amellyel javítható a visszaadott válaszok pontossága. A tudásbázist egy bővített csevegési felületen keresztül tesztelheti, amely lehetővé teszi a szerkesztést is.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 273548ec095ce04772438a2d732b914d80d976cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353153"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>A Tudásbázis tesztelése QnA Maker

A QnA Maker Tudásbázis tesztelése egy iterációs folyamat fontos részét képezi, amellyel javítható a visszaadott válaszok pontossága. A tudásbázist egy bővített csevegési felületen keresztül tesztelheti, amely lehetővé teszi a szerkesztést is.

## <a name="interactively-test-in-qna-maker-portal"></a>Interaktív tesztelés QnA Maker portálon

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

1. A Tudásbázisban a saját **Tudásbázis** lapján megjelenő név kiválasztásával férhet hozzá.
1. A teszt kivetítése panel eléréséhez válassza a **tesztelés** lehetőséget az alkalmazás felső paneljén.
1. Írjon be egy lekérdezést a szövegmezőbe, majd válassza az ENTER billentyűt.
1. A program válaszként a Tudásbázis legjobb egyezésű válaszát adja vissza.

### <a name="clear-test-panel"></a>Teszt panel törlése

Ha törölni szeretné az összes megadott tesztelési lekérdezést és azok eredményeit a tesztelési konzolról, válassza az **Indítás** lehetőséget a teszt panel bal felső sarkában.

### <a name="close-test-panel"></a>A teszt panel lezárása

A teszt panel bezárásához kattintson újra a **teszt** gombra. Amíg a teszt panel meg van nyitva, nem szerkesztheti a Tudásbázis tartalmát.

### <a name="inspect-score"></a>Pontszám vizsgálata

A teszt eredményének részleteit a vizsgálat panelen tekintheti meg.

1.  Nyissa meg a teszt kivetítése panelt, **és válassza a vizsgálat lehetőséget** a válasz további részleteinek megtekintéséhez.

    ![Válaszok vizsgálata](../media/qnamaker-how-to-test-knowledge-bases/inspect.png)

2.  Megjelenik a vizsgálat panel. A panel tartalmazza a leggyakoribb pontozási szándékot, valamint az azonosított entitásokat. A panel a kiválasztott kifejezés eredményét jeleníti meg.

### <a name="correct-the-top-scoring-answer"></a>A legfontosabb pontozásos válasz javítása

Ha a felső pontozási válasz helytelen, válassza ki a megfelelő választ a listából, és válassza a **Mentés és a betanítás** lehetőséget.

![A legfontosabb pontozásos válasz javítása](../media/qnamaker-how-to-test-knowledge-bases/choose-answer.png)

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Adott válaszhoz hozzáadhat alternatív űrlapokat is. Írja be a helyettesítő válaszokat a szövegmezőbe, és válassza az ENTER billentyűt a hozzáadásához. A frissítések mentéséhez válassza a **Mentés és a betanítás** lehetőséget.

![Alternatív kérdések hozzáadása](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Új válasz hozzáadása

Új választ adhat hozzá, ha a meglévő válaszok bármelyike helytelen, vagy a válasz nem létezik a Tudásbázisban (nem található megfelelő egyezés a KB-ban).

A válaszok lista alján található szövegmezővel adjon meg egy új választ, és az ENTER billentyű lenyomásával adja hozzá.

Válassza a **Mentés és a tanítás** lehetőséget a válasz megőrzéséhez. Mostantól új kérdés-válasz pár lett hozzáadva a tudásbázishoz.

> [!NOTE]
> A Tudásbázis összes módosítása csak a **Mentés és a betanítás** gomb megnyomása után menthető.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

1. A Tudásbázisban a saját **Tudásbázis** lapján megjelenő név kiválasztásával férhet hozzá.
1. A teszt kivetítése panel eléréséhez válassza a **tesztelés** lehetőséget az alkalmazás felső paneljén. 
1. Ekkor megjelenik egy jelölőnégyzet a felső **megjelenített rövid válaszon**, amely alapértelmezés szerint ki van választva. Ezzel a beállítással engedélyezheti a MRC-alapú válaszadási tartomány észlelését a tesztelési panelen. 
1. Írjon be egy lekérdezést a szövegmezőbe, majd válassza az ENTER billentyűt. 
1. Minden lekérdezés esetében, ha van egy pontos válasz/rövid válasz a válasz áthaladásakor, majd a legjobb egyezéssel rendelkező válasz áthaladását, amely a Tudásbázisban szerepel, a lekérdezéshez is rövid választ fog adni.
    ![Felügyelt engedélyezett teszt panel](../media/qnamaker-how-to-test-knowledge-bases/test-pane-with-managed-detail.png)
1. Ha kijelöli a **rövid válasz megjelenítése** lehetőséget, akkor a rendszer csak a Tudásbázis legjobb egyezésű válaszát adja vissza válaszként.

### <a name="clear-test-panel"></a>Teszt panel törlése

Ha törölni szeretné az összes megadott tesztelési lekérdezést és azok eredményeit a tesztelési konzolról, válassza az **Indítás** lehetőséget a teszt panel bal felső sarkában.

### <a name="close-test-panel"></a>A teszt panel lezárása

A teszt panel bezárásához kattintson újra a **teszt** gombra. Amíg a teszt panel meg van nyitva, nem szerkesztheti a Tudásbázis tartalmát.

### <a name="inspect-score"></a>Pontszám vizsgálata

A teszt eredményének részleteit a vizsgálat panelen tekintheti meg.

1.  Nyissa meg a teszt kivetítése panelt, **és válassza a vizsgálat lehetőséget** a válasz további részleteinek megtekintéséhez.

    ![A válaszok előzetes verziójának vizsgálata](../media/qnamaker-how-to-test-knowledge-bases/inspect-with-managed.png)

2.  Megjelenik a vizsgálat panel. A panel tartalmazza a leggyakoribb pontozási szándékot, valamint az azonosított entitásokat. A panel a kiválasztott kifejezés eredményét jeleníti meg.
3. A panelen látható a válasz áthaladási pontszáma, valamint az észlelt válaszok száma.

### <a name="correct-the-top-scoring-answer"></a>A legfontosabb pontozásos válasz javítása

Ha a felső pontozási válasz helytelen, válassza ki a megfelelő választ a listából, és válassza a **Mentés és a betanítás** lehetőséget.

![A legfontosabb pontozási válasz előzetes verziójának javítása](../media/qnamaker-how-to-test-knowledge-bases/choose-answer-managed.png)

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása

Adott válaszhoz hozzáadhat alternatív űrlapokat is. Írja be a helyettesítő válaszokat a szövegmezőbe, és válassza az ENTER billentyűt a hozzáadásához. A frissítések mentéséhez válassza a **Mentés és a betanítás** lehetőséget.

![Alternatív kérdések hozzáadása – előzetes verzió](../media/qnamaker-how-to-test-knowledge-bases/add-alternate-question-with-managed.png)

### <a name="add-a-new-answer"></a>Új válasz hozzáadása

Új választ adhat hozzá, ha a meglévő válaszok bármelyike helytelen, vagy a válasz nem létezik a Tudásbázisban (nem található megfelelő egyezés a KB-ban).

A válaszok lista alján található szövegmezővel adjon meg egy új választ, és az ENTER billentyű lenyomásával adja hozzá.

Válassza a **Mentés és a tanítás** lehetőséget a válasz megőrzéséhez. Mostantól új kérdés-válasz pár lett hozzáadva a tudásbázishoz.

---

### <a name="test-the-published-knowledge-base"></a>A közzétett Tudásbázis tesztelése

A Tudásbázis közzétett verzióját tesztelheti a teszt ablaktáblán. Miután közzétette a KB-ot, válassza ki a **közzétett tudásbázist** , és küldjön egy lekérdezést a közzétett Tudásbázis eredményeinek lekéréséhez.

![Tesztelés egy közzétett TUDÁSBÁZISban](../media/qnamaker-how-to-test-knowledge-bases/test-against-published-knowledge-base.png)

## <a name="batch-test-with-tool"></a>Batch-teszt eszközzel

A Batch Testing eszközt a következő esetekben használhatja:

* a legfontosabb válasz és a pontszám meghatározása a kérdésekre
* a várt válasz ellenőrzése a kérdéses csoportra vonatkozóan

### <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* [Hozzon létre egy QnA Maker szolgáltatást](../Quickstarts/create-publish-knowledge-base.md) , vagy használjon egy meglévő szolgáltatást, amely az angol nyelvet használja.
* A többfunkciós [minta `.docx` fájl](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) letöltése
* Töltse le a [Batch Testing eszközt](https://aka.ms/qnamakerbatchtestingtool), bontsa ki a `.zip` fájlból a végrehajtható fájlt.

### <a name="sign-into-qna-maker-portal"></a>Bejelentkezés QnA Maker portálra

[Jelentkezzen be](https://www.qnamaker.ai/) a QnA Maker portálra.

### <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Új Tudásbázis létrehozása a multi-turn sample.docx fájlból

1. Válassza a **Tudásbázis létrehozása** elemet az eszköztáron.
1. Hagyja ki az **1. lépést** , mert már rendelkeznie kell egy QnA Maker erőforrással, a **2. lépésre** való áttéréssel pedig kiválaszthatja a meglévő erőforrás-információkat:
    * Azure Active Directory azonosítója
    * Azure-előfizetés neve
    * Azure QnA szolgáltatás neve
    * Nyelv – az angol nyelv
1. Adja meg a `Multi-turn batch test quickstart` Tudásbázis nevét.

1. A **4. lépésben** konfigurálja a beállításokat a következő táblázattal:

    |Beállítás|Érték|
    |--|--|
    |**Az URL-címek, a. PDF vagy a. docx fájlok többszörös kinyerésének engedélyezése.**|Jelölje be|
    |**Alapértelmezett válasz szövege**| `Batch test - default answer not found.`|
    |**+ Fájl hozzáadása**|Válassza ki a letöltött `.docx` fájl listáját az előfeltételek között.|
    |**Csevegés**|**Professional** kiválasztása|

1. Az **5. lépésben** válassza **a saját kb létrehozása** lehetőséget.

    A létrehozási folyamat befejeződése után a portálon megjelenik a szerkeszthető Tudásbázis.

### <a name="save-train-and-publish-knowledge-base"></a>Tudásbázis mentése, betanítása és közzététele

1. A Tudásbázis mentéséhez válassza a **Mentés és a betanítás** lehetőséget az eszköztárból.
1. Válassza a **Közzététel** lehetőséget az eszköztáron, majd válassza a **Közzététel** újra lehetőséget a Tudásbázis közzétételéhez. A közzététel elérhetővé teszi a tudásbázist egy nyilvános URL-végpontról érkező lekérdezések számára. A közzététel befejezésekor mentse a **közzétételi lapon** megjelenő gazdagép URL-címét és a végponti kulcs információit.

    |Szükséges adatértékek| Példa|
    |--|--|
    |Közzétett gazdagép|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Közzétett kulcs|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (32 karakterből álló karakterlánc látható `Endpoint` )|
    |Alkalmazásazonosító|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (36 karakterből álló karakterlánc `POST` ) |

### <a name="create-batch-test-file-with-question-ids"></a>Batch-tesztkörnyezet létrehozása a kérdés-azonosítókkal

A Batch test eszköz használatához hozzon létre egy nevű fájlt `batch-test-data-1.tsv` egy szövegszerkesztővel. A fájlnak UTF-8 formátumúnak kell lennie, és a következő oszlopokat kell elválasztania egy lapon.

|TSV bemeneti fájl mezői|Jegyzetek|Példa|
|--|--|--|
|Tudásbázis-azonosító|A Tudásbázis-azonosító a közzétételi oldalon található. Egy adott szolgáltatásban egyszerre több tudásbázist is kipróbálhat egyetlen fájlban, ha különböző Tudásbázis-azonosítókat használ egyetlen fájlban.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (36 karakterből álló karakterlánc `POST` ) |
|Kérdés|A felhasználó által megadott kérdés szövege. 1 000 karakter max.|`How do I sign out?`|
|Metaadatcímkék|választható|`topic:power` a `key:value` formátumot használja|
|Felső paraméter|választható|`25`|
|Várt válasz azonosítója|választható|`13`|

Ebben a Tudásbázisban vegyen fel három sort csak a két szükséges oszlop közül a fájlhoz. Az első oszlop a Tudásbázis-azonosító, a második oszlop pedig a következő kérdéseket tartalmazza:

|2. oszlop – kérdések|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Ezek a kérdések a Tudásbázis pontos szövege, és a 100 értéket kell visszaadni a megbízhatósági pontszámnak.

Ezután vegyen fel néhány kérdést, amely hasonló a kérdésekhez, de nem pontosan ugyanaz, mint három sorban, ugyanazzal a Tudásbázis-AZONOSÍTÓval:

|2. oszlop – kérdések|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Győződjön meg arról, hogy az egyes oszlopok csak tabulátorral vannak elválasztva. A kezdő vagy záró szóközök bekerülnek az oszlopba, és a program kivételeket okoz, ha a típus vagy a méret helytelen.

A Batch-tesztüzenet az Excelben való megnyitásakor az alábbi képhez hasonlóan néz ki. A Tudásbázis-azonosítót a `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` biztonsági rendszer váltotta fel. Saját batch-teszt esetén győződjön meg arról, hogy az oszlop megjeleníti a Tudásbázis AZONOSÍTÓját.

> [!div class="mx-imgBorder"]
> ![A. TSV fájl első verziójának beírása a Batch-tesztből](../media/batch-test/batch-test-1-input.png)

### <a name="test-the-batch-file"></a>A batch-fájl tesztelése

Futtassa a Batch Testing programot a parancssorban az alábbi CLI-formátum használatával.

Cserélje `YOUR-RESOURCE-NAME` le `ENDPOINT-KEY` a és a értéket a szolgáltatás nevére és a végponti kulcsra vonatkozó saját értékekre. Ezek az értékek a QnA Maker portál **Beállítások** lapján találhatók.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
A teszt befejeződött, és létrehozza a `out.tsv` fájlt:

> [!div class="mx-imgBorder"]
> ![A. TSV fájl első verziójának kimenete a Batch testből](../media/batch-test/batch-test-1-output.png)

A Tudásbázis-azonosítót a `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` biztonsági rendszer váltotta fel. A saját batch-teszt esetében az oszlop megjeleníti a Tudásbázis AZONOSÍTÓját.

A megbízhatósági pontszám teszt kimenete a negyedik oszlopban azt mutatja, hogy az első három kérdés a várt 100 pontszámot adta vissza, mivel minden kérdés pontosan ugyanaz, mint a Tudásbázisban. Az utolsó három kérdés, amelynek új szövege a kérdés, a 100 nem ad vissza megbízhatósági pontszámként. Ahhoz, hogy a teszthez és a felhasználókhoz is növelje a pontszámot, további alternatív kérdéseket kell felvennie a tudásbázisba.

### <a name="testing-with-the-optional-fields"></a>Tesztelés a választható mezőkkel

Ha megértette a formátumot és a folyamatot, létrehozhat egy tesztelési fájlt, amellyel a Tudásbázisban futtathat egy adatforrásból, például a csevegési naplókból.

Mivel az adatforrás és a folyamat automatizált, a tesztoldal többször is futtatható különböző beállításokkal a megfelelő értékek meghatározásához.

Ha például van egy csevegési naplója, és meg szeretné határozni, hogy mely csevegési szöveg vonatkozzon a metaadatok mezőire, hozzon létre egy tesztoldalt, és állítsa be az összes sor metaadat-mezőit. Futtassa a tesztet, majd tekintse át a metaadatoknak megfelelő sorokat. Általában a egyezéseknek pozitívnak kell lenniük, de érdemes áttekintenie a hamis pozitív eredményeket. A hamis pozitív érték egy olyan sor, amely megfelel a metaadatoknak, de a szöveg alapján nem egyezik.

### <a name="using-optional-fields-in-the-input-batch-test-file"></a>Választható mezők használata a bemeneti batch-teszt fájlban

A következő diagram segítségével megismerheti, hogyan keresheti meg a választható adatokhoz tartozó mezőértékeket.

|Oszlop száma|Nem kötelező oszlop|Az adatok tárolási helye|
|--|--|--|
|3|metaadatok|Létező Tudásbázis exportálása meglévő párok számára `key:value` .|
|4|top|Az alapértelmezett érték a `25` javasolt.|
|5|Kérdés-és Levelesláda-készlet azonosítója|A meglévő Tudásbázis exportálása az azonosító értékekhez. Azt is figyelje meg, hogy az azonosítók a kimeneti fájlban lettek visszaadva.|

### <a name="add-metadata-to-the-knowledge-base"></a>Metaadatok hozzáadása a tudásbázishoz

1. A QnA-portál **Szerkesztés** lapján adja hozzá a metaadatokat `topic:power` a következő kérdésekhez:

    |Kérdések|
    |--|
    |A Surface Pro 4 feltöltése|
    |Az akkumulátor szintjének ellenõrzése|

    Két QnA pár rendelkezik a metaadat-készlettel.

    > [!TIP]
    > Az egyes készletekhez tartozó metaadatok és QnA-azonosítók megtekintéséhez exportálja a tudásbázist. Válassza a **Beállítások** lapot, majd válassza az **Exportálás** `.xls` fájlként lehetőséget. Keresse meg ezt a letöltött fájlt, és nyissa meg az Excel-áttekintés a metaadatokhoz és az AZONOSÍTÓhoz.

1. Válassza a **Mentés és a betanítás** lehetőséget, majd válassza ki a **közzétételi** lapot, majd kattintson a **Közzététel** gombra. Ezek a műveletek a Batch-teszt számára elérhetővé teszik a módosítást. Töltse le a tudásbázist a **Beállítások** lapról.

    A letöltött fájl formátuma megfelelő a metaadatokhoz, valamint a helyes kérdés-és válaszfájl-AZONOSÍTÓhoz. Használja ezeket a mezőket a következő szakaszban

    > [!div class="mx-imgBorder"]
    > ![Exportált Tudásbázis metaadatokkal](../media/batch-test/exported-knowledge-base-with-metadata.png)

### <a name="create-a-second-batch-test"></a>Második batch-teszt létrehozása

A Batch tesztelésének két fő forgatókönyve van:
* **Csevegési naplófájlok feldolgozása** – a leggyakoribb válasz egy korábban láthatatlan kérdés – a leggyakoribb eset az, amikor a feldolgozáshoz a lekérdezéseket tartalmazó naplófájlt kell használnia, például egy csevegési robot felhasználói kérdéseit. Hozzon létre egy batch-fájl tesztet, amely csak a szükséges oszlopokkal rendelkezik. A teszt az egyes kérdések legfelső szintű válaszát adja vissza. Ez nem jelenti azt, hogy a legjobb válasz a helyes válasz. A teszt befejezése után lépjen be az érvényesítési tesztbe.
* **Ellenőrzési teszt** – a várt válasz ellenőrzése. Ehhez a teszthez meg kell határozni, hogy a Batch-tesztben szereplő összes kérdés és a hozzá tartozó várt válasz érvényesítve lett. Ehhez manuális folyamatra lehet szükség.

A következő eljárás azt feltételezi, hogy az a forgatókönyv a csevegési naplók feldolgozására

1. Hozzon létre egy új batch-tesztelési fájlt, amely tartalmazza a választható adatértékeket `batch-test-data-2.tsv` . Adja hozzá a hat sort az eredeti batch test bemeneti fájljához, majd adja hozzá a metaadatokat, a felső és a QnA pár azonosítót az egyes sorokhoz.

    Ha szimulálni szeretné az új szövegnek a tudásbázisból való ellenőrzésének automatikus folyamatát, állítsa az egyes oszlopok metaadatait ugyanarra az értékre: `topic:power` .

    > [!div class="mx-imgBorder"]
    > ![A. TSV fájl bemeneti második verziója a Batch testből](../media/batch-test/batch-test-2-input.png)

1. Futtassa újra a tesztet, és módosítsa a bemeneti és kimeneti fájlneveket, hogy jelezze a második tesztet.

    > [!div class="mx-imgBorder"]
    > ![A. TSV fájl kimeneti második verziója a Batch testből](../media/batch-test/batch-test-2-output.png)

### <a name="test-results-and-an-automated-test-system"></a>Tesztelési eredmények és automatizált tesztelési rendszer

Ez a teszt kimeneti fájl egy automatizált folyamatos tesztelési folyamat részeként elemezhető.

Az adott teszt kimenetét a következőként kell értelmezni: az egyes sorok metaadatokkal vannak szűrve, és mivel az egyes sorok nem egyeznek a tudásbázisban található metaadatokkal, az alapértelmezett válasz a nem egyező sorokhoz ("nem található megfelelő egyezés a KB-ban"). A megegyező sorok a QnA-azonosítót és a pontszámot adtak vissza.

Az összes sor helytelen címkét adott vissza, mert egyetlen sor sem felelt meg a válasz AZONOSÍTÓjának.

Ezekkel az eredményekkel láthatja, hogy csevegési naplót szeretne készíteni, és a szöveget használja az egyes sorok lekérdezéséhez. Az eredmények nem ismerik fel az adatmennyiséget, így az eredmények sokat mondanak arról, hogy milyen információkat használhat a mozgó továbbítás:

* meta-adatszolgáltatások
* QnA-azonosító
* pontszám

A teszteléshez jó ötlet volt a meta-adattal való szűrés? Igen és nem. A tesztelési rendszernek létre kell hoznia minden egyes meta-adatpárokhoz tartozó tesztelési fájlt, valamint egy olyan tesztet, amely nem tartalmaz meta-adatpárokat.

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a Tudásbázis tesztelését, törölje a Batch file eszközt és a tesztelési fájlokat.

Ha nem fogja tovább használni ezt a tudásbázist, törölje a tudásbázist a következő lépésekkel:

1. A QnA Maker portálon válassza a **saját Tudásbázis** lehetőséget a felső menüben.
1. A tudásbázisok listájában válassza a **Törlés** ikont a rövid útmutató tudásbázisának sorában.

[Az eszközre vonatkozó dokumentáció az](../reference-tsv-format-batch-testing.md) alábbiakat tartalmazza:

* az eszköz parancssori példája
* a TSV bemeneti és kimeneti fájljainak formátuma

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis közzététele](../quickstarts/create-publish-knowledge-base.md)