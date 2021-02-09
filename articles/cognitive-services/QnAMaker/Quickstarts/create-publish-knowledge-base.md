---
title: 'Gyors útmutató: Tudásbázis létrehozása, betanítása és közzététele – QnA Maker'
description: Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. Ebből a cikkből megtudhatja, hogyan hozhat létre egy QnA Maker tudásbázist egy egyszerű gyakori kérdések weblapján, hogy válaszoljon a kérdésekre QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: c59529db0981a1071b76714c48aacaf675e4b17a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987897"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Rövid útmutató: a QnA Maker Tudásbázis létrehozása, betanítása és közzététele

Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. Ebből a cikkből megtudhatja, hogyan hozhat létre egy QnA Maker tudásbázist egy egyszerű gyakori kérdések weblapján, hogy válaszoljon a kérdésekre QnA Maker.

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/) a virtuális gép létrehozásának megkezdése előtt.
> * A Azure Portalban létrehozott QnA Maker [erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) . Jegyezze fel Azure Active Directory AZONOSÍTÓját, előfizetését, az erőforrás létrehozásakor kiválasztott QnA-erőforrás nevét.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Az első QnA Maker Tudásbázis létrehozása

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

1. Jelentkezzen be az [QnAMaker.ai](https://QnAMaker.ai) -portálra az Azure-beli hitelesítő adataival.

2. A QnA Maker portálon válassza a **Tudásbázis létrehozása** elemet.

3. A **Létrehozás** lapon hagyja ki az **1. lépést** , ha már rendelkezik a QnA Maker erőforrással.

    Ha még nem hozta létre az erőforrást, válassza **a QnA-szolgáltatás létrehozása** lehetőséget. A rendszer átirányítja az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), ahol be kell állítania az előfizetésében egy QnA Maker-szolgáltatást. Jegyezze fel Azure Active Directory AZONOSÍTÓját, előfizetését, az erőforrás létrehozásakor kiválasztott QnA-erőforrás nevét.

    Amikor elkészült az erőforrás létrehozásával a Azure Portalban, térjen vissza a QnA Maker portálra, frissítse a böngésző oldalát, és folytassa a **2. lépéssel**.

4. A **2. lépésben** válassza ki az Active Directoryt, előfizetést, szolgáltatást (erőforrás) és a szolgáltatásban létrehozott összes Tudásbázis nyelvét.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="Képernyőkép a QnA Maker szolgáltatás tudásbázisának kiválasztásáról":::

5. A **3. lépésben** nevezze el az Ön tudásbázisát a **minta QnA kb**-ra.

6. A **4. lépésben** konfigurálja a beállításokat a következő táblázattal:

    |Beállítás|Érték|
    |--|--|
    |**Az URL-címek, a. PDF vagy a. docx fájlok többszörös kinyerésének engedélyezése.**|Jelölje be|
    |**Többszörös kapcsolású alapértelmezett szöveg**| Kiválasztás és beállítás|
    |**+ URL-cím hozzáadása**|`https://www.microsoft.com/en-us/software-download/faq`|
    |**Csevegés**|**Professional** kiválasztása|

7. Az **5. lépésben** válassza **a saját kb létrehozása** lehetőséget.

    A kinyerési folyamat néhány percet vesz igénybe, hogy beolvassa a dokumentumot, és azonosítsa a kérdéseket és a válaszokat.

    Miután QnA Maker sikeresen létrehozta a tudásbázist, megnyílik a **Tudásbázis** lap. Ezen a lapon szerkesztheti a Tudásbázis tartalmát.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

1. Jelentkezzen be az [QnAMaker.ai](https://QnAMaker.ai) -portálra az Azure-beli hitelesítő adataival.

2. A QnA Maker portálon válassza a **Tudásbázis létrehozása** elemet.

3. A **Létrehozás** lapon hagyja ki az **1. lépést** , ha már rendelkezik a QnA Maker erőforrással.

    Ha még nem hozta létre az erőforrást, válassza **a QnA-szolgáltatás létrehozása** lehetőséget. A rendszer átirányítja az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), ahol be kell állítania az előfizetésében egy QnA Maker-szolgáltatást. Jegyezze fel Azure Active Directory AZONOSÍTÓját, előfizetését, az erőforrás létrehozásakor kiválasztott QnA-erőforrás nevét.

    Amikor elkészült az erőforrás létrehozásával a Azure Portalban, térjen vissza a QnA Maker portálra, frissítse a böngésző oldalát, és folytassa a **2. lépéssel**.

