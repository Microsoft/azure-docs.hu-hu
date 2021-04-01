---
title: Hibaelhárítás – QnA Maker
description: A QnA Maker szolgáltatással kapcsolatos leggyakrabban felmerülő kérdések válogatott listája segít a szolgáltatás gyorsabb és jobb eredményekkel való elfogadásában.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: 7b7ac20672ee653cbf6d2b82b7a9454c1d742b2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612694"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker hibaelhárítása

A QnA Maker szolgáltatással kapcsolatos leggyakrabban felmerülő kérdések válogatott listája segít a szolgáltatás gyorsabb és jobb eredményekkel való elfogadásában.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Előrejelzések kezelése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

<details>
<summary><b>Hogyan javíthatom a lekérdezési előrejelzések teljesítményének teljesítményét?</b></summary>

**Válasz**: az átviteli teljesítménnyel kapcsolatos problémák azt jelzik, hogy az App Service és a Cognitive Search számára is vertikális felskálázásra van szükség. A teljesítmény növelése érdekében érdemes lehet egy replikát hozzáadni a Cognitive Searchhoz.

További információ a [díjszabási szintekről](Concepts/azure-resources.md).
</details>

<details>
<summary><b>A QnAMaker szolgáltatás végpontjának beszerzése</b></summary>

**Válasz**: a QnAMaker szolgáltatás végpontja hibakeresési célokra használható, amikor felveszi a kapcsolatot a QnAMaker támogatási szolgálatával vagy a UserVoice. A végpont egy URL-cím ebben az űrlapban: `https://your-resource-name.azurewebsites.net` .