4. A **2. lépésben** válassza ki az Active Directoryt, előfizetést, szolgáltatást (erőforrás) és a szolgáltatásban létrehozott összes Tudásbázis nyelvét.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/connect-your-knowledge-base.png" alt-text="Képernyőfelvétel a QnA Maker Service Tudásbázis felügyelt előzetes verziójának kiválasztásáról":::

5. Ha az első tudásbázist hozza létre a szolgáltatáshoz, a **2. lépésben** lehetősége van arra, hogy az összes Tudásbázisban egyedi nyelvi beállítást biztosítson. Miután definiálta a nyelvi beállítást az első tudásbázishoz, a szolgáltatás beállításainak későbbi módosítása nem engedélyezett.

6. A **3. lépésben** nevezze el az Ön tudásbázisát a **minta QnA kb**-ra. 

7. A **4. lépésben** konfigurálja a beállításokat a következő táblázattal:

    |Beállítás|Érték|
    |--|--|
    |**Az URL-címek, a. PDF vagy a. docx fájlok többszörös kinyerésének engedélyezése.**|Jelölje be|
    |**Többszörös kapcsolású alapértelmezett szöveg**| Kiválasztás és beállítás|
    |**+ Fájl hozzáadása**| A Surface laptop manuális letöltése a következő helyről: https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf 
    |**Csevegés**|**Professional** kiválasztása|

8. Az **5. lépésben** válassza **a saját kb létrehozása** lehetőséget.

    A kinyerési folyamat néhány percet vesz igénybe, hogy beolvassa a dokumentumot, és azonosítsa a kérdéseket és a válaszokat.

    Miután QnA Maker sikeresen létrehozta a tudásbázist, megnyílik a **Tudásbázis** lap. Ezen a lapon szerkesztheti a Tudásbázis tartalmát.

---

## <a name="add-a-new-question-and-answer-set"></a>Új kérdés és válaszfájl hozzáadása

1. A QnA Maker portál **Szerkesztés** lapján válassza a **+ QnA pár hozzáadása** elemet a helyi eszköztárból.
1. Adja hozzá a következő kérdést:

    `How many Azure services are used by a knowledge base?`

1. Adja hozzá a _Markdown_ formázott választ:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="Adja hozzá a kérdést szövegként és a Markdown formázott választ.":::

    A Markdown szimbóluma `*` . Az `\n` új sorhoz használatos.

    A **Szerkesztés** oldalon látható a Markdown. Amikor később a **tesztelési** panelt használja, látni fogja, hogy a Markdown megfelelően jelenik-e meg.

## <a name="save-and-train"></a>Mentés és betanítás

A jobb felső sarokban kattintson a **Mentés és a betanítás** lehetőségre a módosítások mentéséhez és a QnA Maker betanításához. A rendszer a módosításokat csak akkor őrzi meg, ha menti őket.

## <a name="test-the-knowledge-base"></a>A Tudásbázis tesztelése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

1. A QnA Maker portál jobb felső részén válassza a **tesztelés** lehetőséget, hogy a módosítások érvénybe léptetése megtörténjen.
2. Írjon be egy példát egy felhasználói lekérdezésre a szövegmezőben.

    `I want to know the difference between 32 bit and 64 bit Windows`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/query-dialogue.png" alt-text="Írjon be egy példát egy felhasználói lekérdezésre a szövegmezőben.":::

3. Válassza az **Inspect** (Vizsgálat) lehetőséget a válasz részletesebb vizsgálatához. A test (teszt) ablak segítségével tesztelheti a Tudásbázis módosításait, mielőtt közzéteszi a tudásbázist.

4. Kattintson ismét a **test** **(teszt)** elemre a teszt panel bezárásához.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

1. A QnA Maker portál jobb felső részén válassza a **tesztelés** lehetőséget, hogy a módosítások érvénybe léptetése megtörténjen.
2. Írjon be egy példát egy felhasználói lekérdezésre a szövegmezőben.

    `whats the size of the touchscreen`

3. Ha engedélyezi a MRC szolgáltatást a Tudásbázisban, a **rövid válasz megjelenítése** lehetőség kiválasztásával pontos választ fog látni, ha van ilyen, valamint a teszt ablaktáblán a válasz szövegrész is. 

    ![Felügyelt engedélyezett teszt panel](../media/conversational-context/test-pane-with-managed.png)
    

4. Válassza a vizsgálat lehetőséget a válasz részletesebb vizsgálatához. A test (teszt) ablak segítségével tesztelheti a Tudásbázis módosításait, mielőtt közzéteszi a tudásbázist. 
5. Kattintson ismét a **test** **(teszt)** elemre a teszt panel bezárásához.
---

## <a name="publish-the-knowledge-base"></a>A tudásbázis közzététele

Ha közzétesz egy tudásbázist, a Tudásbázis tartalma az `test` indexből egy `prod` Azure Search-indexbe kerül.

![Képernyőkép a Tudásbázis tartalmának áthelyezéséről](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. A QnA Maker portálon válassza a **Közzététel** lehetőséget. Ezután a megerősítéshez válassza az oldalon a **Publish** (Közzététel) lehetőséget.

    Ezzel a QnA Maker-szolgáltatás sikeresen közzé lett téve. Mostantól használhatja a végpontot az alkalmazásában vagy robotkódjában.

    ![A sikeres közzététel képernyőképe](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Robot létrehozása

A közzététel után létrehozhat egy robotot a **közzétételi lapról:**

* Több robotot is létrehozhat, amelyek mindegyike azonos tudásbázisra mutat a különböző régiókban vagy díjszabási tervekben az egyes robotok számára.
* Ha csak egy robotot szeretne használni a Tudásbázisban, használja a Azure Portal hivatkozásán látható **összes robot megtekintését** az aktuális robotok listájának megtekintéséhez.

Ha módosítja a tudásbázist, és újból közzéteszi, nem kell további műveleteket végrehajtania a robottal. Már konfigurálva van a Tudásbázisban való együttműködésre, és a Tudásbázis minden jövőbeli módosításával működik. Minden alkalommal, amikor közzétesz egy tudásbázist, az ahhoz kapcsolódó összes robot automatikusan frissül.

1. A QnA Maker portál **Közzététel** lapján válassza a **robot létrehozása** lehetőséget. Ez a gomb csak azután jelenik meg, hogy közzétette a tudásbázist.

    ![A robot létrehozásának képernyőképe](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Megnyílik egy új böngésző lap a Azure Portal számára a Azure Bot Service létrehozási oldalával. Konfigurálja az Azure bot Service-t. A robot és a QnA Maker megoszthatják a Web App Service-csomagot, de nem oszthatják meg a webalkalmazást. Ez azt jelenti, hogy a robot **alkalmazásának neve** nem lehet a QnA Maker szolgáltatás alkalmazásának neve.

    * **Ajánlott**
        * A bot fogantyújának módosítása – ha nem egyedi.
        * Válassza ki az SDK nyelvét. A robot létrehozása után letöltheti a kódot a helyi fejlesztési környezetbe, és folytathatja a fejlesztési folyamatot.
    * **Nem ajánlott**
        * a robot létrehozásakor módosítsa a Azure Portal következő beállításait. A meglévő Tudásbázisban előre ki vannak töltve:
           * QnA-hitelesítési kulcs
           * App Service-csomag és-hely


1. A robot létrehozása után nyissa meg a **bot Service** -erőforrást.
1. A **bot Management** alatt válassza **a tesztelés webes csevegésben** lehetőséget.
1. Írja be a következőt a csevegési **üzenetbe**:

    `Azure services?`

    A csevegési robot a Tudásbázisban válaszol.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Adjon meg egy felhasználói lekérdezést a teszt webes csevegésben.":::

## <a name="what-did-you-accomplish"></a>Mit ért el?

Létrehozott egy új tudásbázist, hozzáadott egy nyilvános URL-címet a tudásbázishoz, hozzáadta a saját QnA párja, betanított, tesztelt és közzétette a tudásbázist.

Miután közzétette a tudásbázist, létrehozott egy robotot, és tesztelte a robotot.

Ez néhány percen belül elvégezhető, anélkül, hogy kódot kellene írnia, vagy törölni kellene a tartalmat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a következő rövid útmutatót, törölje a QnA Maker és a bot Framework erőforrásait a Azure Portalban.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Metaadatokkal rendelkező kérdések felvétele](add-question-metadata-portal.md)

További információk:

* [Markdown-formátum a válaszokban](../reference-markdown-format.md)
* QnA Maker [adatforrások](../Concepts/data-sources-and-content.md).