1. Lépjen a QnAMaker szolgáltatásra (erőforráscsoport) a [Azure Portal](https://portal.azure.com)

    ![QnAMaker Azure-erőforráscsoport Azure Portal](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Válassza ki a QnA Maker erőforráshoz társított App Service. A nevek általában azonosak.

     ![QnAMaker kiválasztása App Service](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. A végpont URL-címe az Áttekintés szakaszban érhető el

    ![QnAMaker végpont](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

<details>
<summary><b>Hogyan javíthatom a lekérdezési előrejelzések teljesítményének teljesítményét?</b></summary>

**Válasz**: az adatátviteli teljesítménnyel kapcsolatos problémák azt jelzik, hogy fel kell mérni a Cognitive Search. A teljesítmény növelése érdekében érdemes lehet egy replikát hozzáadni a Cognitive Searchhoz.

További információ a [díjszabási szintekről](Concepts/azure-resources.md).
</details>

---

## <a name="manage-the-knowledge-base"></a>A Tudásbázis kezelése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

<details>
<summary><b>Véletlenül töröltem a QnA Maker egy részét, mit tegyek?</b></summary>

**Válasz**: ne törölje a létrehozott Azure-szolgáltatásokat a QnA Maker erőforrással, például a Search vagy a Web App szolgáltatással együtt. Ezek a QnA Maker működéséhez szükségesek, ha törli az egyiket, QnA Maker nem fog megfelelően működni.

Az összes törlés végleges, beleértve a kérdés-és a választ, a fájlokat, az URL-eket, az egyéni kérdéseket és válaszokat, a tudásbázisokat vagy az Azure-erőforrásokat. A Tudásbázis bármely részének törlése előtt győződjön meg arról, hogy a tudásbázist a **Beállítások** lapról exportálja.

</details>

<details>
<summary><b>Miért nem/file a kérdés-válasz párokat az URL-ek (k)?</b></summary>

**Válasz**: lehetséges, hogy QnA Maker nem tud automatikusan kinyerni néhány kérdés-válasz (QnA) tartalmat az érvényes GYIK URL-címekről. Ilyen esetekben beillesztheti a QnA tartalmát egy. txt fájlba, és megtudhatja, hogy az eszköz képes-e a betöltésre. Másik lehetőségként a [QnA Maker portálon](https://qnamaker.ai)is hozzáadhat tartalmat a tudásbázishoz.

</details>

<details>
<summary><b>Mekkora lehet a Tudásbázis létrehozása?</b></summary>

**Válasz**: a Tudásbázis mérete az QnA Maker szolgáltatás létrehozásakor választott Azure Search SKU-ból függ. További [részletekért olvassa el](./concepts/azure-resources.md) a következőt:.

</details>

<details>
<summary><b>Miért nem látok semmit a legördülő listából, amikor megpróbálok új tudásbázist létrehozni?</b></summary>

**Válasz**: még nem hozott létre QnA Maker szolgáltatásokat az Azure-ban. Ennek [megismeréséhez olvassa el](./How-To/set-up-qnamaker-service-azure.md) a következőt:.

</details>

<details>
<summary><b>Hogyan megoszthat tudásbázist másokkal?</b></summary>

**Válasz**: a megosztás a QnA Maker szolgáltatás szintjén működik, azaz a szolgáltatásban található összes Tudásbázis meg lesz osztva. [Itt](./index.yml) olvashat arról, hogyan lehet együttműködni a Tudásbázisban.

</details>

<details>
<summary><b>Megoszthat egy tudásbázist olyan közreműködővel, amely nem ugyanabban az HRE-bérlőben található, a Tudásbázis módosításához?</b></summary>

**Válasz**: a megosztás az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) alapján történik. Ha egy másik felhasználóval is megoszthat _bármilyen_ erőforrást az Azure-ban, megoszthatja QnA Maker.

</details>

<details>
<summary><b>Ha van App Service csomag 5 QnAMaker tudásbázissal. Az olvasási/írási jogosultság 5 különböző felhasználóhoz rendelhető, így mindegyikhez csak 1 QnAMaker-alapú Tudásbázis férhet hozzá?</b></summary>

**Válasz**: megoszthat egy teljes QnAMaker szolgáltatást, nem pedig egyedi tudásbázisokat.

</details>

<details>
<summary><b>Hogyan változtathatom meg az alapértelmezett üzenetet, ha nem találok megfelelő egyezést?</b></summary>

**Válasz**: az alapértelmezett üzenet az App Service-ben található beállítások részét képezi.
- Nyissa meg az App Service-erőforrást a Azure Portal

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Kattintson a **Beállítások** lehetőségre.

![qnamaker appservice beállításai](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **DefaultAnswer** -beállítás értékének módosítása
- Indítsa újra az App Service-t

![qnamaker appservice újraindítása](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Miért nem kapok kibontásban a SharePoint-hivatkozást?</b></summary>

**Válasz**: további információért tekintse meg az [adatforrás helyét](./concepts/data-sources-and-content.md#data-source-locations) ismertető témakört.

</details>

<details>
<summary><b>A Tudásbázisban létrehozott frissítések nem jelennek meg a közzététel során. miért ne?</b></summary>

**Válasz**: minden szerkesztési műveletet, legyen szó a tábla frissítéséről, teszteléséről vagy beállításáról, a közzététel előtt menteni kell. Ügyeljen rá, hogy minden szerkesztési művelet után kattintson a **Mentés és a betanítás** gombra.

</details>

<details>
<summary><b>Támogatja a Tudásbázis a gazdag és a multimédiás adatok használatát?</b></summary>

**Válasz**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Multimédiás Automatikus kibontás fájlok és URL-címek esetén

* URL-címek – korlátozott HTML-Markdown átalakítási képesség.
* Fájlok – nem támogatott

#### <a name="answer-text-in-markdown"></a>Válasz szövege a Markdown
Ha a QnA párok szerepelnek a Tudásbázisban, szerkesztheti a válasz Markdown szövegét, hogy a nyilvános URL-címekről elérhető adathordozóra mutató hivatkozásokat is tartalmazzon.


</details>

<details>
<summary><b>A QnA Maker támogatja a nem angol nyelvű nyelveket?</b></summary>

**Válasz**: Tekintse meg a [támogatott nyelvek](./overview/language-support.md)további részleteit.

Ha több nyelvből származó tartalommal rendelkezik, ügyeljen arra, hogy minden nyelvhez külön szolgáltatást hozzon létre.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

<details>
<summary><b>Miért nem/file a kérdés-válasz párokat az URL-ek (k)?</b></summary>

**Válasz**: lehetséges, hogy QnA Maker nem tud automatikusan kinyerni néhány kérdés-válasz (QnA) tartalmat az érvényes GYIK URL-címekről. Ilyen esetekben beillesztheti a QnA tartalmát egy. txt fájlba, és megtudhatja, hogy az eszköz képes-e a betöltésre. Másik lehetőségként a [QnA Maker portálon](https://qnamaker.ai)is hozzáadhat tartalmat a tudásbázishoz.

</details>

<details>
<summary><b>Mekkora lehet a Tudásbázis létrehozása?</b></summary>

**Válasz**: a Tudásbázis mérete az QnA Maker szolgáltatás létrehozásakor választott Azure Search SKU-ból függ. További [részletekért olvassa el](./concepts/azure-resources.md) a következőt:.

</details>

<details>
<summary><b>Miért nem látok semmit a legördülő listából, amikor megpróbálok új tudásbázist létrehozni?</b></summary>

**Válasz**: még nem hozott létre QnA Maker szolgáltatásokat az Azure-ban. Ennek [megismeréséhez olvassa el](./How-To/set-up-qnamaker-service-azure.md) a következőt:.

</details>

<details>
<summary><b>Hogyan megoszthat tudásbázist másokkal?</b></summary>

**Válasz**: a megosztás a QnA Maker szolgáltatás szintjén működik, azaz a szolgáltatásban található összes Tudásbázis meg lesz osztva. [Itt](./index.yml) olvashat arról, hogyan lehet együttműködni a Tudásbázisban.

</details>

<details>
<summary><b>Megoszthat egy tudásbázist olyan közreműködővel, amely nem ugyanabban a Azure Active Directory bérlőben található, a Tudásbázis módosításához?</b></summary>

**Válasz**: a megosztás az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) alapján történik. Ha egy másik felhasználóval is megoszthat _bármilyen_ erőforrást az Azure-ban, megoszthatja QnA Maker.

</details>

<details>
<summary><b>Az olvasási/írási jogosultság 5 különböző felhasználóhoz rendelhető, így mindegyikhez csak 1 QnAMaker-alapú Tudásbázis férhet hozzá?</b></summary>

**Válasz**: megoszthat egy teljes QnAMaker szolgáltatást, nem pedig egyedi tudásbázisokat.

</details>

<details>
<summary><b>Miért nem kapok kibontásban a SharePoint-hivatkozást?</b></summary>

**Válasz**: további információért tekintse meg az [adatforrás helyét](./concepts/data-sources-and-content.md#data-source-locations) ismertető témakört.

</details>

<details>
<summary><b>A Tudásbázisban létrehozott frissítések nem jelennek meg a közzététel során. miért ne?</b></summary>

**Válasz**: minden szerkesztési műveletet, legyen szó a tábla frissítéséről, teszteléséről vagy beállításáról, a közzététel előtt menteni kell. Ügyeljen rá, hogy minden szerkesztési művelet után kattintson a **Mentés és a betanítás** gombra.

</details>

<details>
<summary><b>Támogatja a Tudásbázis a gazdag és a multimédiás adatok használatát?</b></summary>

**Válasz**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Multimédiás Automatikus kibontás fájlok és URL-címek esetén

* URL-címek – korlátozott HTML-Markdown átalakítási képesség.
* Fájlok – nem támogatott

#### <a name="answer-text-in-markdown"></a>Válasz szövege a Markdown
Ha a QnA párok szerepelnek a Tudásbázisban, szerkesztheti a válasz Markdown szövegét, hogy a nyilvános URL-címekről elérhető adathordozóra mutató hivatkozásokat is tartalmazzon.


</details>

<details>
<summary><b>A QnA Maker támogatja a nem angol nyelvű nyelveket?</b></summary>

**Válasz**: Tekintse meg a [támogatott nyelvek](./overview/language-support.md)további részleteit.

Ha több nyelvből származó tartalommal rendelkezik, ügyeljen arra, hogy minden nyelvhez külön szolgáltatást hozzon létre.

</details>

---

## <a name="manage-service"></a>Szolgáltatás kezelése

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

<details>
<summary><b>Mikor kell újraindítani az App Service-t?</b></summary>

**Válasz**: frissítse az App Service-t, ha a **felhasználói beállítások** [lap](https://www.qnamaker.ai/UserSettings) **Endpoint Keys (végponti kulcsok** ) táblájában a tudásbázishoz tartozó verzió értéke melletti figyelmeztető ikon található.

</details>

<details>
<summary><b>Törölte a meglévő keresési szolgáltatást. Hogyan javíthatom ezt?</b></summary>

**Válasz**: Ha töröl egy Azure Cognitive Search indexet, a művelet végleges, és az index nem állítható helyre.

</details>

<details>
<summary><b>Töröltem az `testkb` indexet a keresési szolgáltatásban. Hogyan javíthatom ezt?</b></summary>

**Válasz**: a régi adatai nem állíthatók helyre. Hozzon létre egy új QnA Maker-erőforrást, és hozza létre újra a tudásbázist.

</details>

<details>
<summary><b>Mikor kell frissíteni a végponti kulcsokat?</b></summary>

**Válasz**: frissítse a végpont kulcsait, ha azt gyanítja, hogy sérült a biztonsága.

</details>

<details>
<summary><b>Használhatom ugyanazt az Azure Cognitive Search-erőforrást a tudásbázisok számára több nyelv használatával?</b></summary>

**Válasz**: Ha több nyelvet és több tudásbázist szeretne használni, a felhasználónak minden nyelvhez létre kell hoznia egy QnA Maker erőforrást. Ez egy külön Azure Search-szolgáltatást hoz létre a nyelv alapján. A különböző nyelvi tudásbázisok egyetlen Azure Search-szolgáltatásban való keverése a találatok teljesítményének romlásához vezet.

</details>

<details>
<summary><b>Hogyan változtathatom meg a QnA Maker által használt Azure Cognitive Search-erőforrás nevét?</b></summary>

**Válasz**: az Azure Cognitive Search erőforrás neve a QnA Maker-erőforrás neve, és néhány véletlenszerűen fűzött betűvel rendelkezik. Így nehéz megkülönböztetni QnA Maker több keresési erőforrását. Hozzon létre egy külön keresési szolgáltatást (nevezze el a kívánt módon), és kapcsolódjon a QnA szolgáltatáshoz. A lépések hasonlóak az [Azure Search verziófrissítéséhez](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)szükséges lépésekhez.

</details>

<details>
<summary><b>Ha QnA Maker visszaadja `Runtime core is not initialized,` , Hogyan javíthatom?</b></summary>

**Válasz**: lehet, hogy az App Service lemezterülete megtelt. A lemezterület kijavításának lépései:

1. A [Azure Portal](https://portal.azure.com)válassza ki a QnA Maker app Service-t, majd állítsa le a szolgáltatást.
1. Noha még mindig az App Service-ben van, válassza a **fejlesztői eszközök**, majd a **speciális eszközök**, majd a **tovább** lehetőséget. Ekkor megnyílik egy új böngészőablak.
1. Válassza a **hibakeresési konzol**, majd a **cmd** parancsot egy parancssori eszköz megnyitásához.
1. Navigáljon a _site/wwwroot/az adat/QnAMaker/_ könyvtárhoz.
1. Távolítsa el az összes olyan mappát, amelynek a neve megkezdődik `rd` .

    Ne **törölje** a következőket:

    * KbIdToRankerMappings.txt fájl
    * Fájl EndpointSettings.js
    * EndpointKeys mappa

1. Indítsa el az App Service-t.
1. A tudásbázishoz férhet hozzá, hogy ellenőrizze, működik-e most.

</details>
<details>
<summary><b>Miért nem működik a Application Insights?</b></summary>

**Válasz**: a probléma megoldásához tekintse át a következő lépéseket:

1. App Service-> beállítások csoport – > konfigurációs szakasz – > Alkalmazásbeállítások – > Name "UserAppInsightsKey" paraméterek megfelelően van konfigurálva, és a megfelelő Application betekintési áttekintés lapra ("Instrumentation-kulcs") GUID azonosítóra van beállítva. 

1. A App Service-> beállítások csoportban – > "Application Insights" szakasz – > győződjön meg arról, hogy az alkalmazás-felismerések engedélyezve vannak és csatlakoztatva vannak a megfelelő Application-adatforráshoz.

</details>

<details>
<summary><b>A Application Insights engedélyezve van, de miért nem működik megfelelően?</b></summary>

**Válasz**: kövesse az alábbi lépéseket: 

1.  A (z) "" APPINSIGHTS_INSTRUMENTATIONKEY "Name" értékének "UserAppInsightsKey" névre való másolásához felülbírálja a értéket, ha már van ilyen érték. 

1.  Ha a "UserAppInsightsKey" kulcs nem létezik az alkalmazás beállításaiban, adjon hozzá egy új kulcsot az adott névvel, és másolja az értéket.

1.  Mentse, és ez automatikusan újraindítja az App Service-t. Ennek fel kell oldania a problémát. 

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)


<details>
<summary><b>Törölte a meglévő keresési szolgáltatást. Hogyan javíthatom ezt?</b></summary>

**Válasz**: Ha töröl egy Azure Cognitive Search indexet, a művelet végleges, és az index nem állítható helyre.

</details>

<details>
<summary><b>Töröltem az `testkb` indexet a keresési szolgáltatásban. Hogyan javíthatom ezt?</b></summary>

**Válasz**: a régi adatai nem állíthatók helyre. Hozzon létre egy új QnA Maker-erőforrást, és hozza létre újra a tudásbázist.

</details>

<details>
<summary><b>Használhatom ugyanazt az Azure Cognitive Search-erőforrást a tudásbázisok számára több nyelv használatával?</b></summary>

**Válasz**: Ha több nyelvet és több tudásbázist szeretne használni, a felhasználónak minden nyelvhez létre kell hoznia egy QnA Maker erőforrást. Ez egy külön Azure Search-szolgáltatást hoz létre a nyelv alapján. A különböző nyelvi tudásbázisok egyetlen Azure Search-szolgáltatásban való keverése a találatok teljesítményének romlásához vezet.

</details>

<details>
<summary><b>Hogyan változtathatom meg a QnA Maker által használt Azure Cognitive Search-erőforrás nevét?</b></summary>

**Válasz**: az Azure Cognitive Search erőforrás neve a QnA Maker-erőforrás neve, és néhány véletlenszerűen fűzött betűvel rendelkezik. Így nehéz megkülönböztetni QnA Maker több keresési erőforrását. Hozzon létre egy külön keresési szolgáltatást (nevezze el a kívánt módon), és kapcsolódjon a QnA szolgáltatáshoz. A lépések hasonlóak az [Azure Search verziófrissítéséhez](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)szükséges lépésekhez.

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>Integrálás más szolgáltatásokkal, többek között a botokkal

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

<details>
<summary><b>Szükség van a bot Framework használatára a QnA Maker használatához?</b></summary>

**Válasz**: nem, a [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) -et nem kell használnia QnA Maker. QnA Maker azonban a [Azure bot Service](/azure/bot-service/)számos sablonjának egyikét kínálja. A bot Service lehetővé teszi a gyors intelligens robot-fejlesztést a Microsoft bot Framework segítségével, és egy kiszolgáló nélküli környezetben fut.

</details>

<details>
<summary><b>Hogyan hozhatok létre egy új robotot a QnA Maker használatával?</b></summary>

**Válasz**: kövesse az [ebben](./Quickstarts/create-publish-knowledge-base.md) a dokumentációban található utasításokat a robot Azure bot Service való létrehozásához.

</details>

<details>
<summary><b>Hogyan használhat egy másik tudásbázist egy meglévő Azure bot Service-szel?</b></summary>

**Válasz**: az alábbi információkra van szüksége a Tudásbázisban:

* Tudásbázis-azonosító.
* A Tudásbázis közzétett végpontjának egyéni altartományának neve, amelyet `host` a **Beállítások** lapon talál a közzététel után.
* A Tudásbázis közzétett végponti kulcsa – a **Beállítások** lapon, a közzététel után.

Ezekkel az információkkal a Azure Portal a robot app Service-t. A **Beállítások – > konfiguráció – > Alkalmazásbeállítások** területen módosítsa ezeket az értékeket.

A Tudásbázis Endpoint Key címkéje `QnAAuthkey` az ABS szolgáltatásban található.

</details>

<details>
<summary><b>Két vagy több ügyfélalkalmazás is megoszthat egy tudásbázist?</b></summary>

**Válasz**: igen, a Tudásbázis tetszőleges számú ügyféltől kérdezhető le. Ha úgy tűnik, hogy a Tudásbázis válasza lassú vagy időtúllépést okoz, érdemes lehet frissíteni a tudásbázishoz társított app Service-szolgáltatási szintet.

</details>

<details>
<summary><b>Hogyan beágyazni a QnA Maker szolgáltatást a saját webhelyemen?</b></summary>

**Válasz**: kövesse az alábbi lépéseket a QnA Maker szolgáltatás webes csevegéses vezérlőként való beágyazásához a webhelyén:

1. A GYIK-robot létrehozásához kövesse az [itt](./Quickstarts/create-publish-knowledge-base.md)található utasításokat.
2. Az [itt](/azure/bot-service/bot-service-channel-connect-webchat) leírt lépéseket követve engedélyezze a webes csevegést

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)


<details>
<summary><b>Szükség van a bot Framework használatára a QnA Maker használatához?</b></summary>

**Válasz**: nem, a [bot Framework](https://github.com/Microsoft/botbuilder-dotnet) -et nem kell használnia QnA Maker. QnA Maker azonban a [Azure bot Service](/azure/bot-service/)számos sablonjának egyikét kínálja. A bot Service lehetővé teszi a gyors intelligens robot-fejlesztést a Microsoft bot Framework segítségével, és egy kiszolgáló nélküli környezetben fut.

</details>

<details>
<summary><b>Hogyan hozhatok létre egy új robotot a QnA Maker használatával?</b></summary>

**Válasz**: kövesse az [ebben](./Quickstarts/create-publish-knowledge-base.md) a dokumentációban található utasításokat a robot Azure bot Service való létrehozásához.

</details>

<details>
<summary><b>Hogyan használhat egy másik tudásbázist egy meglévő Azure bot Service-szel?</b></summary>

**Válasz**: az alábbi információkra van szüksége a Tudásbázisban:

* Tudásbázis-azonosító.
* A Tudásbázis közzétett végpontjának egyéni altartományának neve, amelyet `host` a **Beállítások** lapon talál a közzététel után.
* A Tudásbázis közzétett végponti kulcsa – a **Beállítások** lapon, a közzététel után.

Ezekkel az információkkal a Azure Portal a robot app Service-t. A **Beállítások – > konfiguráció – > Alkalmazásbeállítások** területen módosítsa ezeket az értékeket.

A Tudásbázis Endpoint Key címkéje `QnAAuthkey` az ABS szolgáltatásban található.

</details>

<details>
<summary><b>Két vagy több ügyfélalkalmazás is megoszthat egy tudásbázist?</b></summary>

**Válasz**: igen, a Tudásbázis tetszőleges számú ügyféltől kérdezhető le. Ha úgy tűnik, hogy a Tudásbázis válasza lassú vagy időtúllépést okoz, érdemes lehet frissíteni a tudásbázishoz társított app Service-szolgáltatási szintet.

</details>

<details>
<summary><b>Hogyan beágyazni a QnA Maker szolgáltatást a saját webhelyemen?</b></summary>

**Válasz**: kövesse az alábbi lépéseket a QnA Maker szolgáltatás webes csevegéses vezérlőként való beágyazásához a webhelyén:

1. A GYIK-robot létrehozásához kövesse az [itt](./Quickstarts/create-publish-knowledge-base.md)található utasításokat.
2. Az [itt](/azure/bot-service/bot-service-channel-connect-webchat) leírt lépéseket követve engedélyezze a webes csevegést

---

## <a name="data-storage"></a>Adattárolás

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

<details>
<summary><b>Milyen adattárolási és tárolási helye van?</b></summary>

**Válasz**:

A QnA Maker szolgáltatás létrehozásakor egy Azure-régiót választott ki. A tudásbázisok és a naplófájlok ebben a régióban tárolódnak.

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

<details>
<summary><b>Milyen adattárolási és tárolási helye van?</b></summary>

**Válasz**:

A QnA Maker szolgáltatás létrehozásakor egy Azure-régiót választott ki. A tudásbázisok és a naplófájlok ebben a régióban tárolódnak.

</details>

---
